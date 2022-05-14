---
title: How to convert IAsync to Task Await Async
author: ianpowell
date: 2016-08-25 18:34:00 +0100
categories: [.net]
tags: [c#]
---

``` c#
var result = await Task<OssObject>.Factory.FromAsync(client.BeginGetObject, client.EndGetObject, bucketName, key, null);
```

In this example, `OssObject` is the return object from `click.BeginGetObject`, the next argument `client.EndGetObject` gets called on the completion.  `bucketName` happens to be an argument passed into `BeginGetObject`.