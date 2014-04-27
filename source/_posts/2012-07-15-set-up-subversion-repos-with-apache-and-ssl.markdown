---
layout: post
title: "Set up Subversion Repos with Apache and SSL"
date: 2012-07-15 21:00
comments: true
categories: scm
---

Several years ago, [subversion(svn)](http://subversion.tigris.org/) was one of the most popular version control systems in the open source world. It's based on the Central VCS model, thus not very convenient for offline usage as you need to be connected to the server before you make any commits.

Actually I strongly recommend you direct to Git if you still have any chance to alternate. Git is based on the DVCS model and you can make any commits any time as you like, even if you are offline. You just need to push the commits back to the remote repository when you are reconnected to the network. Meanwhile, it has many more advanced features that can help you sleep early every day.

I set up this subversion system for another team. I just leave this note for your reference if you need to set up another one. 


## Choose Your Model

Actually svn supports many repo access models, like svn protocol, svn over ssh, http access and https access. I prefer using the svn protocol since it's much easier to config the system. Unfortunately, we have a very strict IT policy and many ports are restricted, including svn protocol's 3690 port. Finally, I find out that we'd better use https for access, since we can take the advantage of authz to control the access right of each user[group] to each directory. Another benefit is that 443 port is usually not disabled within corporate environments. That team is located behind the firewall of their company. Thus it's the safest solution. What https beats http is that it's more secure during the communication.


## Hardware Requirements

It really depends on your projects and the user activity statistics. Normally a dual core CPU, 512 MB RAM and 20 GB disk space are enough for a 20 user team and 10 repos. But if you want better performance, you are not limited to this basic standard. For example, what I assigned to the server is a dual core CPU, 2GB RAM and 60 GB disk space.


## Software Requirements

For software requirements, I suggest you install *Ubuntu server edition* or *CentOS 5.x* as the host environment. Normally the server edition of a distribution will best fit into the usage since the graphical user interface in the corresponding desktop edition won't bring much benefit, but take up a lot of overhead on disk space and the processing power. For example, a normal installation of Ubuntu 12.04 desktop edition (32 bit) will take around 5GB space, while a server edition will only take a little more than 1GB space.


## Account Preparation

Ubuntu created *www-data* as a system user to work with Apache 2 to deal with incoming requests. If you want to make svn repo work with http/https, you need to grant *www-data* the proper access right. We will create a group *subversion* to access the svn repo files and you need to add *www-data* and yourself into this group:

``` bash
$ sudo addgroup subversion
$ sudo usermod -G subversion -a www-data
$ sudo usermod -G subversion -a dinny
```

> Note: **-a** here is necessary. It will add *subversion* to user as a supplementary group. It will only work with **-G** option.


## Server Installation

I choose Ubuntu 12.04 server edition for use. Therefore CentOS won't be mentioned here. Except some difference on the config file location, setting up on CentOS is much the same. During installation, Ubuntu will prompt to ask which services you want to have. I chose LAMP stacks and SSH. For remote system configuration and management, you need SSH for a text-based user interface. For https access to svn repos, you need Apache to sit in front. Others are not necessary, and you can configure them as you like.

## Package Installation

By default, Ubuntu doesn't have developer tools out of box. That means, you need to install them as you need. You need to install below packages before you make detailed configuration:

``` bash
$ sudo apt-get install subversion libapache2-svn ssl-cert
```

## Create the SVN Repo

``` bash
$ PROJ_PATH="/var/svn/repos/sample-project"
$ sudo mkdir -p $PROJ_PATH
$ sudo svnadmin create $PROJ_PATH
$ sudo chown -R www-data:subversion $PROJ_PATH
$ sudo chmod -R g+rws $PROJ_PATH
```

## Configure Apache with Dav support

Edit dav_svn.conf with below info:

``` bash
$ cat /etc/apache2/mods-available/dav_svn.conf
<Location /svn>
    DAV svn
    SVNParentPath /var/svn/repos
    SVNListParentPath On
    AuthType Basic
    AuthName "Sample Subversion Project"
    AuthUserFile /etc/apache2/dav_svn.passwd
    SSLRequireSSL
    Require valid-user
    <IfModule mod_authz_svn.c>
        AuthzSVNAccessFile /etc/apache2/dav_svn.authz
    </IfModule>
</Location>
```

Edit dav_svn.authz with below info:

``` bash
$ cat /etc/apache2/dav_svn.authz 
[groups]
admin = dinny
devteam = dinny
[/]
@admin = rw
@devteam = rw
```

Add a user account(note that if it's not the first time you create the user, you should omit **-c**):

``` bash
$ sudo htpasswd -c -m /etc/apache2/dav_svn.passwd dinny
```

Enable the modules:

``` bash
$ sudo a2enmod dav_svn authz_svn
```

## Configure Apaches with SSL support

Generate the SSL certificate with make-ssl-cert tool from package ssl-cert:

``` bash
$ sudo mkdir -p /etc/apache2/ssl
$ sudo /usr/sbin/make-ssl-cert /usr/share/ssl-cert/ssleay.cnf /etc/apache2/ssl/apache.pem
```

Create  the virtual host as below:

``` bash
$ sudo cp /etc/apache2/sites-available/default-ssl /etc/apache2/sites-available/svn-ssl
```

Make below changes to svn-ssl:

``` bash
$ sudoedit /etc/apache2/sites-available/svn-ssl
-	SSLCertificateFile    /etc/ssl/certs/ssl-cert-snakeoil.pem
-	SSLCertificateKeyFile /etc/ssl/private/ssl-cert-snakeoil.key
+	#SSLCertificateFile    /etc/ssl/certs/ssl-cert-snakeoil.pem
+	#SSLCertificateKeyFile /etc/ssl/private/ssl-cert-snakeoil.key
+	SSLCertificateFile    /etc/apache2/ssl/apache.pem
```

Enable the ssl module and the svn-ssl site, and restart apache:

``` bash
$ sudo a2enmod ssl
$ sudo a2dissite default default-ssl
$ sudo a2ensite svn-ssl
$ sudo service apache2 restart
```

That's all the work. Now you can try to checkout the repo for a test!

