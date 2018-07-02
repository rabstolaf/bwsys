# Applications

This document contains some applications that you should have on your headnode.
They will be useful in keeping a record of configuration changes and running parallel computing processes in your cluster.
First we install `etckeeper` for keeping track of the `/etc` directory.

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

## 3. Munge and Slurm

> Munge and Slurm are applications for job-scheduling for Linux and Unix Kernels. They are also useful for resource management. Go to [this link](https://kb.iu.edu/d/bdah) to read more about it.

* First we install `munge` &mdash; `$ apt install munge`
* Change permissions of folders:
  * `$ chmod 0700 /etc/munge/`
  * `$ chmod 0711 /var/lib/munge/`
  * `$ chmod 0700 /var/log/munge/`
* Make the key
  * `$ mkdir /var/run/munge/` &mdash; might exist already
  * `$ chown munge:munge /var/run/munge/`
  * `$ chmod 0755 /var/run/munge/`
  * `$ dd if=/dev/urandom bs=1 count=1024 > /etc/munge/munge.key` &mdash; you may need to become `root` for this one
  * $ `chown munge:munge /etc/munge/munge.key`
  * $ `chmod 600 /etc/munge/munge.key`
  * $ `chmod 755 /var/log/`
* `$ systemctl start munge`
* Now we install `slurm` &mdash; `$ apt install libpam-slurm libpmi0 libpmi0-dev libpmi2-0 libpmi2-0-dev`
* Copy the `slurm.conf` configuration file provided over to `/etc/slurm-llnl/slurm.conf` &mdash; make the directory if it does not exist
* You can test this after setting up the Golden Node

## 4. OpenMPI

> Message Passing Interface (MPI) is a standardized and portable message-passing standard designed by a group of researchers from academia and industry to function on a wide variety of parallel computing architectures.
> [This video](https://www.youtube.com/watch?v=D0-xSWBGNAw) gives a brief introduction on MPI and OpenMPI.

* `$ wget https://www.open-mpi.org/software/ompi/v1.6/downloads/openmpi-1.6.5.tar.gz`
* `$ tar -xzf openmpi-1.6.5.tar.gz` &mdash; extract it and go into the directory
* `$  ./configure --prefix=/usr/lib/openmpi-1.6.5` &mdash; this runs the `configure` script with the install location.
This step will take some time.
* `$ sudo make all install` &mdash; this will take a long time, be sure to use `sudo`
* `$ cd /usr/lib`
* `$ ln -s openmpi-1.6.5 openmpi`
* This step involves writing a script.
Refer to [the scripting tutorial](03_scripting.md) to know more about how to do this.
  * `update-alternatives --install "/usr/bin/${prog}" "$prog" "/usr/lib/openmpi/bin/${prog}" 1` &mdash; this command should run for every `prog` in the following list:
`ompi-checkpoint` `ompi-clean` `ompi_info` `ompi-ps` `ompi-restart` `ompi-server` `opal_wrapper` `ortec++` `orted` `orte-ps` `orterun` `ortecc` `orte-clean` `orte-iof` `ompi-clean` `ompi_info` `ompi-iof` `ompi-ps` `ompi-server` `mpic++` `mpicc` `mpiCC` `mpicxx` `mpiexec` `mpif77` `mpiru`
  * Change the permissions of the script file and execute it with the programs listed above as argument
* You can test this step when setting up the Golden Node

## 5. Netbooting
