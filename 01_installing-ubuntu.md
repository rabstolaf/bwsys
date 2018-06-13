# Training 1
Before you start this training, make sure to have an image (ISO) of **Ubuntu 18.04 Server** downloaded and **VirtualBox** installed on your machine.

#### 1. Create a Virtual Machine (VM)
* Open VirtualBox and click on *New* to create a new VM
* Give it a name, set the type to *Linux* and version to *Ubuntu (64-bit)*
* Allocate some RAM (atleast 1 GB) and create a *Virtual Hard Disk* (fixed size -- atleast 10 GB) for the VM
* After it's done, select the VM you just created and click on *Settings* --> *Storage*
* Click on the optical disk under *Controller: IDE* and then click on the tiny optical disk icon to the right under *Attributes* and select your **Ubuntu 18.04 Server** ISO file
* Then go to *Network* (on the left pane) and attach *Adapter 1* to *Bridged Adapter*
    >This lets your VM access the internet. Refer to [this website](https://www.virtualbox.org/manual/ch06.html) to know more about this setting.
* This is the basic configuration we need right now. Feel free to play around with the settings as long as you know what you are doing!

#### 2. Installing Ubuntu 18.04 Server
* Start your VM, it should load up the installer
* Choose your language and keyboard layout and choose *Install Ubuntu*
* The next screen shows your network interface along with your IPv4 and IPv6 configurations, along with the protocol it will use to set the IP addresses. Hit *Done*
* Leave the proxy address field blank
    > A proxy server is basically another computer which serves as a hub through which internet requests are processed. It may act as a firewall or can be used to anonymously surf the web.
    
* For disk partitioning, choose *Use An Entire Disk*, unless you know how to manually partition. In that case, partition it logically keeping enough space for `/` and `/home` directories.
* Selet *Continue* when it requests confirmation for formatting the partitions
* Fill in the fields appropriately. You will need to remember your *username* and *password*. Hit *Done* and wait for installation to finish
* Hit *Reboot Now* and press *Enter* when prompted to remove installation media
* After reboot finishes, you should be able to login with your *username* and *password*!


***
The rest of the tutorial consists of typing a lot of commands which will be written in the following manner: 
`$ ls -lah`

Some commands would require more permissions to run them, use `sudo` with the command in that case. It is advised to not become `root` while going through this training, unless absolutely necessary.
***

#### 3. Update and Install Some Packages
* `$ apt update`
* `$ apt upgrade`
    > These two commands are used to update the versions of already installed packages on the machine.

* `$ apt install <package_name>` --- used for installing new packages
* Install the following packages: w3m, iptables, openssh-client, wget, nano, info, man-db, manpages, friendly-recovery, bash-completion, command-not-found, dnsutils, lshw, lsof, mtr, psmisc, tcpdump, apt-transport-https, update-manager-core, gcc, g++, libtool, gedit, emacs, pssh, sshpass, xorg

#### 4. Configuring LDAP
> LDAP is used to look up authorizations on a server so that users get access only to things they should have access to. [This link](http://www.gracion.com/server/whatldap.html) and [this video](https://www.youtube.com/watch?v=F2nFtlS8uEo) explains more about LDAP.

* `$ apt install libnss-ldap`
    * The St. Olaf LDAP server is at `ldaps://ad.stolaf.edu` 
    * The base dn is `dc=ad,dc=stolaf,dc=edu`
    * The bind dn is `cn=csmanaged,ou=LDAPBindAccounts,dc=ad,dc=stolaf,dc=edu`
    * After installing LDAP, configure it by running: `$ auth-client-config -t nss -p lac_ldap`
    * The files you might (probably will) have to modify after this step include `/etc/ldap.conf`, `/etc/ldap/ldap.conf`, and `/etc/nsswitch.conf`. These are the files that contain the LDAP configuration. Ask a Cluster Manager for bind password and certificate requirements.
* `$ apt install libpam-ldap`
    > **libpam-ldap** provides an interface between an LDAP server and the PAM user authentication system. Using it along with libnss-ldapd or libnss-ldap allows LDAP to entirely replace other lookup methods (such as NIS or flat-file) for system account tables.

* It is time to test LDAP. Logout of your VM and try to log back in using your St. Olaf *username* and *password*. If it works, then you did it right!

#### 5. Setting Up NTP
> NTP is used for synchronizing clocks between computer systems. [Here](http://www.ntp.org/ntpfaq/NTP-s-def.htm) is a comprehensive documentation on NTP. [This video](https://www.youtube.com/watch?v=EkQPkQb2D3g) gives a concise explanation on this topic.