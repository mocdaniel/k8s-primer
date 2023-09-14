# Overview

This website is meant to lead you through the process of **creating a simple Kubernetes environment** right on **your local machine** and make your first steps on it.

Over the course of the next ~3 hours, we will first have a look at the **possible reasons** why and when you might want to use Kubernetes, before installing the following tools on your local machine:

- **[Docker](https://docs.docker.com/)**, a container runtime engine
- **[KinD](https://kind.sigs.k8s.io/)**, **K**ubernetes **in** **D**ocker, a tool for running Kubernetes clusters using Docker container
- **[kubectl](https://kubernetes.io/docs/reference/kubectl/)**, a command line interface for running commands against Kubernetes clusters

The end result will be a local Kubernetes cluster that you can use to run your own applications, while already mimicking a real-world cluster to some degree.

From there on, you will look at the **different components** of a Kubernetes cluster, and how they interact with each other. You will also learn how to **deploy your own applications** to the cluster, and how to **scale** them up and down.

Let's get started with a short overview of Kubernetes, its history, and its main concepts.
