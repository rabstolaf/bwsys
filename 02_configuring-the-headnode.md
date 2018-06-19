# Configuring the Head Node

This is the second training document. 
First we configure our machine to access the St. Olaf LDAP server to login to our machine using our St. Olaf credentials. 
Then we synchronize time on our machine using NTP.
After that, we modify the `/etc/sudoers.tmp` file to allow `sudo` permissions for a group.

Be sure to have completed *Installing Ubuntu* before proceeding with this document.

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

