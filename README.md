# Final Project SO S3
Tugas Akhir Sistem Operasi
SSH SERVER
WEB SERVER

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
    - [Update](#update)
    - [Firewall UFW](#firewall-ufw)
    - [SSH and Users](#ssh-and-users)
    - [Fail2Ban](#fail2ban)
    - [Rootkits](#rootkits)
    - [Unattended Upgrades](#unattended-upgrades)
    - [Apache2 Mod-Evasive](#apache2-mod-evasive)
- [Packages](#packages)
    - [Common Items](#common-items)
    - [Enable PPA Repositories](#enable-ppa-repositories)
- [Commands](#commands)
    - [SSH Welcome Message](#ssh-welcome-message)  
    - [Searching](#searching)
    - [User Management](#user-management)
    - [SFTP User](#sftp-user)
- [Manage Network Scripts](#manage-network-scripts)
    - [Checking Ports](#checking-ports)

---
#Security
Keamanan dasar.

###Update
With any new installation you want to update!

    sudo apt-get update
    sudo apt-get upgrade -y

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

#####User SSH Login
---

As your new user (**leviea**), if you want to login with an SSH key, make sure you have a key on your **local** machine.

    ssh-keygen -t rsa -b 4096 -C "your-email@domain.com"
file key tersiman di /root dengan nama key dan dipindak he ~/.ssh

Create your **remote** SSH folder and authorized_keys. Paste your `id_rsa.pub`to authorized_host:

    mkdir ~/.ssh
    vim /etc/authorized_keys

Your **local** `~/.ssh/id_rsa.pub` must match the **remote** `~/.ssh/authorized_keys`. Make sure it's on **one line!**

#####SSH File Permissions
Here are the permissions for your files (local and remote).

    chmod 700 ~/.ssh &&\
    chmod 600 ~/.ssh/authorized_keys &&\
    chmod 644 ~/.ssh/id_rsa.pub &&\
    chmod 600 ~/.ssh/id_rsa

Don't keep your `id_rsa` private key on the **remote** host, all you need to login is the `authorized_keys` file. _Only host your private key for a locked down user for deployments._

#####Quick SSH Login
On your local machine edit or create an ssh config for quick connection:

    $ vim ~/.ssh/config
    -------------------
    Host myhost
    Hostname 123.123.123.555
    Port 1234
    User user1

You should now be able to connect with:

    ssh myhost

###Fail2Ban
Bans IPs that attempt too many password failures, searching for exploits and the like. The default configuration is good.

    sudo apt-get install fail2ban

###Rootkits

    sudo apt-get install chkrootkit rkhunter

Edit the chkrootkit configuration:

    sudo vim /etc/chkrootkit.conf

We will run both weekly; However we need to change the configuration:

    RUN_DAILY="true"
    RUN_DAILY_OPTS=""
    DIFF_MODE="false"

For your reference, rkhunter's configuration file is located here: `/etc/default/rkhunter`

Rename the rkhunter's update job with a different name before moving the other items to the weekly CRON:

    sudo mv /etc/cron.weekly/rkhunter /etc/cron.weekly/rkhunter_update

Next move the daily CRON to the weekly:

    sudo mv /etc/cron.daily/chkrootkit /etc/cron.weekly
    sudo mv /etc/cron.daily/rkhunter /etc/cron.weekly


###Unattended Upgrades
Keep security updates on a cron.

    sudo apt-get install unattended-upgrades

Edit the periodic updated file:

    sudo vim /etc/apt/apt.conf.d/10periodic

Update your values to something like this:

    APT::Periodic::Update-Package-Lists "1";
    APT::Periodic::Download-Upgradeable-Packages "1";
    APT::Periodic::AutocleanInterval "7";
    APT::Periodic::Unattended-Upgrade "1";



###Apache2 Mod-Evasive
This is useful for DDOS attacks. First install the needed packages.

    sudo apt-get install apache2 apache2-utils libapache2-mod-evasive

Create the log directory.

    sudo mkdir /var/log/mod_evasive
    sudo chown www-data:www-data /var/log/mod_evasive

Edit the configuration file:

    sudo vim /etc/apache2/mods-available/mod_evasive.conf

Uncomment everything except `DOSSystemCommand` and add your email after `DOSEmailNotify`.

Reload Apache:

    sudo a2enmod evasive
    sudo service apache2 reload

#Packages
The location for aptitude `apt` package sources is:

    /etc/apt/sources.list     # This is one long file of defaults
    /etc/apt/sources.list.d/  # These are separate files for things like PPA adding
    
If you choose to manually add a package I would recommend adding it to the `/etc/apt/sources.list.d/your-source.list` directory, that way you can just delete it and `sudo apt-get update` if you don't want it -- rather than editing the main `sources.list` file.

###Common Items
These are some common packages you can use. If you prefer `nginx` over `apache` then install that instead.

    sudo apt-get install\
    git htop xclip\
    python-dev python-pip\
    php5 php5-dev\
    apache2 apache2-utils


###Enable PPA Repositories
This should exist by default, but if it doesn't install it:

    sudo apt-get install python-software-properties

#Commands
These are commands for reference.

###SSH Welcome Message
When you login to your SSH, you can add a custom welcome banner that looks cool:

    sudo vim /etc/ssh/sshd_config
    Banner /etc/banner

Then create the file and add anything you want:

    sudo vim /etc/banner
    
Here is an example:
```
   __ _____ _____ _____ _____ 
 __|  | __  |   __|  _  |     |
|  |  |    -|   __|     | | | |
|_____|__|__|_____|__|__|_|_|_|
-------------------------------
Server 01               Welcome
-------------------------------
```
    
I used a text to ASCII generator for that. Then restart and it will appear next time you login!

    sudo service ssh restart

##Searching

Search for a filename from system path

    $ find / --name filename

Search the contents of a file

    $ cat filename | grep "text-to-find-here"

Search within files in the current directory

    $ grep -Ril "text-to-find-here" .

    R (recursive)
    i (case insensitive)
    l (show the file name, not the result itself)

##User Management

See the user defaults, and add a user with the defaults:

    useradd -D
    useradd user2

    useradd -m user2                # Create Home, Default Shell
    useradd -m -s /bin/bash user2   # Set Shell, Create Home

    passwd user2                     # Change Passwd
    userdel user2                     # Delete User

    cat /etc/passwd # See Users
    cat /etc/group # See Groups

Manually Add sudo (Super User)

    $ visudo
    --------
    user2 ALL=(ALL) ALL

Change a users shell

    sudo chsh -s /bin/bash user2

Add Existing user to Existing Group

    usermod -a -G www-data user2

##SFTP User
For SFTP Access you should create a group an ddo the following:

    sudo groupadd sftp_users
    sudo usermod -G sftp_users user2

For a webserver, you should add the webserver group AS WELL

    sudo usermod -G www-data user2

Edit your SSHD config and append to the end of the file

    $ sudo vim /etc/ssh/sshd_config
    -------------------------------
    Match group filetransfer
        ChrootDirectory %h
        X11Forwarding no
        AllowTcpForwarding no
        ForceCommand internal-sftp

Restart SSH

    sudo service ssh restart

#Manage Network Scripts
You can add your own startup/shutdown scripts and the like in folders in this area:

    /etc/network/if-down.d/
    /etc/network/if-pre-up.d/

Just make sure to `chmod +x filename.sh`

##Checking Ports
Beginner commands to [http://www.linux.com/learn/tutorials/290879-beginners-guide-to-nmap](nmap)

    apt-get install nmap

There are many ways to check open ports:

    sudo ufw status
    sudo nmap -sT -O localhost

Other ways to check ports

    netstat -anp | grep 222
    lsof -i | grep 222
    telnet localhost 222

---

Permission to use, copy, modify, and/or distribute this software for any purpose with or without fee is hereby granted, provided that notice appear in all copies.

&copy;2016 MIT License | Jesse Boyer | JREAM.com

=============================================================================================================================================================================================================================================================================================================================================================================================================================================================================================================================================

# WEB SERVER
## Ubuntu-22.04-Web-Server-Setup
## Using nginx, php, mariadb, certbot, filezila and adminer.php to setup a web server.
Describes the linux commands needed to set up a web server on ubuntu 22.04
We start from the idea that you already have an ssh file private key to connect to the ubuntu server as root user where you will carry out the installation.

Note: As is obvious and expected, some words like: your_server_ip, your_ssh_file, your_port, your_domain, your_new_user, dbAdmin, your_db_password and others can or should be replaced with your server connection information or data. I hope you have already purchased a domain from amazon aws, godaddy, digitalocean or any other dns or web hosting provider. And they have already provided you with all this information with which to replace the words said above. Of course, the password for your ubuntu user and mysql (mariaDB) user will have to be decided by you. This is not going to be given to you by any web service provider.
 
## *** Initial Server Setup with Ubuntu 22.04 ***
Taken from: https://www.digitalocean.com/community/tutorials/initial-server-setup-with-ubuntu-22-04

### Step 1 - Using your_ssh_file (private key) to log in as root on your ubuntu server.
````
$ ssh root@your_server_ip -i your_ssh_file -p your_port
````

````
Example: $ ssh root@68.227.102.147 -i my_private_key.ssh -p 22
````

### Step 2 - Creating a new user.

````
$ adduser your_new_user
````

````
Example: $ adduser julio // Add an ubuntu user. This can be your name or just "user" or "admin" or "ubuntu" or any other.
````

### Step 3 - Granting administrative privileges.

````
$ usermod -aG sudo your_new_user
````

````
Example: $ usermod -aG sudo julio
````

Note: You can now type sudo before commands to run them with superuser privileges when logged in as your regular user.

### Step 4 - Setting Up a Firewall
````
$ ufw app list // Examine the list of installed UFW profiles.
$ ufw allow OpenSSH  //  Allows SSH connections.
$ ufw allow 22/tcp // We will use 22 as port to connect by ssh. The default port is 22. You may use other like 2221 or similar.
$ sudo ufw allow http // This is the same as "sudo ufw allow 80/tcp"
$ sudo ufw allow https // This is the same as "sudo ufw allow 443/tcp"
$ ufw enable // Enable the firewall.
$ ufw status // See that SSH connections are still allowed.
````

### Step 5 - Enabling external access for your regular user on your ubuntu server.

````
$ rsync --archive --chown=your_new_user:your_new_user ~/.ssh /home/your_new_user
````

````
Example: rsync --archive --chown=julio:julio ~/.ssh /home/julio
````

### 6.- Enter to your server again using the new user:

````
ssh your_new_user@your_ip_address -i your_ssh_file -p your_port
````
````
Example: ssh julio@68.227.102.147 -i my_private_key.ssh  -p 22
````

### 7.- (Optional) - Change the name of the ubuntu server to ubuntu-server or any other name.
````
$ sudo hostnamectl set-hostname ubuntu-server 
````


## *** How To Install Nginx on Ubuntu 22.04 ***
Taken from: https://www.digitalocean.com/community/tutorials/how-to-install-nginx-on-ubuntu-22-04

### Step 1 - Installing Nginx.

````
$ sudo apt update
$ sudo apt install nginx
````

### Step 2 - Adjusting the Firewall.

````
sudo ufw app list
sudo ufw allow 'Nginx Full' // This will allow ports 80 for http and 443 for https.
sudo ufw status
````

### Step 3 - Checking your Web Server.

````
systemctl status nginx
curl -4 icanhazip.com  // it will give you your ip address.
````

From a browser go to:
http://your_server_ip

### Step 4 - Managing the Nginx Process.
Now that you have your web server up and running, let’s review some basic management commands.

````
$ sudo systemctl stop nginx // To stop your web server.
$ sudo systemctl start nginx // To start the web server when it is stopped.
$ sudo systemctl restart nginx // To stop and then start the service again.
$ sudo systemctl reload nginx // If you are only making configuration changes, Nginx can often reload without dropping connections. 
$ sudo systemctl disable nginx // By default, Nginx is configured to start automatically when the server boots. If this is not what you want, you can disable this behavior.
$ sudo systemctl enable nginx // To re-enable the service to start up at boot.
````

### Step 5 - Setting Up Server Blocks.

$ sudo mkdir -p /var/www/your_domain // Create the directory for your_domain. 

$ sudo chown -R www-data:www-data /var/www/your_domain // Assign ownership of the directory so php can be used.

Setting permissions for yuor folders and files. 
You must use the + symbol as the end in the following two commands:

````
$ sudo find /var/www/your_domain -type d -exec chmod 2775 {} +  // files inherit the folder’s group
$ sudo find /var/www/your_domain -type f -exec chmod 0664 {} +  // chmod cannot discriminare folders/files
````

### Create a sample index.html page using nano.
$ sudo nano /var/www/your_domain/index.html 

Inside, add the following sample HTML:

````
<html>
    <head>
        <title>Welcome to your_domain!</title>
    </head>
    <body>
        <p>Success!  The your_domain server block is working!</p>
    </body>
</html>
````
Save and close the file by pressing Ctrl+X to exit, then when prompted to save, Y and then Enter.

### Create a block file in this path using your_domain as file.
$ sudo nano /etc/nginx/sites-available/your_domain 

Now paste in the following configuration block, which is similar to the default:
Note: Remember to change your_domain for the name of your domain. 
````
server {
  # Example PHP Nginx FPM config file 
  root /var/www/your_domain;

  # Add index.php to setup Nginx, PHP & PHP-FPM config 
  index index.php index.html index.htm index.nginx-debian.html;

  server_name your_domain;

  location / {
    try_files $uri $uri/ =404;
  }

  # pass PHP scripts on Nginx to FastCGI (PHP-FPM) server 
  location ~ \.php$ {
    include snippets/fastcgi-php.conf;

    # Nginx php-fpm sock config:
    fastcgi_pass unix:/run/php/php8.1-fpm.sock;
    # Nginx php-cgi config :
    # Nginx PHP fastcgi_pass 127.0.0.1:9000;
  }

  # deny access to Apache .htaccess on Nginx with PHP, if Apache and Nginx document roots concur 
  location ~ /\.ht {
    deny all;
  }
  
} 
````

Notice that we’ve updated the root configuration to our new directory, and the server_name to our domain name.

### Next, let’s enable the file by creating a link from it to the sites-enabled directory, which Nginx reads from during startup.

````
$ sudo ln -fs /etc/nginx/sites-available/your_domain /etc/nginx/sites-enabled/
````

To avoid a possible hash bucket memory problem that can arise from adding additional server names, it is necessary to adjust a single value in the /etc/nginx/nginx.conf file. Open the file:

````
$ sudo nano /etc/nginx/nginx.conf
````

Find the server_names_hash_bucket_size directive and remove the # symbol to uncomment the line. If you are using nano, you can quickly search for words in the file by pressing CTRL and w.

Note: Commenting out lines of code – usually by putting # at the start of a line – is another way of disabling them without needing to actually delete them. Many configuration files ship with multiple options commented out so that they can be enabled or disabled, by toggling them between active code and documentation.

$ sudo nano /etc/nginx/nginx.conf
````
http {
    ...
    server_names_hash_bucket_size 64;
    ...
}
````
Save and close the file when you are finished.

$ sudo nginx -t  // Test to make sure that there are no syntax errors in any of your Nginx files.

### If there aren’t any problems, restart Nginx to enable your changes:
````
$ sudo systemctl restart nginx
````

Nginx should now be serving your domain name. You can test this by navigating to http://your_domain, where you should see something like this:
Nginx first server block

### Step 6 – Getting Familiar with Important Nginx Files and Directories
Now that you know how to manage the Nginx service itself, you should take a few minutes to familiarize yourself with a few important directories and files.

Server Logs
````
sudo nano /var/log/nginx/access.log // Every request to your web server is recorded in this log file unless Nginx is configured to do otherwise.
sudo nano /var/log/nginx/error.log // Any Nginx errors will be recorded in this log.
````

## *** Installing PHP ***
Taken from: https://tecadmin.net/how-to-install-php-on-ubuntu-22-04/ 

### Update and upgrade your Ubuntu Linux Server.

````
$ sudo apt update && sudo apt upgrade  // update and upgrade ubuntu
````

### Install a few dependencies required by this tutorial with the below-mentioned command:

````
$ sudo apt install software-properties-common ca-certificates lsb-release apt-transport-https 
````

### Add the Ondrej PPA to your system, which contains all versions of PHP packages for Ubuntu systems.

````
$ LC_ALL=C.UTF-8 sudo add-apt-repository ppa:ondrej/php 
$ LC_ALL=C.UTF-8 sudo add-apt-repository ppa:ondrej/nginx
````

### Update again and install php
````
$ sudo apt update  // update the Apt package manager cache.
$ sudo apt install php8.1 // Install php. Use the version that you need.
````

### Install other extensions

````
$ sudo apt install php8.1-mysql php8.1-mbstring php8.1-xml php8.1-curl 
$ sudo apt install php-fpm
````


## *** How To Install MariaDB on Ubuntu 22.04 ***
Taken from: https://www.digitalocean.com/community/tutorials/how-to-install-mariadb-on-ubuntu-22-04

### Step 1 — Installing MariaDB

````
$ sudo apt update // Update your package index using apt
$ sudo apt install mariadb-server // Install the mariadb-server package using apt.
````

### Step 2 — Configuring MariaDB

````
$ sudo mysql_secure_installation // Run this to restrict access to the server

Enter current password for root (enter for none): // Press <Enter>

Switch to unix_socket authentication [Y/n] n
 ... skipping.

Change the root password? [Y/n] n
 ... skipping.

Remove anonymous users? [Y/n] y
 ... Success!

Disallow root login remotely? [Y/n] y
 ... Success!

Remove test database and access to it? [Y/n] y
 - Dropping test database...
 ... Success!
 - Removing privileges on test database...
 ... Success!

Reloading the privilege tables will ensure that all changes made so far
will take effect immediately.

Reload privilege tables now? [Y/n] y
 ... Success!

Cleaning up...

All done!  If you've completed all of the above steps, your MariaDB
installation should now be secure.

Thanks for using MariaDB!
````

### Step 3 — Creating an Administrative User to employs Password Authentication

We will create a new mariadb account called dbAdmin with the same capabilities as the root account, but configured for password authentication. Open up the MariaDB prompt from your terminal:

````
$ sudo mariadb
````

Then create a new user called dbAdmin with root privileges and password-based access do the following. Be sure to change your_db_password for a strong password. This will be set as your password.

````
GRANT ALL ON *.* TO 'dbAdmin'@'localhost' IDENTIFIED BY 'your_db_password' WITH GRANT OPTION;

FLUSH PRIVILEGES; // Flush the privileges to ensure that they are saved and available in the current session:

exit; // Exit the MariaDB shell:
 
````

Finally, let’s test the MariaDB installation.

### Step 4 — Testing MariaDB

When installed from the default repositories, MariaDB will start running automatically. To test this, check its status.
 
````
$ sudo systemctl status mariadb
````

You’ll receive output that is similar to the following:

````
Output
● mariadb.service - MariaDB 10.5.12 database server
     Loaded: loaded (/lib/systemd/system/mariadb.service; enabled; vendor preset: enabled)
     Active: active (running) since Fri 2022-03-11 22:01:33 UTC; 14min ago
       Docs: man:mariadbd(8)
             https://mariadb.com/kb/en/library/systemd/
````
 
If MariaDB isn’t running, you can start it with the command:
 
````
$ sudo systemctl start mariadb.
````


### Step 5 - Create a mariadb DATABASE to store the data of your php proyect or web site.

Enter to mysql (mariaDB). Your dbAdmin password may be asked. On the next GRANT ALL PRIVILEGES command you must use the password that you already set for the dbAdmin account that you create on the step 4.

````
mysql -u admin -p 

CREATE DATABASE your_new_db CHARACTER SET utf8mb4 COLLATE utf8mb4_general_ci; 
FLUSH PRIVILEGES;

GRANT ALL PRIVILEGES ON your_new_db.* TO 'dbAdmin'@'localhost' IDENTIFIED BY 'your_db_password'; 

FLUSH PRIVILEGES;
```` 

# *** Installing Certbot to download an https certificate from letsencrypt. ***
Taken from https://certbot.eff.org/lets-encrypt/ubuntubionic-nginx 

```` 
$ apt-get install certbot python3-certbot-nginx 
$ sudo certbot --nginx -d your_domain
```` 
After following a few questions you may have been success. 

The following lines should be added to your nginx block file on /etc/nginx/sites-available/your_domain

```` 
$ sudo nano /etc/nginx/sites-available/your_domain
```` 

```` 
  listen [::]:443 ssl ipv6only=on; # managed by Certbot
  listen 443 ssl; # managed by Certbot
  ssl_certificate /etc/letsencrypt/live/your_domain/fullchain.pem; # managed by Certbot
  ssl_certificate_key /etc/letsencrypt/live/your_domain/privkey.pem; # managed by Certbot
  include /etc/letsencrypt/options-ssl-nginx.conf; # managed by Certbot
  ssl_dhparam /etc/letsencrypt/ssl-dhparams.pem; # managed by Certbot
````   

# *** Download Adminer ***  
Taken from: https://stackoverflow.com/questions/65053387/problem-with-adminer-php-8-0-on-ubuntu-20-04

From Trevor:
Support was added in December 2020, unfortunately only weeks after your question @The-Evil-Fox. The only problem is it seems the Ubuntu repo is not up to date so if you install via apt install adminer you'll still be on a version that doesn't support PHP 8.

The easiest way I found to update is to still install it that way, but then use wget to download the latest version of adminer.php. So do something like this:

Go to the adminer folder
````
cd /usr/share/adminer
````
Download the latest version of adminer:
````
sudo wget https://www.adminer.org/latest.php
````
Then rename latest.php to /var/www/your_domain/adminer.php 
````
sudo mv latest.php /var/www/your_domain/adminer.php 
````

From a web browwer type:
https://your_domain/adminer.php and use your dbAdmin and your_db_account that you set.

# *** Upload your php files to your server

1.- Install on your Windows PC the FileZila Client app from:
    https://filezilla-project.org/download.php?platform=win64
    
2.- Setup your ftp server with your own connection server info as the following example:

![image](https://user-images.githubusercontent.com/18542304/233743203-2bc76d2a-690b-4cbc-afed-db3427d8a23f.png)

Note: The connection info used on this screenshot is not the real info, but an example so you can use your own.

Once you have use FileZila to upload your php files to /var/www/your_domain you may need to apply this command again to all your folders and files, except to adminer.php 

````
- sudo find /var/www/your_domain -type d -exec chmod 2775 {} + (files inherit the folder’s group)
- sudo find /var/www/your_domain -type f -exec chmod 0664 {} + (chmod cannot discriminare folders/files)
````

### Adminer login

Adminer is to nginx what phpmyadmin is to apache. If you can not enter to https://your_domain/adminer.php, you may need to download adminer.php again as described above in the Download Adminer section.

You have to use your mysql dbAdmin and the password that you set for it. 

![image](https://user-images.githubusercontent.com/18542304/234631606-fa681802-6cb3-4c61-be47-e5fb86845c9a.png)


Now, just learn more about adminer and how to use it at: https://www.adminer.org/

