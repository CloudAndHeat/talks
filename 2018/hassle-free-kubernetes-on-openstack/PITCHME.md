---?image=assets/images/bg.png&position=bottom -140% right -55%&size=70%

# Hassle-free Kubernetes on OpenStack

---

### About Us

<div class="columns" style="margin-top: 3em;">
	<div>
		<h5>Matthias Goerens</h5>
		<span class="small">Cloud Infrastructure Architect</span>
	</div>
	<div>
		<h5>Daniel Trautmann</h5>
		<span class="small">DevOps Engineer</span>
	</div>
</div>

---

### About CLOUD&HEAT

- Build energy efficient data centers
- Operate public and private cloud infrastructures based on OpenStack
- Develop security extensions for OpenStack

Note:

- Technology to use the heat produced by servers
- Watercooled servers
- Micro- and containerized data centers

---

### The Problem... even with cloud solutions

- Worry about high availability of services
- Create new servers and scripts for each new service
- Maintain a lot of (virtual) servers

Note:

- Scripts: configuration management system
- Maintain: Install updates / security fixes
- Some companies might not even have automation

---

![Tasks](2018/hassle-free-kubernetes-on-openstack/img/automated_tasks.png)

Note:

- Example: deploy tasks of a basic website
- done with Ansible (YAML files)
- took around two days

---

![Workflow](2018/hassle-free-kubernetes-on-openstack/img/deployment_workflow.png)

Note:

- Deploy software: Automated tasks (Ansible)
- Still usable for other things
- Was good for some time, but now we can do better
- HAND OVER TO MATTHIAS

---

### The Kubernetes (k8s) Project

- Originally developed by Google, open-source since 2014
- Written in Go (Golang)
- The leading Container Orchestrator vs Docker Swarm and Mesos

<div class="columns" style="align-items: center;">
	<div>
		<img src="https://raw.githubusercontent.com/kubernetes/kubernetes/master/logo/logo.png" style="height: 50%; width: 50%;">
	</div>
	<div>
![CNCF](2018/hassle-free-kubernetes-on-openstack/img/cncf.png)
	</div>
</div>

---

### Monolith vs MicroServices

![Workflow](2018/hassle-free-kubernetes-on-openstack/img/microservices.png)

Note:

- a set of independent services that are developed, deployed and maintained separately
- Better Flexibility : Update of a single microservice doesn't affect the other parts of the application
- Better Resiliency : Eliminates SPOF
- Better Agility : Update more often, brings newer version of the app more often.

---

### Kubernetes High Level Architecture

- An orchestrator of containarized applications
- Two types of linux hosts
- One or more Masters : brain & memory
- A bunch of Nodes : muscles - where the *Pods* are actually deployed

Note:

- containerized applications : for example docker
- Masters & Nodes (Minions)
- Masters decide on which node to schedule application services on; monitor the cluster; implement changes; respond to events
- Nodes are where the application services run; report back to the masters; watch for changes.
- Masters and Nodes can be bare metal servers or VMs (in Openstack of course)

---?image=2018/hassle-free-kubernetes-on-openstack/img/k8s_architecture.png&size=70%

### Kubernetes High Level Architecture

Note:

- containerized applications : for example docker
- Masters & Nodes (Minions)
- Masters decide on which node to schedule application services on; monitor the cluster; implement changes; respond to events
- Nodes are where the application services run; report back to the masters; watch for changes.
- Masters and Nodes can be bare metal servers or VMs (in Openstack of course)

---

### Kubernetes principles

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

### Manifest file in YAML

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

### Major components - Pods

<div class="columns">
	<div>
- The atomic unit of deployment
	</div>
	<div>
	</div>
</div>
<!-- .slide: data-background-transition="none" -->
<!-- .slide: data-transition="fade" -->

Note:

- Atomic unit of deployment in VMware / KVM / Hyper-V : VM
- Atomic unit of deployment in Docker : Container
- You don't run a container directly on a k8s cluster, you need to run it inside a Pod.
- A Pod is a shared execution environment for one or more containers : they share a hostname, IP address, memory address space, sockets, volumes, ...
- You do not scale by adding more of the same container to an existing Pod, but by adding more copy of your Pod.
- Pods are mortal. If they die unexpectedly, we don't bother trying to bring them back to life. We just start a fresh new Pod (new ID, new IP address)

+++

### Major components - Pods

<div class="columns">
	<div>
- The atomic unit of deployment
- A fenced environment in which is ran a container
	</div>
	<div>
![Pod](2018/hassle-free-kubernetes-on-openstack/img/Pod.png)
	</div>
</div>
<!-- .slide: data-background-transition="none" -->
<!-- .slide: data-transition="fade" -->

Note:

- Atomic unit of deployment in VMware / KVM / Hyper-V : VM
- Atomic unit of deployment in Docker : Container
- You don't run a container directly on a k8s cluster, you need to run it inside a Pod.
- A Pod is a shared execution environment for one or more containers : they share a hostname, IP address, memory address space, sockets, volumes, ...
- You do not scale by adding more of the same container to an existing Pod, but by adding more copy of your Pod.
- Pods are mortal. If they die unexpectedly, we don't bother trying to bring them back to life. We just start a fresh new Pod (new ID, new IP address)
+++

### Major components - Pods

<div class="columns">
	<div>
- The atomic unit of deployment
- A fenced environment in which is ran a container
- The minimum unit of scaling
- Pods are mortal
	</div>
	<div>
![Scaling](2018/hassle-free-kubernetes-on-openstack/img/Scaling.png)
	</div>
</div>
<!-- .slide: data-background-transition="none" -->
<!-- .slide: data-transition="fade" -->

Note:

- Atomic unit of deployment in VMware / KVM / Hyper-V : VM
- Atomic unit of deployment in Docker : Container
- You don't run a container directly on a k8s cluster, you need to run it inside a Pod.
- A Pod is a shared execution environment for one or more containers : they share a hostname, IP address, memory address space, sockets, volumes, ...
- You do not scale by adding more of the same container to an existing Pod, but by adding more copy of your Pod.
- Pods are cattle. If they die unexpectedly, we don't bother trying to bring them back to life. We just start a fresh new Pod (new ID, new IP address)

---
### ReplicaSets Watch Loop
<div class="columns">
	<div>
![WatchLoop1](2018/hassle-free-kubernetes-on-openstack/img/WatchLoop1.png)
	</div>
	<div style='width: 65%'>
- Take a Pod template and deploy a desired number of *replicas* of it
- Instantiate background reconciliation loops
	</div>
</div>
<!-- .slide: data-background-transition="none" -->
<!-- .slide: data-transition="fade" -->

Note:

- Instantiate background reconciliation loops (that check and make sure that the desired number of replicas are always running)
- Specify a Pod template + a number of desired replicas in the manifest file

+++

### ReplicaSets Self-Healing

<div class="columns">
	<div>
![WatchSelfHealing](2018/hassle-free-kubernetes-on-openstack/img/WatchSelfHealing.png)
	</div>
	<div style='width: 65%'>
- Take a Pod template and deploy a desired number of *replicas* of it
- Instantiate background reconciliation loops
- Add self-healing
	</div>
</div>
<!-- .slide: data-background-transition="none" -->
<!-- .slide: data-transition="fade" -->

Note:

- Instantiate background reconciliation loops (that check and make sure that the desired number of replicas are always running)
- Specify a Pod template + a number of desired replicas in the manifest file

+++

### ReplicaSets Scaling

<div class="columns">
	<div>
![WatchLoopScaling](2018/hassle-free-kubernetes-on-openstack/img/WatchLoopScaling.png)
	</div>
	<div style='width: 65%'>
- Take a Pod template and deploy a desired number of *replicas* of it
- Instantiate background reconciliation loops
- Add self-healing, and scalability possibilities
	</div>
</div>
<!-- .slide: data-background-transition="none" -->
<!-- .slide: data-transition="fade" -->

Note:

- Instantiate background reconciliation loops (that check and make sure that the desired number of replicas are always running)
- Specify a Pod template + a number of desired replicas in the manifest file

---

### Major components - Services

- Provide a reliable networking endpoint for a set of Pods
- Provide stable DNS, IP addresses, and support TCP and UDP
- Perform simple randomized load-balancing across Pods
- Automatically updates itself when Pods come and go

Note:

- Pods IP are unreliable
- A Service gets its own **stable** IP address, DNS name, and port
- It **dynamically** gets associated with a set of Pod using *labels*

---

### Major components - Services

<div>
![Service](2018/hassle-free-kubernetes-on-openstack/img/Service.png)
</div>

<!-- .slide: data-background-transition="none" -->
<!-- .slide: data-transition="fade" -->

+++

### Major components - Services


![Service2](2018/hassle-free-kubernetes-on-openstack/img/Service2.png)


<!-- .slide: data-background-transition="none" -->
<!-- .slide: data-transition="fade" -->

---

### Major components - Deployments

- Come on top of ReplicaSets, manage their lifecycle
- Add rolling updates and simple rollbacks

---

### Rolling Updates
<div class="columns">
	<div>
![RollingUpdate1](2018/hassle-free-kubernetes-on-openstack/img/RollingUpdate1.png)
	</div>
	<div style='width: 65%'>
- One single command to perform a Rolling Update
- One single command to perform a Rollback
- Minimum number of *Pods* available
- Amount of time to wait between to iteration
	</div>
</div>
<!-- .slide: data-background-transition="none" -->
<!-- .slide: data-transition="fade" -->

Note:
- *Deployment* creates a new *ReplicaSet* for the next revision
- *Pods* are deleted one by one on the old *ReplicaSet*
- New *Pods* with the new version of the application are created on the new *ReplicaSet*

+++

### Rolling Updates
<div class="columns">
	<div>
![RollingUpdate2](2018/hassle-free-kubernetes-on-openstack/img/RollingUpdate2.png)
	</div>
	<div style='width: 65%'>
- One single command to perform a Rolling Update
- One single command to perform a Rollback
- Minimum number of *Pods* available
- Amount of time to wait between to iteration
	</div>
</div>
<!-- .slide: data-background-transition="none" -->
<!-- .slide: data-transition="fade" -->

Note:
- *Deployment* creates a new *ReplicaSet* for the next revision
- *Pods* are deleted one by one on the old *ReplicaSet*
- New *Pods* with the new version of the application are created on the new *ReplicaSet*

+++

### Rolling Updates
<div class="columns">
	<div>
![RollingUpdate3](2018/hassle-free-kubernetes-on-openstack/img/RollingUpdate3.png)
	</div>
	<div style='width: 65%'>
- One single command to perform a Rolling Update
- One single command to perform a Rollback
- Minimum number of *Pods* available
- Amount of time to wait between to iteration
	</div>
</div>
<!-- .slide: data-background-transition="none" -->
<!-- .slide: data-transition="fade" -->

Note:
- *Deployment* creates a new *ReplicaSet* for the next revision
- *Pods* are deleted one by one on the old *ReplicaSet*
- New *Pods* with the new version of the application are created on the new *ReplicaSet*

---

### K8s Benefits

- Self-healing
- Scalability
- Version Control
- Simple Rolling Updates and Rollbacks
- Basic Load-Balancing
- Run code in production without setting up new servers

Note:
- Self-healing
- Scalability
- Version Control
- Load-Balancing
- run code in production without setting up new servers
- run new service with one command / API call
- fits perfectly for CI/CD
- start test environments within seconds
- fast rollbacks
- takes care of availability

---

### Openstack
- Openstack, an Open Source software for building Public and Private Clouds
- A collection of services for Compute, Network, Storage resources, and more
- Openstack Magnum for the automated deployment of k8s clusters

Note:
- What is Openstack ? Cloud platform / sofware
- Openstack Heat to deploy templates on Openstack
- Openstack Magnum using Openstack Heat to deploy k8s clusters on Openstack
- Production setup : 3 k8s Master nodes, running on different nodes.

---

### Kubernetes over Openstack

- Creation of cluster templates
- Creation of multiple clusters, based on those templates
- No manual installation of Kubernetes required !

Note:
- What is Openstack ? Cloud platform / sofware
- Openstack Heat to deploy templates on Openstack
- Openstack Magnum using Openstack Heat to deploy k8s clusters on Openstack
- Production setup : 3 k8s Master nodes, running on different nodes.
- Automatically spawns a Master, some nodes
- Supports Multi Master mode

---

### Books & pieces of documentations

- The Kubernetes Book by Nigel Poulton
- PWK : https://labs.play-with-k8s.com/

---

<span style="font-size: 2em;">Enjoy the 🍺</span>
<br><br><br>
**Slides:** https://github.com/CloudAndHeat/talks

---?image=2018/hassle-free-kubernetes-on-openstack/img/qr.png&position=center&size=40%
