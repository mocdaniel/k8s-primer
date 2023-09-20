# Overview

This website is meant to be a workshop that leads you through the process of **creating a simple Kubernetes environment** right on **your local machine** and make your first steps on it.

Over the course of the next ~3 hours, we will first have a look at the **possible reasons** why and when you might want to use Kubernetes, before installing the following tools on our local machines:

- **[Docker](https://docs.docker.com/)**, a container runtime engine
- **[KinD](https://kind.sigs.k8s.io/)**, **K**ubernetes **in** **D**ocker, a tool for running Kubernetes clusters using Docker containers as nodes
- **[kubectl](https://kubernetes.io/docs/reference/kubectl/)**, a command line interface for running commands against Kubernetes clusters

The result will be a local Kubernetes cluster that we can use to run our own applications, while already mimicking a real-world cluster to some degree.

From there on, we will look at the **different components** of a Kubernetes cluster, and how they interact with each other. We will also learn how to **deploy our own applications** to the cluster, and how to **scale** them up and down.

This workshop glosses over some of the more advanced topics of Kubernetes, such as **storage**, **security**, and **observability**. It is meant to give you a first impression of Kubernetes, and to get you started with it. Once you are comfortable with the basics, you can start diving deeper into the more advanced topics.

## Navigating the Workshop

The workshop is structured **linearly**, so we will just follow the links at the bottom of each page to get to the next one. If you want to jump to a specific section, you can use the navigation on the left side of the page.

### Admonitions

From time to time, you will find so-called *admonitions* in the text. They look like this:

!!! quote "Admonition"
    I contain additional information, hands-on labs, or caveats.

I will stick to using the following five admonitions:

!!! info "Good to know"
    The `Good to know` admonition will contain additional information you can read or skip over.

!!! warning ""Argh!""
    The `"Argh!"` admonition will contain information that is important to know, and that you should not skip over, or things might go south.

    This can be related to a setup task, some common misconceptions about Kubernetes, or anything else that I think is important to know.

!!! lab "Your turn"
    The `Your turn` admonition will contain the hands-on labs that you can follow along with.

!!! question "What's this?"
    The `What's this?` admonition will contain further information, in case you want to dive deeper into a specific topic I chose not to cover in detail.

!!! stretch "Stretch goal"
    The `Stretch goal` admonition will contain additional tasks that you can do if you want to go the extra mile.

### Tooltip and More Information

Sometimes, I want to clarify things right away, because that way the context gets clearer than with an admonition. In these cases, I will use a tooltip, which can be hovered to reveal additional information.

*[hovered]: Hey, you found it!

Alternatively, especially in **code snippets**, I will use the (1) button, which will reveal additional information when clicked:
{ .annotate }

1.  Hey, you found it! :tada:

With this technicalities out of our way, there's only one thing to do:

[Start learning K8s! :rocket: ](introduction/what-is-kubernetes.md){ .md-button .md-button--primary }
