[**PREVIOUS: Golden Node Configuration**](06_golden-node-configuration.md)

# Creating More Nodes

With the Headnode and Golden Node properly configured, all that remains is to clone your Golden Node to create worker nodes, which will allow you to learn on a more realistic cluster setup.

## 1. Cloning the Golden Node

* Power off your Golden Node, and then right click on it in the VirtualBox Manager. Click on the clone button.
* Rename the clone. It can be convenient to have a consistent naming system (`worker01`, `worker02`, ...), but choose whatever system works for you.
* Make sure that a new MAC address is generated.
* Create a full clone &mdash; we'll have to change the hostname, and a linked clone would cause any changes made in the new VM to be reflected in the original (which is not what we want!)
* If you've taken any snapshots of your Golden Node, you can include them in the clone, or just take the current machine state. 
* Wait for VirtualBox to clone the virtual hard drive, which could take a bit.

## 2. Updating MAC address and hostname

* Before starting your VM, check the MAC address in the network settings of the VirtualBox Manager. Change the MAC address if you'd like something easier to type.
* Boot up the cloned node and update the `/etc/hostname` file to reflect the hostname of this node. If your Golden Node's hostname is `worker01`, the hostname file of this cloned VM should just contain `worker02`
* Reboot your worker node for the hostname change to take effect.

## 3. Update dhcpd.conf in Headnode

* Just like when configuring the Golden Node, you'll need to update the `/etc/dhcp/dhpcd.conf` file in the Headnode so its DHCP server knows which IP to assign the newly-created node. This is the same process as when editing the file for the Golden Node, just make sure to update the hostname and IP address so that they match the hostfile.

That's there is to cloning a node! You can repeat this process multiple times to create a bigger cluster, but you'll eventually be limited by how many computing resources your host computer can provide.


[**NEXT: Introduction to Docker**](08_Docker.md)
