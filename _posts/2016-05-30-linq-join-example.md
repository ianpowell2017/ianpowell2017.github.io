---
title: Linq 'Join' example
author: ianpowell
date: 2016-05-30 19:47:00 +0100
categories: [.net]
tags: [c#,linq]
---

## Example

``` csharp
var joined = from Item1 in list1
             join Item2 in list2
             on Item1.Id equals Item2.Id // join on some property
             select new { Item1, Item2 };
```
