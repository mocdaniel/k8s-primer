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
        type: webserver
    spec:
      containers:
      - image: nginx:1.25.2
        name: nginx
        resources: {}
status: {}
```

Reapply the manifest and `get` the `Deployments` and `ReplicaSets` again - the output should look like this:

!!! info "CLI Prompts"
    From this section onward, I will cease to document repeating commands again and again. Try remembering them where possible and skip back and forth if you need a reminder. 😉

```bash
NAME                    READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/nginx   1/1     1            1           37m

NAME                               DESIRED   CURRENT   READY   AGE
replicaset.apps/nginx-7bf8c77b5b   0         0         0       37m
replicaset.apps/nginx-84d5f8c5f6   1         1         1       5s
```

## Rolling Back Deployments

✅ Our deployment has been updated, and a new `ReplicaSet` has been created to manage the new pod. The old `ReplicaSet` however has **not** been deleted - it's merely scaled down to 0 replicas.

This handling of `ReplicaSets` by our `Deployments` allows us to roll back to a previous version of our deployment at any time. Again, `kubectl` got our backs with a handy cmdlet, `kubectl rollout`:

```bash
kubectl rollout undo deployment nginx
```

You should now be able to spot the old `ReplicaSet` having one replica again - rollback successful!

!!! info "Rolling Back to a Specific Version"
    If you want to roll back to a specific version of your deployment, you can do so by specifying the `--to-revision` flag:

    ```bash
    kubectl rollout undo deployment nginx --to-revision=1
    ```

    By default, Kubernetes will keep the last 10 revisions of your deployment. You can change this behavior by setting the `spec.revisionHistoryLimit` field in your deployment manifest.
