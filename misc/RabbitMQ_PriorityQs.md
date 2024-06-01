# RabbitMQ priority queue

> Messages on a queue are not dequeued until higher-priority queues are empty. No concept of fairness or consideration of starvation

This statement refers to the behavior of priority queues in RabbitMQ, specifically regarding how messages are dequeued (removed from the queue) based on their assigned priorities.

In RabbitMQ, you can create multiple queues with varying priority levels. When there are messages waiting in queues of different priorities, RabbitMQ adheres to the following principle:

"Messages on a queue are not dequeued until higher-priority queues are empty."

This means that RabbitMQ will not dequeue (remove) any messages from a lower-priority queue until all the messages in the higher-priority queues have been dequeued and processed first. It will keep servicing the highest-priority queue until it is completely empty before moving on to the next lower-priority queue.

The statement then continues with: "No concept of fairness or consideration of starvation."

This part highlights a potential issue with this priority queue implementation in RabbitMQ. By strictly enforcing the dequeuing of higher-priority messages first, there is no inherent mechanism to ensure fairness or prevent starvation of lower-priority messages.

Starvation, in this context, refers to a situation where lower-priority messages may never get processed because higher-priority messages keep arriving continuously, effectively starving the lower-priority queues indefinitely.

RabbitMQ's priority queue implementation does not have any built-in concept or consideration for fairness or preventing starvation. It does not automatically introduce any mechanisms to ensure that lower-priority messages eventually get processed, even if higher-priority messages keep arriving at a high rate.

This lack of fairness or starvation consideration could potentially lead to situations where lower-priority messages are neglected for an extended period, which might not be desirable in certain use cases or systems that require a more balanced approach to message processing across different priority levels.
