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
### Installation of kubectl, kubeadm, and kubelet (2 lines explain kubectl, kubeadm, kubelet)
Install ansible

                sudo apt install ansible

Install python3

                sudo apt install python3

Update the python default version to be 3.x

                sudo update-alternatives --install /lib/bin/python python /lib/bin/python3.x 1 (you need to replace x with an appropriate number)
                
Using ansible to execute kubernetes-setup.yaml

                ansible-pull -U https://stogit.cs.stolaf.edu/bw/training/training-documents.git/
### Cluster initialize
To initialize a Kubernestes cluster, we will use kubeadm. The command syntax is as follow:

                sudo kubeadm init [options]
For the options part, we have the two most used command:

                --control-plane-endpoint=<IP-address>: this will make the IP address specify with the flag be the communication point between the headnode of kubernetes cluster and the kubernetes worker nodes
                --pod-network-cidr=<IP-net>: this will make the Kubernetes cluster understand the pod networking cidr (if needed)

After kubeadm init finish it job. Copy the last kubeadm join command.
The next step is to make the cluster being executable by the Kubernetes headnode.
Next we create a directory by running:

                mkdir -p $HOME/.kube
Then we populate it with files that allow us to run kubectl commands without sudo on the headnode:

                sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
Then we make that directory accessible:

                sudo chown $(id -u):$(id -g) $HOME/.kube/config
After initialization, we now need a pod networking so that pod inside our Kubernetes cluster can communicate with each other. The three most famous pod networking services are Flannel, Weaves, and Calico. In this training, there will be 2 commands to initialize weave networking and calico since these two are being used at St. Olaf College right now. We only use one at a time 
For weaves

                kubectl create -f "https://cloud.weave.works/k8s/net?k8s-version=$(kubectl version | base64 | tr -d '\n')"

For calico

                kubectl create -f https://docs.projectcalico.org/v3.19/manifests/calico.yaml

Now execute the join command that you copy previously. If you forget, you can scroll up if you are using desktop version. Another work around way is to use this command:

                kubeadm token create --print-join-command
Copy the part that look similar to this example:

                kubeadm join 162.210.90.20:6443 --token dc7h4j.75tmslmo98g33ocg     --discovery-token-ca-cert-hash sha256:097a93e3b296422f567ccb0fb372c36601e68c7c0d3111510c5def2245f6e7a9

Then either turn on the worker nodes or ssh to worker nodes and type (this is just an example)

                sudo kubeadm join 162.210.90.20:6443 --token dc7h4j.75tmslmo98g33ocg     --discovery-token-ca-cert-hash sha256:097a93e3b296422f567ccb0fb372c36601e68c7c0d3111510c5def2245f6e7a9

Finally to check you Kubernetes Cluster, type

                kubectl get nodes -o wide
If the status of all your nodes are ready, then you finish the training. Well done!

In case, there is 1 or more nodes status are notReady. Use this command to explore the error

                kubectl describe node <the node name>

Feel free to type down the error on google to figure out how to fix it or contact one of our cluster manager.

[**PREVIOUS: Introduction to Docker**](08_Docker.md). 

[**NEXT: Introduction to Django**](10_web-server.md)
