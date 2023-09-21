# Installing Charts

Now that we got the Helm CLI installed, we can go ahead and install our first **chart**. A chart is a collection of files that describe a related set of Kubernetes resources. A single chart might be used to deploy something simple, like a single pod, or something complex, like a full web app stack with HTTP servers, databases, caches, and so on.

## Configuring the Repository

Similar to many OS package managers, Helm is able to fetch charts from remote repositories, compressed as `.tar.gz` archives. We can add the [podinfo Helm chart](https://stefanprodan.github.io/podinfo) to our local Helm installation:

``` bash
helm repo add podinfo https://stefanprodan.github.io/podinfo
```

Once the repository is added, we can search for charts in it:

``` bash
helm search repo podinfo
```

We can see that the repository contains a single chart, `podinfo/podinfo`, in **Chart version** `6.4.1` and **App version** `6.4.1`. The **Chart version** is the version of the chart itself, while the **App version** is the version of the application that the chart installs.

``` bash
NAME                	CHART VERSION	APP VERSION	DESCRIPTION
examples/hello-world	0.1.0        	1.16.0     	A Helm chart for Kubernetes
```

## Configuring the Chart

As mentioned before, charts can be configured using **values**. Values are a set of parameters ranging from numbers to strings to complex structures such as lists or nested objects. We can see the default values for the `podinfo/podinfo` chart by running:

``` bash
helm show values podinfo/podinfo
```

!!! question "That's so much YAML!"
    Well, yes, it is. However, trust me, you will get used to it over time - many Helm charts expose a shared set of values, so navigating those `values.yaml` files gets easier over time. 🙂

These values can be set either by creating a file to be passed to the `helm install` command, or by passing them directly to the command via the `--set` flag. In this case, **nested values** can be passed using **dot notation**.

!!! info "Naming convention"
    Technically, the values file can be named anything, but the convention is to name it `values.yaml`. This allows us to easily spot a deployment's configuration e.g. in a large Git repository.

## Installing the Chart

Now, here's what we will do: We will deploy the **same setup** we created for `podinfo` *manually* over the course of the previous labs, but this time using a Helm chart. This will allow us to configure the whole stack all at once, using a single command.

Since we need a few values, we will create a `values.yaml` file to store them:

``` yaml
replicaCount: 3
service:
  enabled: true
  type: ClusterIP
ingress:
  enabled: true
  hosts:
    - host: helm-podinfo.127.0.0.1.nip.io
      paths:
        - path: /
          pathType: Prefix
```

Save the file to your workshop workspace, and we're ready to install the chart:

``` bash
helm install helm-podinfo podinfo/podinfo -f values.yaml
```

After a short moment, the deployment will have succeeded and some instructions on how to connect to your deployed application will be printed to the console. Go and give them a try!

!!! lab "Lab 9: Explore the Helm installation"
    Have a look around - did we really manage to deploy all the manifests we created in the previous labs with a single command?

    1. Start by displaying `Services`, `Deployments`, and `Ingresses`
    2. Check the Ingress by connecting via the URL printed to the console
    3. Have a closer look at some of the manifests created by us vs. Helm - where do they differ?

!!! tip "Stretch Goal"
    Play around with Helm for a bit. Try to install the chart without a `values.yaml` file, and pass the values directly to the `helm install` command using the `--set` flag. 

    Try to change the number of replicas, or the ingress host. Try to install the chart again, but this time using a different release name. What happens?
