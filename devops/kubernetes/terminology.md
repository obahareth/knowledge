# Terminology

_Mostly taken from the_ [_Kubernetes glossary_](https://kubernetes.io/docs/reference/glossary/?fundamental=true) _with added changes to help me understand better_

* **Cluster** - A set of machines, called **nodes**, that run containerized applications managed by Kubernetes. A cluster has at least one worker node and at least one master node.
* **Container** - A lightweight and portable executable image that contains software and all of its dependencies.
* **Container Runtime** - The container runtime is the software that is responsible for running **containers**.
* **Container runtime interface \(CRI\)** - The container runtime interface \(CRI\) is an API for container runtimes to integrate with **kubelet** on a **node**.
* **Control Plane** - The container orchestration layer that exposes the API and interfaces to define, deploy, and manage the lifecycle of **containers**.
* **Controller** - In Kubernetes, controllers are control loops that watch the state of your **cluster**, then make or request changes where needed. Each **controller** tries to move the current cluster state closer to the desired state.
* **DaemonSet** - Ensures a copy of a **Pod** is running across a set of **nodes** in a **cluster**.
* **Deployment** - An API object that manages a replicated application.
* **Ephemeral Container** - A **Container** type that you can temporarily run inside a **Pod**.
* **Image** - Stored instance of a **container** that holds a set of software needed to run an application.
* **Init Container** - One or more initialization containers that must run to completion before any app containers run.
* **Job** - A finite or batch task that runs to completion.
* **kube-controller-manager** Component on the master that runs **controllers**.
* **kube-proxy** - [kube-proxy](https://kubernetes.io/docs/reference/command-line-tools-reference/kube-proxy/) is a network proxy that runs on **each node** in **your cluster**, implementing part of the Kubernetes [Service](https://kubernetes.io/docs/concepts/services-networking/service/) concept.
* **Kubectl** - A command line tool for communicating with a [Kubernetes API](https://kubernetes.io/docs/concepts/overview/kubernetes-api/) server.
* **Kubelet** An agent that runs on **each node** in **the cluster**. It makes sure that **containers** are running in a **pod**. **Each node must have a Kubelet**.
* **Kubernetes API** - The application that serves Kubernetes functionality through a RESTful interface and stores the state of the cluster.
* **Label** - Tags objects with identifying attributes that are meaningful and relevant to users \(e.g. `"environment" : "production"`\).
* **Namespace** - An abstraction used by Kubernetes to support **multiple virtual clusters on the same physical** [**cluster**](https://kubernetes.io/docs/reference/glossary/?all=true#term-cluster).
* **Node** - A node is a worker machine in Kubernetes. A node has **Pods** running inside it.
* **NodePort** - A service type used to expose the Service on each Node’s IP at a static port \(the `NodePort`\). A `ClusterIP` Service, to which the `NodePort` Service routes, is automatically created. You’ll be able to contact the `NodePort` Service, from outside the cluster, by requesting `<NodeIP>:<NodePort>`.
* **Pod** - The smallest and simplest Kubernetes object. A Pod represents a set of running [containers](https://kubernetes.io/docs/concepts/overview/what-is-kubernetes/#why-containers) on your cluster. An example of a pod is a Rails app backend, it can scale up/down horizontally through **ReplicaSets** which replicates pods.
* **ReplicaSet** - A ReplicaSet \(aims to\) maintain a set of replica Pods running at any given time.
* **Pod Lifecycle** - A high-level summary of what phase the **Pod** is in within its lifecyle.
* **Pod Security Policy** - Enables fine-grained authorization of **Pod** creation and updates.
* [**Selector**](https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/#label-selectors) - Allows users to filter a list of resources based on labels. Examples:
  * `environment = production`
  * `tier != frontend`
  * `environment in (production, qa)`
  * `tier notin (frontend, backend)`
* **Service** - An abstract way to expose an application running on a set of [Pods](https://kubernetes.io/docs/concepts/workloads/pods/pod-overview/) as a network service.
* **Service Account** - Provides an identity for processes that run in a [Pod](https://kubernetes.io/docs/concepts/workloads/pods/pod-overview/).
* **StatefulSet** - Manages the deployment and scaling of a set of [Pods](https://kubernetes.io/docs/concepts/workloads/pods/pod-overview/), _and provides guarantees about the ordering and uniqueness_ of these Pods.
* **Static Pod** - A [pod](https://kubernetes.io/docs/concepts/workloads/pods/pod-overview/) managed directly by the **kubelet daemon** on a specific node.
* **Volume** - A directory containing data, accessible to the containers in a [pod](https://kubernetes.io/docs/concepts/workloads/pods/pod-overview/).

