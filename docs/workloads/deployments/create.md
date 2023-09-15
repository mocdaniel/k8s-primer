# Creating a Deployment

Let's start by creating a deployment for our `nginx` pod from the previous chapter, using the trick we already used to create a basic **manifest file** for us:

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

Take a look at the highlighted lines of the manifest:

Different from the pod manifest, the `Deployment` manifest has a `spec.replicas` field as well as a section defining a `selector`. This is because a deployment is a **controller** that manages a set of pods, and it needs to know **how many replicas** of the pod it should manage, and **how to select** the pods it should manage.

Apart from that, we can see that everything under the `spec.template` field is **the same** as the pod manifest we created in the previous section. This is because the `Deployment` is basically **templating**, **deploying**, and **managing** the pod described in the template **times X**, where X is the number of replicas we define in the `spec.replicas` field.

We can go ahead and deploy our `nginx` deployment using the manifest we just created:

```bash
kubectl apply -f workloads/nginx-deployment.yaml
```

Once applied, we can check if our deployment is running:

<div class="annotate" markdown>
```bash
kubectl get deployments,replicasets # (1)!
```
</div>

1.  With this syntax, we can `get` multiple types of objects at the same time.

If everything went well, the output should look like this:

```bash
NAME                    READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/nginx   5/5     5            5           19m

NAME                               DESIRED   CURRENT   READY   AGE
replicaset.apps/nginx-7bf8c77b5b   5         5         5       19m
```

!!! question "What's a ReplicaSet?"
    `ReplicaSets` are used by `Deployments` to manage things behind the scenes. The precise hierarchy of managed objects is:
    
    **`Deployment` manages `ReplicaSet(s)`, `ReplicaSet` manages `Pod(s)`**.

    This enables a `Deployment` to **roll back** between different versions seamlessly, as we will see.
