# Services

**Services** work similar to **Deployments** in that they use **selectors** to match pods, but instead of managing workloads, they create **virtual IPs** that can be used to reach the pods.

When a service is created, it is assigned a **cluster IP** that is **stable** and **unique** within the cluster. This IP is used to reach the service, and it will **load-balance** the traffic to the pods that back it. This means that we can reach the service from any node in the cluster, and the traffic will be routed to the correct pod(s).

While doing so, the service will forward traffic **only** to the pods that match its selector **and** are responsive. This means that if a pod is **not ready**, it will not receive any traffic from the service.

Kubernetes provides three kinds of services:

- **ClusterIP** - the default type, creates a virtual IP inside the cluster that can be used to reach the service
- **NodePort** - creates a port on every node in the cluster, and forwards traffic to the service
- **LoadBalancer** - creates a load balancer in the cloud (via the **Cloud-Controller-Manager** or similar means), and forwards traffic to the service

Internal networking and implications of connectivity differ between each variant, so we will look at all of them separately!