# Installing Ubuntu
Before you start this training, make sure to have an image (ISO) of **Ubuntu 18.04 Server** downloaded and **VirtualBox** installed on your machine (see README).

This is the first training document.
This will take you through the steps for creating a virtual machine and configuring it properly.
Then it will guide you through the process of installing Ubuntu 18.04 Server on that virtual machine. 
The Server is a version of the operating system that comes with just a terminal/command line interface (CLI). 
It does not have a Graphical User Interface (GUI) where you can use the mouse and click on things. 
After that, there is a link to a website that can help you get familiar with the Linux Command Line environment.

Also, note that depending on how VirtualBox is configured on your computer, sometimes your mouse can become locked on your virtual machine, and you won't be able to move the mouse outside of it. Press the right Ctrl button to release the mouse if this happens and you need to use a program outside of the virtual machine.

## 1. Create a Virtual Machine (VM)

* Open VirtualBox and click on *New* to create a new VM
* Give it a name, set the type to *Linux* and version to *Ubuntu (64-bit)*
* Allocate some RAM (at least 1 GB) and create a *Virtual Hard Disk* (fixed size - at least 10 GB) for the VM
* After it's done, select the VM you just created and click on *Settings* &rarr; *Storage*
* Click on the optical disk under *Controller: IDE* and then click on the tiny optical disk icon to the right under *Attributes* and select your **Ubuntu 18.04 Server** ISO file
* Then go to *Network* (on the left pane) and attach *Adapter 1* to *Bridged Adapter*
  > This lets your VM access the internet. Refer to [this website](https://www.virtualbox.org/manual/ch06.html) to know more about this setting.
  > **If you're not on-campus,** set *Adapter 1* to *NAT* and enable the VPN on your host machine. 

* Go to the *Adapter 2* tab and enable it. Attach it to *Internal Network* and give it some name.
Click on the *Advanced* and set *Adapter Type* to "PCnet-FAST III" and *Promiscuous Mode* to "Allow All"
* This is the basic configuration we need right now. Feel free to play around with the settings as long as you know what you are doing!

## 2. Installing Ubuntu 18.04 Server

* Start your VM, it should load up the installer
* Note: Use the space bar for selecting.

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
* If prompted, install the `OpenSSH Server`.
* If a screen pops up with a list of things to install, you may disregard it and select *Done*
* Hit *Reboot Now* and press *Enter* when prompted to remove installation media
* After reboot finishes, you should be able to login with your *username* and *password*!

## 3. Command Line Basics

The rest of the tutorial consists of typing a lot of commands which will be written in the following manner:

`$ ls -lah`

Note that the `$` is not part of the command you type, and that the command line in your virtual machine also has a `$` at the end of the line. The `$` generally indicates that you're running commands without `root` privileges. When running commands as `root`, the command line will generally have a `#` symbol at the end of the command line. While some commands require you to become `root`, it's best to avoid using `root` unless necessary, as it becomes a lot easier to break important components of your system.   

To run a command as `root`, preface your command with the command `sudo`. To see this, first try running this command:

`$ apt update`

You'll see some errors that include messages like "Permission denied". This sort of error message indicates that you might need `root` privileges to execute the command. Sometimes programs will include a message asking "Are you `root`?" in their error messages if you need to run them as `root`, but sometimes you'll only see a generic "Permission denied". One remedy to this problem is to use the `sudo` command to run a command as `root`. Try typing the same command as earlier, but with `sudo` in front:

`$ sudo apt update`

If this is the first time you're using `sudo` in this session, you'll be prompted to enter your password. Note that neither the letters you type nor asterisk will actually appear in the terminal. Press enter once you've typed in your password, and you should see the command execute successfully.

One last trick: the following command executes your last run command, but as `root`. This can be helpful if you just typed a long command but forgot to put `sudo` in front. Try it like this:

`$ apt upgrade`  
*You'll see error messages here indicating your lack of permissions*  
`$ sudo !!`  


It is advised to not become `root` while going through this training, unless absolutely necessary.

---

## 4. Update and Install Some Packages

* `$ sudo apt update` and `$ sudo apt upgrade` &mdash; These two commands are used to update the versions of already installed packages on the machine
* `$ sudo apt install <package_name>` &mdash; used for installing new packages
* **Install the following packages**: `w3m`, `iptables`, `openssh-client`, `wget`, `nano`, `info`, `man-db`, `manpages`, `friendly-recovery`, `tmux`, `grub2`, `traceroute`, `bash-completion`, `command-not-found`, `dnsutils`, `lshw`, `lsof`, `mtr`, `psmisc`, `tcpdump`, `apt-transport-https`, `update-manager-core`, `gcc`, `g++`, `libtool`, `gedit`, `emacs`, `ssh`, `sshpass`, `xorg`, `make`
* If you are new to the Linux Command Line, you may find [this link](https://web.archive.org/web/20180104184520/http://linuxcommand.org/lc3_lts0010.php) useful to get familiar with the basic commands.
* `$ sudo shutdown now` and `$ sudo reboot now` are command line instructions to shutdown and restart your VM
* <kbd>Ctrl</kbd>+<kbd>Alt</kbd>+<kbd>F2</kbd> &mdash; Use this or any other key like <kbd>F3</kbd> or <kbd>F4</kbd> to open another terminal
* Restart your VM and proceed to the next section!  
  
  
[**PREVIOUS: Important Terms**](00_terms.md). 

[**NEXT: Configuring the Head Node**](02_configuring-the-headnode.md)
