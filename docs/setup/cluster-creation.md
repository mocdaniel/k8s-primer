# Cluster Creation

We're nearly there. We installed all our prerequisites, and now we're ready to create our Kubernetes cluster. We will aim at an environment that mimics a **real-world cluster** as **closely as possible** - this implicates a few things:

- we want **more than one node** - otherwise we won't see many of Kubernetes' strengths in action
- we want **proper connectivity** from the outside

Let's get started with KinD's config.

## KinD

KinD gets configured at the time of **cluster creation** by passing a configuration file. So, let's start by creating a new working directory for the remainder of this workshop to use.

```bash
mkdir k8s-workshop && cd k8s-workshop
vim kind-config.yaml
```

### Configuration

We want to create the following configuration - feel free to expand the various tooltips(1) for explanations on the different settings.
{ .annotate}

1. This is a tooltip 😉

<div class="annotate" markdown>
``` yaml
kind: Cluster
apiVersion: kind.x-k8s.io/v1alpha4
name: workshop-cluster
  #networking:
  #  disableDefaultCNI: true
nodes: # (1)!
  - role: control-plane
    extraPortMappings: # (2)!
      - containerPort: 80
        hostPort: 80
        listenAddress: "127.0.0.1"
        protocol: TCP
      - containerPort: 443
        hostPort: 443
        listenAddress: "127.0.0.1"
        protocol: TCP
      - containerPort: 31000
        hostPort: 31000
        listenAddress: "127.0.0.1"
        protocol: TCP
    labels: # (3)!
        ingress-ready: "true"
  - role: worker
    extraPortMappings:
      - containerPort: 31000
        hostPort: 31001
        listenAddress: "127.0.0.1"
        protocol: TCP
  - role: worker
    extraPortMappings:
      - containerPort: 31000
        hostPort: 31002
        listenAddress: "127.0.0.1"
        protocol: TCP

```
</div>
1.  We want three nodes - one control plane member and two workers. This checks the *more than one node* box.
2.  We want to expose ports 80 and 443 on the host machine. KinD can do this by itself, **unless** we are running on Docker Desktop - so it's better to make sure 😉
    
    We also want to expose port 31000 on all nodes. We will need this extra port for one of the labs later in this workshop.

    This checks the *proper connectivity* box.

3.  We want to label our control plane node so that we can deploy an ingress controller on it later on.

### Deployment

Once we've written our config file, we can create our cluster. This will take a few minutes, so feel free to grab a coffee or a tea in the meantime.

```bash
kind create cluster --config kind-config.yaml
```

!!! warning "Deployment crashed?"
    Unfortunately, KinD runs into some edge cases due to Docker Desktop, OS networking stacks, and other factors here and there. I tried to keep the installation path as 'broad' as possible, but if your installation fails here, check out the [troubleshooting section](https://kind.sigs.k8s.io/docs/user/known-issues/) of the KinD documentation.

### Verification

The cluster is installed, we did it! 🥳 Let's check if everything's alright:

```bash
kubectl config use-context kind-workshop-cluster
kubectl get nodes 
```

Once entered, the output should look like this:

```bash
NAME                             STATUS   ROLES           AGE     VERSION
workshop-cluster-control-plane   Ready    control-plane   4m6s    v1.27.3
workshop-cluster-worker          Ready    <none>          3m42s   v1.27.3
workshop-cluster-worker2         Ready    <none>          3m47s   v1.27.3
```

✅ Our cluster is indeed running and reachable using `kubectl`, and all nodes display a status of `Ready`.

**This is it,** as far as setting things up is concerned. From now on, we will learn how to actually **interact with** a Kubernetes cluster, how to **deploy workloads**, and how to **connect** to them!
