# Scaling a Deployment

I told you that deployments allow us to easily scale our workloads in the beginning of this chapter, and indeed, we already saw the `spec.replicas` field in the manifest we just created. Let's see how we can use it to scale our deployment - once again there is one *imperative* way to do so and two *declarative* ways.

## Scaling Deployments Imperatively

As for many other things like running a pod or creating deployments, there exists a cmdlet for **scaling deployments** in `kubectl`:

```bash
kubectl scale deployment nginx --replicas=3
```

Once applied, we can confirm the changes to our deployment using 

```bash
kubectl get deployment nginx
```

We can see that the number of replicas has indeed changed:

```bash
NAME    READY   UP-TO-DATE   AVAILABLE   AGE
nginx   3/3     3            3           2m17s
```

Using `kubectl scale` you can scale deployments up and down arbitrarily, set **preconditions** like the **current number of pods**, or even scale **multiple deployments** at once.

## Scaling Deployments Declaratively

We already talked about *imperative* and *declarative* ways of interacting with Kubernetes objects and their respective (dis-)advantages - and it's no different for scaling deployments. Therefore, we can use the same two ways we already know to scale deployments declaratively - by simply **editing and reapplying** our manifest:

``` yaml hl_lines="5"
apiVersion: apps/v1
kind: Deployment
[...]
spec:
  replicas: 5
  selector:
    [...]
```

```bash
kubectl apply -f workloads/nginx-deployment.yaml
```

Another way to declaratively scale deployments (or edit Kubernetes objects in general) is to use the `kubectl edit` command:

```bash
kubectl edit deployment nginx
```

This will open the **object's definition** in an **interactive editor** (usually `vi` or `vim`) and allow you to **edit** it **directly** 'within' the API. Once you're done, you can save and exit the editor, and `kubectl` will **apply** the changes **automatically**.

!!! tip "Tip"
    `kubectl edit` won't allow you to break things. If the edited object fails the validation by the API, the editor will re-open and display a concise error message for you to fix.

    So don't be scared, try to interactively (and yet declaratively!) scale our `nginx` deployment back to 1!
