# What is Kubernetes?

!!! quote "Quote"
    **Kubernetes is a platform for building platforms. It's a better place to start; not the endgame.**
   
    \- Kelsey Hightower [on Twitter](https://twitter.com/kelseyhightower/status/935252923721793536)

!!! quote "Quote"
    **I still think Kubernetes is a practical joke played by Google on the rest of the world.**

    \- Something I overheard at KubeCon EU 2023

!!! question "...played by **Google**?"
    If you want to know more about the history of Kubernetes, and how it came to be, I recommend watching [this documentary](https://www.youtube.com/watch?v=BE77h7dmoQU) on Youtube.


Kubernetes is a **container orchestrator**, a platform for running and managing containerized applications at scale. It is designed to manage clusters of hosts running containers, and provides the following features:

- **Self-healing**: Kubernetes can automatically restart containers that fail, replace containers, kill containers that don't respond to your user-defined health check, and doesn't advertise them to clients until they are ready to serve.

- **Horizontal scaling**: Kubernetes can scale your application up and down with a simple command, or automatically based on CPU usage, or arbitrary metrics.

- **Service discovery and load balancing**: Kubernetes can route traffic to containers, and distribute traffic across multiple containers providing the same service.

- **Automated rollouts and rollbacks**: Kubernetes can roll out new features without downtime, and rollback to previous versions if something goes wrong.

- **Automatic bin packing**: Kubernetes can automatically place containers based on their resource requirements and other constraints, while not sacrificing availability.

- **Secret and configuration management**: Kubernetes can deploy and update secrets and application configuration without rebuilding your container images and without exposing secrets in your stack configuration.

Kubernetes does all this by providing a **set of APIs** that allow you to describe your application, and **controllers** that continuously works to make your actual state match your desired state. We will learn more about the inner workings of Kubernetes in the next section.

!!! warning "Kubernetes **is not** Docker!"
    While Kubernetes does all of the above, it does **not** run your containers! This is done by a local **container runtime** to be installed on every node of the cluster. In this course, we will use **Docker**.
