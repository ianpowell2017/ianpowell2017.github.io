---
title: How to find where .net framework classes have been moved between versions
author: ianpowell
date: 2022-05-17 19:36:00 +0100
categories: [.net]
tags: [c#,testing,moq]
---

## Moq

### Code that needs mocking

```c#
var request = new ReceiveMessageRequest();
var msg = await Task<ReceiveMessageResponse>.Factory.FromAsync(queue.BeginReceiveMessage, queue.EndReceiveMessage, request, null);
```

### Successfully mocked method

```c#
var receiveMessageAsyncResult = new Mock<IAsyncResult>();
_queue.Setup(x => x.BeginReceiveMessage(It.IsAny<ReceiveMessageRequest>(), It.IsAny<AsyncCallback>(), It.IsAny<object>()))
      .Returns(receiveMessageAsyncResult.Object)
      .Callback((ReceiveMessageRequest rmr, AsyncCallback cb, object state) =>
      {
          receiveMessageAsyncResult.Setup(x => x.AsyncState).Returns(state);
          cb(receiveMessageAsyncResult.Object);
      });
_queue.Setup(x => x.EndReceiveMessage(It.IsAny<IAsyncResult>())).Returns(() =>
{
    if (messageCount > 0)
    {
        throw new MessageNotExistException("No more messages");
    }

    messageCount++;
    return new ReceiveMessageResponse
    {
        Message = new Message
        {
            Body = "SGVsbG8gV29ybGQ=", // Hello World
            ReceiptHandle = "12345"
        }
    };
});
```
