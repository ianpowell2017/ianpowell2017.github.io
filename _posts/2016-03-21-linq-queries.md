---
title: Linq examples
author: ianpowell
date: 2016-05-30 13:24:00 +0000
categories: [.net]
tags: [c#,linq]
---

Linq examples

## Group by

``` csharp
var query = (from t in Transactions
             group t by new {t.MaterialID, t.ProductID}
             into grp
                    select new
                    {
                        grp.Key.MaterialID,
                        grp.Key.ProductID,
                        Quantity = grp.Sum(t => t.Quantity)
                    }).ToList();
```
From <http://stackoverflow.com/questions/847066/group-by-multiple-columns> 

## Join

``` csharp
var joined = from Item1 in list1
             join Item2 in list2
             on Item1.Id equals Item2.Id // join on some property
             select new { Item1, Item2 };
```
