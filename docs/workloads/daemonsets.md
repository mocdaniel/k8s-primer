# DaemonSets

Where the `Deployment` is like a **Swiss Army Knife**, usable for a broad variety of workloads and deployment strategies, the `DaemonSet` is like a **tin opener**: Built for a very specific use-case, i.e. running exactly **one pod per node**.

This can be useful for system utilities like **log collectors**, **monitoring agents**, or **storage daemons** that need to run on every node in the cluster.

In fact, we already got a `DaemonSet` running in our cluster:

<div class="annotate" markdown>

```bash
kubectl get daemonsets,pods -n kube-system -l app=kindnet -o wide # (1)!
```

</div>

1.  `-n kube-system` tells `kubectl` to look for `DaemonSets` and `Pods` in the `kube-system` namespace.

    `-l app=kindnet` tells `kubectl` to **only** show objects which are **labeled** with `app=kindnet`.

    `-o wide` tells `kubectl` to show us more information in the **output**.

```bash
NAME                     DESIRED   CURRENT   READY   UP-TO-DATE   AVAILABLE   NODE SELECTOR            AGE   CONTAINERS    IMAGES                                          SELECTOR
daemonset.apps/kindnet   3         3         3       3            3           kubernetes.io/os=linux   71m   kindnet-cni   docker.io/kindest/kindnetd:v20230511-dc714da8   app=kindnet

NAME                READY   STATUS    RESTARTS   AGE   IP           NODE                             NOMINATED NODE   READINESS GATES
pod/kindnet-n2xxq   1/1     Running   0          71m   172.18.0.2   workshop-cluster-worker2         <none>           <none>
pod/kindnet-nvkd2   1/1     Running   0          71m   172.18.0.4   workshop-cluster-worker          <none>           <none>
pod/kindnet-qdnvk   1/1     Running   0          71m   172.18.0.3   workshop-cluster-control-plane   <none>           <none>
```

The `kindnet` `DaemonSet` is a **networking daemon** that is responsible for **networking** in our cluster. It's a **core component** of KinD, and needs to run on every node to provide **connectivity** between our pods, and nodes.

Therefore, it's a perfect example for a `DaemonSet` and when they come in handy!