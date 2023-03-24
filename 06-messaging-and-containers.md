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
* visibility timeout - new message will be visible for consumers after this time
* retention period - after it message will be deleted from queue
* delivery delay - new message will 

```sh
QUEUE_URL=https://sqs.us-east-1.amazonaws.com/948919258198/cherkavi-queue
aws sqs send-message --queue-url $QUEUE_URL --message-body "Information about the largest city in Any Region." 


aws sqs receive-message --queue-url $QUEUE_URL
```