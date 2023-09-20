# Kubernetes Architecture

As mentioned in the previous section, Kubernetes basically consists of a set of APIs and controllers that continuously work to make your **actual state** match your **desired state**. In this section, we will learn more about the inner workings of Kubernetes.

Consider this graphic from the [official Kubernetes documentation](https://kubernetes.io/docs/concepts/overview/components/):

<figure markdown>
  ![Kubernetes architecture](../assets/images/components-of-kubernetes.svg)
  <figcaption>Architectural overview of a Kubernetes cluster (Click the image to zoom in)</figcaption>
</figure>

We can see a variety of different components, running either on the **control plane** or on the **(worker) node(s)**. Let's go through them one by one.

## Control Plane Components

There are a few components that need to be run **only** on the control plane, but **not** on the worker nodes. These components are typically tasked with either **administrative, elevated, or housekeeping tasks** which should not be run on normal cluster nodes, be it for **security** or **performance** reasons.

### API Server

The API server is at the core of the Kubernetes control plane. It exposes the Kubernetes API, which is used by all other components to communicate with the cluster. It is the only component that interacts with the **etcd** key-value store, which is used to store the cluster state.

It can be accessed **programmatically**, using many available libraries and SDKs for a variety of languages, on the **commandline**, using simple tools like **cURL** or more sophisticated ones like [kubectl](https://kubernetes.io/docs/reference/kubectl/) or [k9s](https://k9scli.io/), or via a **web UI**, e.g. the [Kubernetes Dashboard](https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/) or [Rancher](https://rancher.com).

### etcd

The aforementioned **etcd** key-value store is a **distributed database** that stores the cluster state. It is a **consistent and highly-available** datastore that can be used to store configuration data, as well as service discovery information.

Due to its **quorum algorithm**, we have to always run an **odd number of etcd nodes** in our cluster. This is why we see many clusters' **control planes** consisting of 3, 5, or more nodes.

### Scheduler

The scheduler is responsible for **scheduling** every new pod to run on the cluster. It continuously watches the API server for new pods that have no node assigned yet, and assigns them to a node based on a variety of factors, such as resource requirements, hardware constraints, and more.

*[pod]: think of a pod as a container for now


### Controller Manager

The controller manager is a collection of controllers that continuously **reconcile** the actual state of the cluster with the desired state. All of those controllers run as a joint process in a single binary, and comprise e.g.:

- the **Node controller**: Responsible for monitoring the state of the nodes, and taking action if a node becomes unavailable, e.g. **rescheduling** pods running on that node.

- the **EndpointSlice controller**: Responsible for creating endpoints objects, which are used to join services and pods.

!!! question "More about controllers"
    This is - by far - not an exhaustive list. There are quite a few more controllers tasked with other responsibilities.

    You can read about them [in the Kubernetes documentation](https://kubernetes.io/docs/concepts/overview/components/#kube-controller-manager).

### Cloud Controller Manager

This component is **optional**. As the name suggests, it is tasked with controlling cloud-related resources, such as **load balancers**, **volumes**, and **networks**. It is only needed if you are running Kubernetes on a cloud provider, such as AWS, Azure, or GCP.

Therefore, we won't see it in our KinD cluster we are about to create.

## Node Components

### kubelet

The **kubelet** is the **primary node agent** that runs on **every** node in the cluster. It is responsible for **starting, stopping, and monitoring** containers by communicating with the **container runtime**, as well as **reporting** the current state of the node to the control plane.

This is where Kubernetes and the installed container runtime meet.

### kube-proxy

The **kube-proxy** is a network proxy that runs on **every** node in the cluster. It is responsible for **routing** traffic to the correct pods, as well as **load balancing** traffic across multiple pods providing the same service.

It does so by maintaining **network rules** by utilizing the locally installed OS packet filtering layer (e.g. **iptables**), and falls back to routing traffic itself if it can't find one.

Enough theory, let's get started with our own clusters!
