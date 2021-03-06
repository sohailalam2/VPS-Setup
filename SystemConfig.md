# System Configuration

## Adding a new (sudo) user

```
sudo useradd <username>
sudo passwd <username>
```
Open the sudoers file: 
```
sudo visudo
```
will open the */etc/sudoers* file in GNU nano. If not, try 
```
export EDITOR="nano"
```
and try sudo visudo again.

Add the below line to the end of file ( *Change the user name before you issue the commands* )
```
username ALL=(ALL) ALL
```

## How To Set Up and Test DNS Subdomains with DigitalOcean's DNS Panel
Digital Ocean Tutorial (https://www.digitalocean.com/community/tutorials/how-to-set-up-and-test-dns-subdomains-with-digitalocean-s-dns-panel)


## Setting up DNS Record

- ``` sudo vi /etc/resolv.conf ```
- Add Google's DNS Server address to the first line: ``` nameserver 8.8.8.8 ```
- Can also add the following - https://developers.google.com/speed/public-dns/


## Change SSH Port

Change the SSH Port by editing the ssh config file, and restart ssh deamon

```
sudo vi /etc/ssh/sshd_config
sudo service sshd restart 
```

## Display SSH Warning Message to user BEFORE login

Enable Banner

```
sudo vi /etc/ssh/ssh_config
```
Search for Banner and uncomment it and provide the path

```
Banner /etc/issue.net (You can provide any path here)
```

Then create the Banner file and add some text like so -

```
sudo vi /etc/issue.net


#####################################################################
#                                                                   #
#              All connections are monitored and recorded           #
#                                                                   #
#     Disconnect IMMEDIATELY if you are not an authorized user!     #
#                                                                   #
#####################################################################

```

Restart ssh deamon
```
sudo service sshd restart
```

## Display SSH Warning Message to user AFTER login

```
sudo vi /etc/motd
```

Put some welcome text here

```
                               ''~``
                              ( o o )
      +------------------.oooO--(_)--Oooo.------------------+
      |                                                     |
      |                                                     |
      |             Welcome to Sohail's Server              |
      |                                                     |
      |                    .oooO                            |
      |                    (   )   Oooo.                    |
      +---------------------\ (----(   )--------------------+
                             \_)    ) /
                                   (_/
```

Restart ssh deamon
```
sudo service sshd restart
```



## Install and Start Docker

```
sudo yum install -y docker
sudo systemctl start docker.service
sudo systemctl enable docker.service
```



## Install and Start Nginx 

```
sudo rpm -Uvh http://nginx.org/packages/centos/7/noarch/RPMS/nginx-release-centos-7-0.el7.ngx.noarch.rpm
sudo yum install nginx
sudo systemctl start nginx.service
sudo systemctl enable nginx.service

sudo firewall-cmd --permanent --zone=public --add-service=http 
sudo firewall-cmd --permanent --zone=public --add-service=https
sudo firewall-cmd --reload
```


## Find Your Server's Public IP Address

```
ip addr show eth0 | grep inet | awk '{ print $2; }' | sed 's/\/.*$//'
```


## Optimising NginX, Node.JS and networking for heavy workloads
Tutorial (https://engineering.gosquared.com/optimising-nginx-node-js-and-networking-for-heavy-workloads)


## Installing Webmin

```
rpm -U webmin-1.730-1.noarch.rpm
wget http://prdownloads.sourceforge.net/webadmin/webmin-1.730-1.noarch.rpm
```

### Using the Webmin YUM repository

If you like to install and update Webmin via RPM, create the /etc/yum.repos.d/webmin.repo file containing :

```
[Webmin]
name=Webmin Distribution Neutral
#baseurl=http://download.webmin.com/download/yum
mirrorlist=http://download.webmin.com/download/yum/mirrorlist
enabled=1
```

You should also fetch and install my GPG key with which the packages are signed, with the commands :
```
wget http://www.webmin.com/jcameron-key.asc
rpm --import jcameron-key.asc
```

You will now be able to install with the command :

```
yum install webmin
```

All dependencies should be resolved automatically.

