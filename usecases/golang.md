---
layout: page
title: Developing Go Applications with Gefyra 
permalink: /usecases/developing-go-with-gefyra/
nav_order: 5
parent: Use Cases and Demos
---
# Developing Go Applications with Gefyra

{:.no_toc}

Simple Usecase
{: .label .label-green }

This example demonstrates how to run a local Golang container with hot code reloading as part of your Kubernetes namespace. 
{: .fs-6 .fw-300 }

<hr />

### What you will learn
{:.no_toc}
* Running a Go application in a local container with hot code reloading
* Using Gefyra to bridge the container into a Kubernetes cluster

### What you will need
{:.no_toc}
* [Gefyra](/installation)
* [Getdeck](https://github.com/Getdeck/getdeck) for setting up the development infrastructure (runs on `k3d`)
* A copy of [https://github.com/gefyrahq/gefyra-demos](https://github.com/gefyrahq/gefyra-demos)
* Optionally: [k3d](https://k3d.io) or any other preferred Kubernetes cluster

### Creating the Development Infrastructure
After cloning the gefyra-demos repository, you can start a local k3d based Kubernetes cluster with the Go demo workload by running
```shell script
cd golang-demo
deck get deck.yaml
```
Once everything is up and running, you can visit [http://gefyra-golang.127.0.0.1.nip.io:8080](http://gefyra-golang.127.0.0.1.nip.io:8080) to verify that everything worked as expected.

### The Demo Application
You can look at and tweak the code for the demo application in `app/main.go`. You will find a very simple HTTP API that returns a String when it receives GET requests at the base route.
In the `app`-directory, you'll also find the Dockerfile that is used to build the container image containing our application. 
**Note**: In the Dockerfile, we install [air](https://github.com/cosmtrek/air), a hot reloading utillity for Go development. In a production Dockerfile, you'd want to specify a dedicated 
build target to install air.

### Enter Gefyra
In order to connect a locally running container to a Kubernetes cluster, we use Gefyra.
First of all, we need to build an image on our development machine like so:
```shell script
cd app
docker build . -t gefyra-golang-example
```

Once the build is finished, we are ready to get started with Gefyra:
```shell script
gefyra up
gefyra run -i gefyra-golang-example -N gefyra-golang-example -n golang-demo -c air -v $(pwd):/app
gefyra bridge -N gefyra-golang-example --container-name gefyra-golang-demo --deployment gefyra-golang-demo --port 3333:3333 -n golang-example
```

`gefyra up` will start the cluster and client side components needed for Gefyra to do it's thing.
The `gefyra run` command will then start a container from the image that we just build in the development infrastructure that we created before.
Finally, 'gefyra bridge' will overlay our local container over the one that we specified in the run command.

Once Gefyra tells you that the bridge is established, you're ready to make some changes and check out hot code reloading in Kubernetes in action.
Visit [http://gefyra-golang.127.0.0.1.nip.io:8080](http://gefyra-golang.127.0.0.1.nip.io:8080) in your browser and try changing the contents of the
`io.WriteString` function. Since we mounted our code into the container during `gefyra run`, air will detect the changes and rebuild/restart our application.
Refresh your browser and check out the results!











