# Remote Cluster Management Training Documents

This repository contains training documents for Remote St. Olaf Cluster Management. This training differs from the standard documentation in walking the trainee through installing a desktop environment, installing a program from source code, and setting up a VPN.

**Note:** It is only necessary to follow the Remote Cluster Management Training if the trainee wants to use bridged network adapter. There are advantages of using a bridged adapter (e.g. the VMs will be accessible via SSH from the host) but the setup is noticable more complex. If a VM is configured to use NAT in the VirtualBox Manager settings and the host computer is connected to the College VPN, the VM should be able to access the College's internal network.

## First Steps

Do the following steps:

* Download and install [**VirtualBox**](https://www.virtualbox.org/)
* Download [**Ubuntu 18.04 Server**](https://releases.ubuntu.com/18.04.5/ubuntu-18.04.5-live-server-amd64.iso) ISO file

Then, start with [00_terms.md](https://stogit.cs.stolaf.edu/bw/training/training-documents/-/blob/master/00_terms.md)
