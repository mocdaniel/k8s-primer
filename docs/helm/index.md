# Helm

Up until now, we deployed our resources to Kubernetes using `kubectl apply`, and it has served us well. Looking back, our deployment of `nginx` consists of the following manifests (omitting the needed ingress controller):

-  `nginx-deployment.yaml`
-  `nginx-clusterip.yaml`
-  `nginx-ingress.yaml`

While this is still bearable, it gets tedious when the **number of microservices** grows - they all might need services, storage, configuration, etc., and we have to keep track of all of them. We might want to look for **different ways** of **deploying to Kubernetes**.

[Helm](https://helm.sh) is one such way. It is a **package manager** for Kubernetes, and it allows us to **define our resources** in a **single place**. We can then **install** or **upgrade** our resources with a **single command**. Even better, we can configure our resources with variables, so-called **values**, at deploy or upgrade time.

While we won't learn how to **package** our own applications (called **Charts**) in this workshop, we will have a short look at how to use Helm to **deploy** applications using Helm.

First, though, we need to install the Helm CLI.
