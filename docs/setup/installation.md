# Installation

As mentioned in the beginning, we are going to install three tools on our machines:

- **[Docker](https://docs.docker.com/)**, a container runtime engine
- **[KinD](https://kind.sigs.k8s.io/)**, **K**ubernetes **in** **D**ocker, a tool for running Kubernetes clusters using Docker containers
- **[kubectl](https://kubernetes.io/docs/reference/kubectl/)**, a command line interface for running commands against Kubernetes clusters

Follow the installation instructions for your OS below, and you should have a fully functional Kubernetes cluster running on your machine in no time!

## Docker

### macOS and Windows

On macOS and Windows, you will need to install [Docker Desktop](https://docs.docker.com/desktop/install/mac-install/). You can download the installer from the [Docker website](https://www.docker.com/products/docker-desktop). This is due to the two OS'es system design.

Docker Desktop will spawn a VM in the background upon first start and install the Docker runtime in this VM. You can then use Docker on Windows and MacOS as usual.

!!! info "WSL users"
    If you are using **WSL** (Windows Subsystem for Linux), please read ahead for Linux instructions.

A recent, more performant, and less resource-hungry alternative **on MacOS** is [OrbStack](https://orbstack.dev). Since I haven't had enough time to properly evaluate it yet, I won't provide installation instructions and/or support for this workshop (yet).

Once installed, you can check if Docker is working by running the `hello-world` container image:

```bash
sudo docker run hello-world
```

### Linux

On Linux, you can install Docker using your package manager of choice. I compiled installation instructions for the most common OS flavors below, if yours isn't among them, please head over to the [official installation documentation](https://docs.docker.com/engine/install/) and pick your OS.

!!! info "Docker Desktop on Linux"
    Docker Desktop is available for Linux distributions since earlier this year, too. However, its features are not needed for this workshop and it comes with a performance penalty compared to installing just the Docker daemon.

First, you will have to set up your package manager to use the Docker repository:

=== "Debian"

    ``` bash
    # Add Docker's official GPG key:
    sudo apt-get update
    sudo apt-get install ca-certificates curl gnupg
    sudo install -m 0755 -d /etc/apt/keyrings
    curl -fsSL https://download.docker.com/linux/debian/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
    sudo chmod a+r /etc/apt/keyrings/docker.gpg

    # Add the repository to Apt sources:
    echo \
      "deb [arch="$(dpkg --print-architecture)" signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/debian \
      "$(. /etc/os-release && echo "$VERSION_CODENAME")" stable" | \
      sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
    sudo apt-get update
    ```

=== "Fedora"

    ```bash
    sudo dnf -y install dnf-plugins-core
    sudo dnf config-manager --add-repo https://download.docker.com/linux/fedora/docker-ce.repo
    ```

=== "Ubuntu"

    ``` bash
    # Add Docker's official GPG key:
    sudo apt-get update
    sudo apt-get install ca-certificates curl gnupg
    sudo install -m 0755 -d /etc/apt/keyrings
    curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
    sudo chmod a+r /etc/apt/keyrings/docker.gpg

    # Add the repository to Apt sources:
    echo \
      "deb [arch="$(dpkg --print-architecture)" signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
      "$(. /etc/os-release && echo "$VERSION_CODENAME")" stable" | \
      sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
    sudo apt-get update
    ```

Then, you can go on and install the latest version of Docker:

=== "Debian"

    ```bash
    sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
    ```

=== "Fedora"

    ```bash
    sudo dnf install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
    ```

=== "Ubuntu"

    ```bash
    sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin

    ```

Finally, start the Docker daemon and check if it is working by running the `hello-world` container image:

=== "Debian"

    ```bash
    sudo systemctl enable --now docker
    sudo docker run hello-world
    ```

=== "Fedora"

    ```bash
    sudo systemctl enable --now docker
    sudo docker run hello-world
    ```

=== "Ubuntu"

    ```bash
    sudo systemctl enable --now docker
    sudo docker run hello-world
    ```

## KinD

With Docker up and running, we can set up our Kubernetes cluster using **KinD**! KinD is a tool for running Kubernetes clusters using Docker containers as nodes. It is a great tool for local development and testing, and we will use it throughout this workshop. In fact, KinD development was started as a means of running integration tests for the Kubernetes project itself.

Depending on your OS, installation instructions vary slightly. Please follow the instructions for your OS below.

=== "Windows (Chocolatey)"
    <div class="annotate" markdown>
    ```bash
    # If you are using chocolatey
    choco install kind # (1)!
    ```
    </div>

    1.  Chocolatey is a package manager for Windows. You can find more information [here](https://chocolatey.org/).

=== "Windows (Powershell)"

    ```bash
    curl.exe -Lo kind-windows-amd64.exe https://kind.sigs.k8s.io/dl/v0.20.0/kind-windows-amd64
    Move-Item .\kind-windows-amd64.exe c:\some-dir-in-your-PATH\kind.exe
    ```

=== "macOS (Homebrew)"
    <div class="annotate" markdown>
    ```bash
    brew install kind # (1)!
    ```
    </div>

    1.  Homebrew is a package manager for macOS. You can find more information [here](https://brew.sh).

=== "macOS (Binary)"

    ```bash
    CLI_ARCH=amd64
    if [ "$(uname -m)" = "arm64" ]; then CLI_ARCH=arm64; fi
    curl -Lo ./kind "https://kind.sigs.k8s.io/dl/v0.20.0/kind-darwin-$CLI_ARCH"
    chmod +x ./kind
    mv ./kind /some-dir-in-your-PATH/kind
    ```

=== "Linux (Binary)"

    ```bash
    CLI_ARCH=amd64
    if [ "$(uname -m)" = "arm64" ]; then CLI_ARCH=arm64; fi
    curl -Lo ./kind "https://kind.sigs.k8s.io/dl/v0.20.0/kind-linux-$CLI_ARCH"
    chmod +x ./kind
    sudo mv ./kind /usr/local/bin/kind
    ```

KinD should be installed now - let's move on to kubectl, the final piece of our Kubernetes puzzle!

## kubectl

kubectl is the command line interface for running commands against Kubernetes clusters. It is the main tool we will use throughout this workshop to interact with our Kubernetes cluster.

Installation instructions for kubectl vary slightly depending on your operating system. Please follow the instructions for your OS below.

!!! info "`kubectl` on Windows"
    **Docker Desktop** for Windows installs its own version of `kubectl` which should be sufficient.
    If you are using **WSL** (Windows Subsystem for Linux), please read ahead for Linux instructions.

### macOS

=== "macOS (Homebrew)"

    ```bash
    brew install kubectl
    ```

=== "macOS (Binary)"

    ```bash
    CLI_ARCH=amd64
    if [ "$(uname -m)" = "arm64" ]; then CLI_ARCH=arm64; fi
    curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/darwin/$CLI_ARCH/kubectl"
    # Make the kubectl binary executable.
    chmod +x ./kubectl
    # Move the binary in to your PATH.
    sudo mv ./kubectl /usr/local/bin/kubectl
    ```

### Linux

=== "Debian-based OSes"

    ```bash
    # Add signing key for the Kubernetes repositories
    sudo mkdir -p -m 755 /etc/apt/keyrings
    sudo curl -fsSL https://pkgs.k8s.io/core:/stable:/v1.28/deb/Release.key | sudo gpg --dearmor -o /etc/apt/keyrings/kubernetes-apt-keyring.gpg
    # Add the repository to Apt sources:
    echo 'deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] https://pkgs.k8s.io/core:/stable:/v1.28/deb/ /' | sudo tee /etc/apt/sources.list.d/kubernetes.list
    # Update repositories and install kubectl
    sudo apt-get update
    sudo apt-get install -y kubectl
    ```

=== "RedHat-based OSes"

    ```bash
    # Add the yum Kubernetes repository
    cat <<EOF | sudo tee /etc/yum.repos.d/kubernetes.repo
    [kubernetes]
    name=Kubernetes
    baseurl=https://pkgs.k8s.io/core:/stable:/v1.28/rpm/
    enabled=1
    gpgcheck=1
    gpgkey=https://pkgs.k8s.io/core:/stable:/v1.28/rpm/repodata/repomd.xml.key
    EOF
    # Install kubectl
    sudo yum install -y kubectl
    ```

=== "Linux (Binary)"

    ```bash
    CLI_ARCH=amd64
    if [ "$(uname -m)" = "arm64" ]; then CLI_ARCH=arm64; fi
    curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/$CLI_ARCH/kubectl"
    # Make the kubectl binary executable.
    chmod +x ./kubectl
    # Move the binary in to your PATH.
    sudo mv ./kubectl /usr/local/bin/kubectl
    ```

For now, we got all the tools we need to create our cluster, so let's move on.
