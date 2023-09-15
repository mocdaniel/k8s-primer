# Pod Networking

At the moment, you should have at least three instances of **nginx** running on your cluster (depending on how you scaled the deployment in the previous lesson).

Let's see how we can reach them from one another via the pod network.

First, we need to find out the IP addresses of the pods using `kubectl get` with the `-o wide` option. Among other things, this will print the IP address of each pod:

``` bash
NAME                     READY   STATUS    RESTARTS   AGE     IP           NODE                       NOMINATED NODE   READINESS GATES
nginx-7bf8c77b5b-vd4ln   1/1     Running   0          3h50m   10.244.2.5   workshop-cluster-worker    <none>           <none>
nginx1                   1/1     Running   0          2m37s   10.244.1.5   workshop-cluster-worker2   <none>           <none>
nginx2                   1/1     Running   0          2m4s    10.244.1.6   workshop-cluster-worker2   <none>           <none>
```

Next, we will start an interactive shell session in one of the pods using `kubectl exec`, a cmdlet we haven't used yet:

``` bash
kubectl exec -it nginx1 -- sh
```

✅ We're in! The `nginx` container image comes with `cURL` preinstalled, so we already got the tool we need to send HTTP requests to the other pods. Let's try to reach `nginx2`, using the IP displayed for the pod in the previous step:

``` bash
curl 10.244.1.6
```

You should be greeted by the default nginx page. If you try to reach `nginx-7bf8c77b5b-vd4ln` instead, the same answer should be returned (from a different pod, of course). This is because the pod network is a **flat network** that spans across all nodes in the cluster, and all pods can reach each other, regardless of which node they are running on.

!!! info "Info"
    On your cluster, the distribution of pods across nodes might be different, and the IP addresses might be different as well. This is expected, and it's not a problem - the important thing is that all pods can reach each other, regardless of the node they're scheduled on.

While connectivity between pods is great, it's not enough to expose our workloads to the outside world: The pod network is a **private network**, and it's not reachable from outside the cluster, most of the time.

Even if it was, we wouldn't want to expose our pods directly to the outside world, because they are **ephemeral** and could be rescheduled **with a new IP** at any time. We need a more **stable way** to reach our workloads, and that's where **services** come in.