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