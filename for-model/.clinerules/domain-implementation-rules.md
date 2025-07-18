Please follow the requirements below and refer to the examples when generating domain service classes to configure the internal code.

1) Domain Entity (AggregateRoot.java)

  - Must inherit from AbstractAggregateRoot<T> to provide domain event registration functionality
  - Call registerEvent(new SomeEvent(this)) inside major state change methods
  - Entity belongs to a single bounded context
  - Code example
  
     @Entity
      public class Order extends AbstractAggregateRoot<Order> {
          @Id
          @GeneratedValue
          private Long id;

          private String status;

          public void complete() {
              this.status = "COMPLETED";
              registerEvent(new OrderCompletedEvent(this));
          }
      }

2) Domain Event Class (Event.java)

  - Always contains domain objects
  - Designed as immutable class (all fields final, constructor-based)
  - Code example

        public class OrderCompletedEvent {
          private final Order order;

          public OrderCompletedEvent(Order order) {
              this.order = order;
          }

          public Order getOrder() {
              return order;
          }
      }

3) Service Layer (Service.java)

  - Explicitly call eventPublisher.publishEvent(...) after executing domain logic
  - Do not use @DomainEvents, @AfterDomainEventPublication
  - Manually publish events after state changes through repository
  - Perform query, registration, modification, deletion as appropriate for each situation, but when logic comes from EventHandler or Controller, it is essential to call the port method of AggregateRoot.
  - Code example

     @Service
      public class OrderService {

          private final OrderRepository orderRepository;
          private final ApplicationEventPublisher eventPublisher;

          public OrderService(OrderRepository orderRepository,
                              ApplicationEventPublisher eventPublisher) {
              this.orderRepository = orderRepository;
              this.eventPublisher = eventPublisher;
          }

          public void completeOrder(Long orderId) {
              Order order = orderRepository.findById(orderId)
                      .orElseThrow(() -> new EntityNotFoundException("Order not found"));

              order.complete();
              orderRepository.save(order);

              order.domainEvents().forEach(eventPublisher::publishEvent);
              order.clearDomainEvents();
          }
      }

4) Event Processing (EventHandler.java)

  - Must use @EventListener
  - Do not use @TransactionalEventListener or @Async
  - Executed within the same transaction and can rollback on exceptions
  - However, event handlers should be located in the 'domain module that has the responsibility to act' in response to that event
  - If outgoingRelations exist according to Event information in Metadata, logic should be implemented in EventHandler located in the target service. (ex. OrderPlaced -> startDelivery should have logic configured in DeliveryEventHandler.java of Delivery service)
  - Code example 

     @Component
      public class OrderEventHandler {

          @EventListener
          public void handleOrderCompleted(OrderCompletedEvent event) {
              Order order = event.getOrder();
              // Business logic that can be processed within transaction
              System.out.println("Order completion processed: " + order.getId());
          }
      }

5) API Processing (Controller.java)
- Use @RestController
- Configure code for CRUD based on REST API
- Configure methods for isRestRepository: true under command in Metadata
- Set all basic CRUD and Extended APIs to commonly call the Service layer
- Code example 

   @RestController
   @RequestMapping("/orders")
   public class OrderController {

       private final OrderService orderService;

       public OrderController(OrderService orderService) {
           this.orderService = orderService;
       }

       @PostMapping("/{orderId}/complete")
       public ResponseEntity<Void> completeOrder(@PathVariable Long orderId) {
           orderService.completeOrder(orderId);
           return ResponseEntity.ok().build();
       }
   }
