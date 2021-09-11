[**PREVIOUS: Important Terms**](00_terms.md) 

# Installing Ubuntu
Make sure to have an image (ISO) of **Ubuntu 18.04 Server** downloaded and **VirtualBox** installed on your machine. See how to do this on [thing README](README.md) if you haven't done that already.

This will take you through the steps for creating a virtual machine and configuring it properly.
Then it will guide you through the process of installing the Operating System called Ubuntu 18.04 Server on that virtual machine. 
The Server is a version of the operating system that comes with just a terminal/command line interface (CLI). 
It does not have a Graphical User Interface (GUI) where you can use the mouse and click on things. 

Also, note that depending on how VirtualBox is configured on your computer, sometimes your mouse can become locked on your virtual machine, and you won't be able to move the mouse outside of it. 

## 1. Create a Virtual Machine (VM)

* Open VirtualBox and click on *New* to create a new VM
* Give it a name, set the type to *Linux* and version to *Ubuntu (64-bit)*
* Allocate some RAM (Random Access Memory) (at least 1 GB) and create a *Virtual Hard Disk* (fixed size - at least 10 GB) for the VM
* After it's done, select the VM you just created and click on *Settings* &rarr; *Storage*
* Click on the optical disk under *Controller: IDE* and then click on the tiny optical disk icon to the right under *Attributes* and select your **Ubuntu 18.04 Server** ISO file that you downloaded at the beginning.
* Save your new settings
* Then go to *Network* (on the left pane) and attach *Adapter 1* to *Bridged Adapter*
  > This lets your VM access the internet. Refer to [this website](https://www.virtualbox.org/manual/ch06.html) if you want to know more about this setting.
  > **If you're not on-campus,** set *Adapter 1* to *NAT* and enable the VPN on your laptop/computer. 

* Go to the *Adapter 2* tab and enable it. Attach it to *Internal Network* and give it some name.
Click on the *Advanced* and set *Adapter Type* to "PCnet-FAST III" and *Promiscuous Mode* to "Allow All"
* This is the basic configuration we need right now. Feel free to play around with the settings as long as you know what you are doing!

## 2. Installing Ubuntu 18.04 Server

* Start your VM, it should load up the installer which will install the operating system.
* Note: Use the space bar for selecting.

* Choose your language and keyboard layout and choose *Install Ubuntu*
* The next screen shows your network interface along with your IPv4 and IPv6 configurations, along with the protocol it will use to set the IP addresses. Hit *Done*
* Leave the proxy address field blank and proceed

* For disk partitioning, choose *Use An Entire Disk*
* Select *Continue* when it requests confirmation for formatting the partitions
* Next, fill in the fields appropriately. Use your St. Olaf *username* but NOT your St. Olaf *password*. You will need to remember both.

Hit *Done* and wait for installation to finish

* If prompted, install the `OpenSSH Server`.
* If a screen pops up with a list of things to install, you may disregard it and select *Done* at the bottom of the screen
* Hit *Reboot Now*. This will take a couple of minutes
* Press *Enter* when prompted to remove installation media
* After reboot finishes, you should be able to login with your St. Olaf *username* and non-St. Olaf *password*!

## 3. Command Line Basics

The rest of the tutorial consists of typing a lot of commands which will be written in the following manner:

* In general, if you do not know how a certain command such as `grep` works, there are two ways to find out. 

1. You can use the `--help` flag to see how to use it.
For example, type
> $ grep --help

2. Use the `man` command to see the manual
For example, 
> $ man grep 

On the terminal, type in:
`$ ls -lah`

This command `ls` will list items in the current directory.

* Note that the `$` is not part of the command you type.  
* The command line in your virtual machine also has a `$` at the end of the line.  
* The `$` generally indicates that you're running commands without `root` privileges.  
* That means you have restricted access and authority to do things on the Virtual Machine.  
* Running commands as root means you have unlimited privileges on the virtual machine.  
* To run a command as `root`, preface your command with the command `sudo`.  

While some commands require you to become `root`, it's best to avoid using `root` unless necessary, as it becomes a lot easier to break important components of your system.   

Now, on the terminal, type the following and press Enter:
`apt update`

The command will give you an error. 
This is because that command requires you to become root. 

In order to run that successfully, type the following instead:
`$ sudo apt update`

If this is the first time you're using `sudo` in this session, you'll be prompted to enter your password. Note that neither the letters you type nor asterisk will actually appear in the terminal. Press enter once you've typed in your password, and you should see the command execute successfully.

It is advised to not become `root` while going through this training, unless absolutely necessary.

---

## 4. Update and Install Some Packages

* `$ sudo apt update` and `$ sudo apt upgrade` &mdash; These two commands are used to update the versions of already installed packages on the machine
* `$ sudo apt install <package_name>` &mdash; used for installing new packages

* apt also known as APT(Advanced Packaging Tool) is a tool that is used by linux computers for installing new software packages and upgrading existing ones among other things.

> You can install multiple packages using apt in the following way:
`sudo apt install package1 package2 package3`

* **Install the following packages**: `w3m iptables openssh-client wget nano info man-db manpages friendly-recovery tmux grub2 traceroute bash-completion command-not-found dnsutils lshw lsof mtr psmisc tcpdump apt-transport-https update-manager-core gcc g++ libtool gedit emacs ssh sshpass xorg make`

* If you are new to the Linux Command Line, you may find [this link](https://web.archive.org/web/20180104184520/http://linuxcommand.org/lc3_lts0010.php) useful to get familiar with the basic commands.
* `$ sudo shutdown now` and `$ sudo reboot now` are command line instructions to shutdown and restart your VM
* <kbd>Ctrl</kbd>+<kbd>Alt</kbd>+<kbd>F2</kbd> &mdash; Use this or any other key like <kbd>F3</kbd> or <kbd>F4</kbd> to open another terminal
* Restart your VM and proceed to the next section!  
  
  
[**NEXT: Configuring the Head Node**](02_configuring-the-headnode.md)
