# Workloads on Kubernetes

As we covered in the very beginning of this workshop, Kubernetes is a container orchestration platform. In order to *utilize* it, we'd better run some *workloads* on it 😉

This is where we are going to start now. We will first have a look at the different types of workloads that can be run on Kubernetes, and then we will deploy our first application to the cluster.

Kubernetes knows about a **variety** of **different workload classes** - we will focus on the **most important** ones in this workshop:

- **Pods**
- **Deployments**
- **StatefulSets**