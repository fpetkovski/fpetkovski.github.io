---
layout: post
title: Implementing an external metrics server for Kubernetes
tags: [kubernetes, go]
---

I recently ran into a use case where I had to horizontaly scale a Kubernetes deployment based on the number of jobs in a particular queue. While the Kubernetes HPA works with works with CPU and memory out of the box, using it with pretty much any any other metric requires you to deploy an entire monitoring solution This at the time seemed like a huge hammer for a small nail.

I wanted to see if I can roll out a leaner solution and find a way to make the HPA work with my specific metric. 
This eventually lead me to writing my own external metrics server for Kubernetes, and I decided to make the proof of concept 
publicly available in case it helps someone with a similar problem.
The entire source code can be found on [github](https://github.com/fpetkovski/k8s-external-metrics-server)

The solution is based on the [custom-metrics-adapter](https://github.com/kubernetes-incubator/custom-metrics-apiserver) project found in the kubernetes-incubator. The project is inteded to serve as a library for bootstraping a metrics server, and it proved to be of immense value for understanding how the kubernetes API can be extended.