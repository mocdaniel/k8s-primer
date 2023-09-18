# Installing Charts

Now that we got the Helm CLI installed, we can go ahead and install our first **chart**. A chart is a collection of files that describe a related set of Kubernetes resources. A single chart might be used to deploy something simple, like a single pod, or something complex, like a full web app stack with HTTP servers, databases, caches, and so on.

## Configuring the repository

Similar to many OS package managers, Helm is able to fetch charts from remote repositories, compressed as `.tar.gz` archives. We can add the [official Helm example repository](https://github.com/helm/examples) to our local Helm installation:

``` bash
helm repo add examples https://helm.github.io/examples
```

Once the repository is added, we can search for charts in it:

``` bash
helm search repo examples
```

We can see that the repository contains a single chart, `examples/hello-world`, in **Chart version** `0.1.0` and **App version** `1.16.0`. The **Chart version** is the version of the chart itself, while the **App version** is the version of the application that the chart installs.

``` bash
NAME                	CHART VERSION	APP VERSION	DESCRIPTION
examples/hello-world	0.1.0        	1.16.0     	A Helm chart for Kubernetes
```

## Configuring the Chart

As mentioned before, charts can be configured using **values**. Values are a set of parameters ranging from numbers to strings to complex structures such as lists or nested objects. We can see the default values for the `examples/hello-world` chart by running:

``` bash
helm show values examples/hello-world
```

These values can be set either by creating a file to be passed to the `helm install` command, or by passing them directly to the command via the `--set` flag. In this case, **nested values** can be passed using **dot notation**.

!!! info "Naming convention"
    Technically, the values file can be named anything, but the convention is to name it `values.yaml`. This allows us to easily spot a deployment's configuration e.g. in a large Git repository.

## Installing the Chart

Let's install our first Helm chart! We've already taken a look at the available values and decided to set `replicaCount` to `5`, as well as `service.port` to `8000`. When installing the chart, we can pass these values to the `helm install` command using the `--set` flag. We also need to give our **release** (that's what Helm calls a deployment) a name, so let's call it `helm-example`, and reference the `hell-world` chart contained in the `examples` repository:

``` bash
helm install helm-example examples/hello-world \
    --set replicaCount=5 --set service.port=8000
```

After a short moment, the deployment will have succeeded and some instructions on how to connect to your deployed application will be printed to the console. Go and give them a try!

!!! tip "Stretch Goal"
    Try creating another `Ingress` resource to expose the application on `hello-world.127.0.0.1.nip.io`! For this you will need to get the **service name** and **service port** from the `helm-example` deployment. 😉