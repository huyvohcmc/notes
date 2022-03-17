# 17.03.2022 Kafka Message Delivery Semantics

In a distributed publish-subscribe messaging system like Kafka, **exactly-once delivery** means a message from the publisher will be delivered to the subscriber exactly once, and only once. This is a standard from a normal point of view for a pub-sub system, but it can be hard to achieved, due to the natures of distributed system:

1. The components that make up the system (the producer, Kafka broker, and the subscriber) can crash independently.
2. Network failure can happen at any time while the producer is sending a message to a Kafka topic, or while the consumer is pulling a message from a Kafka topic. 

Kafka supports 3 message delivery semantics: at-least-once, at-most-once, and exactly-once.

### At-least-once delivery

When the producer sends a message to Kafka broker, it expects a returned message saying that the broker has finished writing the message to the topic. If the broker crashes right before it sends the returned message or the returned message fails to reach the producer, the producer thinks that the message was not written successfully, and will retry to send it again to the broker. In the end, at-least-once delivery guarantee that a message will never be lost, but you may have duplicate messages in the topic, leading to incorrect results from the consumer end.

### At-most-once delivery

When the producer doesn’t receive the “ack” from the broker after it has finished sending the message, it does not retry to send the message again, and consider that the message has been lost (fire-and-forget). For the at-most-once delivery semantic, a message is delivered either one time only or not at all.

### Exactly-once delivery

Even when the producer retries sending a message multiple times to the broker, still the consumer only pull the message once and only once. Kafka can do this by using **idempotent operations** and **transactions**.

### Idempotence

With idempotence, each batch of messages sent to Kafka will contain a sequence number. This sequence number is persisted to the replicated log, and the broker will use it to check for any duplicate messages in the future. The producer can send the same message to the broker how many times it wanted, but the broker only writes the message to the topic *once.*

### Transactions

Kafka uses transactions to enable atomic writes to multiple Kafka topics and partitions. Meaning, when the producer sends a batch of messages to Kafka, either all of them will be successfully written to the topic or none of them will be. Likewise, the consumer can also use the transaction to pull either all the messages from the topic or none at all, allowing end-to-end exactly-once semantics.

```java
producer.initTransactions();
try {
  producer.beginTransaction();
  producer.send(record1);
  producer.send(record2);
  producer.commitTransaction();
} catch(ProducerFencedException e) {
  producer.close();
} catch(KafkaException e) {
  producer.abortTransaction();
}
```

Read more at:

- [https://www.confluent.io/blog/exactly-once-semantics-are-possible-heres-how-apache-kafka-does-it/](https://www.confluent.io/blog/exactly-once-semantics-are-possible-heres-how-apache-kafka-does-it/)
- [https://www.confluent.io/blog/transactions-apache-kafka/](https://www.confluent.io/blog/transactions-apache-kafka/)
