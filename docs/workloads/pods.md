# Pods

**Pods** are the **smallest, atomic** unit of abstraction in Kubernetes. Earlier in this workshop I told you to think of pods as containers - this was half the truth. 

Pods are actually a **wrapper** around **one or more** containers, which allows Kubernetes to handle closely related containers as a single unit.

Once again, consider a figure from the official [Kubernetes documentation](https://kubernetes.io/docs/tutorials/kubernetes-basics/explore/explore-intro/):

<figure markdown>
  ![Pod Overview](../assets/images/pods.svg){ width="900" }
  <figcaption>Structure of a pod</figcaption>
</figure>

For one, we see confirmed what I just told you: Pods are a wrapper around one or more containers. But we also see that there are a few more things going on here:

- **volumes** (i.e. files, or directories) seem to be **shared** by **all containers** of a pod
- there is a **network namespace** shared by all containers of a pod (i.e. they share the same IP address and port space)

However, I can sit here and tell you about pods vs containers all day long, while all the time our shiny new clusters are waiting in the background. So let's deploy some ourselves!

## Deploying a Pod

When deploying things to Kubernetes, we got two options to choose from: We can deploy either *imperatively* or *declaratively*.

*[imperatively]: tell the cluster what to do
*[declaratively]: tell the cluster what we want to end up with

### Deploying Imperatively

For the **imperative** way of deploying things, `kubectl` got a lot of helpful cmdlets in store which we will learn about over the course of the workshop. We'll start with `run`, which is meant to do exactly what it says: Run a container in a pod on the cluster.

```bash
kubectl run nginx --image=nginx:latest
watch kubectl get pods
```

This command tells Kubernetes to run a container named `nginx` using the `nginx:latest` image. The second command displays **all pods** in the current **namespace** for us so we can follow our workload's creation.

!!!lab "Lab 1: Deploy your first pod imperatively"
    It's time now, you get to run your first pod on Kubernetes! 🎉

    1. Adapt the above command to run a pod named `nginx`, using the `nginx:latest` image.

    2. Make sure the pod comes up and stays healthy, using `kubectl get`.

*[namespace]: a logical grouping of resources in Kubernetes

The *imperative* approach of deploying resources to Kubernetes has a few short-comings though:

- If many people work on the same set of resources this way, things can get **messy** quickly
- It is hard to **keep track** of what has been deployed to the cluster
- It is hard to **reproduce** what has been deployed in the past

So, if you're not in a **debugging session** or **trying things out**, it's better to work *declaratively*.

### Deploying Declaratively

Deploying to Kubernetes declaratively means that we tell the cluster what we want to end up with, and let it figure out how to get there on its own. This is done by providing a **manifest** file, which is a YAML file describing the desired state of the cluster.

Those YAML manifests take some time to get used to and it's easy to forget a required object field in the beginning. Good thing that `kubectl` got a nifty way of generating those manifests for us!

<div class="annotate" markdown>
```bash
mkdir workloads
kubectl run nginx2 --image=nginx:latest \
    --dry-run=client \
    -o=yaml > workloads/nginx-pod.yaml # (1)!
```
</div>

1.  `--dry-run=client` tells `kubectl` to not actually run the command, but only generate the manifest file for us
    
    ``-o=yaml` tells `kubectl` to output the manifest in YAML format to `stdout`

Let's take a look at the generated manifest:

```yaml
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    run: nginx2
  name: nginx2
spec:
  containers:
  - image: nginx:latest
    name: nginx2
    resources: {}
  dnsPolicy: ClusterFirst
  restartPolicy: Always
status: {}
```

This is a **minimal** manifest for a pod. It contains the `apiVersion`and `kind` of the resource we want to deploy, as well as some `metadata` and the `specification` of the pod.

!!! info "Kubernetes Object Manifests"
    These four fields of Kubernetes' API specification are **common to most** of the available objects, so you will see them a lot and it's wise to try and remember them. 😉

Now that we got a manifest, we can apply it to our clusters:

```bash
kubectl apply -f workloads/nginx-pod.yaml
watch kubectl get pods
```

!!!lab "Lab 2: Deploy a pod declaratively"
    As we just learned, running *anything* on Kubernetes imperatively should be our last resort. So, go ahead and deploy an HTTPd pod declaratively!
    
    1. Create a manifest for a pod named `httpd` using the `httpd:latest` image.

    2. Deploy the pod to the cluster.

    3. Take a look at the pod's manifest - it should look similar to the one above.
