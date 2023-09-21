# NodePort

While we can get away with using `ClusterIP` services for internal communication between pods, we need a different type of service to expose our applications to the outside world, e.g. to a local office network, or even to the internet.

This is where `NodePort` services come in. They expose a service on a port on each node of the cluster. This allows external clients to access the service via `<NodeIP>:<NodePort>`. The `NodePort` is a port in the range `30000-32767` by default, but we can specify a custom port in the service manifest.

<figure markdown>
  ![NodePort](../../assets/images/nodeport.png)
  <figcaption>NodePort traffic, including different NAT-ing depending on the entered node</figcaption>
</figure>

!!! question "Where's the `Service`?"
    When connecting to a `NodePort` service from outside the cluster, i.e. via the port specified, the traffic is indeed routed through our service, and **not** directly to the pods.
    
    However, the NAT-ing happening behind the scenes makes it look like the traffic is routed directly to the pods. This is why the `Service` is not shown in the figure above.

    If you know your way around iptables, hop onto one of your nodes and have a look at the `nat` table. You'll see the `DNAT` and `SNAT` rules that make this possible. 😉

*[NAT]: Network Address Translation
*[DNAT]: Destination Network Address Translation
*[SNAT]: Source Network Address Translation

# Creating a NodePort Service

Let's see how to transform a `ClusterIP` service into a `NodePort` service. We'll start with the manifest of our `nginx` service from the previous section and add a few lines:

<div class="annotate" markdown>

``` yaml hl_lines="9 14"
apiVersion: v1
kind: Service
metadata:
  creationTimestamp: null
  labels:
    app: podinfo
  name: podinfo
spec:
  type: NodePort # (1)!
  ports:
  - port: 80 
    protocol: TCP
    targetPort: 9898
    nodePort: 31000 # (2)!
  selector: 
    app: podinfo
status:
  loadBalancer: {}

```

</div>

1.  `type: NodePort` needs to be set explicitly, as the default service type is `ClusterIP`.
2.  `nodePort: 31000` specifies the port on which the service is exposed on each node. If we don't specify a port, Kubernetes will assign one in the range `30000-32767` by default.

The YAML manifest looks *almost* the same - all we needed to add were fields for the `type` and `nodePort`. Let's see what our service looks like once we applied it:

``` bash
kubectl get svc,endpoints -l app=podinfo
```

```bash
NAME              TYPE       CLUSTER-IP     EXTERNAL-IP   PORT(S)        AGE
service/podinfo   NodePort   10.96.132.79   <none>        80:31000/TCP   8m42s

NAME                ENDPOINTS                                            AGE
endpoints/podinfo   10.244.1.16:9898,10.244.2.15:9898,10.244.2.16:9898   8m42s
```

We can confirm that our `podinfo` service was changed from merely being a `ClusterIP` to being of type `NodePort` - we should now be able to access it from outside the cluster, via the designated port on the respective node.

!!! warning "Remember our cluster configuration"
    Do you remember these parts of our **KinD configuration?** They were added in order to allow us to access our services from outside the cluster in our local lab environments:
    
    ``` yaml
    [...]
    extraPortMappings:
      - containerPort: 31000
        hostPort: 31001
        listenAddress: "127.0.0.1"
        protocol: TCP
    ```

    In case you don't remember, here's the mappings again:

    - `127.0.0.1:31000`-> `workshop-cluster-control-plane:31000`
    - `127.0.0.1:31001` -> `workshop-cluster-worker:31000`
    - `127.0.0.1:31002` -> `workshop-cluster-worker2:31000`

We can check if our service is accessible from outside the cluster by using `cURL` - I prepared a tiny script that will do this for us:

``` bash
#!/bin/bash

# Define the addresses and hostnames to connect to
addresses=("127.0.0.1:31000 workshop-cluster-control-plane"
           "127.0.0.1:31001 workshop-cluster-worker"
           "127.0.0.1:31002 workshop-cluster-worker2")

# Loop through the addresses and connect to each one
for address in "${addresses[@]}"
do
    if curl --output /dev/null --silent --fail "http://${address%% *}"; then
        echo "Successfully connected to ${address##* } at ${address%% *}"
    else
        echo "Failed to connect to ${address##* } at ${address%% *}"
    fi
done
```

Copy the script to e.g. `networking/connectivity-check.sh` and run it! You should get three success manages! Feel free to hit the nodes with `cURL` manually to see `podinfo` inside our cluster answer the request.

!!! lab "Lab 8: Connecting to `podinfo` via browser"
    Now that we got connectivity via our nodes, let's connect to `podinfo` from our browsers!

    1. Open your browser and navigate to `http://127.0.0.1:31000` or one of the other addresses we used in the script above.
    2. You should see the `podinfo` page, which is served by one of the pods behind our service.
    3. Refresh the page a few times and see the pod name change - this is because our service is load balancing the requests between the pods.