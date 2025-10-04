## Introduction

Open source software to deploy and manage complex applications at scale through containers.

Kubernetes is Greek for pilot or helmsman, the person who steers the ship - the person standing at the helm (the ship’s wheel).

The correct Greek pronunciation of Kubernetes is Kie-ver-nee-tees but in technical conversations, we mostly often hear Koo-ber-netties or Koo-ber-nay’-tace or rarely Koo-ber-nets.
It’s also referred to as Kube or K8s (pronounced Kates).


## History

Comes from Borg and Omega at Google to :
- Automate the deployment and management of a large number of containers
- Be cost effective
- Abstract the infrastructure

Some Google figures :
- 2 billions of containers are started every week (2014)
- Google's energy use suggests that they run 900 000 servers (2024)
- data centers over multiple regions

Kubernetes first version was released in july 2015 and it became an Open source project operated by the Cloud Native Computing Foundation (CNCF) in march 2016. 

## Products

Ecosystem around Kubernetes. 

Commercial offerings :
- OpenShift (Redhat)
- Pivotal Container Service (Pivotal, Broadcom now)
- Rancher
- etc.


## Popularity

- Fits well with the microservice architecture
- Devops friendly by reducing the gap between devs and ops
- Cloud standardization (less vendor lock in)

## Benefits

- Self-service deployment of applications
- Reducing costs via better infrastructure utilization
- Automatically adjusting to changing load
- Keeping applications running smoothly
- Simplifying application development

## Under the hood

Kubernetes is like an operating system for a cluster of machines. Just like an OS seats between applications and the hardware, Kube seats between applications and a cluster of machines.
It provides the following functionalities :
- Service discovery
- Horizontal scaling
- Self healing
- Load balancing
- Leader election (master vs workers)

Computers in a cluster are divided into two groups
- Control plane containing the master nodes, which holds and controls the state of the cluster
- Workload plane containing the worker nodes where the applications run

Interactions with the nodes are abstracted through the Kubernetes API.

Control plane components :
- The Kubernetes API Server : exposes the RESTful API
- The etcd distributed datastore
- The Scheduler : decides on which worker node each application runs
- Controllers : creates objects

Workload place components :
- Kubelet : agent talking to the API Server. Manages the applications running inside the nodes.
- Container runtime
- Kubernetes Service Proxy : load-balances network traffic between applications

Add-ons components : other components (DNS servers, network plugins, logging agents, etc.) that run on worker nodes (or master nodes).

Everything in Kube is an object. Different types :
- Deployment descriptor
- Runtime instance
- Service of a set of instances
- etc.

Objects are described in one or many manifest files in YAML or JSON.

<img width="649" height="507" alt="image" src="https://github.com/user-attachments/assets/c2a3bdb4-4eee-4507-af8f-bdca2bd74633" />


