---
title: "Nagios Network Monitoring"
description: "Build Documentatiob for a Nagios Install"
---

## Build Documentation
Open NMon01. This is primarily where we’re gonna host everything.
First, we need the prerequisites. We need Apache, PHP, and some libraries. Type yum install -y httpd httpd-tools php gcc glibc glibc-common gd gd-devel make net-snmp to get all you need.

Next, create a Nagios user and group. Do this by typing useradd nagios, and then groupadd nagcmd. Add the Nagios and Apache users to the group. Afterwards, we download the Nagios Core and the Nagios Plugin. We first have to make a directory: /root/nagios. Change to that, and we can begin the process. Use wget on the following urls: 

https://assets.nagios.com/downloads/nagioscore/releases/nagios-4.4.5.tar.gz

https://nagios-plugins.org/download/nagios-plugins-2.2.1.tar.gz 

Next, extract them, with tar -xvf nagios-4.4.5.tar.gz, and the same for nagios-plugins-2.2.1.tar.gz.

Time to configure the core. Change directory to nagios-4.4.5/ and configure with the command ./configure --with-command-group=nagcmd. Compile it with make all and install the libraries into the machine with make install. Install the init scripts with make install-init. We also really want Nagios to work with the command line, so make install-command mode to get that. We should get some sample files as well, so make install-config.

Now we gotta customize the configuration. Open contacts.cfg with your preferred text editor. It should be under /usr/local/nagios/etc/objects/contacts.cfg. Set the email address to one you use, to get alerts about the servers.

Ready to get funky with web interfaces? In the Nagios 4.4.5 directory, make install-webconf to configure the web interface for Nagios. The nagiosadmin user is already created, but it needs a password. Type htpasswd -s -c /usr/local/nagios/etc/htpasswd.users nagiosadmin. Restart Apache to make them take effect.
Next we have to compile and install the plugin. Go to /root/nagios and change into the nagios-plugins-2.2.1/ plugin directory from there. All you have to do is configure using ./configure --with-nagios-user=nagios --with-nagios-group=nagios and make, then make install. Go back up to the Nagios directory and type /usr/local/nagios/bin/nagios -v /usr/local/nagios/etc/nagios.cfg to verify the files.

![image](https://user-images.githubusercontent.com/70775749/108377551-28696a00-71d2-11eb-87fc-dc39d1b8edc6.png)

We also have to make sure Nagios works across reboots. In root, enable nagios and httpd, and then start nagios.service.

### Installing Nagios Client ++ on Windows
Next, please connect to your nagios core by entering the Ip address you assigned earlier. At this step it will ask you to enter in your username and password. Unless changed your username should be: nagiosadmin And the password should have been set in an early step.

![image](https://user-images.githubusercontent.com/70775749/108377785-5fd81680-71d2-11eb-822d-e6ecd861f59e.png)

This is what it will look like when you first log in.

![image](https://user-images.githubusercontent.com/70775749/108377865-73837d00-71d2-11eb-97c1-c8876191d91c.png)

Now that we know the webclient it working, we will need to install NSClient++
To do so please go to this link:  https://sourceforge.net/projects/nscplus/files/latest/download/

Steps on going the the install are below:

![image](https://user-images.githubusercontent.com/70775749/108378014-9f9efe00-71d2-11eb-839b-77ee95771aa0.png)

![image](https://user-images.githubusercontent.com/70775749/108378068-ad548380-71d2-11eb-8003-5950ecce10ab.png)

Make sure that you enter in the IP address of the nagios core server.

![image](https://user-images.githubusercontent.com/70775749/108378184-c6f5cb00-71d2-11eb-9e0b-06ef0386f63b.png)

![image](https://user-images.githubusercontent.com/70775749/108378245-d412ba00-71d2-11eb-98b7-2ddc26cdc338.png)


Once the installation is complete please start the service:

![image](https://user-images.githubusercontent.com/70775749/108378318-e260d600-71d2-11eb-9199-23b4b0ebdd19.png)

Let's make sure that the service is running now by going into the Services and locating NSClient++

![image](https://user-images.githubusercontent.com/70775749/108378397-f4db0f80-71d2-11eb-89eb-ce16f25c8835.png)

From here go to properties -> Log On and select “Allow service to interact with desktop”

![image](https://user-images.githubusercontent.com/70775749/108378462-045a5880-71d3-11eb-8d55-408181d2ae66.png)

Restart the service

Now we need to add the host to the Nagios Core server.

First lets make a backup

To get here got to /usr/local/nagios/etc/objects/

![image](https://user-images.githubusercontent.com/70775749/108378557-1b994600-71d3-11eb-9b67-647b6db29b36.png)

_Sudo nano windows.cfg_

![image](https://user-images.githubusercontent.com/70775749/108378652-3370ca00-71d3-11eb-933d-5344819ac966.png)


Add the host exactly like it is below

![image](https://user-images.githubusercontent.com/70775749/108378719-42577c80-71d3-11eb-8066-06e1019ed42e.png)

**Note: If you would like to check services on this device under host name you will need to add the host**

![image](https://user-images.githubusercontent.com/70775749/108378796-5307f280-71d3-11eb-969f-081c5688d333.png)

![image](https://user-images.githubusercontent.com/70775749/108378862-61560e80-71d3-11eb-94fb-ead116dcb899.png)

Next let's make sure that everything is okay by verifying the files again. Remember the command? If not here it is again: /usr/local/nagios/bin/nagios -v /usr/local/nagios/etc/nagios.cfg

![image](https://user-images.githubusercontent.com/70775749/108378920-6d41d080-71d3-11eb-97fc-b3c464c4b30f.png)

If you are seeing your Host on nagios and it is saying that it is down but, services are still being checked. Please go into your firewall and enable “File and Printer Sharing(Echo Request IPv4). Schedule another check and it should be all set from there. 
This happens because the way Nagios is checking to see if the system is up and running is by sending a ping command and sometimes by default this port will be disabled, not allowing ping requests to get through.

![image](https://user-images.githubusercontent.com/70775749/108379025-8480be00-71d3-11eb-9389-e5da78171168.png)

### Installing Nagios Client (nrpe) on CentOS
Type yum -y install wget openssl-devel and press Enter

Type cd /tmp and press Enter

Type wget http://assets.nagios.com/downloads/nagiosxi/agents/linux-nrpe-agent.tar.gz and press Enter

Type tar xzf linux-nrpe-agent.tar.gz and press Enter

Type cd linux-nrpe-agent and press Enter

Type ./fullinstall and press Enter

Type y and press Enter

![image](https://user-images.githubusercontent.com/70775749/108440256-c4ba5d80-7220-11eb-8e52-f7e1f715b2f2.png)

![image](https://user-images.githubusercontent.com/70775749/108440271-ce43c580-7220-11eb-80f3-3ecb709bc83b.png)

Type cd /tmp and press Enter

Type wget http://assets.nagios.com/downloads/nagiosxi/agents/linux-nrpe-agent.tar.gz and press Enter

![image](https://user-images.githubusercontent.com/70775749/108440310-def43b80-7220-11eb-8f49-76d3b4d4c17b.png)

Type tar xzf linux-nrpe-agent.tar.gz and press Enter

Type cd linux-nrpe-agent and press Enter

Type ./fullinstall and press Enter

![image](https://user-images.githubusercontent.com/70775749/108440353-f501fc00-7220-11eb-9bfd-4996113ac598.png)

When prompted, type the IP Address of your Nagios Core host

![image](https://user-images.githubusercontent.com/70775749/108440390-06e39f00-7221-11eb-9367-8338347d6d63.png)

Sources:

https://support.nagios.com/kb/article/nagios-core-installing-nagios-core-from-source-96.html#CentOS

https://www.tecmint.com/install-nagios-in-linux/

https://tecadmin.net/install-nrpe-on-centos-rhel/
