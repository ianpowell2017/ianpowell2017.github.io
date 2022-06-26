---
title: Linq 'Group by' example
author: ianpowell
date: 2016-03-21 12:55:00 +0100
categories: [.net]
tags: [c#,linq]
---

## Example

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
