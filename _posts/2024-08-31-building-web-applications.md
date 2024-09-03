---
title: Just one JavaScript asset away from getting tickets
author: ianpowell
date: 2024-08-31 11:32:00 +0000
categories: [Blogging]
tags: [best-practice]
---

**Note before you start reading this, please remember these are my own thoughts and not associated with any company**

Building web applications is a complicated business and we are taught to break up applications into re-usable parts.  This is to remove unnecessary duplication and centralise code and to leverage inbuilt browser caching.  For most websites this works well but in high traffic situations this can become problematic, this I saw earlier today when I had the task of trying to buy Oasis tickets for their reunion tour taking place in 2025.

I was trying to use ticketmaster.co.uk website.  The web page struggled to load due to the sheer volume of people trying in vain to get tickets and I frequently saw blank pages with errors, as a user all I could do was hit the refresh button which only further compounded the problem.  When I eventually got a page to load it was inoperable which required further page refreshes.  Eventually it would load something that resembles a webpage but would then be frustrated by the spinning disc which spun endlessley.  As a seasoned web developer I thought I'd take a peek at the Chrome Developer toolbar in an attempt to discover what was actually going on.

After the initial page loaded, dozens of additional HTTP requests were being made for images, CSS and Javascript assets, and they were all struggling to load and in some cases returning 503 (Service Unavailable) and 504 (Gateway timeout) errors.  For most customers, seeing the spinning disc they would wait for further content to load, but looking at the traffic tab, this was may never happen. The only option therefore is to hit the refresh button yet again, until the network tab showed that the additional content had now loaded, hence the title of this article, getting tickets on this website meant my efforts were being frauted by one or more JavaScript assets not being able to load due to the traffic volume.  Separating this content out to yet another file has the benefits that many pages could share its content and reduce network traffic, but when it means the web page won't actually work without it, most users will hit the refresh button and further increase network congestion.

To add to matters, ticketmaster had deployed additional smartz to kick those users who are deemed to be bots off the website. Constant refreshing is a sign of being a Bot so I was dangerously close to being flagged as a Bot simply for trying to get a page to finally load correctly.

When it comes to performance tuning there are other solutions handling the extra assets eg. Content Delivery Network or you could just put all the dependent content into the actual HTML page reducing the need for those additional HTTP requests.

Through perserverance and an understanding of what was really going on, meant I got the tickets, but other customers may not have been so lucky and are still looking at the spinning circle in the middle of the page hoping to get tickets, when actually the page has failed.