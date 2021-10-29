# Download FusionPBX

&ensp;[Note](#1)

# Install Script

##  Ubuntu Install
Ubuntu 20.04 LTS. is the recommended operating system and version. Start with a minimal install. You can download Ubuntu 20.04 LTS here. Then run the following commands as 'root'.

wget -O - https://raw.githubusercontent.com/fusionpbx/fusionpbx-install.sh/master/ubuntu/pre-install.sh | sh;

cd /usr/src/fusionpbx-install.sh/ubuntu && ./install.sh


##  Debian Install

Debian 9 is currently the recommended version for Debian. Start with a minimal install. You can download Debian 10 here. Debian 9 can be downloaded from the link that follows. here. Then run the following commands as 'root'.

wget -O - https://raw.githubusercontent.com/fusionpbx/fusionpbx-install.sh/master/debian/pre-install.sh | sh;

cd /usr/src/fusionpbx-install.sh/debian && ./install.sh


Raspberry Pi OS (previously called Raspbian) Install
Based on Debian Buster, Raspberry Pi OS works on the Raspberry Pi 2, 3, 4, and Zero W. (Installing on the Raspberry Pi Zero is not recommended due to its slower processing capabilities.)

Debian 9 is currently the recommended operating system and version. Download the newest version of Raspbian. Tools and information on unzipping the image and writing the operating system image to an SD card are available on the Installing OS Images page. Then run the following commands as 'root'.

wget -O - https://raw.githubusercontent.com/fusionpbx/fusionpbx-install.sh/master/debian/pre-install.sh | sh;

cd /usr/src/fusionpbx-install.sh/debian && ./install.sh




##  FreeBSD Install
Start with a minimal install of FreeBSD 11.1. Then run the following commands as 'root'.

pkg install --yes git

cd /usr/src && git clone https://github.com/fusionpbx/fusionpbx-install.sh.git

cd /usr/src/fusionpbx-install.sh/freebsd && ./install.sh


##  CentOS Install
Start with a minimal install of CentOS 7. Then run the following commands as 'root'.

yum install wget

wget -O - https://raw.githubusercontent.com/fusionpbx/fusionpbx-install.sh/master/centos/pre-install.sh | sh

cd /usr/src/fusionpbx-install.sh/centos && ./install.sh

# <a name="1">Note</a>

- The following must be installed first: 
    Web Server - NGINX
    
=>  The default is NGINX as its small fast and easy to configure HTTPs is pre-configured with a self signed certificate.


- Database Server - PostgreSQL or SQLite
PostgreSQL is an advanced ANSI SQL compliant database. It is stable and powerful. It has native UUID data types, native JSON support, Foreign Data Wrappers, multi-master replication and a lot more. Multi-master file and database replication are taught in the FusionPBX Advanced training. SQLite is a great choice for embedded systems. SQLite should not be used for the FusionPBX database for multi-tenant systems.
