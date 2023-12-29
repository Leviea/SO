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

### Step 1 - Using your_ssh_file (private key) to log in as root on your ubuntu server.

````
Example: $ ssh root@68.227.102.147 -i my_private_key.ssh -p 23
````

![ssh](url)
