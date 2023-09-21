# ClusterIP

The simplest way to expose a service is to use a ClusterIP. This is the default service type and it exposes the service on a cluster-internal IP. Only pods within the cluster can access the service.

<figure markdown>
  ![ClusterIP traffic](../../assets/images/clusterip.png)
  <figcaption>ClusterIP traffic including DNAT-ing</figcaption>
</figure>

As we can see in the figure above, the `ClusterIP` service is exposed via a **virtual IP** in a dedicated **service subnet**.

Upon request by a client, the **service IP** gets DNAT-ed to the **pod IP** of one of the pods in the service. The pod is selected by the **service selector**. On the way back, the pod IP gets reverse DNAT-ed, back to the service IP.

## DNS Resolution

Another advantage of using `Services` instead of `Pods` is that `Services` get a **DNS entry** in the cluster-internal DNS server. This allows us to use the service name as a hostname in our applications.

<figure markdown>
  ![DNS resolution](../../assets/images/clusterdns.png)
  <figcaption>DNS resolution in Kubernetes - <code>.svc.</code> indicates the service domain to the DNS server</figcaption>
</figure>

With this DNS service in place, we can use the service name as a hostname in our applications. The DNS server will resolve the service name to the service IP. This is great, because it allows us to deploy microservices independent from one another - all that needs to be established is the **service name**.

## Creating services

Let's try and see for ourselves if all my claims hold true: we'll create a `ClusterIP` service for our `podinfo` deployment and see if we can access it from within the cluster.

Once again, we can use an imperative cmdlet to create our service manifest:

```bash
mkdir networking
kubectl expose deployment podinfo --port 80 --target-port 9898 --dry-run=client -o yaml > networking/podinfo-clusterip.yaml
```

The manifest looks like this:

<div class="annotate" markdown>

```yaml
apiVersion: v1
kind: Service
metadata:
  creationTimestamp: null
  labels:
    app: podinfo
  name: podinfo
spec:
  ports:
  - port: 80 # (1)!
    protocol: TCP
    targetPort: 9898 # (2)!
  selector: # (3)!
    app: podinfo
status:
  loadBalancer: {}
```

</div>

1.  By default, `port` and `targetPort` are the same. As we want to route HTTP traffic, setting `port` to 80 for our service allows us to omit the port when calling it.
2.  The `targetPort` needs to stay `9898` in order for traffic to be forwarded correctly. 
3.  The service selector is the same as the deployment selector. This means that the service will select the same pods as the deployment.

Let's apply the manifest and take a look at the service and its detected endpoints:

```bash
kubectl apply -f networking/podinfo-clusterip.yaml
kubectl get service,endpoints -l app=podinfo
```

✅ Looking good! We can see our service with its virtual IP, the port it's listening on and the endpoints it has detected and will forward traffic to.

```bash
NAME              TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)   AGE
service/podinfo   ClusterIP   10.96.132.79   <none>        80/TCP    0s

NAME                ENDPOINTS                                            AGE
endpoints/podinfo   10.244.1.16:9898,10.244.2.15:9898,10.244.2.16:9898   0s
```

!!! lab "Lab 7: Connecting to your service"
    Let's try connecting to our service from within the cluster:

    1. Start an interactive shell session in your `nginx` pod using `kubectl exec`.
    2. Try `cURL`ing the displayed `ClusterIP`

!!! stretch "Stretch Goal: DNS resolution"
    1. Try `cURL`ing the service name instead of the `ClusterIP` from within the `nginx` pod.
    2. Try calling the 'complete' DNS name of the service, following the pattern above.

*[NAT]: Network Address Translation
*[DNAT]: Destination Network Address Translation
*[SNAT]: Source Network Address Translation

