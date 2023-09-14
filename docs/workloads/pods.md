# Pods

**Pods** are the **smallest, atomic** unit of abstraction in Kubernetes. Earlier in this workshop I told you to think of pods as containers - this was half the truth. 

Pods are actually a **wrapper** around **one or more** containers, which allows Kubernetes to handle closely related containers as a single unit.

Once again, consider a figure from the official [Kubernetes documentation](https://kubernetes.io/docs/tutorials/kubernetes-basics/explore/explore-intro/):

<figure markdown>
  ![Pod Overview](../assets/images/pods.svg){ width="900" }
  <figcaption>Structure of a pod</figcaption>
</figure>

For one, we see confirmed what I just told you: Pods are a wrapper around one or more containers. But we also see that there are a few more things going on here:

- **volumes** (i.e. files, directories, and other mountable entities) seem to be **shared** by **all containers** of a pod
- there is a **network namespace** shared by all containers of a pod (i.e. they share the same IP address and port space)

I can sit here and tell you about pods vs containers all day long, while all the time our shiny new clusters are waiting in the background. So let's deploy some ourselves!

## Deploying a pod

When deploying things to Kubernetes, we got at least two options at our disposal: We can deploy either *imperatively* (tell the cluster **what to do**) or *declaratively* (tell the cluster what we want to **end up with**).