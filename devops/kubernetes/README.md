# Kubernetes

_See_ [_Terminology_](https://github.com/obahareth/knowledge/tree/333c0901236edb40cfc5774172c5b200f5f2fe58/devops/kubernetes/terminology.md) _for a reference_

Kubernetes \(K8s\) is an open-source orchestration system for automating deployment, scaling, and management of containerized applications.

\[TOC\]

## Architecture

![](https://www.aquasec.com/wiki/download/attachments/2855092/kubertes.png?version=1&modificationDate=1520363380138&api=v2)

​ \(Image taken from [https://x-team.com/blog/introduction-kubernetes-architecture/](https://x-team.com/blog/introduction-kubernetes-architecture/)\)

* Everything must be in a namespace, a **default** namespace is created when you create a cluster.

## Default objects

### Master Node \(one is always present in a cluster\)

* **API Server** - Allows interaction with kubernetes API, it's the frontend for the control plane.
* **kube-scheduler** - Assigns **pods** to **nodes** at runtime, and checks resources, quality of service, policies, and specifications before scheduling
* **Controller Manager** - Runs **controllers** \(background threads that run tasks in a cluster\). It's compiled into a single binary and has a number of roles:
  * **Node Controller** - Responsible for worker states.
  * **Replication Controller** - Responsible for maintining correct number of **pods** for replicated controllers.
  * **Endpoint Controller** - Responsible for joining **services** and **pods** togethers
  * **Service Account Contoller and Token Controller** - Responisble for handling access management to **API Server**.

### etcd

Database used by Kubernetes to store all cluster data \(job scheduling info, pod details, stage information, etc.\).

### kubectl

Commandline interface used to interact with the **master node**. It can be prunounced as Kube Cuttle. Kubectl has a config file which has server information and authentication information to access the API server.

## Nodes

A Node requires the following to be running:

* **Supervisord** - Supervisor is a client/server system that allows its users to monitor and control a number of processes on UNIX-like operating systems
* **Container tooling** \(e.g. Docker\)

### kubelet

The main service on a node, regularly taking in new or modified pod specifications \(primarily through the kube-apiserver\) and ensuring that pods and their containers are healthy and running in the desired state. This component also reports to the master on the health of the host where it is running. It executed pod containers via the container engine and mounts pod volume and secrets. **Kubelet must be running on all nodes**, and if it isn't working correctly, you're going to have issues.

It takes a set of **Podspec**s \(Yaml files provided by API Server\) that describe a pod.

Kubelet only manages containers that were created by the API server, not any container running on the node.

### kube-proxy

A proxy service that runs on each worker node to deal with individual host subnetting and expose services to the external world. It performs request forwarding to the correct pods/containers across the various isolated networks in a cluster. **kube-proxy must be running in each node**.

* It can do simple network stream or round-robin forwarding across a set of backends.
* Services definged against the API server: kube-proxy watches the API server for the addition and removal of services.
* For each new service, kube-proxy opens a **randomly chosen port** on the local node.
* Connections made to the chosen port are proxied to one of the corresponding back-end pods.

Modes of kube-proxy:

* User space mode \(most common\).
* Iptables mode.
* Ipvs mode.

## Pods

A pod is one or more containers that should be controlled as a single application. It encapsulates application containers, storage resources, a unique network ID and other configuration on how to run the containers.

Pods are ephemeral/disposable, they never self-heal and are not restarted by the scheduler, never create them by themselves in production. Instead use higher level constructs like **controllers**.

**States**

* **Pending** - Accepted by Kubernetes system but a container hasn't been created yet.
* **Running** - A pod has been scheduled on a node, all containers have been created, and at least one container is in the **running** state.
* **Succeeded** - All containers in the pod have exited with a status of 0 \(successful execution and will not be restarted\)
* **Failed** - All containers have exited and at least one has failed \(returned non-zero exit status\)
* **CrashLoopBackOff** - A container fails to start and Kubernetes keeps trying to restart it

## Controllers

In Kubernetes, controllers are control loops that watch the state of your **cluster**, then make or request changes where needed. Each **controller** tries to move the current **cluster state** closer to the desired state. They help with application reliability, scaling, and load balancing.

### Controller Types

#### ReplicaSet

Ensures that the specified number of **replicas** for a **pod** are **running at all times**.

#### Deployment

Most applications are packaged as Deployments. Deployments provide declarative updates for **pods** and **ReplicaSets**. Used to describe desired state in a YAML file and the Deployment controller will align the actual state to match. Can be used to create new **ReplicaSets** or replace them with new ones. A **Deployment** manages the **ReplicaSet**, and the **ReplicaSet** manages the **Pod**. This architecture allows deployments to support a rollback mechanism.

A new **ReplicaSet** is created each time a new Deployment config is deployed but the old ReplicaSet is still kept to allow for easier rollbacks.

**Use-cases**

* **Pod management** - Running a ReplicaSet allows us to deploy a number of pods and check their status as a single unit.
* Scaling a ReplicaSet scales out the pods and allows for a deployment to handle more traffic.
* **Pause and Resume** \(traffic will still get passed to existing replica set\)
  * Used with larger changesets.
  * Pause, make changes, and resume deployment.

#### DaemonSet

Ensures all nodes run a copy of a specific pod. As nodes are added or removed from the cluster, a DaemonSet will add or remove the required pods.

**Use-cases**

* Run a single log aggregator.
* Monitoring agent.

#### Job

Supervisor process for pods carrying out batch jobs. Used to run individual processes that need to run once and complete successfully.

**Use-cases**

* Cron job to run nightly report or database backup.

#### Service

Allows the comunication between one set of deployments with another. When a service is created it is assigned a unique IP address that never changes throughout the lifetime of the service. Pods are then configured to talk to the service and can rely on the service IP on any requests that might be sent to the pod. Services are an important concept because they allow one set of pods to communicate with another set of pods in an easy way.

**Example**

Frontend deployment needs to talk to backend deployment \(which consists of multiple pods\), a backend service can provide a single IP for the frontend pod to talk with.

**Kinds of Services**

* **Internal** - IP is only reachable within the cluster
* **External** - Endpoint available through node IP \(known as **NodePort**\)
* **Load balancer** - Exposes application to the internet with a load balancer \(available with a cloud provider\)

## Labels and Selectors

Covered in the [terminology page](https://github.com/obahareth/knowledge/tree/333c0901236edb40cfc5774172c5b200f5f2fe58/devops/kubernetes/terminology.md).

## Namespaces

* Allows teams to access resources, with accountability.
* Great way to divide cluster resources between users.
* Names for resources **must be unique within a namespace**.
* A **default** namespace is created when kubernetes is launched.
* Newer applications install their resources in a different namespace so they don't interfere with an existing cluster.

## Maintenance

* Features are backward-compatible and APIs are versioned.
* Host can be turned off/on during maintenance.

## Logging and Monitoring

**Application Monitoring**

* Built-in TCP/HTTP/container-execution health checks.

**Node health check**

* Failures are monitored by node controller.

**Kubernetes status**

* Can be monitored through addons like [metrics-server](https://github.com/kubernetes-sigs/metrics-server) or the [Prometheus Operator](https://github.com/coreos/prometheus-operator).

## Limitations

_As of 1.16, see_ [_official Limitations page_](https://kubernetes.io/docs/setup/best-practices/cluster-large/)_._

* No more than 5000 nodes
* No more than 150000 total pods
* No more than 300000 total containers
* No more than 100 pods per node

## References

* [Kubernetes Architecture 101](https://www.aquasec.com/wiki/display/containers/Kubernetes+Architecture+101)
* [Learning Kubernetes](https://www.linkedin.com/learning/learning-kubernetes)

