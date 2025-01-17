---
layout: page
title: Run a Ubuntu container instance
permalink: /usecases/ubuntu-in-namespace/
nav_order: 1
parent: Use Cases and Demos
---
# Making any container part of your Kubernetes namespace  
{:.no_toc}

Simple Usecase
{: .label .label-green }

This example demonstrates how to run a local Ubuntu container instance as part of your Kubernetes namespace. 
{: .fs-6 .fw-300 }

<hr />

### What you will learn
{:.no_toc}
* Run a Ubuntu container as part of a Kubernetes namespace
* Install additional software to that instance
* Use this container to call Kubernetes services

<hr />

### What you will need
{:.no_toc}
* [Gefyra](/installation)
* [Getdeck](https://github.com/Schille/getdeck) for setting up the development infrastructure (runs on `k3d`)
* kubectl
* Optionally: [k3d](https://k3d.io) or any other preferred Kubernetes cluster

<hr />

### Table of contents
{:.no_toc}
1. TOC
{:toc}


<hr />

## Creating the local development infrastructure
First, we need a Kubernetes-based development infrastructure which contains all required components. Luckily this can
be achieved quite easily with the [`Deck CLI` from here](https://github.com/Getdeck/getdeck).

Just run: 
```bash
deck get https://github.com/Blueshoe/buzzword-charts.git
``` 
and you will get a fresh `k3d` cluster running locally with all required components installed. 

**Important:** These workloads are intended for demonstration purposes and are not safe for production deployments.

**Optional:** If you don't want to create the development infrastructure using `Getdeck` you can also provide it
yourself. You need:
* a Kubernetes cluster
* some workload, you can choose the example 
from [here](https://github.com/Blueshoe/buzzword-charts/tree/main/buzzword-counter) and `helm install` it yourself
* a node port at 31820:31820/UDP (if running it locally)

## Getting the App Running
**Optional:** In order to observe the workload booting up, check out 
[the Kubernetes dashboard](http://dashboard.127.0.0.1.nip.io:8080/#/workloads?namespace=buzzword) coming with this `deck`.

## Connecting Gefyra to the Kubernetes cluster
The first would be to spin up Gefyra with `gefyra up`. Please be sure to still have the development cluster 
active in your current `kubectl` context. 

**Important:** If you are running a remote Kubernetes cluster you need to specify the `--host` argument with _IP_
of one of your data plane nodes. The default port is _31820_ (`--port`), it may be different depending on firewalls and the cluster
networking.

## Running a container in a Kubernetes namespace
In this example, a Ubuntu will become part of the cluster namespace _buzzword_.
Start the container instance like so :
```bash
$> gefyra run -i ubuntu -N myubuntu -n buzzword -c "bash -c 'tail -f /dev/null'"
> [INFO] Container image 'ubuntu:latest' started with name 'myubuntu' in Kubernetes namespace 'buzzword'
```
No worries, the following explains the parameter list:
* _-i ubuntu_: run the public Docker image ob Ubuntu from here: [https://hub.docker.com/_/ubuntu](https://hub.docker.com/_/ubuntu) 
* _-N myubuntu_: name this local Docker instance _myubuntu_ for further reference
* _-n buzzword_: place this Docker instance in the Kubernetes namespace _buzzword_ (where this example plays)
* _-c "bash -c 'tail -f /dev/null'"_: start this Docker instance and keep it running forever

You can check the output of `docker ps` to see your container instance running. It should look something like this:

```bash
CONTAINER ID   IMAGE                         COMMAND                  CREATED          STATUS          PORTS                                                            NAMES
660ce52ce4e1   ubuntu                        "bash -c 'tail -f /d…"   9 seconds ago    Up 8 seconds                                                                     myubunut
e0add97dee80   gefyra-cargo:20220426153151   "/init"                  14 seconds ago   Up 13 seconds                                                                    gefyra-cargo
97f9908c55df   rancher/k3d-proxy:4.4.8       "/bin/sh -c nginx-pr…"   12 minutes ago   Up 12 minutes   0.0.0.0:8080->80/tcp, :::8080->80/tcp, 0.0.0.0:34089->6443/tcp   k3d-another-cluster-serverlb
1dccf93fc087   rancher/k3s:v1.20.4-k3s1      "/bin/k3s agent"         12 minutes ago   Up 12 minutes   0.0.0.0:31820->31820/udp, :::31820->31820/udp                    k3d-another-cluster-agent-0
91ef49d000b5   rancher/k3s:v1.20.4-k3s1      "/bin/k3s server --t…"   12 minutes ago   Up 12 minutes                                                                    k3d-another-cluster-server-0
```

### Enter the container and call a service
Now that the container is running, you can enter a bash by running: `docker exec -it myubuntu bash`
```bash
root@6178770cd6b1:/#
```
In order to call an http service from this Kubernetes namespace a terminal application could be handy. The official
Ubuntu Docker image does not provide on of my favorites out of the box, but it is easy to add it.

```bash
root@6178770cd6b1:/# apt update && apt install wget -y
[...]
```

On another terminal (so not in your Ubuntu bash) you can consult `kubectl` to inspect the services in the _buzzword_ namespace. For this example it
tells:

```bash
$> kubectl -n buzzword get services
NAME                                   TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)                                 AGE
dashboard-kubernetes-dashboard         ClusterIP   10.43.147.1     <none>        8080/TCP                                18m
[...]
buzzword-counter                       ClusterIP   10.43.221.222   <none>        9000/TCP                                18m
buzzword-counter-postgresql            ClusterIP   10.43.140.176   <none>        5432/TCP                                18m
buzzword-counter-postgresql-headless   ClusterIP   None            <none>        5432/TCP                                18m
buzzword-counter-rabbitmq              ClusterIP   10.43.0.48      <none>        4369/TCP,5672/TCP,25672/TCP,15672/TCP   18m
buzzword-counter-rabbitmq-headless     ClusterIP   None            <none>        4369/TCP,5672/TCP,25672/TCP,15672/TCP   18m
```

Let's see how this works. From within your running Ubuntu bash, you can now call the _buzzword-counter_ service on port
9000:

```bash
root@6178770cd6b1:/# wget -O- buzzword-counter:9000
--2022-04-26 13:41:43--  http://buzzword-counter:9000/
Resolving buzzword-counter (buzzword-counter)... 10.43.221.222
Connecting to buzzword-counter (buzzword-counter)|10.43.221.222|:9000... connected.
HTTP request sent, awaiting response... 200 OK
Length: 565 [text/html]
Saving to: 'STDOUT'

-                                                0%[                                                                                                   ]       0  --.-KB/s               <h1>Buzzwords</h1>
<form action="/increase-counter/" method="post">
    <input type="hidden" name="csrfmiddlewaretoken" value="HrGPa8q6GyjVi5ZeHsf4noTFZoOpxA78OmpOEe8cWut8uyeuVxyZ8wPLN0e3QISM">
    <table>
        <tr>
            <th>Buzzword</th>
            <th>Count</th>
            <th>Increase</th>
            <th>Decrease</th>
        </tr>
    
    </table>
    <div>
        <label for="new_buzzword">New Buzzword:</label>
        <input type="text" id="new_buzzword" name="new_buzzword">
        <button type="submit">Submit</button>
    </div>
</form>
-                                              100%[==================================================================================================>]     565  --.-KB/s    in 0s      

2022-04-26 13:41:43 (18.9 MB/s) - written to stdout [565/565]
```

Et voila! The service responded to the HTTP Get request with the same answer you would get with your browser at:
[http://buzzword-counter.127.0.0.1.nip.io:8080](http://buzzword-counter.127.0.0.1.nip.io:8080).
You can now look around and make yourself familiar with the services
in this namespace - or even connect with the `psql` client to the _PostgreSQL_ instance running in the cluster.

### Remove the Ubuntu container
Once you are done with your work, you can remove this Ubuntu instance again with:
`docker kill myubuntu`. That's it.


## Remove the Development Infrastructure
First run `gefyra down` to uninstall Gefyra's components. If you have initially created the development infrastructure using `Getdeck` you can now run:
```bash
$> deck remove --cluster https://github.com/Blueshoe/buzzword-charts.git
[INFO] Deleting the k3d cluster with name another-cluster
```

If you created the infrastructure yourself, you probably already know how to get rid of everything yourself ;-)

## Additional Notes
If you want maximum convenience for your developers and a supported team oriented workflow, we recommend you 
check out [Unikube](https://unikube.io).
Gefyra is part of Unikube's development workflow.


