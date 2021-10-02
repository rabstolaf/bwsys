[**PREVIOUS: Installing Ubuntu**](01_installing-ubuntu.md) 

# Configuring the Head Node

<!-- Cutdown these sequences -->
* Next, we configure our machine to access the St. Olaf LDAP server to login to our machine using our St.Olaf credentials.
* Next we configure NTP to synchronize time on our VMs.
* After that, we modify the `/etc/sudoers` file to allow `sudo` permissions i.e allow certain users and groups access to resources on the MV that are otherwise inaccessible.
* We create a SHA key to enable passwordless ssh on our machine and change the hosts file to add our worker nodes (which are VMs that you will create later).
* After that, we activate our second network interface and configure it to have a static ip address.
* We use NFS to publish our `/home` and `/opt` folders from the headnode.
* Lastly, we configure NAT(Network Address Translation) and DHCP(Dynamic Host Configuration Protocol) so that, later, our workers nodes can get proper IP addresses and connect to the internet.
<!-- End of cut down -->  

Be sure to have completed [*Installing Ubuntu*](01_installing-ubuntu.md) before proceeding with this document.  

###  This is only applicable if you are doing this training REMOTELY.  Set up your VPN.
* Download a VPN of your choice onto your laptop or desktop computer. 
* Activate your VPN.
* Once its connected, go to the network settings of your head node on VirtualBox (which is what you configuring)
* Change the 1st interface from bridged Ethernet to NAT (You may need to turn off your Virtual Machine in order to be able to do that)

### Modifying configuration files

A lot of times in Cluster Management, you'll need to edit configuration files. This is usually done through text editors. An example modifying a file with a text editor is this:

`$ sudo nano /etc/dhcp/dhcpd.conf`  

where `nano` is the text editor called to open the file `/etc/dhcp/dhcpd.conf`.

Commonly used text editors include:
* nano (Easier to handle) ([more info here](https://linuxize.com/post/how-to-use-nano-text-editor/))
* vim (More powerful, but harder to understand) ([more info here](https://opensource.com/article/19/3/getting-started-vim))


## 1. Configuring LDAP

> LDAP is used to look up authorizations on a server so that users get access only to things they should have access to.
> We are using it to enable logging in to the VM using your St. Olaf credentials.
> [This link](http://www.gracion.com/server/whatldap.html) and [this video](https://www.youtube.com/watch?v=F2nFtlS8uEo) explains more about LDAP.

* `$ sudo apt install libpam-ldap`
  * The St. Olaf LDAP server is at `ldaps://ad.stolaf.edu` (**Two `/` not Three `/`**)
  * The base dn is `ou=stoUsers,dc=ad,dc=stolaf,dc=edu`
  * LDAP Version is 3
  * **Do not** make local root database admin
  * LDAP Database **does not** require login
  * The files you have to modify for this step include `/etc/ldap.conf`, `/etc/ldap/ldap.conf` and maybe `/etc/nsswitch.conf`.
These are the files that contain the LDAP configuration.
  * In `/etc/ldap.conf`, the bind dn is `cn=csmanaged,ou=LDAPBindAccounts,dc=ad,dc=stolaf,dc=edu`
  * Ask a Cluster Manager for bind password.
  * At the end of the files `/etc/ldap.conf` and `/etc/ldap/ldap.conf`, put `TLS_REQCERT never`
  * Modify `/etc/nsswitch.conf` file and set the `hosts` line to `files mdns4_minimal [NOTFOUND=return] dns mdns4`

* It is time to test LDAP.
  Logout of your VM and try to log back in using your St. Olaf *username* and *password*.
  If it works, then you did it right!
* **Note:** If your *username* on your VM is different than your St. Olaf *username*, then you need to create a user first.
  Run the following command before testing: `sudo useradd -m -s /bin/bash <stolaf-username>`

## 2. Setting Up NTP

> NTP is used for synchronizing clocks between computer systems.
> [Here](http://www.ntp.org/ntpfaq/NTP-s-def.htm) is a comprehensive documentation on NTP.
> [This video](https://www.youtube.com/watch?v=EkQPkQb2D3g) gives a concise explanation on this topic.

* `$ date` &mdash; this command will show you the date and time
* `$ sudo rm /etc/localtime` &mdash; will delete the file.
* `$ sudo ln -s /usr/share/zoneinfo/US/Central /etc/localtime` &mdash; will create another symlink with the same name and link it to `/usr/share/zoneinfo/US/Central`.This should/will fix your timezone.

* `$ sudo apt install ntp`
* We need to edit the `/etc/ntp.conf` file to make the machine access time from the St. Olaf time servers to enable faster time synchronization.
  * Comment out all lines that access ubuntu time servers for the time.
  * Add `sever timehost.stolaf.edu` which is the St. Olaf College's time server
  * Add the line `restrict 172.22.0.0 mask 255.255.255.0 nomodify notrap` in the appropriate place

    > This restricts the nodes from directly accessing the time servers for time.
    > Instead, they should sync time with your headnode.

* `$ sudo systemctl restart ntp` &mdash; restarts the service
* `$ ntpq -p` &mdash; Check and see if these values are as one would expect (Refer to the [linked video](https://www.youtube.com/watch?v=EkQPkQb2D3g)).
If they are, then you did it right!

## 3. Alter sudoers

> This step modifies the `/etc/sudoers.tmp` file.
> This is necessary to let people in a group be able to use `sudo` on your machine.
> To learn more about this file and changing it, refer to [this link](https://www.garron.me/en/linux/visudo-command-sudoers-file-sudo-default-editor.html).

* `$ sudo groupadd bw-sudo` &mdash; Create a new group called "bw-sudo"
* `$ sudo visudo` &mdash; this opens up the `/etc/sudoers.tmp` file
  * Add the line `%bw-sudo ALL=(ALL:ALL) ALL` in the appropriate place
  * Save and exit as you would for `nano` editor
* `$ sudo usermod -aG bw-sudo <username>` &mdash; this adds yourself to the group bw-sudo
* To test this, use:
  `$ groups <username>`
* Your user should be in the group bw-sudo.

## 4. Passwordless SSH

> SSH helps set up a secure connection between two machines.
> Passwordless SSH would not require you to type in your password everytime you try to establish the connection.
> Go to [this website](https://www.ssh.com/ssh/protocol/) to learn more about SSH.
> [This link](https://blog.tinned-software.net/ssh-passwordless-login-with-ssh-key/) may be useful for learning about SSH Keys and Passwordless SSH.

* `$ ssh-keygen -t rsa -b 4096` &mdash; This generates an SSH key with RSA encryption
  * Press `Enter` to use default directory for saving the SSH key
  * Press `Enter` to use no passphrase (Passphrase is not necessary either)
* `$ cat ~/.ssh/id_rsa.pub > ~/.ssh/authorized_keys` &mdash; this is the file your nodes will access to SSH into your machine
* You can test this after setting up your golden node

## 5. Adding hosts

> This step modifies the `/etc/hosts` file and adds IP addresses corresponding to the worker nodes that we will use in setting up our cluster. This allows the computer to recognize the IPs by the host name that you give them. For instance, the line:

<pre> <code> 172.22.0.254   worker01 </code></pre>

recognizes the name `worker01` as the IP `172.22.0.254`.
> [This discussion](https://askubuntu.com/questions/183176/what-is-the-use-of-etc-hosts) gives a nice idea about the intents and purposes for editing this file.

* We need to edit the `/etc/hosts` file to add the worker nodes in our cluster
  * Add a new line for every worker node you want to add.
    For example: `172.22.0.1  worker01`
  * Add a line to set the headnode IP to `172.22.0.254`, in the format of the line above.
* We can test this step:
  * `$ ssh localhost` should log you back into your machine
  * `$ ssh <worker_node>` should give you a `no route to host` error

## 6. Configure Networking Interfaces

> In this step, we will configure the networking interfaces on our machine.
> One interface will receive DHCP from St. Olaf and the other (Internal Network) will be connected to our our cluster.
> We will do this using `netplan`.
> [This document](https://www.howtoforge.com/linux-basics-set-a-static-ip-on-ubuntu) explains how to do this.
> Google networking terms and concepts you are unfamiliar with.

* `$ ip ad` &mdash; this lists out all the interfaces you have and their configurations (should show 3)
* The following interfaces are ** lo **, which stands for loopback, and **enp0s3**, which is the interface connecting to wifi/St. Olaf network. 
* Each interface has an IP address (e.g. enp0s3 has an IP address of 10.42.240.251)

<pre> <code>
1: lo: &#60;LOOPBACK,UP,LOWER_UP&#62; mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
3: enp0s3: &#60;BROADCAST,MULTICAST,UP,LOWER_UP&#62; mtu 1500 qdisc fq_codel state UP group default qlen 1000
    link/ether 08:00:27:6d:b1:5e brd ff:ff:ff:ff:ff:ff
    inet 10.42.240.251/16 brd 10.42.255.255 scope global dynamic enp0s3
       valid_lft 86163sec preferred_lft 86163sec
</code> </pre>

* Edit the `/etc/netplan/<file>.yaml` &mdash; is the name of the `.yaml` file present in that folder.
Be sure to use spaces and **not tabs** in the file.
(Tabs are invalid in yaml files)
* Add the not-configured interface under `ethernets` (Use website as template)
* Set `renderer` to `networkd`
* Set `addresses` to `[172.22.0.254/24]` &mdash; This creates a network of ip addresses from `172.22.0.0` to `172.22.0.255`
* Set `dhcp4` to `no`
* **Do not set any gateway**
* Save and exit
* `$ sudo netplan --debug apply` &mdash; This puts the configuration into effect
* Check that the interface is configured using `$ ip ad`
* Make sure you still have internet by using `ping` command

## 7. Setting up NFS

> We will use NFS to mount our `/home` and `/opt` folders on the headnode onto the `/home` and `/opt` folders on our worker nodes through the network. 
> NFS allows us to share files between computers, saving space, and synchronizing the contents of the folders being shared. Additionally, we'll be installing some software to the `/opt` folder, which means we'll only have to install the program on the headnode, but the worker nodes will be able to access and use the software.
> [This video](https://www.youtube.com/watch?v=wpg4WgNXoV8&t=1254s) describes how to do this.

* `$ sudo apt install nfs-kernel-server` &mdash; this package controls the NFS mounting
* Open the `/etc/exports` file
* Add the line `/home	172.22.0.0/24(rw,sync)` &mdash; this publishes your `/home` folder and makes it available for mounting in the ip range mentioned
* Also add `/opt	172.22.0.0/24(rw,sync)` &mdash;
* Save and exit
* `$ sudo exportfs -a` &mdash; have changes to /etc/exports take effect immediately
* `$ sudo showmount -e` &mdash; this should show you the folder that you published

## 8. Configuring NAT

> NAT will allow the worker nodes to get access to the internet.
> It will translate and forward packets through the headnode so that they reach the correct worker nodes.
> [This video](https://www.youtube.com/watch?v=QBqPzHEDzvo) explains NAT in more detail.

* Open the file `/etc/sysctl.conf` &mdash; this file contains kernel runtime configuration.
Be careful when you modify it
* Uncomment the line `net.ipv4.ip_forward=1`
* Save and exit
* Test it: `$ sudo sysctl -p` should display the changes you made
* `$ sudo iptables -t nat -L` &mdash; this will list out all present iptables rule.
This should be clean.
If not, run `$ sudo iptables -t nat -F`, and then `$ sudo iptables -t nat -L` again to verify it is empty now.
* `$ sudo iptables -t nat -A POSTROUTING -s 172.22.0.0/24 -o *interface* -j MASQUERADE` &mdash; where *interface* is your WiFi/St. Olaf network facing interface. This allow your worker nodes access to St. Olaf's network, this will be the `tun0` interface.
This command will mask the IP addresses of your cluster so that the nodes can access the internet
* List the rules again to see if the rule was added
* `$ sudo apt install iptables-persistent` &mdash; this package will permanently save your iptable rules (which does not happen otherwise).

Save the `ipv4` rules and not the `ipv6` rules.
* If you reboot your machine and list the rules again, the rule should still be there!
* If you need to modify the iptables rules, run `$ sudo dpkg-reconfigure iptables-persistent`, and save the `ipv4` rules.

## 9. Configure DHCP

> DHCP is a protocol that assigns IP addresses to nodes so that they can be uniquely identified on a network.
> [This video](https://www.youtube.com/watch?v=S43CFcpOZSI) explains how DHCP works in a network.

* `$ sudo apt install isc-dhcp-server` &mdash; this package will let your headnode provide dhcp to the worker nodes
* Open the file `/etc/dhcp/dhcpd.conf` &mdash; this is the configuration file
* Make sure all lines are commented out
* Refer to the provided `/etc/dhcp/dhcpd.conf` file to create your own file
* Save and exit
* Open the `/etc/default/isc-dhcp-server` file
* Enable DHCP only on your cluster facing interface (look at the comments in the file to get a hint on how to do this)
  * Uncomment the line: `DHCPDv4_CONF=/etc/dhcp/dhcpd.conf`
  * Change the line `INTERFACES=""` to `INTERFACES="<interface>"`, where &#60;interface&#62; is the cluster-facing interface (**NOT** the Wifi-facing one)
* Save and exit
* `$ sudo systemctl start isc-dhcp-server`
* `$ systemctl status isc-dhcp-server` &mdash; use this to check if there were any errors and if the server is listening on the correct interface
* If the `systemctl status` command shows that your DHCP server is `failed`, you can use `$ dhcpd -t -cf /etc/dhcp/dhcpd.conf` to check your configuration file for errors

[**NEXT: Basic Scripting**](03_scripting.md)
