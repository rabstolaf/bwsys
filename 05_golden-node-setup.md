[**PREVIOUS: Applications**](04_applications.md)

# Golden Node Setup

This document is for setting up your Golden Node. The Golden Node is the worker node with the ideal "worker node" configuration.
This node is useful for creating other worker node. The Golden Node can be cloned to create new worker nodes. 

In this document, we will go through the steps of getting Ubuntu 18.04 Server (Linux Operating System) running on this Golden Node. 

## 1. Making the Virtual Machine

> These are the instructions for creating your Golden Node.

* Create another virtual machine on VirtualBox by clicking on "New".
* Give it a name, set the type to *Linux* and the version to *Ubuntu (64-bit)*.
* Allocate some RAM (at least 1 GB) and create a *Virtual Hard Disk* (fixed size - at least 10 GBs) for the VM.
* After it's done, select the VM you just created and click on *Settings* &rarr; *Storage*.
* Click on the optical disk under *Controller: IDE* and then click on the tiny optical disk icon to the right under *Attributes* and select your **Ubuntu 18.04 Server** ISO file.


* Do the following for the Golden Node networking configuration: 
  * Under *Settings* &rarr; *Network*, enable *Adapter 1* only, 
  * Connect it to *Internal Network* and give it the same name and advanced configuration as the *Internal Network* on your headnode virtual machine
  * Take a note of the *MAC Address* of this VM which is found under *Setings* &rarr; *Network* &rarr; *Adapter1* &rarr; *Advanced* &rarr; *MAC Address*.

## 2. On the HEADNODE, Edit the `/etc/dhcp/dhcpd.conf` file

> This will enable your golden node to get an IP Address from your headnode.

* Go to your headnode and edit the `/etc/dhcp/dhcpd.conf` file. Refer to the dhcpd [config file on this repository](config_files) to see how this is done. 
* Set the MAC Address of the first host to the MAC Address of the Golden Node VM. Remember the MAC address can be found under *Setings* &rarr; *Network* &rarr; *Adapter1* &rarr; *Advanced* &rarr; *MAC Address*
* Save and Exit and restart the `isc-dhcp-server` by running the command on the headnode: `sudo systemctl restart isc-dhcp-server`


## 3. Installing Ubuntu 18.04 Server on the golden node

* Note: Use the space bar for selecting.

* Start your golden node VM, it should load up the installer
* Choose your language and keyboard layout and choose *Install Ubuntu*
* The next screen shows your network interface along with your IPv4 and IPv6 configurations, along with the protocol it will use to set the IP addresses. Hit *Done*
* Leave the proxy address field blank
  > A proxy server is basically another computer which serves as a hub through which internet requests are processed. 
  > It may act as a firewall or can be used to anonymously surf the web.

* For disk partitioning, choose *Use An Entire Disk*.
* Select *Continue* when it requests confirmation for formatting the partitions
* Fill in the fields appropriately. You will need to remember your *username* and *password*.
It is recommended to use your St. Olaf *username* but not your St. Olaf *password*.
Hit *Done* and wait for installation to finish
* If prompted, install the `OpenSSH Server`
* If a screen pops up with a list of things to install, you may disregard it and select *Done*
* Hit *Reboot Now* and press *Enter* when prompted to remove installation media
* After reboot finishes, you should be able to login with your *username* and *password*!
  

[**NEXT: Golden Node Configuration**](06_golden-node-configuration.md)
