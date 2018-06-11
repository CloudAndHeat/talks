---?image=assets/images/bg.png&position=bottom -140% right -55%&size=70%

# Hassle-free Kubernetes on OpenStack

---

## About Us

<div class="columns">
	<div>
		<img src="http://placeholder.pics/svg/200x200">
		<br>
		**Matthias Goerens**
		<br>
		<span class="small">Cloud Infrastructure Architect</span>
	</div>
	<div>
		<img src="http://placeholder.pics/svg/200x200">
		<br>
		**Daniel Trautmann**
		<br>
		<span class="small">DevOps Engineer</span>
	</div>
</div>

---

## About CLOUD&HEAT

- Build energy efficient data centers
- Operate public and private cloud infrastructure based on OpenStack
- Develop security extensions for OpenStack

Note:

- Micro- and containerized data centers
- Watercooled

---

## The Problem... even with cloud solutions

- Worry about high availability of services
- Create new servers and scripts for each new service
- Maintain a lot of (virtual) server

Note:

- Scripts: configuration management system
- Maintain: Install updates / security fixes

---

![Tasks](2018/hassle-free-kubernetes-on-openstack/img/tasks.png)

Note:

- Example: deploy tasks of a basic website
- done with Ansible (YAML files)
- took around two days

---

![Workflow](2018/hassle-free-kubernetes-on-openstack/img/workflow.png)

Note:

- Deploy software: Automated tasks (Asnible)

---

## The Kubernetes (k8s) Project

- Originally developed by Google, open-source since 2014
- Written in Go (Golang)
- The leading Container Orchestrator vs Docker Swarm and Mesos

---

## Kubernetes High Level Architecture

- An orchestrator of containarized applications
- Two types of linux hosts
- One or more Masters : brain & memory
- A bunch of Nodes : muscles - where the pods are actually deployed

Note:

- containerized applications : for example docker
- Masters & Nodes (Minions)
- Masters decide on which node to schedule application services on; monitor the cluster; implement changes; respond to events
- Nodes are where the application services run; report back to the masters; watch for changes.
- Masters and Nodes can be bare metal servers or VMs (in Openstack of course)

---

## Kubernetes principles

- Manifest file written in JSON or YAML
- *Declarative model*, as opposition to the *imperative model*
- Current state vs desired state comparison

Note:

1. Manifest files tell k8s how we want our application to look -> Describe the desired state
2. POST it to the k8s API server
3. k8s records the configuration in the *cluster store*
4. k8s deploys the app on the Nodes :
	Pulling docker images
	Starting containers
	Building networks
5. k8s implements a *watch loop* (or *background reconciliation loop*) that constantly monitors the state of the cluster
6. If the *current state* of the cluster differs from the *desired state*, k8s uses the manifest files to fix it.

---

## Manifest file in YAML

```
apiVersion: apps/v1beta1
kind: ReplicaSet
metadata:
  name: web-rs
spec:
  replicas: 10
  template:
    metadata:
      labels:
        app: nginx-app
    spec:
      containers:
      - name: nginx-ctr
        image: nginx
        port:
        - containerPort: 8080
```

---

## Major components - Pods

- The atomic unit of deployment
- A fenced environment in which is ran a container (or multiple containers in some special cases)
- The minimum unit of scaling
+Schema
- Pods are cattle

Note:

- Atomic unit of deployment in VMware / Openstack : VM
- Atomic unit of deployment in Docker : Container
- You don't run a container directly on a k8s cluster, you need to run it inside a Pod.
- A Pod is a shared execution environment for one or more containers : they share a hostname, IP address, memory address space, sockets, volumes, ...
- You do not scale by adding more of the same container to an existing Pod, but by adding more copy of your Pod.
- Pods are mortal. If they die unexpectedly, we don't bother trying to bring them back to life. We just start a fresh new Pod (new ID, new IP address)

---

## Major components - Pods

![Pods](2018/hassle-free-kubernetes-on-openstack/img/Pod.png)

---

## Major components - ReplicaSets

- Take a Pod template and deploy a desired number of *replicas* of it
- Instantiate background reconciliation loops
- Add self-healing, and scalability possibilities

Note:

- Instantiate background reconciliation loops (that check and make sure that the desired number of replicas are always running)
- Specify a Pod template + a number of desired replicas in the manifest file

---

## Major components - Services

- Provide a reliable networking endpoint for a set of Pods
- Provide stable DNS, IP addresses, and support TCP and UDP
- Perform simple randomized load-balancing across Pods
- Automatically updates itself when Pods come and go

Note:

- Pods IP are unreliable
- A Service gets its own **stable** IP address, DNS name, and port
- It **dynamically** get associated with a set of Pod using *labels*

---

## Major components - Deployments

- Come on top of ReplicaSets, manage their lifecycle
- Add rolling updates and simple rollbacks

---

## Benefits - But why are we actually doing all of this ?
## k8s FTW

- Self-healing
- Scalability
- Version Control
- Basic Load-Balancing

Note:
- Example of an application ran with Docker / with K8s ?
- Self-healing
- Scalability
- Version Control
- Load-Balancing
run code in production without setting up new servers
run new service with one command / API call
fits perfectly for CI/CD
start test environments within seconds
fast rollbacks
takes care of availability

---

## Kubernetes over Openstack

- Openstack, an Open Source software for building Public and Private Clouds
- A collection of services for Compute, Network, Storage resources, and more
- Openstack Magnum for the automated deployment of k8s clusters
- Creation of Cluster templates
- Creation of multiple clusters, based on those templates.
- No manual installation of Kubernetes required !

Note:
- What is Openstack ? Cloud platform / sofware
- Openstack Heat to deploy templates on Openstack
- Openstack Magnum using Openstack Heat to deploy k8s clusters on Openstack
- customer needs
- Production setup : 3 k8s Master nodes, running on different nodes.

---

## Books & pieces of documentations

- The Kubernetes Book by Nigel Poulton
- PWK : https://labs.play-with-k8s.com/
