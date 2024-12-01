## MessageChannel

https://docs.spring.io/spring-integration/reference/channel/interfaces.html

Main interface :

```java
public interface MessageChannel {

    boolean send(Message message);

    boolean send(Message message, long timeout);
}
```

Two subinterfaces : 
- `SubscribableChannel` : non-buffering channel. Implementations :
  * `PublishSubscribeChannel` : used for notifications
  * `DirectChannel` : same as PublishSubscribeChannel but only one subscriber receives the message at a time. Mmultiple susbscribers are allowed, and a load balancer defines to who messages are delivered.
- `PollableChannel` : buffering channel. Implementations :
  * `QueueChannel` : point to point channel with a queue to bufferize messages
  * `PriorityChannel` : same as QueueChannel but handles priority to change FIFO order.
  * `RendezvousChannel` : same as QueueChannel but sender is assured message has been handled to a receiver when the send method returns

```java
public interface SubscribableChannel extends MessageChannel {

    boolean subscribe(MessageHandler handler);

    boolean unsubscribe(MessageHandler handler);

}

public interface PollableChannel extends MessageChannel {

   Message<?> receive();

   Message<?> receive(long timeout);

}
```

The `DirectChannel` implementation has point to point semantics but behaves like a PublishSubscribeChannel for one subscriber. It is the default implemntation used in Spring Integration since it is also the cheapest (no queue, no thread polling management).
