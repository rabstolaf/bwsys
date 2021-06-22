# Installing Ubuntu

This is the first training document.
This will take you through the steps for creating a virtual machine and configuring it properly.
Then it will guide you through the process of installing Ubuntu 18.04 Server on that virtual machine.
After that, there is a link to a website that can help you get familiar with the Linux Command Line environment.

Before you start this training, make sure to have an image (ISO) of **Ubuntu 18.04 Server** downloaded and **VirtualBox** installed on your machine (see README).

## 1. Create a Virtual Machine (VM)

* Open VirtualBox and click on *New* to create a new VM
* Give it a name, set the type to *Linux* and version to *Ubuntu (64-bit)*
* Allocate some RAM (atleast 1 GB) and create a *Virtual Hard Disk* (fixed size - atleast 10 GB) for the VM
* After it's done, select the VM you just created and click on *Settings* &rarr; *Storage*
* Click on the optical disk under *Controller: IDE* and then click on the tiny optical disk icon to the right under *Attributes* and select your **Ubuntu 18.04 Server** ISO file
* Then go to *Network* (on the left pane) and attach *Adapter 1* to *Bridged Adapter*
  > This lets your VM access the internet. Refer to [this website](https://www.virtualbox.org/manual/ch06.html) to know more about this setting.

* Go to the *Adapter 2* tab and enable it. Attach it to *Internal Network* and give it some name.
Click on the *Advanced* and set *Adapter Type* to "PCnet-FAST III" and *Promiscuous Mode* to "Allow All"
* This is the basic configuration we need right now. Feel free to play around with the settings as long as you know what you are doing!

## 2. Installing Ubuntu 18.04 Server

* Start your VM, it should load up the installer
* Choose your language and keyboard layout and choose *Install Ubuntu*
* The next screen shows your network interface along with your IPv4 and IPv6 configurations, along with the protocol it will use to set the IP addresses. Hit *Done*
* Leave the proxy address field blank
  > A proxy server is basically another computer which serves as a hub through which internet requests are processed. 
  > It may act as a firewall or can be used to anonymously surf the web.

* For disk partitioning, choose *Use An Entire Disk*, unless you know how to manually partition.
In that case, partition it logically keeping enough space for `/` and `/home` directories.
* Select *Continue* when it requests confirmation for formatting the partitions
* Fill in the fields appropriately. You will need to remember your *username* and *password*.
It is recommended to use your St. Olaf *username* but not your St. Olaf *password*.
Hit *Done* and wait for installation to finish
* If prompted, install the `OpenSSH Server`
* If a screen pops up with a list of things to install, you may disregard it and select *Done*
* Hit *Reboot Now* and press *Enter* when prompted to remove installation media
* After reboot finishes, you should be able to login with your *username* and *password*!

---

The rest of the tutorial consists of typing a lot of commands which will be written in the following manner:

`$ ls -lah`

Some commands would require more permissions to run them, use `sudo` with the command in that case.
It is advised to not become `root` while going through this training, unless absolutely necessary.

---

## 3. Update and Install Some Packages

* `$ apt update` and `$ apt upgrade` &mdash; These two commands are used to update the versions of already installed packages on the machine
* `$ apt install <package_name>` &mdash; used for installing new packages
* **Install the following packages**: `w3m`, `iptables`, `openssh-client`, `wget`, `nano`, `info`, `man-db`, `manpages`, `friendly-recovery`, `tmux`, `grub2`, `traceroute`, `bash-completion`, `command-not-found`, `dnsutils`, `lshw`, `lsof`, `mtr`, `psmisc`, `tcpdump`, `apt-transport-https`, `update-manager-core`, `gcc`, `g++`, `libtool`, `gedit`, `emacs`, `ssh`, `sshpass`, `xorg`, `make`
* If you are new to the Linux Command Line, you may find [this link](https://web.archive.org/web/20180104184520/http://linuxcommand.org/lc3_lts0010.php) useful to get familiar with the basic commands.
* `$ sudo shutdown now` and `$ sudo reboot now` are command line instructions to shutdown and restart your VM
* <kbd>Ctrl</kbd>+<kbd>Alt</kbd>+<kbd>F2</kbd> &mdash; Use this or any other key like <kbd>F3</kbd> or <kbd>F4</kbd> to open another terminal
* Restart your VM and proceed to the next section!
