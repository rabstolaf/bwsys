# Kubernetes Walk Through
## Introduction to Kubernestes
Previously, we have learnt about Docker. In this chapter, we will learn about how containers are organized in a system.
### What is Kubernestes ?
Kubernetes is a portable, extensible, open-source platform for managing containerized workloads and services, that facilitates both declarative configuration and automation
In general, Kubernestes is the tool to manage our containers.
### Some fundamental definition
        Pod: a group of containers
        Persistent Volume (PV): a piece of storage in a cluster that an administrator has provisioned
        Persistent Volume Claim (PVC): a request for persistent volume 
        Job: one or more Pods and will continue to retry execution of the Pods until a specified number of them successfully terminate.
        Deployment: provides declarative updates for Pods
Many more definitions will be available on the official website of Kubernestes.

## Kubernetes Cluster setup
### Installation of kubectl, kubeadm, and kubelet
Install the stable release version of Kubernetes (1.18.3) with the command

                curl -LO https://dl.k8s.io/release/v1.18.3/bin/linux/amd64/kubectl
Install kubectl

                sudo install -o root -g root -m 0755 kubectl /usr/local/bin/kubectl
Give you the access to execute kubectl as non-root

                chmod +x kubectl
                mkdir -p ~/.local/bin/kubectl
                mv ./kubectl ~/.local/bin/kubectl
                # and then add ~/.local/bin/kubectl to $PATH

To learn more about other option for installation, go check this website: https://kubernetes.io/docs/tasks/tools/install-kubectl-linux/

[**PREVIOUS: Introduction to Docker**](08_Docker.md). 

[**NEXT: Introduction to Django**](10_web-server.md)
