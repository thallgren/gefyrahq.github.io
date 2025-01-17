---
layout: page
title: Run vs Bridge
permalink: /run-vs-bridge/
nav_order: 5
---

<script src="https://cdnjs.cloudflare.com/ajax/libs/svg.js/3.1.2/svg.min.js" integrity="sha512-I+rKw3hArzZIHzrkdELbKqrXfkSvw/h0lW/GgB8FThaBVz2e5ZUlSW8kY8v3q6wq37eybIwyufkEZxe4qSlGcg==" crossorigin="anonymous" referrerpolicy="no-referrer"></script>

# Run vs Bridge

Gefyra support two modes of operation: Run and Bridge. Let's dive into the differences.

**TLDR**

| | Run | Bridge |
| --- | --- | --- |
| What | Run a local container and connect it to a Kubernetes cluster | Build on top of run. Connect a local container to a Kubernetes cluster and reroute traffic to it |
| Multi-User | Multiple users can run multiple containers connected to the same cluster | Multiple users cannot bridge the same workload |
| When | When you want to test a service in the cluster or write code that depends on Kubernetes resources | When you want to expose a local container to other services within the cluster |

**Run** -  allows to run a local container and connect it to a Kubernetes cluster. It is executed within a given namespace and can communicate
with other services running in the cluster. This allows your local container to talk to things databases or internal APIs within the cluster
you connected it to. Multiple users can run multiple containers connected to the same cluster. 

{% include_relative _includes/run_animation.html %}

**Bridge** - builds on top of `Run`. In certain situation you want other services to connect to your local container. For this, your local container
needs some kind of representation within the Kubernetes cluster it is connected to. With `Bridge` Gefyra patches a given workload within your 
Kubernetes cluster and reroutes all the incoming traffic to your local container. Multiple users cannot bridge the same workload. 

{% include_relative _includes/bridge_animation.html %}
