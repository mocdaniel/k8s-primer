# Networking on Kubernetes

By now we got workloads running in our cluster, and coincidentally, they happen to be **web servers**, waiting to be reached by **clients**. But how do we **connect** to them?

**Networking** is a fundamental part of Kubernetes, and it's important to understand how it works. At the same time, it's not trivial - let's look at the different traffic that flows through our cluster:

- **Pod-to-Pod** - these pods can happen to be colocated on the same node, or on different nodes
- **Pod-to-Service** - a pod can reach a service by its name, and the service will load-balance the traffic to the pods that back it. We will learn more about **services** in a moment
- **External-to-Service** - clients can reach a service from outside the cluster
- **External-to-Pod** - clients can reach a pod from outside the cluster

All of these different flows traverse the **pod network**, which is a **flat network** that spans across all nodes in the cluster. This means that all pods can reach each other, regardless of which node they are running on.

Note that this is different from the **node network**, which is the network that connects the nodes themselves.

Translation of the pod network to the node network and vice versa is done by a **network plugin**, also called CNI. There are many different plugins, and we already saw the one that we are using in the previous lesson - **kindnet**.

*[CNI]: Container Network Interface

These plugins have different capabilities, and some of them are more suited for production environments than others. For example, **kindnet** is a simple plugin that is easy to use, but it doesn't support **Network Policies**, needed to enforce security rules between pods.
