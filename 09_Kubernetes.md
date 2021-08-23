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
Install ansible
                sudo apt install ansible
Install python3
                sudo apt install python3
Update the python default version to be 3.x
                sudo update-alternatives --install /lib/bin/python python /lib/bin/python3.x 1 (you need to replace x with an appropriate number)
Using ansible to execute kubernetes-setup.yaml
                ansible-pull -U https://stogit.cs.stolaf.edu/bw/training/training-documents.git/kubernetes-setup.yaml
### Cluster initialize
To initialize a Kubernestes cluster, we will use kubeadm. The command syntax is as follow:
                sudo kubeadm init [options]
For the options part, we have the two most used command:
                --control-plane-endpoint=<IP-address>: this will make the IP address specify with the flag be the communication point between the headnode of kubernetes cluster and the kubernetes worker nodes
                --pod-network-cidr=<IP-net>: this will make the Kubernetes cluster understand the pod networking cidr (if needed)

The next step is to make the cluster being executable by the Kubernetes headnode.
After initialization, we now need a pod networking so that pod inside our Kubernetes cluster can communicate with each other. The three most famous pod networking services are Flannel, Weaves, and Calico. In this training, there will be 2 commands to initialize weave networking and calico since these two are being used at St. Olaf College right now.  

[**PREVIOUS: Introduction to Docker**](08_Docker.md). 

[**NEXT: Introduction to Django**](10_web-server.md)
