# Installing Helm

**Helm** is a **client-side** CLI - it basically **renders templates** and **applies** the resulting manifests to Kubernetes, same as we would do manually. Therefore, we need to install the CLI to deploy to our clusters:

=== "Windows (Chocolatey)"

    ``` powershell
    choco install kubernetes-helm
    ```

=== "macOS (Homebrew)"

    ``` bash
    brew install helm
    ```

=== "Debian/Ubuntu"

    ``` bash
    curl https://baltocdn.com/helm/signing.asc | gpg --dearmor | sudo tee /usr/share/keyrings/helm.gpg > /dev/null
    sudo apt-get install apt-transport-https --yes
    echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/helm.gpg] https://baltocdn.com/helm/stable/debian/ all main" | sudo tee /etc/apt/sources.list.d/helm-stable-debian.list
    sudo apt-get update
    sudo apt-get install helm
    ```

=== "Fedora"

    ``` bash
    sudo dnf install helm
    ```


You can test the installation by running `helm version`:

``` bash
helm version
```
