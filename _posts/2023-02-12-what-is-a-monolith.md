---
title: What is a monolith
author: ianpowell
date: 2023-02-12 11:32:00 +0000
categories: [Blogging]
tags: [best-practice]
---

# What is a monolith

Recently I worked with a team of developers which had begun working on a new greenfield solution, each of the developers had <= 3 years commercial experience and had been assigned tasks and were busy developing their solutions.

Each of solution delivered were a NodeJS lambda function all sat behind a single API Gateway. When I pointed out the overhead having them all as separate projects in different git repos I was met with "this would create a monolith" and "monoliths are bad".  But is it? Are they bad? And what really is a monolith?

Monoliths have got a bad rap not necessarily because of the size of the code base but often due to the bad coding practices that they contain. A good code base does not suffer from the networking latency, the additional complexity or the extra cost of hosting.  Micro-service architectures, once famed by Spotify, The Financial Times and others were held up as the solution we should all be developing, but often <u>we</u> developers are **not** working for companies that need or have such huge code bases. 

In a start-up business where costs are watched very carefully, starting with the monolith and structuring the application into modules will allow for more code reuse and lower hosting costs.  Keeping an eye on the "[class coupling](https://learn.microsoft.com/en-us/visualstudio/code-quality/code-metrics-values)" metrics will mean that should the application need to be broken up (in a few years time and assuming the business hasn't failed) then the job should be relatively easy.

## Further reading
- [Death by a thousand micro-services](https://renegadeotter.com/2023/09/10/death-by-a-thousand-microservices.html)