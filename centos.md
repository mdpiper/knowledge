# CentOS (and siwenna)

CentOS 7 is installed on ***siwenna***.
```
$ lsb_release -a
LSB Version:    :core-4.1-amd64:core-4.1-noarch:cxx-4.1-amd64:cxx-4.1-noarch:desktop-4.1-amd64:desktop-4.1-noarch:languages-4.1-amd64:languages-4.1-noarch:printing-4.1-amd64:printing-4.1-noarch
Distributor ID: CentOS
Description:    CentOS Linux release 7.4.1708 (Core)
Release:        7.4.1708
Codename:       Core
```

Compared with CentOS 6,
almost everything is already set up for me.

Note that there's some crossover with my
[Linux](./linux.md) notes.

## Package manager

`yum` is the package manager.

	$ yum list
	$ yum search
	$ yum info
	$ yum install


## Firewall

CentOS 7 uses `firewalld` to configure and manage its firewall.
Access it through `systemctl` with:

    sudo systemctl status firewalld

Also `stop`, `start`, and `restart`.
It's far too complex for what I need.

Find the current zone.

    $ sudo firewall-cmd --get-active-zones
    public
      interfaces: enp0s25

Allow web traffic in the "public" zone.

    sudo firewall-cmd --add-service=http --permanent
    sudo firewall-cmd --add-service=https --permanent

Get move verbose information on the current zone,
and show that the above commands worked.

    $ sudo firewall-cmd --list-all
	public (active)
	  target: default
	  icmp-block-inversion: no
	  interfaces: enp0s25
      sources:
	  services: ssh dhcpv6-client http https
	  ports:
	  protocols:
      masquerade: no
	  forward-ports:
      source-ports:
	  icmp-blocks:
      rich rules:

Open port 8000 for JupyterHub.

    $ sudo firewall-cmd --zone=public  --permanent --add-port=8000/tcp
	success
	$ sudo firewall-cmd --list-ports
	8000/tcp
    $ sudo firewall-cmd --list-all
	public (active)
	  target: default
	  icmp-block-inversion: no
	  interfaces: enp0s25
      sources:
	  services: ssh dhcpv6-client http https
	  ports: 8000/tcp
	  protocols:
      masquerade: no
	  forward-ports:
      source-ports:
	  icmp-blocks:
      rich rules:

Restart the firewall.

    sudo systemctl restart firewalld

*References:*

* https://www.digitalocean.com/community/tutorials/how-to-set-up-a-firewall-using-firewalld-on-centos-7
* https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/security_guide/sec-using_firewalls#sec-Introduction_to_firewalld
* https://www.linode.com/docs/web-servers/apache/install-and-configure-apache-on-centos-7
* https://stackoverflow.com/questions/24729024/centos-7-open-firewall-port


## Web server

When installing CentOS 7,
I set up the internets as well as my hostname.

    $ hostname
	siwenna.colorado.edu

Install Apache.

    sudo yum install httpd

The configuration lives in **/etc/httpd/conf** and
document root is **/var/www/html**.
The only change in the **httpd.conf** file is

    ServerName siwenna.colorado.edu:80

Start the web server with:

    sudo systemctl start httpd

And set it to start on boot:

    sudo systemctl enable httpd.service

I can now access http://siwenna.colorado.edu.
Access to https://siwenna.colorado.edu is also allowed,
but the certificates used are those provided on install,
and aren't valid,
so the web browser issues a warning.
To get valid certificates,
I used [Certbot](https://certbot.eff.org/#centosrhel7-apache).

Results:
> - Congratulations! Your certificate and chain have been saved at:
   /etc/letsencrypt/live/siwenna.colorado.edu/fullchain.pem
   Your key file has been saved at:
   /etc/letsencrypt/live/siwenna.colorado.edu/privkey.pem
   Your cert will expire on 2018-01-23. To obtain a new or tweaked
   version of this certificate in the future, simply run certbot
   again. To non-interactively renew *all* of your certificates, run
   "certbot renew"
> - Your account credentials have been saved in your Certbot
   configuration directory at /etc/letsencrypt. You should make a
   secure backup of this folder now. This configuration directory will
   also contain certificates and private keys obtained by Certbot so
   making regular backups of this folder is ideal.

I added the paths to these certs to **ssl.conf**.
Note that the certs expire in 90 days.

I set up a daily `cron` job to request new certs from Certbot.
It Does The Right Thing and checks to see whether I actually
need new certs. If not, it returns.

From ***solaria***, I can see that this certificate is being used,
as well as its start and end dates:

    curl https://siwenna.colorado.edu --verbose

### Disable SSLv3

I got a nastygram from CU stating that SSLv3 had to be disabled
else I'm open to a POODLE attack.
Sounds terrible.
In **/etc/httpd/conf.d/ssl.conf**,
I changed `SSLProtocol` to

    SSLProtocol all -SSLv2 -SSLv3

I then checked this from ***solaria***.

```bash
$ openssl s_client -connect 128.138.87.12:443 -ssl3
CONNECTED(00000003)
140735227875408:error:14094410:SSL routines:ssl3_read_bytes:sslv3 alert handshake failure:s3_pkt.c:1500:SSL alert number 40
140735227875408:error:1409E0E5:SSL routines:ssl3_write_bytes:ssl handshake failure:s3_pkt.c:659:
---
no peer certificate available
---
No client certificate CA names sent
---
SSL handshake has read 7 bytes and written 0 bytes
---
New, (NONE), Cipher is (NONE)
Secure Renegotiation IS NOT supported
Compression: NONE
Expansion: NONE
No ALPN negotiated
SSL-Session:
    Protocol  : SSLv3
    Cipher    : 0000
    Session-ID:
    Session-ID-ctx:
    Master-Key:
    Key-Arg   : None
    PSK identity: None
    PSK identity hint: None
    SRP username: None
    Start Time: 1519333402
    Timeout   : 7200 (sec)
    Verify return code: 0 (ok)
---
```

The `ssl handshake failure` line means it's disabled.

*Reference:*

* https://www.centos.org/forums/viewtopic.php?t=49029


## SSH access

Shortly after installing CentOS 7 and getting ***siwenna*** accessible
outside CU's VPN, I noticed a stream of attempted root logins
(several per minute) from IP addresses in China. Hackers!
See the log file, **/var/log/secure**.

I added a rule to **/etc/hosts.allow** to allow only logins
from CU:

    sshd : 128.138. : allow

and a complimentary rule to **/etc/hosts.deny**

    sshd : ALL

Hopefully this will only be temporary.

**Update:**
I think I have a better solution.
Edit **/etc/ssh/sshd_config**,
setting

    PermitRootLogin no

Then restart sshd:

    sudo systemctl restart sshd

This should disable remote root logins.
See the Redhat link below.

*References:*

* https://serverfault.com/a/345541
* https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/4/html/Security_Guide/s2-wstation-privileges-noroot.html


## Users and groups

This doesn't quite fit in this page,
but I have to do it often enough that I'll include it.

Make a new user and give them a password:

    sudo adduser saganaut
	sudo passwd saganaut

BY default, each user is included in an eponymous group.
To include the user in another group:

    sudo usermod -aG csdms saganaut

Including the user in the `wheel` group grants them sudo privileges.

*References:*

* https://www.digitalocean.com/community/tutorials/how-to-add-and-delete-users-on-a-centos-7-server
* https://www.digitalocean.com/community/tutorials/how-to-create-a-sudo-user-on-centos-quickstart


# CentOS 6

From 2013-2017,
***siwenna*** run CentOS 6.
It started at 6.5 and ended at 6.9.
In October 2017,
I upgraded ***siwenna*** to CentOS 7.
The following sections were obsoleted by this upgrade.


## Emacs

Emacs isn't installed by default.
I downloaded the source tarball, installed it to **~/local**,
and added it to `PATH`:

    $ PATH=~/local/bin:$PATH


## Dev tools

Need to install gcc, autoconf, automake, etc.:

	$ sudo yum groupinstall development


## Login service

I started `sshd` so I can login from ***anacreon*** and ***solaria***:

	$ sudo /sbin/service sshd status
	$ sudo /sbin/service sshd start

I also made sure the daemon starts on boot. Set this up with

	$ sudo system-config-services

See also:

[http://www.techotopia.com/index.php/Configuring_CentOS_Remote_Access_using_SSH](http://www.techotopia.com/index.php/Configuring_CentOS_Remote_Access_using_SSH)

Set the "-X" option to enable X forwarding:

	$ ssh -X mapi8461@siwenna.colorado.edu


## Web server

I installed apache (package name is `httpd`).
Configuration in **/etc/httpd/conf** and
document root in **/var/www/html**.

Start web server with:

	$ sudo /sbin/service httpd start

And set it to start on boot (see sshd above).

Can now access http://localhost.

Instructions for setting up webserver: 

* http://ostechnix.wordpress.com/2013/03/05/install-apache-webserver-in-centos-6-3-scientific-linux-6-3-rhel-6-3/

It works! See http://siwenna.colorado.edu.
Need VPN to access.

I haven't yet set up my **public_html/** directory.
See: 

* http://www.coreymaynard.com/blog/centos-serving-home-directory-with-apache

I disabled stoopid SELinux:
edit **/etc/sysconfig/selinux**.


### Setting up HTTPS

See instructions at:

* https://wiki.centos.org/HowTos/Https

Make sure the old **ca.*** files are replaced.

Chrome, e.g., complains about the certificate,
but it works: https://siwenna.colorado.edu.


## Firewall

CentOS 6 uses `iptables` to configure and manage its firewall.
Access it through `/sbin/service` with:

    sudo /sbin/service iptables status

Also `stop`, `start`, `restart`, and `save`.
Save is important because modifications to the firewall don't persist.
(This is also a good thing because you can experiment,
then restart to reset any changes.)

Rules in iptables are executed sequentially.
There's a reject rule at the bottom of the input table.
Insert new rules above this rule.
For example, allow HTTPS:

    iptables -I INPUT 6 -p tcp --dport 443 -j ACCEPT

Open a range of ports:

    iptables -I INPUT 6 -p tcp --dport 8000:8100 -j ACCEPT

The flags are
`A` = append,
`I` = insert (at top, by default),
`D` = delete.

See also:

* https://wiki.centos.org/HowTos/Network/IPTables
* https://stackoverflow.com/a/19034727/1563298
* https://serverfault.com/a/594846
* https://stackoverflow.com/a/10197461/1563298


## Newer versions of packages

CentOS, like RHEL, values stability, so many of its packages are old.

### Python 2.7

Use anaconda,
or follow [this article](https://www.digitalocean.com/community/tutorials/how-to-set-up-python-2-7-6-and-3-3-3-on-centos-6-4) [digitalocean.com].

### CMake > 2.8

[Dakota](./dakota.md) requires CMake 2.8.9 or higher.

I [downloaded](http://www.cmake.org/files/v2.8/) CMake 2.8.9 and installed with

	$ ./bootstrap --prefix=/usr/local
	$ make
	# make install

I used `/usr/local` so as not to conflict with the CentOS 6.4 version
(cmake 2.6.4).

### Subversion 1.7

I enabled the `rpmforge-extras` repo:

	$ sudo yum-config-manager --enable rpmforge-extras

and used it to install version 1.7.
It successfully removed 1.6,
which is the CentOS default.
