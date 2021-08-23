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
Install Kubernetes prerequisites including apt-transport-https, curl, and gnupg2
        sudo apt install apt-transport-https curl gnupg2

[**PREVIOUS: Introduction to Docker**](08_Docker.md). 

[**NEXT: Introduction to Django**](10_web-server.md)
