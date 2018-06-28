# Configuring the Head Node

This is the second training document.
First we configure our machine to access the St. Olaf LDAP server to login to our machine using our St. Olaf credentials.
Then we synchronize time on our machine using NTP.
After that, we modify the `/etc/sudoers.tmp` file to allow `sudo` permissions for a group.
We create a SHA key to enable passwordless ssh on our machine and change the hosts file to add our worker nodes.
After that, we activate our second network interface and configure it to have a static ip address.


Be sure to have completed [*Installing Ubuntu*](01_installing-ubuntu.md) before proceeding with this document.

## 1. Configuring LDAP

> LDAP is used to look up authorizations on a server so that users get access only to things they should have access to.
> We are using it to enable logging in to the VM using your St. Olaf credentials.
> [This link](http://www.gracion.com/server/whatldap.html) and [this video](https://www.youtube.com/watch?v=F2nFtlS8uEo) explains more about LDAP.

* `$ apt install libnss-ldap`
  * The St. Olaf LDAP server is at `ldaps://ad.stolaf.edu`
  * The base dn is `ou=stoUsers,dc=ad,dc=stolaf,dc=edu`
  * The bind dn is `cn=csmanaged,ou=LDAPBindAccounts,dc=ad,dc=stolaf,dc=edu`
  * The files you might (probably will) have to modify for this step include `/etc/ldap.conf`, `/etc/ldap/ldap.conf`.
These are the files that contain the LDAP configuration.
Ask a Cluster Manager for bind password and certificate requirements.
  * Modify the `/etc/nsswitch.conf` file and set the `hosts` line to `files mdns4_minimal [NOTFOUND=return] dns mdns4`.
Save the file and exit

* `$ apt install libpam-ldap`
  > **libpam-ldap** provides an interface between an LDAP server and the PAM user authentication system.
  > Using it along with libnss-ldapd or libnss-ldap allows LDAP to entirely replace other lookup methods (such as NIS or flat-file) for system account tables.

* It is time to test LDAP.
  Logout of your VM and try to log back in using your St. Olaf *username* and *password*.
  If it works, then you did it right!

## 2. Setting Up NTP

> NTP is used for synchronizing clocks between computer systems.
> [Here](http://www.ntp.org/ntpfaq/NTP-s-def.htm) is a comprehensive documentation on NTP.
> [This video](https://www.youtube.com/watch?v=EkQPkQb2D3g) gives a concise explanation on this topic.

* `$ apt install ntp`
* We need to edit the `/etc/ntp.conf` file to make the machine access time from the St. Olaf time servers to enable faster time synchronization.
  * Comment out all lines that access ubuntu time servers for the time
  * Add `timehost.stolaf.edu` as a time server instead
  * Add the line `restrict 10.0.0.0 mask 255.255.255.0 nomodify notrap` in the appropriate place

    > This restricts the nodes from directly accessing the time servers for time.
    > Instead, they should sync time with your headnode.

* `$ systemctl restart ntp` &mdash; restarts the service
* `$ ntpq -p` &mdash; Check and see if these values are as one would expect.
  If they are, then you did it right!

## 3. Alter sudoers

> This step modifies the `/etc/sudoers.tmp` file.
> This is necessary to let people in a group be able to use `sudo` on your machine.
> To learn more about this file and changing it, refer to [this link](https://www.garron.me/en/linux/visudo-command-sudoers-file-sudo-default-editor.html).

* `$ groupadd bw-sudo` &mdash; Create a new group called "bw-sudo"
* `$ visudo` &mdash; this opens up the `/etc/sudoers.tmp` file
  * Add the line `%bw-sudo ALL=(ALL:ALL) ALL` in the appropriate place
  * Save and exit as you would for `nano` editor
* `$ usermod -aG bw-sudo <username>` &mdash; this adds yourself to the group bw-sudo
* Log out and log back in for the `usermod` to take effect

## 4. Passwordless SSH

> SSH helps set up a secure connection between two machines.
> Passwordless SSH would not require you to type in your password everytime you try to establish the connection.
> Go to [this website](https://www.ssh.com/ssh/protocol/) to learn more about SSH.
> [This link](https://blog.tinned-software.net/ssh-passwordless-login-with-ssh-key/) may be useful for learning about SSH Keys and Passwordless SSH.

* `mkdir ~/.ssh` &mdash; This needs to be done if you have not used SSH on your machine before
* `$ ssh-keygen -t rsa -b 4096` &mdash; This generates an SSH key with RSA encryption
  * Press `Enter` to use default directory for saving the SSH key
  * Press `Enter` to use no passphrase (Passphrase is not necessary either)
* `$ cat ~/.ssh/id_rsa.pub > .ssh/authorized_keys` &mdash; this is the file your nodes will access to SSH into your machine
* You can test this after setting up your golden node

## 5. Adding hosts

> This step modifies the `/etc/hosts` file and adds IP addresses corresponding to the worker nodes that we will use in setting up our cluster.
> [This discussion](https://askubuntu.com/questions/183176/what-is-the-use-of-etc-hosts) gives a nice idea about the intents and purposes for editing this file.

* We need to edit the `/etc/hosts` file to add the worker nodes in our cluster
  * Add a new line for every worker node you want to add.
    For example: `10.0.0.1  worker01`
  * Add a line to set the headnode IP to `10.0.0.254`
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
* Edit the `/etc/netplan/<file>.yaml` &mdash; <file> is the name of the `.yaml` file present in that folder
* Add the not-configured interface under `ethernets` (Use website as template)
* Set `addresses` to `[10.0.0.0/24]` &mdash; This creates a network of ip addressess from `10.0.0.0` to `10.0.0.255`
* Set `dhcp4` to `no`
* **Do not set any gateway**
* Save and exit
* `$ netplan --debug apply` &mdash; This puts the configuration into effect
* Check that the interface is configured using `$ ip ad`
* Make sure you still have internet by using `ping` command

## 7. Install etckeeper

> `etckeeper` is a version control system for the `/etc` folder.
> It automatically commits the changes you make to the `/etc` folder.

* `$ apt install etckeeper`

## 8. Setting up NFS

> We will use NFS to mount our `/home` folders on the headnode onto the `/home` folders on our worker nodes through the network. 
> This enables us to use one folder to store all our work instead of having separate folders on the separate machines.
> [This video](https://www.youtube.com/watch?v=wpg4WgNXoV8&t=1254s) describes how to do this.

* `$ apt install nfs-kernel-server` &mdash; this package controls the NFS mounting
* Open the `/etc/exports` file
* Add the line `/home	10.0.0.0/24(rw,sync)` &mdash; this publishes your `/home` folder and makes it available for mounting in the ip range mentioned
* Save and exit
* You can test this step when setting up your golden node

## 9. Configure NAT

> NAT will allow the worker nodes to get access to the internet.
> It will translate and forward packets through the headnode so that they reach the correct worker nodes.
> [This video](https://www.youtube.com/watch?v=QBqPzHEDzvo) explains NAT in more detail.

* Open the file `/etc/sysctl.conf` &mdash; this file contains kernel runtime configuration.
Be careful when you modify it
* Uncomment the line `net.ipv4.ip_forward=1`
* Save and exit
* Test it: `$ sysctl -p` should display the changes you made
* Now we have to write a script.
Take a look at the [scripting tutorial](03_scripting.md) to get an idea of how to do it.
  * Write a script containing the following command: `iptables -t nat -A POSTROUTING -s 10.0.0.0/24 -o *interface* -j MASQUERADE` where *interface* is the name of your St. Olaf network facing interface.
  * Save it in a file called `ipv4forward.sh` in the folder `/etc/init.d` &mdash; this folder contains scripts that run when the machine boots.
  * Change permissions on the file with `chmod` and run the script.
  * `$ sudo update-rc.d ipv4forward.sh defaults` &mdash; may execute with some error
* You can test this step when setting up the golden node

## 10. Configure DHCP

> DHCP is a protocol that assigns IP addresses to nodes so that they can be uniquely identified on a network.
> [This video](https://www.youtube.com/watch?v=S43CFcpOZSI) explains how DHCP works in a network.

* `$ apt install isc-dhcp-server` &mdash; this package will let your headnode provide dhcp to the worker nodes
* Open the file `/etc/dhcp/dhcpd.conf` &mdash; this is the configuration file
* Make sure all lines are commented out
* Refer to the provided `/etc/dhcp/dhcpd.conf` file to create your own file
* Save and exit
* Open the `/etc/default/isc-dhcp-server` file
* Enable DHCP only on your cluster facing interface
* Save and exit
* `$ systemctl start isc-dhcp-server`
* `$ tail -n 30 /var/log/syslog` &mdash; use this to check if there were any errors and if the server is listening on the correct interface
