# Golden Node Configuration

After updating the repository and upgrading packages on your Golden Node, we will:
* update the `/etc/hosts` file 
* enable passworldless ssh
* Use NFS to mount our `/home` and `/opt` folders from our headnode onto the golden node
* Set the headnode as the NTP server for the headnode
* Install and configure LDAP
* Configure Golden Node through the mounted `/opt` folder to use OpenMPI

## 1. Update `/etc/hosts` file

> This file is mainly for hostname resolving, mapping a hostname to a specific IP address, so make this identical to the headnode's file.
> Refer to the document about [Configuring The Headnode](02_configuring-the-headnode.md) to see what you did and do the same here.
>
<br/>Either:
* Go back and take a look at the `/etc/hosts` file in your headnode and make this file identical to that one
<br/>Or:
*  Use scp to copy the file from the headnode to the Golden Node:
* $ sudo scp *user*@*headnode address*:/etc/hosts /etc/hosts
<br/>Then:
* Test using the same testing process (ssh) as mentioned in the [second document](02_configuring-the-headnode.md)
  * $ ssh localhost should log you back into your machine
  * $ ssh <worker_node> should require a password to login

## 2. Passwordless SSH

> This will enable you to SSH into your headnode from your Golden Node and vice-versa without a password.
> Refer to the [second document](02_configuring-the-headnode.md) for information on SSH.

* `$ ssh-keygen -t rsa -b 4096` &mdash; this generates an SSH Key on your Golden Node.
Do not change the default folder.
Do not create a passphrase.
* `$ cat ~/.ssh/id_rsa.pub > ~/.ssh/authorized_keys` &mdash; to be able to SSH into yourself
* `$ ssh-copy-id *headnode*` &mdash; where *headnode* is the name of your headnode
* `$ ssh *headnode*` &mdash; this should let you SSH into your heanode without asking for a password
* Now that you are on your headnode, run: `$ ssh-copy-id *golden*` where *golden* is the name of your Golden Node.
* `$ ssh *golden*` &mdash; this should let you ssh into your golden node without asking for a password
* `$ exit` or <kbd>Ctrl</kbd>+<kbd>D</kbd> &mdash; this allows you to close an SSH session.
Use it the appropriate number of times to get back to your Golden Node. The command `who` may help

## 3. NFS

> In our headnode, we had used the `/etc/exports` file to publish and make our `/home` directory and `/opt` directory available for mounting. We will now mount it on the Golden Node

* `$ apt install nfs-kernel-server`
* `$ showmount -e *headnode*` &mdash; where *headnode* is the name of your headnode.
This will show you the directories available for mounting from your headnode
* Open `/etc/fstab`
* Add the following lines:
<br/>`*headnode*:/home	/home	nfs	defaults	0	0` &mdash; where *headnode* is the headnode name
<br/>`*headnode*:/opt	/opt	nfs	defaults	0	0` &mdash; where *headnode* is the headnode name
* `$ mount -av` &mdash; this will mount everything `/etc/fstab` and tell you what it did
* You might have to log out and log back in to your golden node for this mounting to take effect
* Test if it worked by creating a file in your `/home` directory on your headnode and check if the file is there in `/home` directory on the Golden Node
* You will utilize your mounted `/opt` directory later with OpenMPI configuration

## 4. NTP

> The Golden Node will only synchronize its time with the headnode.

* `$ apt install ntp`
* Open the `/etc/ntp.conf` file
* Recall what you did with the file for your headnode.
Do the same except the following:
  * Instead of `server timehost.stolaf.edu` write `server *headnode*`
  * No need to add the lines about restricting access
* `$ systemctl start ntp`
* Use `ntpq -p` to check if its working

## 5. LDAP

> The purpose of LDAP here is the same.
> So the configuration process is the same as well.

* `$ apt install libnss-ldap libpam-ldap`
<br/>Either:
* Follow through the instructions and do exactly what you did for the headnode
* This essentially means that `/etc/ldap.conf`, `/etc/ldap/ldap.conf` and `/etc/nsswitch.conf` should be the same as the ones in your headnode
<br/>Or:
* You could use `scp` to copy the configuration files from the headnode:
  * `$ sudo scp *user*@*headnode*:/etc/ldap.conf /etc/ldap.conf`&mdash; where \*user\* and \*headnode\* are the username and address of your headnode. Assuming your hosts file is setup, you should be able to type `headnode` literally. 
  * Remember, you would have to run the scp command for each `/etc/ldap.conf`, `/etc/ldap/ldap.conf` and `/etc/nsswitch.conf`!
<br/>Then:
* Test it the same way you tested for the headnode (
Logout of your VM and try to log back in using your St. Olaf username and password.
If it works, then you did it right!)

## 6. OpenMPI

* Using the mounted `/opt` folder, the golden node can access all the files needed to run OpenMPI via NFS. So, all you have to do is run the same script you wrote earlier to link the binaries in `/opt/openmpi/bin/` to your local `/usr/bin/`
* `$ ./mpi_script.sh`

***
* Should we put this part somewhere else? -> Depends how we are implementing the other parts and if this page is the last page.
This is the end of the Cluster Manager Training!
Congratulations for making it!
Feel free to go through the training multiple times as that will give you a better understanding of how everything works.
There will be serveral other documents with information about other stuff you can learn.
All of them will build off of this basic training.
That means that you are mostly free to read through and work with those documents in any order you want.
Best of luck!
