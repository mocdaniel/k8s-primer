# Scaling a Deployment

If you went for the **stretch goal** in the last section, you might've felt a bit silly - **deleting** and **recreating** a deployment just to scale it seems a bit over the top?! I even told you that deployments allow us to easily scale our workloads in the beginning of this chapter, and indeed, we already saw the `spec.replicas` field in the manifest we just created.

Let's see how we can use it to scale our deployment - once again there is one *imperative* way to do so and two *declarative* ways.

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

Using `kubectl scale`, you can scale deployments up and down arbitrarily, set **preconditions** like the **current number of pods**, or even scale **multiple deployments** at once.

## Scaling Deployments Declaratively

We already talked about *imperative* and *declarative* ways of interacting with Kubernetes objects and their respective (dis-)advantages - and it's no different for scaling deployments. Therefore, we can scale deployments declaratively - by simply **editing and reapplying** our manifest:

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

!!! info "Got you!"
    Turns out, we didn't need to delete and recreate our deployment after all! 😅

    Sorry for making you jump through extra hoops in the last stretch goal, but I wanted to show you that there are multiple ways of doing things in Kubernetes, and that you should always try to find the most appropriate one for your use case.

Another way to declaratively scale deployments (**or edit Kubernetes objects in general**) is to use the `kubectl edit` command:

```bash
kubectl edit deployment nginx
```

This will open the **object's definition** in an **interactive editor** (usually `vi` or `vim`) and allow you to **edit** it **directly** 'within' the API. Once you're done, you can save and exit the editor, and `kubectl` will **apply** the changes **automatically**.

!!! lab "Lab 4: Scale your deployment up and down!"

    Now that we know how to scale deployments, let's try it out!

    1. Scale our `podinfo` deployment up to 5 replicas using `kubectl scale`!

    2. Scale the same deployment down to 1 replica!

    3. Observe the changes happening to your deployment in real-time using `kubectl get`!

    4. Finally, scale your deployment to 3 replicas - that's a solid number for a small deployment like this one!


!!! stretch "Tip"
    `kubectl edit` won't allow you to break things. If the edited object fails the validation by the API, the editor will re-open and display a concise error message for you to fix.

    So don't be scared, try to interactively (and yet declaratively!) scale your `podinfo` deployment back to 1!
