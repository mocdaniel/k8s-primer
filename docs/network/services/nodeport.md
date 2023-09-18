# NodePort

While we can get away with using `ClusterIP` services for internal communication between pods, we need a different type of service to expose our applications to the outside world, e.g. to a local office network, or even to the internet.

This is where `NodePort` services come in. They expose a service on a port on each node of the cluster. This allows external clients to access the service via `<NodeIP>:<NodePort>`. The `NodePort` is a port in the range `30000-32767` by default. We can specify a custom port in the service manifest.

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

``` yaml
apiVersion: v1
kind: Service
metadata:
  creationTimestamp: null
  labels:
    app: nginx
  name: nginx
spec:
  type: NodePort # (1)!
  ports:
  - port: 80
    protocol: TCP
    targetPort: 80
    nodePort: 31000 # (2)!
  selector:
    app: nginx
status:
  loadBalancer: {}
```

</div>

1.  `type: NodePort` needs to be set explicitly, as the default service type is `ClusterIP`.
2.  `nodePort: 31000` specifies the port on which the service is exposed on each node. If we don't specify a port, Kubernetes will assign one in the range `30000-32767` by default.

The YAML manifest looks *almost* the same - all we needed to add were fields for the `type` and `nodePort`. Let's see what our service looks like once we applied it:

``` bash
kubectl get svc,endpoints -l app=nginx
```

We can confirm that our `nginx` service was changed from being merely a `ClusterIP` to being a node port - we should now be able to access it from outside the cluster, via the designated port on the respective node.

!!! warning "Remember our cluster configuration"
    Do you remember these parts of our **KinD configuration?** They were added in order to allow us to access our services from outside the cluster:
    
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
    if curl --output /dev/null --silent --head --fail "http://${address%% *}"; then
        echo "Successfully connected to ${address##* } at ${address%% *}"
    else
        echo "Failed to connect to ${address##* } at ${address%% *}"
    fi
done
```

Copy the script to e.g. `networking/connectivity-check.sh` and run it! You should get three success manages! Feel free to hit the nodes with `cURL` manually to see NGINX inside our cluster answer the request.