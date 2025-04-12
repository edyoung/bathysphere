---
title: Deferred Maintenance is a better metaphor than Technical Debt
description: Software Engineering Metaphors debated
slug: deferred-maintenance
date: 2022-04-24 00:00:00+0000
image: image.png
type: post
tags:
    - Software Engineering
---

There's a lot of discussion about Technical Debt in Software Engineering. I believe the term originated [on the C2 wiki](http://wiki.c2.com/?TechnicalDebt). It's often used to explain to non-software-engineers why it's important to invest in code quality.

I'm far from the first person to think that the term Technical Debt is potentially misleading. Rob Jeffries has a discussion [here](https://ronjeffries.com/articles/015-11/tech-debt/). 

To oversimplify, debt involves interest payments. These compound over time, occur at predictable intervals, can be paid off, and cost a fairly predictable amount of money. None of these are true when you decide to take shortcuts in software: the passage of time has no direct compounding effect, and you don't know when the bill will come due or how expensive it will be.

This mismatch between the term and the implications can be a problem if it conveys the wrong impression to our non-software colleague.

I like the term *deferred maintenance* better. This is also an inexact metaphor but I think it's closer. Anyone who has owned a car understands that it needs maintenance. If you don't change the oil at regular intervals, the car will gradually run more slowly and roughly. Eventually, at an upredictable time, the engine will seize up and you'll be faced with a large and unpredictable bill to replace the entire thing. If you don't service the brakes, the results can be significantly worse. Consider a 747 instead of a Corolla if you really want to emphasize the importance of maintenance.

You can extend this notion to help justify the idea of scheduling time explicitly for maintenance activity, like a monthly or quarterly 'tune-up'.

Bottom line: if you have a large piece of software which is not maintained, you own a very complicated machine which is not performing as well as it could, and might break down at any point. 


