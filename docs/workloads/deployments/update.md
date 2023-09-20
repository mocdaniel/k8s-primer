# Updating and Restoring Deployments

Scaling deployments is nice, but what if we want to update our workloads? Sometimes we might want to change more than just the number of replicas, say the used **image version** or the set of **labels** passed on to the pods.

## Updating Deployments

You might have guessed it by now, we can do so by editing our manifest once again - let's change the `image version` and add a `type` label to all pods of the deployment:

``` yaml hl_lines="19 22"
apiVersion: apps/v1
kind: Deployment
metadata:
  creationTimestamp: null
  labels:
    app: small-httpd
  name: small-httpd
spec:
  replicas: 3
  selector:
    matchLabels:
      app: small-httpd
  strategy: {}
  template:
    metadata:
      creationTimestamp: null
      labels:
        app: small-httpd
        type: webserver
    spec:
      containers:
      - image: httpd:buster
        name: small-httpd
        resources: {}
status: {}
```

!!! lab "Lab 5: Edit our deployment!"

    Let's edit our deployment to change the image version and add a label!

    1. Edit the `small-httpd` deployment manifest to change the image version to `httpd:buster` and add a `type` label to all pods of the deployment!

    2. Apply the changes to your deployment using `kubectl apply`!

!!! stretch "...Labels?"
    Labels are a way for Kubernetes to embed **human-readable** information into objects. They are **key-value pairs** that can be used to **identify** and **group** objects.

    Try to find **at least one way** to confirm that the `label` you added to your deployment is actually present on the pods!

Once done with **Lab 5**, you should now see the following output for `kubectl get deployment,replicasets`:

```bash
NAME                                       READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/small-httpd                3/3     2            3           26s

NAME                                                 DESIRED   CURRENT   READY   AGE
replicaset.apps/small-httpd-8556459c74               2         2         2       26s
replicaset.apps/small-httpd-86d67b9b5b               2         2         1       5s
```

## Rolling Back Deployments

✅ Our deployment has been updated, and a new `ReplicaSet` has been created to manage the new pod. The old `ReplicaSet` however has **not** been deleted - it's merely scaled down to 0 replicas.

This handling of `ReplicaSets` by our `Deployments` allows us to roll back to a previous version of our deployment at any time. Again, `kubectl` got our backs with a handy cmdlet, `kubectl rollout`:

```bash
kubectl rollout undo deployment small-httpd
```

!!! lab "Lab 6: Try it out!"
    This lab is a short one - try to roll back your deployment to the previous version!

!!! info "Rolling Back to a Specific Version"
    If you want to roll back to a specific version of your deployment, you can do so by specifying the `--to-revision` flag:

    ```bash
    kubectl rollout undo deployment small-httpd --to-revision=1
    ```

    By default, Kubernetes will keep the last 10 revisions of your deployment. You can change this behavior by setting the `spec.revisionHistoryLimit` field in your deployment manifest.
