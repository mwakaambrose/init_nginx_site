### Automatically creating new virtual hosts with Nginx (Bash script) Posted on March 12, 2011

Setting up virtual hosts on any sort of web server normally takes at least a few minutes and several commands (if you’re not running a control panel of some variety, and even then it normally takes a good number of clicks and checking boxes to get what you want). All of this can be quite annoying when you have to set up several of these a day.

So I put together a simple bash script to quickly provision the hosting for a new static site running on Nginx. The script was originally build for use on the Amazon AMI running on AWS. The script uses the sudo command as the password is not required for the default user on the AWS AMI, however if you are running as a non root user with access to the sudo command then you should be prompted for your password when running the script.

#### What does the script do:
- Creates a new vhosts entry for nginx using a basic template
- Creates a new directory for the new vhost and sets nginx as the owner
- Adds a simple index.html file to the new directory to show the site is working.
- Reloads Nginx to allow the new vhost to be picked up

#### How it works:
It uses the debian style set up for apache as its basis for handling vhosts with Nginx. This means under your Nginx config directory (/etc/nginx) you need to have two extra directories:

```
	sites-available
	sites-enabled
```
In the sites available dir goes the the individual config files for each vhost, each new vhost using a new file. Under the sites enabled directory goes a symlink to the config file in the sites available directory, this gives you the ability to disable sites without removing tis files or config. This all works by adding the following line to the end of the /etc/nginx/nginx.conf file (within the http section)

#### Load all vhosts !
```
include /etc/nginx/sites-enabled/*.conf;
```
The hosting directory for the new site can be set by modifying the `WEB_DIR` value at the top of the script which defaults to `/var/www`
So the new hosting directory for your domain `example.com` ends up as:

`/var/www/example_com`

Whilst the original version of the script created a new user for each site and made that user the owner of the hosting directory I have removed this functionality to make the script less taylored to my needs (if needed this user creation bit could easily be re-added) . For the moment then the script grants the nginx user owner rights to the new vhost directory (which from a security perspective is not great), so you may want to modify this.

#### How to use it:
Simply download the tar file at the end of the this article and extract it. If your not sure how to extract a tar file either read the man pages or use the command below:
```
tar -xzf create_nginx_vhost.tar.gz
```
Once you have extracted the archive just run the create_nginx_site.sh script passing to it the domain name as the only parameter (you will need execute permissions), e.g.
```
./create_nginx_site.sh example.com
```
And you should see an output similar to the following:

```
Creating hosting for: example.com
Reloading nginx:                                             [  OK  ]
Site Created for example.com
```

### credit
http://www.sebdangerfield.me.uk/2011/03/automatically-creating-new-virtual-hosts-with-nginx-bash-script/