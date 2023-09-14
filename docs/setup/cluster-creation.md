# Cluster Creation

We're nearly there. We installed all our prerequisites, and now we're ready to create our Kubernetes cluster. We will aim at an environment that mimics a **real-world cluster** as **closely as possible** - this implicates a few things:

- we want **more than one node** - otherwise we won't see many of Kubernetes' strengths in action
- we want **proper connectivity** from the outside
- we **don't** want 'mock' or low performance services - those aren't used in production either

Let's get started with KinD's config.

## KinD

KinD gets configured at the time of **cluster creation** by passing a configuration file. So, let's start by creating a new working directory for the remainder of this workshop to use.

```bash
mkdir k8s-workshop && cd k8s-workshop
vim kind-config.yaml
```

### Configuration

We want to create the following configuration - feel free to expand the various tooltips for explanations on the different settings.

<div class="annotate" markdown>
``` yaml
kind: Cluster
apiVersion: kind.x-k8s.io/v1alpha4
name: workshop-cluster
networking:
  disableDefaultCNI: true # (1)!
nodes: # (2)!
  - role: control-plane
    extraPortMappings: # (3)!
      - containerPort: 80
        hostPort: 80
        listenAddress: "127.0.0.1"
        protocol: TCP
      - containerPort: 443
        hostPort: 443
        listenAddress: "127.0.0.1"
        protocol: TCP
    labels: # (4)!
        ingress-ready: "true"
  - role: worker
  - role: worker
```
</div>
1.  Remember the *no mocks* implication? This is one of the reasons why we disable the default CNI. We will use a proper CNI plugin instead.
2.  We want three nodes - one control plane member and two workers. This checks the *more than one node* box.
3.  We want to expose ports 80 and 443 on the host machine. KinD can do this by itself, **unless** we are running on Docker Desktop - so it's better to make sure 😉
    
    This checks the *proper connectivity* box.

4.  We want to label our control plane node so that we can deploy an ingress controller on it later on.

*[CNI]: Container Network Interface
*[ingress controller]: Think of this as a reverse proxy for the time being. We will cover this in more detail later on.

### Deployment

Once we've written our config file, we can create our cluster. This will take a few minutes, so feel free to grab a coffee or a tea in the meantime.

```bash
kind create cluster --config kind-config.yaml
```

!!! warning "Deployment crashed?"
    Unfortunately, KinD runs into some edge cases due to Docker Desktop, OS networking stacks, and other factors here and there. I tried to keep the installation path as 'broad' as possible, but if your installation fails here, check out the [troubleshooting section](https://kind.sigs.k8s.io/docs/user/known-issues/) of the KinD documentation.

### Verification

The cluster is installed, we did it! 🥳 Let's check if everything's alright:

```bash
kubectl config use-context kind-workshop-cluster
kubectl get nodes 
```

Once entered, the output should look like this:

```bash
NAME                             STATUS     ROLES           AGE   VERSION
workshop-cluster-control-plane   NotReady   control-plane   84s   v1.27.3
workshop-cluster-worker          NotReady   <none>          65s   v1.27.3
workshop-cluster-worker2         NotReady   <none>          60s   v1.27.3
```

✅ Our cluster is indeed running and reachable using `kubectl`. However, all our nodes display a `NotReady` status - this is expected. After all, we didn't install a CNI plugin yet, so our nodes don't have any networking capabilities. Let's fix that next!

## Cilium

In Kubernetes, a CNI plugin is used to provide networking capabilities to the pods running on the cluster. Without a CNI plugin, nodes don't have any networking capabilities. Cilium is one of the CNI plugins available for Kubernetes, and is supported by AWS, Azure, and Google Cloud, among others.

It comes with its own CLI for installing, managing, and troubleshooting Cilium clusters. Let's install it:

=== "Windows (Powershell)"
    
    ```bash
    curl.exe -Lo cilium-windows-amd64.tar.gz https://github.com/cilium/cilium-cli/releases/download/v0.15.8/cilium-windows-amd64.tar.gz
    Expand-Archive -Path .\cilium-windows-amd64.tar.gz -DestinationPath C:\path\to\extract\to
    ```

=== "macOS (Homebrew)"

    ```bash
    brew install cilium-cli
    ```

=== "macOS (Binary)"

    ```bash
    CILIUM_CLI_VERSION=$(curl -s https://raw.githubusercontent.com/cilium/cilium-cli/main/stable.txt)
    CLI_ARCH=amd64
    if [ "$(uname -m)" = "arm64" ]; then CLI_ARCH=arm64; fi
    curl -L --fail --remote-name-all https://github.com/cilium/cilium-cli/releases/download/${CILIUM_CLI_VERSION}/cilium-darwin-${CLI_ARCH}.tar.gz{,.sha256sum}
    shasum -a 256 -c cilium-darwin-${CLI_ARCH}.tar.gz.sha256sum
    sudo tar xzvfC cilium-darwin-${CLI_ARCH}.tar.gz /usr/local/bin
    rm cilium-darwin-${CLI_ARCH}.tar.gz{,.sha256sum}
    ```

=== "Linux (Binary)"

    ```bash
    CILIUM_CLI_VERSION=$(curl -s https://raw.githubusercontent.com/cilium/cilium-cli/main/stable.txt)
    CLI_ARCH=amd64
    if [ "$(uname -m)" = "aarch64" ]; then CLI_ARCH=arm64; fi
    curl -L --fail --remote-name-all https://github.com/cilium/cilium-cli/releases/download/${CILIUM_CLI_VERSION}/cilium-linux-${CLI_ARCH}.tar.gz{,.sha256sum}
    sha256sum --check cilium-linux-${CLI_ARCH}.tar.gz.sha256sum
    sudo tar xzvfC cilium-linux-${CLI_ARCH}.tar.gz /usr/local/bin
    rm cilium-linux-${CLI_ARCH}.tar.gz{,.sha256sum}
    ```

Now that we got the CLI, we can go on and install Cilium into our clusters!

```bash
cilium install
cilium status --wait
```

Once finished, Cilium will provide lots of information for us - take a moment to read through it. We can also verify that our nodes are now ready:

```bash
kubectl get nodes
```

This time, the output should display all three nodes as `Ready`:

```bash
NAME                             STATUS   ROLES           AGE   VERSION
workshop-cluster-control-plane   Ready    control-plane   26m   v1.27.3
workshop-cluster-worker          Ready    <none>          26m   v1.27.3
workshop-cluster-worker2         Ready    <none>          25m   v1.27.3
```

**This is it,** as far as setting things up is concerned. From now on, we will learn how to actually **interact with** a Kubernetes cluster, how to **deploy workloads**, and how to **connect** to them!
