# Configuring the Head Node

This is the second training document. 
First we configure our machine to access the St. Olaf LDAP server to login to our machine using our St. Olaf credentials. 
Then we synchronize time on our machine using NTP.
After that, we modify the `/etc/sudoers.tmp` file to allow `sudo` permissions for a group.

Be sure to have completed [*Installing Ubuntu*](01_installing-ubuntu.md) before proceeding with this document.

## 1. Configuring LDAP

> LDAP is used to look up authorizations on a server so that users get access only to things they should have access to. 
> We are using it to enable logging in to the VM using your St. Olaf credentials.
> [This link](http://www.gracion.com/server/whatldap.html) and [this video](https://www.youtube.com/watch?v=F2nFtlS8uEo) explains more about LDAP.

* `$ apt install libnss-ldap`
  * The St. Olaf LDAP server is at `ldaps://ad.stolaf.edu` 
  * The base dn is `dc=ad,dc=stolaf,dc=edu`
  * The bind dn is `cn=csmanaged,ou=LDAPBindAccounts,dc=ad,dc=stolaf,dc=edu`
  * After installing LDAP, configure it by running: `$ auth-client-config -t nss -p lac_ldap`
  * The files you might (probably will) have to modify after this step include `/etc/ldap.conf`, `/etc/ldap/ldap.conf`, and `/etc/nsswitch.conf`. 
These are the files that contain the LDAP configuration. 
Ask a Cluster Manager for bind password and certificate requirements.

* `$ apt install libpam-ldap`
  > **libpam-ldap** provides an interface between an LDAP server and the PAM user authentication system. 
  > Using it along with libnss-ldapd or libnss-ldap allows LDAP to entirely replace other lookup methods (such as NIS or flat-file) for system account tables.

* It is time to test LDAP. Logout of your VM and try to log back in using your St. Olaf *username* and *password*. If it works, then you did it right!

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

* `$ systemctl restart ntp` - restarts the service
* `$ ntpq -p` &#8594; Check and see if these values are as one would expect.
If they are, then you did it right!

## 3. Alter sudoers

> This step modifies the `/etc/sudoers.tmp` file.
> This is necessary to let people in a group be able to use `sudo` on your machine.
> To learn more about this file and changing it, refer to [this link](https://www.garron.me/en/linux/visudo-command-sudoers-file-sudo-default-editor.html).

* `$ groupadd bw-sudo` - Create a new group called "bw-sudo"
* `$ visudo` - this opens up the `/etc/sudoers.tmp` file
  * Add the line `%bw-sudo ALL=(ALL:ALL) ALL` in the appropriate place
  * Save and exit as you would for `nano` editor
* `$ usermod -aG bw-sudo <username>` - this adds yourself to the group bw-sudo
* Log out and log back in for the `usermod` to take effect

## 4. Passwordless SSH

> SSH helps set up a secure connection between two machines.
> Passwordless SSH would not require you to type in your password everytime you try to establish the connection.
> Go to [this website](https://www.ssh.com/ssh/protocol/) to learn more about SSH.
> [This link](https://blog.tinned-software.net/ssh-passwordless-login-with-ssh-key/) may be useful for learning about SSH Keys and Passwordless SSH.

* `mkdir ~/.ssh` - This needs to be done if you have not used SSH on your machine before
* `$ ssh-keygen -t rsa` - This generates an SSH key with RSA encryption
  * Press `Enter` to use default directory for saving the SSH key
  * Press `Enter` to use no passphrase (Passphrase is not necessary either)
* `$ cat ~/.ssh/id_rsa.pub > .ssh/authorized_keys` - this is the file your nodes will access to SSH into your machine
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

## 6. Configure Networking
