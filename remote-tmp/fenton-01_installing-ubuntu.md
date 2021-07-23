# Installing Ubuntu

This is the first training document.
This will take you through the steps for creating a virtual machine and configuring it properly.
Then it will guide you through the process of installing Ubuntu 18.04 Server on that virtual machine.
After that, there is a link to a website that can help you get familiar with the Linux Command Line environment.

Before you start this training, make sure to have an image (ISO) of **Ubuntu 18.04 Server** downloaded and **VirtualBox** installed on your machine (see README).

Also, note that depending on how VirtualBox is configured on your computer, sometimes your mouse can become locked into your virtual machine, and you won't be able to move the mouse outside of it. Press the right <kbd>Ctrl</kbd> button to release the mouse if this happens and you need to use a program outside of the virtual machine.

## 1. Create a Virtual Machine (VM)

* Open VirtualBox and click on *New* to create a new VM
* Give it a name, set the type to *Linux* and version to *Ubuntu (64-bit)*
* Allocate some RAM (at least 1 GB) and create a *Virtual Hard Disk* (fixed size - at least 10 GB) for the VM
* After it's done, select the VM you just created and click on *Settings* &rarr; *Storage*
* Click on the optical disk under *Controller: IDE* and then click on the tiny optical disk icon to the right under *Attributes* and select your **Ubuntu 18.04 Server** ISO file
* Then go to *Network* (on the left pane) and attach *Adapter 1* to *Bridged Adapter*
  > This lets your VM access the internet. Refer to [this website](https://www.virtualbox.org/manual/ch06.html) to know more about this setting.
<!-- removed globalprotect and copied to main document
  > If you're not on-campus and want to skip installing the GlobalProtect VPN on your VM, you can set *Adapter 1* to *NAT* and enable the VPN on your host machine. Then, simply disregard later instructions about installing and configuring GlobalProtect. -->


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
* If prompted, install the `OpenSSH Server` by pressing the space button to check the box and enter to proceed.
* If a screen pops up with a list of things to install, you may disregard it and select *Done*
* Hit *Reboot Now* and press *Enter* when prompted to remove installation media
* After reboot finishes, you should be able to login with your *username* and *password*!

---

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

* `$ apt update` and `$ apt upgrade` &mdash; These two commands are used to update the versions of already installed packages on the machine. Run them now, if you didn't earlier.
* `$ apt install <package_name>` &mdash; used for installing new packages
* **Install the following packages**: `w3m`, `iptables`, `openssh-client`, `wget`, `nano`, `info`, `man-db`, `manpages`, `friendly-recovery`, `tmux`, `grub2`, `traceroute`, `bash-completion`, `command-not-found`, `dnsutils`, `lshw`, `lsof`, `mtr`, `psmisc`, `tcpdump`, `apt-transport-https`, `update-manager-core`, `gcc`, `g++`, `libtool`, `gedit`, `emacs`, `ssh`, `sshpass`, `xorg`, `make`
* If you are new to the Linux Command Line, you may find [this link](https://web.archive.org/web/20180104184520/http://linuxcommand.org/lc3_lts0010.php) useful to get familiar with the basic commands.

---

## 5. Installing a Desktop Environment

By default, the Ubuntu server we've just installed can *only* be interacted with through the command line. It's important to be comfortable with typing commands and learning about a computer with just a command line, but it can be slightly intimidating at first. Installing a desktop environment can make your virtual machine look a bit more familiar, but we'll also need to install one to use a program to connect St. Olaf's network using a VPN. 

* Begin by installing the tasksel utility
* `$ sudo apt install tasksel`
* There are a lot of different desktop environments you can install. [This link](https://phoenixnap.com/kb/how-to-install-a-gui-on-ubuntu) has a few listed with some pictures and descriptions.
* If you're not sure which to install, Mate is a good balance between performance and aesthetics. Install it with this command, or replace the "ubuntu-mate-core" with a different one if you'd rather something else.
* `$ sudo tasksel install ubuntu-mate-core`
* The desktop environment will take a bit to install, and you'll also notice your VM will take a bit longer to boot up now.
* Since we just installed the desktop environment, the display manager isn't running yet. Start it with
* `$ sudo service lightdm restart`
* You'll now see a login screen, and once you sign in, you'll load into the desktop environment.
* `$ sudo shutdown now` and `$ sudo reboot now` are command line instructions to shutdown and restart your VM
* <kbd>Ctrl</kbd>+<kbd>Alt</kbd>+<kbd>F2</kbd> &mdash; Use this or any other key like <kbd>F3</kbd> or <kbd>F4</kbd> to open another terminal. If you want to go back to the graphical environment, restart the display manager service again.
* Now, whenever you reboot your VM, the display manager will automatically start and your desktop environment will load. Sometimes, it may take a few seconds and you'll see a command line asking you to sign in, but just wait a bit (~10 seconds) and your desktop environment should boot up.

---

## 6. Increasing VM Performance

Depending on your VirtualBox settings, your VM might feel slower now that you have a desktop environment installed, as they tend to use more system resources (memory, processing power). One way to increase the responsiveness of your VM is to allot it more RAM (Random Access Memory), as RAM is used to run programs. More memory means your computer can run more programs, faster.

* First, check how much RAM your laptop or desktop has available. This varies depending on whether you're using Windows or MacOS.
* In Windows, you can type "View RAM info" into the search bar and open the program. Look next to "Installed RAM" to see your available RAM. 
* In MacOS, click the Apple icon in the top-left hand corner of your screen, and select "About This Mac." To see your available RAM, look next to "Memory"
* You'll likely have either 4GB or 8GB of RAM installed on your computer. It's generally a good idea to never allot more than half of your available RAM to a virtual machine, especially when you're running a few VM's. As long as you have at least 4GB of RAM, you can allot your VM 2GB, which should give you a noticeable performance boost.
* To increase the RAM of a VM, first open the VirtualBox Manager program (where you stop and start your VM)
* Make sure your headnode VM is powered off, then select your VM and click the orange gear labeled "Settings"
* In the Setting dialog box, click the System tab on  the left. Then, use either the slider or input box to increase the "Base Memory" (RAM) the VM can use. Note that VirtualBox is expecting a number in MB rather than GB. Multiple GB by 1024 to convert to MB.

|GB|MB|
|---|---|
|1|1024|
|2|2048|
|4|4096|

* You can also increase settings such as processors or video memory, depending on what hardware your laptop or desktop computer has installed. See if increasing the memory helps, and if your VM is still laggy, try increasing some of those other settings. Be sure to research what your specific computer has installed so you know you're not overworking your host computer.

## 7. Using a Desktop Environment

Depending on which desktop environment you installed, you'll have different utilities and programs available. All environments will have some way to type commands, usually in a program called a Terminal. In Mate, the program is called MATE Terminal, in the System Tools menu, accessed from the top left of your VM screen. You can pin this program to your Desktop by right clicking it.  

Look through the rest of the menu and open up a few programs that seem interesting. As you add more programs to your VM, you'll notice some of them will begin to appear in that menu, and you can always add shortcuts on your desktop to get to them faster. 

There are a few helpful tools under the Preferences section of the menu. One of them is Displays, which allows you to change the size of your VM window, using the Resolution setting.

Two others are MATE Tweak and Appearance, which are also under the Preferences menu. MATE Tweak has a few sections, but the most interesting one is in the Panel section. Here, you can change the panel layout to change how programs appear on your VM. Try "Cupertino" or "Pantheon" if you're used to MacOS, or "Redmond" if you want something that reminds you of Windows. None of these change the functionality of your VM; they're all just visual changes. Choose one that feels comfortable to you, and customize it to your liking.

Appearance allows you to change the default color scheme of windows, the desktop background, fonts, and other miscellaneous interface options.  

## 8. VirtualBox Guest Additions (optional)

Although you now have a way to graphically interact with your VM, you still can't do things like copy/paste or drag files into your VM from your host computer. To access these features, you'll need to install VirtualBox Guest Additions on your VM. If you want these features, follow the instructions below, but know that this step is entirely optional. You can go to the next training document whenever you want and still complete the entire training.

* At the top of your VM window (but not inside your actual VM), you should see a few menus: File, Machine, View, Input, Devices, and Help. Hover over Devices, and then click "Insert Guest Additions CD image..."
* A dialog box should open in your VM, asking if you want to run the software. Click the Ok button, and then the Run button to install it, entering your password when prompted.
* Once Guest Additions has installed successfully, go back to the Devices menu, and switch the "Shared Clipboard" and "Drag and Drop" settings to "Bidirectional"
* Reboot your VM, and you should now be able to drag files from your VM to your Host, and your clipboard (copy/paste) should be shared between both machines. 
* To paste into a terminal, use <KBD>Ctrl</KBD><KBD>Shift</KBD><KBD>V</KBD>. You can also right click and select either copy or paste.
* Now that Guest Additions is installed, you can also set the VM to automatically resize the display based on the size of the VM window. Find this setting under the View menu.
* You can also eject the (virtual) Guest Additions CD with the `eject` command
* `$ eject`


* Congratulations! You now have a working virtual machine installed on your computer. You can proceed to the next section.
