# Azure Functions 

### Tutorials 
Msft VS Live Azure Functions 
https://youtu.be/82QnxMp8PRY?si=Fa819UwqroaCfkTT

# Azure Service Bus & Azure Functions Training Guide

## 📌 Overview
Azure Service Bus enables **asynchronous messaging** with **publish-subscribe** capabilities. Azure Functions act as **event-driven subscribers**, processing messages based on **topics and subscriptions**.

---

## 🎯 Event Handling in Azure Service Bus

An **event** in Azure Service Bus is:
- A **message** published to a **topic**.
- Distributed to **subscriptions**, which filter messages.
- Processed by **Azure Functions**, which trigger when a matching event arrives.

---

## 🏗 Example: Publishing Messages to Azure Service Bus Topic

The following **Azure Function publishes messages** to a Service Bus topic when triggered by an HTTP request.

```csharp
public static class PublishEventFunction
{
    [FunctionName("PublishEventFunction")]
    public static async Task<IActionResult> Run(
        [HttpTrigger(AuthorizationLevel.Function, "post")] HttpRequest req,
        ILogger log)
    {
        string requestBody = await new StreamReader(req.Body).ReadToEndAsync();
        var messageObject = JsonSerializer.Deserialize<MessageModel>(requestBody);

        await SendMessageToServiceBus(messageObject);

        return new OkObjectResult("Message published successfully!");
    }

    private static async Task SendMessageToServiceBus(MessageModel message)
    {
        await using var client = new ServiceBusClient(Environment.GetEnvironmentVariable("ServiceBusConnectionString"));
        ServiceBusSender sender = client.CreateSender("my-topic");

        var serviceBusMessage = new ServiceBusMessage(JsonSerializer.Serialize(message));

        await sender.SendMessageAsync(serviceBusMessage);
    }
}
```

---

## 🏗 Example: Subscriber Function Processing Messages

This **Azure Function subscriber** listens for messages on a **subscription** and processes them.

```csharp
public static class TopicSubscriberFunction
{
    [FunctionName("TopicSubscriberFunction")]
    public static async Task Run(
        [ServiceBusTrigger("my-topic", "my-subscription", Connection = "ServiceBusConnectionString")]
        ServiceBusReceivedMessage message,
        ILogger log)
    {
        var messageObject = JsonSerializer.Deserialize<MessageModel>(message.Body.ToString());

        log.LogInformation($"Processing message ID: {messageObject.Id}, Content: {messageObject.Content}");

        await ProcessMessage(messageObject);
    }

    private static Task ProcessMessage(MessageModel message)
    {
        return Task.CompletedTask;
    }
}
```

---

## 🏆 Advanced Use Cases for Subscriber Functions

### ✅ Message Routing & Filtering
- Define **SQL filters** or **correlation filters** to process only relevant messages.

### ✅ Dead-Letter Queue (DLQ) Handling
- Automatically process **failed messages** for troubleshooting.

### ✅ Retry & Failure Handling
- Implement **custom retry logic** to prevent transient failures.

### ✅ Real-Time Analytics
- Send data to **Azure Application Insights** for monitoring.

---

## 🔍 Example: SQL Filtering in Azure Service Bus

SQL filtering ensures only **specific messages** are delivered to a subscriber.

```sh
az servicebus topic subscription rule create \
  --resource-group myResourceGroup \
  --namespace-name myNamespace \
  --topic-name my-topic \
  --subscription-name my-subscription \
  --name ImportantMessagesRule \
  --filter-sql-expression "category = 'important'"
```

Azure Functions then process **only filtered messages**:

```csharp
public static class FilteredTopicSubscriberFunction
{
    [FunctionName("FilteredTopicSubscriberFunction")]
    public static async Task Run(
        [ServiceBusTrigger("my-topic", "my-subscription", Connection = "ServiceBusConnectionString")]
        ServiceBusReceivedMessage message,
        ILogger log)
    {
        var messageObject = JsonSerializer.Deserialize<MessageModel>(message.Body.ToString());
        log.LogInformation($"Processing filtered message ID: {messageObject.Id}, Category: {messageObject.Category}");

        await ProcessFilteredMessage(messageObject);
    }
}
```

---

## 🔍 Message Locking in Azure Service Bus
- Messages are **locked per subscription**, preventing duplicate processing.
- Locks last **30 seconds** (default) and can be **renewed**.
- If a subscriber **fails**, the message is **released** for retry.

---

## 🛠 Example: Handling Message Lock Expiration & Retries

To prevent message loss, **retry logic** is implemented inside an Azure Function:

```csharp
public static class ResilientSubscriberFunction
{
    [FunctionName("ResilientSubscriberFunction")]
    public static async Task Run(
        [ServiceBusTrigger("my-topic", "my-subscription", Connection = "ServiceBusConnectionString")]
        ServiceBusReceivedMessage message,
        ServiceBusClient client,
        ILogger log)
    {
        try
        {
            var messageObject = JsonSerializer.Deserialize<MessageModel>(message.Body.ToString());
            log.LogInformation($"Processing message ID: {messageObject.Id}, Content: {messageObject.Content}");

            await ProcessMessage(messageObject);

            var receiver = client.CreateReceiver("my-topic", "my-subscription");
            await receiver.CompleteMessageAsync(message);
        }
        catch (Exception ex)
        {
            log.LogError($"Error processing message ID {message.MessageId}: {ex.Message}");

            var receiver = client.CreateReceiver("my-topic", "my-subscription");
            await receiver.AbandonMessageAsync(message);
        }
    }
}
```

---

## 🚨 Handling Dead-Letter Messages (DLQ)

A **dead-letter queue (DLQ)** stores **failed messages** for later processing.

### 🎯 Example: Processing Messages from Dead-Letter Queue

```csharp
public static class DeadLetterProcessorFunction
{
    [FunctionName("DeadLetterProcessorFunction")]
    public static async Task Run(
        [ServiceBusTrigger("my-topic/$DeadLetterQueue", Connection = "ServiceBusConnectionString")]
        ServiceBusReceivedMessage message,
        ILogger log)
    {
        log.LogWarning($"Processing dead-letter message ID: {message.MessageId}");

        var messageObject = JsonSerializer.Deserialize<MessageModel>(message.Body.ToString());
        log.LogWarning($"Failed message details - ID: {messageObject.Id}, Content: {messageObject.Content}");

        await StoreFailedMessage(messageObject);
    }
}
```

---

## 📌 Best Practices for Azure Service Bus
✅ **Use dead-letter queues** for troubleshooting failed messages.  
✅ **Filter messages efficiently** using SQL or correlation filters.  
✅ **Implement retry logic** to handle transient failures.  
✅ **Monitor with Application Insights** to analyze message flow.  

---
