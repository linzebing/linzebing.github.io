---
layout: post
title:  "[En][Reading] Designing Data-Intensive Applications Part I"
date:   2020-04-05 21:03:36 +0530
---

Foundations of Data Systems

## Reliable, Scalable, and Maintainable Applications

Fan-out: we use it to describe the number of requests to other services that we need in order to serve one incoming
request.

Twitter uses a hybrid approach that delivers Tweets to its users. Most users' tweets are fanned out to home timelines
(contents written to cache to be read by users), but a small number of users with a very large number of followers are
excepted from this fan-out. Tweets from any celebrities that a user may follow are fetched separately and merged with
that user's home timeline when it is read.

In an early-stage startup or an unproven product it's usually more important to be able to iterate quickly on product features than it is to scale to some hypothetical future load.
