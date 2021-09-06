[**PREVIOUS: Scripting basics**](03_scripting.md)

# Applications

This document contains some applications that you should have on your headnode.
They will be useful in keeping a record of configuration changes and running parallel computing processes in your cluster.
<!-- * First, we install apt-cacher to create a local ubuntu mirror on our headnode. -->
* First, we install and configure OpenMPI.
* Finally, we download the netbooting image for Ubuntu and configure our headnode to allow the golden node to netboot from it.

<!-- ## 1. Install etckeeper

> `etckeeper` is a version control system for the `/etc` folder.
> It automatically commits the changes you make to the `/etc` folder.

* `$ apt install etckeeper`


## 1. Apt-Cacher

> Apt-Cacher (pronounced: *apt casher* ) is a software to cache the applications in your headnode. 
> A cache is a piece of software that is used for storing data (in our case our apt installations), so that it can be used and served much faster in the future.
> As a result, it acts like an ubuntu mirror for the other nodes.
> Here is a [link](https://www.unix-ag.uni-kl.de/~bloch/acng/html/index.html) to the `apt-cacher-ng` user manual.

* `$ sudo apt install apt-cacher-ng`
* `$ echo 'Acquire::http { Proxy "http://localhost:3142"; };' | sudo tee /etc/apt/apt.conf.d/01proxy` &mdash; this sets port 3142 of your headnode as the proxy
  * A proxy is a machine that acts as a filter and a firewall to provide shared network connections and cache data to speed up common requests.
* `$ sudo apt update` &mdash; update your repository before caching
* Import the apt cache
  * `$ sudo cp -laf /var/cache/apt/archives /var/cache/apt-cacher-ng/_import`
  * `$ sudo chown apt-cacher-ng /var/cache/apt-cacher-ng/_import`
* Import the repositories
  * Replace "IP" with your headnode **IP that is connected to wifi** in this link and visit this link: `http://ip:3142/acng-report.html`
    * To find that use `ip ad` and find the IP that is **neither** 127.0.0.1 **nor** 10.0.0.254.
  * At the bottom, click `Start Import` and wait for it to finish
* Done!
-->

## 1. OpenMPI
> Now, this is where you actually get to use the cluster! Open MPI is a library that allows us to run job(s) (programs, written in C or C++) on multiple computers, in this case, on your cluster of Virtual Machines.
> Open MPI is the open source version of Message Passing Interface (MPI). It is a standardized and portable message-passing implementation of MPI designed by a group of researchers from academia and industry to function on a wide variety of parallel computing architectures.
> [This video](https://www.youtube.com/watch?v=D0-xSWBGNAw) gives a brief introduction on MPI and OpenMPI.
### Installing Open MPI
We will use `wget` to download the Open MPI. `wget` is a program that retrieves content from web pages. Here, we are retrieving a tarball from open-mpi.org.
* `$ wget https://download.open-mpi.org/release/open-mpi/v4.1/openmpi-4.1.1.tar.gz`
  * `wget` is a tool for downloading files from online that aren't readily available in repositories (e.g. apt or git). That means that you need to know the URL to download from beforehand (download links can usually be copied by right-clicking and selecting copy-link. That is very useful when using ssh connections that allow for copying and pasting to and from a terminal connected to a different user).
  * For more info on ssh view [this](https://www.youtube.com/watch?v=z7jVOenqFYk)
Next, we unzip the compressed archive file that we just downloaded. The file's extension or file type `.tar.gz` reflects two things:
1. The `tar` command was used to collect multiple files which were combined into one archive file which is often referred to as a tarball, allowing for compression of files and more efficient data storage and transfer. [The `tar` command](https://linux.die.net/man/1/tar) can also be used to send files over the internet.
2. The `gz` extension which is short for GNU Zip shows how the the archive file was compressed using the [`gzip` program](https://linux.die.net/man/1/gzip). File compression is the method computers use to make files smaller by reducing the number of bits (1's and 0's) used to store the information.

In the following commmand, the `-zxf` option is a combination of two options. `-x` tells `tar` to extract the files from the tarball, `-f` is a flag representing that the next item in the command is the target (in our case the tarball) the `tar` command is to be carried out on and `-z` makes `tar` direct the tarball through gzip. 
* Run: `$ tar -zxf openmpi-4.1.1.tar.gz` &mdash; extract it and go into the directory.
* Run: `$ sudo ./configure --prefix=/usr/lib/openmpi-4.1.1` &mdash; this runs the `configure` script with the install location.
  > This step will take some time.
* Run: `$ sudo make all install` &mdash; this will take a long time, be sure to use `sudo`, or you'll need to spend double the time on the command.
  * `make` is a utility that serves for the compilation of large programs that have multiple prerequesites. With `sudo make all install` you run `make all` and `make install` together.
* Run: `$ cd /usr/lib`
* Run: `$ sudo ln -s openmpi-4.1.1 openmpi`

* We want to provide alternatives to the default installations of openmpi, by using the ones located in the `/usr/lib/openmpi-4.1.1/bin`. To list all of them, run:
  * `ls /usr/lib/openmpi-4.1.1/bin`
  * The output should be: `aggregate_profile.pl  mpic++  mpicc  mpiCC  mpicxx  mpiexec  mpif77  mpif90  mpifort  mpirun  ompi-clean  ompi_info  ompi-server  opal_wrapper  ortecc  orte-clean  orted  orte-info  orterun  orte-server  profile2mat.pl`
* For this, we need a for-loop in a bash script to run the following command repeatedly (`$prog` is a variable that represents each of the programs listed above): 
  * `sudo update-alternatives --install "/usr/bin/${prog}" "$prog" "/usr/lib/openmpi-4.1.1/bin/${prog}" 1` &mdash; this command should run for every `$prog` in the following list (Look at the For Statements section in [03_scripting](03_scripting.md) for more info)
  * Change the permissions of the script file and execute it (Look at the Basics section in [03_scripting](03_scripting.md) for more info)

* We are now going to copy the openmpi files to the /opt directory which we made accessible to the worker nodes.
* `$ sudo mkdir -p /opt/openmpi-4.1.1/bin`  - This creates the /opt/openmpi-4.1.1/bin directory
* `$ sudo cp -r /usr/lib/openmpi-4.1.1/bin /opt/openmpi-4.1.1`  - This copies the files over

* You can test this step on the Golden Node after setting up NFS. You should have the same files in /opt/openmpi-4.1.1/bin that are on the head node on the worker node as well.

## 2. Netbooting
> Netbooting will allow the Golden Node to boot using the network. The headnode will provide the installation file for the operating system.
> [This link](https://www.howtogeek.com/57601/what-is-network-booting-pxe-and-how-can-you-use-it/) can help you understand this concept in greater detail.

* `$ sudo apt install syslinux tftpd-hpa` &mdash; install the necessary packages
* `$ wget http://archive.ubuntu.com/ubuntu/dists/bionic/main/installer-amd64/current/images/netboot/netboot.tar.gz` &mdash; this is the netboot installer for Ubuntu 18.04
* `$ sudo tar -xzf netboot.tar.gz -C /var/lib/tftpboot/` &mdash; unpack the tar to this location
* Add the following lines to `/var/lib/tftpboot/pxelinux.cfg/default` to control netbooting:
<br/>`ONTIMEOUT localboot`
<br/>`LABEL localboot`
<br/>`MENU LABEL Boot From Hard Disk`
<br/>`LOCALBOOT  0`
* Save and exit
* `$ sudo chmod 755 /var/lib/tftpboot/ubuntu-installer/amd64/*` &mdash; to let other machines access the installer

This concludes what we have to do on the headnode (for now)! Now we move on to another physical machine (or another virtual machine) that will be our Golden Node!

[**NEXT: Golden Node Setup**](05_golden-node-setup.md)
