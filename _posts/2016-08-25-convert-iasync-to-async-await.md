---
title: How to convert IAsync to Task Await Async
author: ianpowell
date: 2016-08-25 18:34:00 +0100
categories: [.net]
tags: [c#,async-pattern]
---

```c#
var result = await Task<OssObject>.Factory.FromAsync(client.BeginGetObject, client.EndGetObject, bucketName, key, null);
```

`OssObject` is the return object in method `click.BeginGetObject`

`client.EndGetObject` is the method which gets called once the `BeginGetObject` has completed.

`bucketName` happens to be an argument passed into `BeginGetObject`.