# Applications

This document contains some applications that you should have on your headnode.
They will be useful in keeping a record of configuration changes and running parallel computing processes in your cluster.
First we install `etckeeper` for keeping track of the `/etc` directory.
After that we install apt-cacher to create a local ubuntu mirror on our headnode.
Next we install and configure OpenMPI.
Finally, we download the netbooting image for Ubuntu and configure our headnode to allow the golden node to netboot from it.

## 1. Install etckeeper

> `etckeeper` is a version control system for the `/etc` folder.
> It automatically commits the changes you make to the `/etc` folder.

* `$ apt install etckeeper`

## 2. Apt-Cacher

> Apt-Cacher is a software to cache the applications in your headnode.
> As a result, it acts like an ubuntu mirror for the other nodes.
> Here is a [link](https://www.unix-ag.uni-kl.de/~bloch/acng/html/index.html) to the `apt-cacher-ng` user manual.

* `$ apt install apt-cacher-ng`
* `$ echo 'Acquire::http { Proxy "http://localhost:3142"; };' | sudo tee /etc/apt/apt.conf.d/01proxy` &mdash; this sets port 3142 of your headnode as the proxy
* `$ apt update` &mdash; update your repository before caching
* Import the apt cache
  * `$ cp -laf /var/cache/apt/archives /var/cache/apt-cacher-ng/_import`
  * `$ chown apt-cacher-ng /var/cache/apt-cacher-ng/_import`
* Import the repositories
  * Replace "IP" with your headnode IP in this link and visit this link: `http://ip:3142/acng-report.html`
  * At the bottom, click `Start Import` and wait for it to finish
* Done!

## 3. OpenMPI

> Message Passing Interface (MPI) is a standardized and portable message-passing standard designed by a group of researchers from academia and industry to function on a wide variety of parallel computing architectures.
> [This video](https://www.youtube.com/watch?v=D0-xSWBGNAw) gives a brief introduction on MPI and OpenMPI.

* `$ wget https://download.open-mpi.org/release/open-mpi/v4.1/openmpi-4.1.1.tar.gz`
* `$ tar -zxf openmpi-4.1.1.tar.gz` &mdash; extract it and go into the directory
* `$  ./configure --prefix=/usr/lib/openmpi-4.1.1` &mdash; this runs the `configure` script with the install location.
This step will take some time.
* `$ sudo make all install` &mdash; this will take a long time, be sure to use `sudo`
* `$ cd /usr/lib`
* `$ ln -s openmpi-4.1.1 openmpi`
* This step involves writing a script.
Refer to [the scripting tutorial](03_scripting.md) to know more about how to do this.
  * `update-alternatives --install "/usr/bin/${prog}" "$prog" "/usr/lib/openmpi/bin/${prog}" 1` &mdash; this command should run for every `prog` in the following list:
`ompi-checkpoint` `ompi-clean` `ompi_info` `ompi-ps` `ompi-restart` `ompi-server` `opal_wrapper` `ortec++` `orted` `orte-ps` `orterun` `ortecc` `orte-clean` `orte-iof` `ompi-clean` `ompi_info` `ompi-iof` `ompi-ps` `ompi-server` `mpic++` `mpicc` `mpiCC` `mpicxx` `mpiexec` `mpif77` `mpirun`
  * Change the permissions of the script file and execute it with the programs listed above as argument
* You can test this step when setting up the Golden Node

## 4. Netbooting

> Netbooting will allow the Golden Node to boot using the network. The headnode will provide the installation file for the operating system.
> [This link](https://www.howtogeek.com/57601/what-is-network-booting-pxe-and-how-can-you-use-it/) can help you understand this concept in greater detail.

* `$ apt install syslinux tftpd-hpa` &mdash; install the necessary packages
* `$ wget http://archive.ubuntu.com/ubuntu/dists/bionic/main/installer-amd64/current/images/netboot/netboot.tar.gz` &mdash; this is the netboot installer for Ubuntu 18.04
* `$ tar -xzf netboot.tar.gz -C /var/lib/tftpboot/` &mdash; unpack the tar to this location
* Add the following lines to `/var/lib/tftpboot/pxelinux.cfg/default` to control netbooting:
<br/>`ONTIMEOUT localboot`
<br/>`LABEL localboot`
<br/>`MENU LABEL Boot From Hard Disk`
<br/>`LOCALBOOT  0`
* Save and exit
* `$ chmod 755 /var/lib/tftpboot/ubuntu-installer/amd64/*` &mdash; to let other machines access the installer

This concludes what we have to do on the headnode (for now)! Now we move on to another physical machine (or another virtual machine) that will be our Golden Node!
