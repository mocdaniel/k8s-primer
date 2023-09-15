# Deployments

Now that we know how to deploy pods, let's take a look at a more advanced workload type: **Deployments**. Deployments are a higher-level abstraction on top of pods that allow us to easily scale and update our workloads.

They also allow us to take advantage of Kubernetes' **self-healing** and **scaling** capabilities - let's see how that works!

## Creating a Deployment

Let's start by creating a deployment for our `nginx` pod from the previous chapter, using the already shown trick to create a basic **manifest file** for us:

```bash
kubectl create deployment nginx --image=nginx:latest \
    --dry-run=client \
    -o=yaml > workloads/nginx-deployment.yaml
```

It should look like this:

``` yaml hl_lines="9-12 14-23"
apiVersion: apps/v1
kind: Deployment
metadata:
  creationTimestamp: null
  labels:
    app: nginx
  name: nginx
spec:
  replicas: 1
  selector:
    matchLabels:
      app: nginx
  strategy: {}
  template:
    metadata:
      creationTimestamp: null
      labels:
        app: nginx
    spec:
      containers:
      - image: nginx:latest
        name: nginx
        resources: {}
status: {}
```

I highlighted some lines of the manifest to point some things out:

Different from the pod manifest, the `Deployment` manifest has a `spec.replicas` field as well as a section defining a `selector`. This is because a deployment is a **controller** that manages a set of pods, and it needs to know **how many replicas** of the pod it should manage, and **how to select** the pods it should manage.

Apart from that, we can see that everything under the `spec.template` field is **the same** as the pod manifest we created in the previous section. This is because the `Deployment` is basically **templating**, **deploying**, and **managing** the pod described in the template **times X**, where X is the number of replicas we define in the `spec.replicas` field.

## Scaling a Deployment

I told you that deployments allow us to easily scale our workloads in the beginning of this section, and indeed, we already saw the `spec.replicas` field in the manifest we just created. Let's see how we can use it to scale our deployment - once again there is one *imperative* way to do so and two *declarative* ways.

### Scaling Deployments Imperatively

As for many other things like running a pod or creating deployments, there exists a cmdlet for **scaling deployments** in `kubectl`:

```bash

```bash