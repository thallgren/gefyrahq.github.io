---
layout: page
title: What is Gefyra
permalink: /details/what-is-gefyra/
nav_order: 1
parent: Technical Details
---

1. TOC
{:toc}

# What is Gefyra?
Gefyra is a toolkit written in Python to organize a local development infrastructure in order to produce software for and with 
Kubernetes while having fun. It is installed on any development computer and starts its work when it is asked. Gefyra runs
as a user-space application and controls the local Docker host and Kubernetes via _Kubernetes Python Client_. 

<p align="center">
  <img src="https://github.com/gefyrahq/gefyra/raw/main/docs/static/img/gefyra-intro.png" alt="Gefyra controls docker and kubeapi"/>
</p>

(_Kubectl_ is not really required but kinda makes sense to be in this picture)

In order for this to work, a few requirements have to be satisfied:
- a Docker host must be available for the user on the development machine (it is convenient if [docker can be run without sudo-privileges](https://docs.docker.com/engine/install/linux-postinstall/#manage-docker-as-a-non-root-user))
- Overlay networking must be supported by the Docker host
- there are a few container capabilities required on both sides, within the Kubernetes cluster and on the local computer
- a node port must be opened on the development cluster for the duration of the development work 

Gefyra makes sure your development container runs as part of the cluster while you still have full access to it. In addition,
Gefyra is able to intercept the target application running within the cluster, i.e. a container in a Pod, and tunnels all traffic hitting said container to the one running 
locally. Now, developers can add new code or fix bugs and run it right away in the Kubernetes cluster, or simply introspect the traffic. 
Gefyra provides the entire infrastructure to do so and provides a high level of developer convenience. 


# Did I hear developer convenience?
The idea is to relieve developers from the hassle to go back and forth to the integration system with containers. Instead, take
the integration system closer to the developer and make the development cycles as short as possible. No more waiting for the CI to complete
just to see the service failing on the first request. Cloud-native (or Kubernetes-native) technologies have completely changed the 
developer experience: infrastructure is increasingly becoming part of developer's business with all the barriers and obstacles, but also chances
to turn the software for the better.  
Gefyra is here to provide a development workflow with the highest convenience possible. It brings low setup times, rapid development, 
high release cadence and super-satisfied managers.

# Gotchas & Current Limitations
- Gefyra's VPN needs a reachable `NodePort`. In most non-local Kubernetes scenarios this requires to set a firewall rule
in order to **allow port 31820 for UDP traffic**. It's simple for most Cloud-providers ("Hyperscaler") and 
doable for custom installations, too.  
- Kubernetes-probes can be faked by Gefyra's Pod component ("Carrier") in order to keep Kubernetes from removing _bridged_ Pods.
However, this is currently only supported for `httpGet` probes. Otherwise, you need to turn off probes during development.
- You will experience issues if you want to _bridge_ containers in Pods which specify a specific `command` (other than common shells).
- This project is in a quite early stage. I assume there are still a few bugs around.

# Why "Gefyra"?
"Gefyra" is the Greek word for "Bridge" and fits nicely with kubernetes' nautical theme.