---
layout: page
title: Docker Desktop Extension
permalink: /docker-desktop-extension/
nav_order: 6
---

# Gefyra Docker Desktop Extension

[Gefyra's Docker Desktop Extension](https://hub.docker.com/r/gefyra/docker-desktop-extension){:target="_blank"} allows you to run containers on your local machine and connect them to Kubernetes-based resources. It is a great way to test a new service in the cluster or write code that depends on Kubernetes resources.

**Looking for CLI rather than a GUI?** Check out [Gefyra's CLI](/installation/){:target="_blank"}!

## 10 minute demo

**Gefyra Docker Desktop Extension Run Demo Video**  
<iframe width="560" height="315" src="https://www.youtube-nocookie.com/embed/4xmaOVul5Ww" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>

**Gefyra Docker Desktop Extension Bridge Demo Video**  
<iframe width="560" height="315" src="https://www.youtube-nocookie.com/embed/EBArR1O2BGk" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>

## Installation
The Gefyra Docker Desktop Extension is available on the Docker Desktop extension [marketplace](https://hub.docker.com/extensions/gefyra/docker-desktop-extension){:target="_blank"}. 

However, you can also install it by running the following command:
```shell
docker extension install gefyra/docker-desktop-extension:latest
```

## Usage

After installing the extension it becomes available in the Docker Desktop Extension sidebar. Gefyra Docker Desktop currently only
support the `run` mode. After clicking on the `run` tile you're guided through the process of running a container on your local machine.

![Docker Desktop Extension Start](/assets/images/extension/home_light.png)

Firstly Gefyra needs to know about your cluster to allow you to set all settings accordingly.

![Docker Desktop Extension Cluster Settings](/assets/images/extension/cluster_light.png)

After choosing your kubeconfig and context you can then proceed to adding the settings for your container. In case you have a remote cluster
you need to provide Gefyra with its connection parameters under `Remote Cluster Settings`.

![Docker Desktop Extension Container Settings](/assets/images/extension/container_light.png)

There are several settings for the container - most importantly the `image`, `namespace` and `command` settings.
The `image` setting is the image that will be used to run the container - Gefyra will you show any image that is available in the local Docker Desktop context as 
well as images that are available in the chosen Kubernetes namespace. The `namespace` setting is the namespace in which the container will be available.
Gefyra allows you to copy the environment variables of a certain workload through the `Copy Environment From` dropdown.
You can add volumes and more variables through the `Add Volume` and `Add Environment Variable` buttons.
As soon as you're done hit run!

![Docker Desktop Extension Load](/assets/images/extension/load_light.png)
Gefyra ensures that its cluster components are in the correct state and starts the container.

Once the container is running Gefyra will show you the logs of the container.
