# Final Project Sistem Operasi
Service :
- SSH 
- Web Server
- MySQL

# Machine
- CPU		: AMD A12-9720p Quad Core 2.7GHz
- Memory	: 8GB
- Storage	: 500GB
- OS		: Ubuntu 22.04
  
# SSH Server
## Installasi dan Konfigurasi SSH Server
Install OpenSSH Server
    sudo apt install openssh-server
    sudo apt update

Konfigurasi OpenSSH server
---------------------------------------------------------------------------------

## Table of Contents
- [Security](#security)

---
#Security
Keamanan dasar.

###Update
With any new installation you want to update!

    sudo apt-get update && upgrade -y

###Firewall UFW
UFW is the uncomplicated firewall.

    sudo ufw enable
    sudo ufw allow 80
    sudo ufw allow 443
    sudo ufw allow 22
    sudo ufw allow 8080
    sudo ufw allow 3306
    sudo ufw Port 23
    sudo ufw allow 989
    sudo ufw allow 990
    

Reload Firewall Rules:

    sudo ufw reload
    sudo ufw status

###SSH and Users
You should first create a **non-root** user. Since default logins are root on port 23:

    sudo useradd -m -s /bin/bash leviea
    passwd leviea

We need **leviea** him to be a **super-user (su)**. Add your in visudo:

    $ visudo
    --------
    # User privilege specification
    root    ALL=(ALL:ALL) ALL
    leviea   ALL=(ALL:ALL) ALL
#####Repository lokal
    
    $ sudo vim /etc/apt/sources.list
    -------------------------------
    deb http://kebo.pens.ac.id/ubuntu/ jammy main restricted universe multiverse
    deb http://kebo.pens.ac.id/ubuntu/ jammy-updates main restricted universe multiverse
    deb http://kebo.pens.ac.id/ubuntu/ jammy-security main restricted universe multiverse
    deb http://kebo.pens.ac.id/ubuntu/ jammy-backports main restricted universe multiverse
    deb http://kebo.pens.ac.id/ubuntu/ jammy-proposed main restricted universe multiverse
    $ sudo apt update && upgrade -y
    
#####Change Default SSH Port
Membuka dan mengubah Port yang

    $ sudo vim /etc/ssh/sshd_config
    -------------------------------
    Port 23   # TCP
    Port 80   # http
    Port 443  # HTTPs
    Port 989  # FTPS data 
    Port 990  # FTPS Control
    Port 3306 # SQL
    
    PermitRootLogin yes
    PrintMotd yes
    PubkeyAuthentication yes
    UsePAM yes
    X11Forwarding yes
    

Reload SSH Configuration:

    sudo service ssh start
    sudo service ssh enable
    sudo service ssh reload

#####Make Sure You can Login
Test your new user by keeping your current terminal connected and opening a second terminal:

    ssh leviea@IP_Address -p23

Also make sure you can use sudo, so type `su -`





=============================================================================================================================================================================================================================================================================================================================================================================================================================================================================================================================================

# WEB SERVER
## Ubuntu-22.04-Web-Server-Setup
## Using Apache2, php, PHPmyadmin to setup a web server.
Describes the linux commands needed to set up a web server on ubuntu 22.04
We start from the idea that you already have an ssh file private key to connect to the ubuntu server as root user where you will carry out the installation.

## *** Initial Server Setup with Ubuntu 22.04 ***
Taken from: https://www.digitalocean.com/community/tutorials/initial-server-setup-with-ubuntu-22-04

###Step 1 — Installing Apache
Apache is available within Ubuntu’s default software repositories, making it possible to install it using conventional package management tools.

Begin by updating the local package index to reflect the latest upstream changes:

    sudo apt update
Then, install the apache2 package:

    sudo apt install apache2


###Step 2 — Adjusting the Firewall
Before testing Apache, it’s necessary to modify the firewall settings to allow outside access to the default web ports. If you followed the instructions in the prerequisites, you should have a UFW firewall configured to restrict access to your server.

During installation, Apache registers itself with UFW to provide a few application profiles that can be used to enable or disable access to Apache through the firewall.

List the ufw application profiles by running the following:

    sudo ufw app list

Output

    Available applications:
    Apache
    Apache Full
    Apache Secure
    OpenSSH
As indicated by the output, there are three profiles available for Apache:

Apache: This profile opens only port 80 (normal, unencrypted web traffic)
Apache Full: This profile opens both port 80 (normal, unencrypted web traffic) and port 443 (TLS/SSL encrypted traffic)
Apache Secure: This profile opens only port 443 (TLS/SSL encrypted traffic)
It is recommended that you enable the most restrictive profile that will still allow the traffic you’ve configured. Since you haven’t configured SSL for your server yet in this guide, you’ll only need to allow traffic on port 80:

    sudo ufw allow 'Apache'
You can verify the change by checking the status:

    sudo ufw status
The output will provide a list of allowed HTTP traffic:

Output

    Status: active

    To                         Action      From
    --                         ------      ----
    OpenSSH                    ALLOW       Anywhere                  
    Apache                     ALLOW       Anywhere                
    OpenSSH (v6)               ALLOW       Anywhere (v6)             
    Apache (v6)                ALLOW       Anywhere (v6)
As indicated by the output, the profile has been activated to allow access to the Apache web server.

###Step 3 — Checking your Web Server
At the end of the installation process, Ubuntu 22.04 starts Apache. The web server will already be up and running.

Make sure the service is active by running the command for the systemd init system:

sudo systemctl status apache2
    
    Output
    ● apache2.service - The Apache HTTP Server
         Loaded: loaded (/lib/systemd/system/apache2.service; enabled; vendor prese>
         Active: active (running) since Tue 2022-04-26 15:33:21 UTC; 43s ago
           Docs: https://httpd.apache.org/docs/2.4/
       Main PID: 5089 (apache2)
          Tasks: 55 (limit: 1119)
         Memory: 4.8M
            CPU: 33ms
         CGroup: /system.slice/apache2.service
                 ├─5089 /usr/sbin/apache2 -k start
                 ├─5091 /usr/sbin/apache2 -k start
                 └─5092 /usr/sbin/apache2 -k start
As confirmed by this output, the service has started successfully. However, the best way to test this is to request a page from Apache.

You can access the default Apache landing page to confirm that the software is running properly through your IP address. If you do not know your server’s IP address, you can get it a few different ways from the command line.

Try writing the following at your server’s command prompt:

    hostname -I
You will receive a few addresses separated by spaces. You can try each in your web browser to determine if they work.

Another option is to use the free icanhazip.com tool. This is a website that, when accessed, returns your machine’s public IP address as read from another location on the internet:

    curl -4 icanhazip.com
When you have your server’s IP address, enter it into your browser’s address bar:

    http://192.168.10.111
You will see the default Ubuntu 22.04 Apache web page as in the following:

    Apache default page

This page indicates that Apache is working correctly. It also includes some basic information about important Apache files and directory locations.

###Step 4 — Managing the Apache Process
Now that you have your web server up and running, let’s review some basic management commands using systemctl.

To stop your web server, run:

    sudo systemctl stop apache2
To start the web server when it is stopped, run:

    sudo systemctl start apache2
To stop and then start the service again, run:

    sudo systemctl restart apache2
If you are simply making configuration changes, Apache can often reload without dropping connections. To do this, use the following command:

    sudo systemctl reload apache2
By default, Apache is configured to start automatically when the server boots. If this is not what you want, disable this behavior by running:

    sudo systemctl disable apache2
To re-enable the service to start up at boot, run:

    sudo systemctl enable apache2


![ssh](url)
