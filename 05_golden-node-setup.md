# Golden Node Setup

This document is for setting up your Golden Node.
This will be a worker node with ideal configuration.
This node will be cloned to create all new worker nodes.

In this document, we will go through the steps of getting Ubuntu 18.04 Server running on this Golden Node.
This will involve letting your Golden Node netboot from the headnode.

## 1. Making the Virtual Machine

> These are the instructions for creating your Golden Node on another Virtual Machine.

* Create another virtual machine
* Refer to the [first document](01_installing-ubuntu.md) for configurations and on how to do this
* There is one change for this VM
  * Under *Network*, enable only one *Adapter*
  * Connect it to *Internal Network* and give it the same name and advanced configuration as the *Internal Network* on your headnode VM
  * Take a note of the *MAC Address* of this VM

## 2. Edit the `/etc/dhcp/dhcpd.conf` file

> This will enable your headnode to get an IP Address from your headnode and netboot from it.

* Go to your headnode and open the `/etc/dhcp/dhcpd.conf` file
* Set the MAC Address of the first host to the MAC Address of the Golden Node VM
* Save and Exit and restart the `isc-dhcp-server`

## 3. Boot the Golden Node

* When the Node starts, press <kbd>F12</kbd> to load the boot menu
* Press <kbd>l</kbd> to select *lan*
* The Node should get DHCP and an IP of 10.0.0.1
* It should then PXE-Boot and show you the Ubuntu 18.04 Server installer menu
* Press <kbd>Enter</kbd> and wait a little

## 4. Installing Ubuntu

* Select your language and country
* Do not detect keyboard layout, select it manually
* `hostname` should be automatically set
* Select the default ubuntu mirror
* Do not enter any http proxy
* Create the same user on the golden node as the one you have on the headnode
* Set timezone
* For partitioning the hard disk, select `Guided - use entire disk`
* Follow through the process and format the disk
* Install security updates automatically
* Scroll down and select OpenSSH Server by pressing <kbd>Space</kbd>.
Then press <kbd>Enter</kbd>
* Install GRUB Boot Loader
* Check if system clock is set to UTC
* Select `Continue` and you should be done!
* Install any packages you feel are necessary.
Refer to the [Installing Ubuntu Document](01_installing-ubuntu.md) to get some suggestions
