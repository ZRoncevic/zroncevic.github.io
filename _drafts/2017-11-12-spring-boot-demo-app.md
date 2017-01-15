---
layout: post
title: Spring boot microservice
categories: [java, programming, spring]
tags: [java, spring, spring-cloud, rest, eureka, admin-dashboard]
---

In this post I will cover the spring boot microservice that I built. Microservice is has a Rest endpoint for flights and also provides receiving flight objects (the same ones that are exposed via Rest WS) via JMS queue - in this case ActiveMQ.

If this was only a microservice that would be boring. So I decided to include spring cloude ecosystem in to the mix.So in this project I added Eurika (discovery service) server, Spring boot actuator (to all projects), spring boot admin from Codecentric and finally from ordina.be microservice dashboard.

So let's break up the demo app into components.

## Spring boot microservice


![Eureka]({{ site.url }}/assets/media/Eureka.png)

![Spring-Boot-Admin]({{ site.url }}/assets/media/Spring-Boot-Admin.png)