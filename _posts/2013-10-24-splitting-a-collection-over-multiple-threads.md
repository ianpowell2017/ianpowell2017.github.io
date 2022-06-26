---
title: Splitting a collection over multiple threads
author: ianpowell
date: 2013-10-24 13:24:00 +0000
categories: [.net]
tags: [c#]
---

Inspired by http://www.drdobbs.com/windows/custom-parallel-partitioning-with-net-4/224600406?pgno=4

.net 4 source code

``` csharp
 [TestMethod]
 public void TestMethod2()
 {
    var list = new List<CustomerNotificationReply>();
    for (var i = 0; i < 1000; i++)
    {
        list.Add(new CustomerNotificationReply()
        {
            DateTime = DateTime.Now,
            NotificationId = 895,
            ReplyType = new Random((int)DateTime.Now.Ticks).Next(1, 12)
        });
    }

    var partitions = Partitioner.Create(list.ToArray()).GetPartitions(Environment.ProcessorCount);
    var tasks = (from partition in partitions
                select Task.Factory.StartNew(() =>
                {
                    using (partition)
                        body(partition);
                })).ToArray();
    Task.WaitAll(tasks);
}

private void body(IEnumerator<CustomerNotificationReply> partition)
{
    INotificationReplyDAL dal = new NotificationReplyDAL();
    while (partition.MoveNext())
    {
        var x = partition.Current;
        dal.InsertCustomerNotificationReply(x);
    }
}
```