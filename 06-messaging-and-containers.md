# Messaging
## SimpleNotificationService ( SNS )
publishes messages to:
* SQS
* Lambda
* Http web hook
* e-mail
* gsm-sms
* mobile push

Entities:
* Topic - producer/publisher
* Subscription - consumer/subscriber

::important:: during sending SNS don't use "\n" as a first message

## Simple Queue Service ( SQS )
> using for asynchronous processing

### SQS Entities:
* Message - small piece of data
* Queue - list of Messages

### SQS Types of queues:
* Standard
  > unlimited number of transactions
* FirstInFirstOut
  > 3000 messages per second
  > "command pattern" with strong ordering
  > multiple copies of messages delivered

### SQS Properties
> When a consumer receives and processes a message from a queue, the message remains in the queue.
> Thus, the consumer must delete the message from the queue after receiving.
* visibility timeout - To prevent other consumers from processing the message again.A period of time during which Amazon SQS prevents other consumers from receiving and processing the message. 
* retention period - after it message will be deleted from queue
* delivery delay - new message will be visible for consumer after this time

```sh
QUEUE_URL=https://sqs.us-east-1.amazonaws.com/948919258198/cherkavi-queue

aws sqs send-message --queue-url $QUEUE_URL --message-body "Information about the largest city in Any Region." 

aws sqs receive-message --queue-url $QUEUE_URL
```

## ElasticContainerService
![ecs components](https://drive.google.com/uc?id=1beRIspFSQFgmia8qMF5Z9RGdOR9mcDCE)