# JupyterHub

My experience in installing, setting up, and running
[JupyterHub](https://github.com/jupyterhub/jupyterhub).


## Installing JupyterHub on ***siwenna***

Use the Python 3 installation in **/usr/local/anaconda3**.

    PATH=/usr/local/anaconda3/bin:$PATH

JupyterHub requires Python >=3.4.

Install `npm` and `nodejs`.

    sudo yum install npm nodejs

This also installs the `node` executable.

Install HTTP proxy and dependencies.

    sudo npm install -g inherits@'2'
	sudo npm install -g configurable-http-proxy

Install JupyterHub.

    sudo /usr/local/anaconda3/bin/pip install jupyterhub

This also retrieves and installs some dependencies.

Update Jupyter Notebook.

    sudo /usr/local/anaconda3/bin/pip install --upgrade notebook

This installed a slew of dependencies.


## Configuring and running JupyterHub on ***siwenna***

The
[Getting Started](https://jupyterhub.readthedocs.io/en/0.7.2/getting-started.html)
document for version 0.7.2 (which is what I installed on ***siwenna***)
was very helpful.

As a test, run JupyterHub as a (nonpriviledged) single-user server.

    jupyterhub

Now, from a browser running locally on ***siwenna***,
I can log in with my PAM credentials at http://127.0.0.1:8000
and get a Jupyter Notebook.
Note that I had to modify the firewall
to open port 8000.

To allow others to log in,
run JupyterHub as root.
Executing

    $ su -
    # jupyterhub --ip 128.138.87.12 --debug

allows me to log in
at http://128.138.87.12:8000.
Jupyter Notebook is started in my home directory.
(This works even when the firewall is enabled.)
This command also allows me to log in
at http://siwenna.colorado.edu:8000.
Yay!

I want to support HTTPS.
Executing

	# jupyterhub --ip 128.138.87.12 --ssl-key /etc/pki/tls/private/ca.key --ssl-cert /etc/pki/tls/certs/ca.crt --debug

allows me to log in
at https://siwenna.colorado.edu:8000,
although the user has to accept my hinky, self-generated, certificate,
and they can no longer access the site via HTTP.
Yay!

Use a JupyterHub configuration file instead of command line args.
Create a config file with

    jupyterhub --generate-config

Install a cookie secret file in **/srv/jupyterhub** with

    openssl rand -base64 2048 > /srv/jupyterhub/cookie_secret

and add this to the `cookie_secret_file` attribute
of the config file.
Be sure to set permissions of 600 (u=rw) on this file.

Generate a 32-character hex string with `openssl`
to use as the proxy authentication token.
Set it as the `proxy_auth_token` attribute of the config file.

Place **jupyterhub_config.py** in **/etc/jupyterhub**
and call JupyterHub with

    # jupyterhub -f /etc/jupyterhub/jupyterhub_config.py


### Think about

* Can I start JupyterHub as a service when ***siwenna*** starts?
See https://github.com/jupyterhub/jupyterhub/wiki/Run-jupyterhub-as-a-system-service.
* It would be great to use the
[OAuthenticator](https://github.com/jupyterhub/oauthenticator)
so that people could log in with, e.g.,
their GitHub or Google (or CSDMS!) credentials.
* Can a maximum number of users be set?
* Can we logout/cull inactive users?
* Can I use Let's Encrypt to get certs from a real CA?
No -- ***siwenna*** is behind CU's VPN.
* Should we move ***siwenna*** out from behind the VPN?


## Installing JupyterHub on ***diluvium***

I'm following installation instructions at
https://github.com/jupyterhub/jupyterhub
for the latest version, 0.8.
Unless noted, all commands are executed as me.

Install Python 3 in **/data/anaconda3**.

    chmod +x Anaconda3-4.4.0-Linux-x86_64.sh
    sudo ./Anaconda3-4.4.0-Linux-x86_64.sh -b -p /data/anaconda3

(Don't use **/usr/local** because OIT checks it.)

Download a Node.js binary (LTS)
from https://nodejs.org/en/download/,
uncompress,
place in **/data/node-v6.11.4-linux-x64**,
and create a symlink, `node.js`, to it.
This gives us both `node` and `npm`.

Update path with

    PATH=/data/anaconda3/bin:/data/node.js/bin:$PATH

Install the HTTP proxy server with

    npm install -g configurable-http-proxy

Install JupyterHub (v0.8) with

    pip install jupyterhub

This also installed several dependencies.

Upgrade the `notebook` package:

    pip install --upgrade notebook

This also installed several dependencies.

Start the Hub server for myself:

    jupyterhub

Using a browser on ***diluvium***,
I can log into http://localhost:8000
with my PAM credentials.

Using `iptables`, I opened port 8000.

    sudo iptables -I INPUT 29 -p tcp --dport 8000 -j ACCEPT
    sudo /sbin/service iptables save

Now, when running as a privileged user

	su -
    /data/anaconda3/bin/jupyterhub --ip 128.138.70.88 --debug

I can access http://csdms.colorado.edu:8000 externally.
(Note that the IP address 128.138.70.87
corresponds to diluvium.colorado.edu.
We wish to run through csdms.colorado.edu.)

With some sleuthing,
I found the TLS certs on ***diluvium***,
so I can use HTTPS with

    sudo /data/anaconda3/bin/jupyterhub --ip 128.138.70.88 --ssl-key /usr/local/adm/config/openssl/certs/key/csdms.colorado.edu.key --ssl-cert /usr/local/adm/config/openssl/certs/crt/csdms.colorado.edu.crt --debug

Create a JupyterHub config file.

    jupyterhub --generate_config

Add the TLS certs to the `ssl_key` and `ssl_cert` attributes
of the config file.

Install a cookie secret file in **/data/anaconda3/etc/jupyter/hub** with

    openssl rand -hex 32 > /data/anaconda3/etc/jupyter/hub/cookie_secret

and add this to the `cookie_secret_file` attribute
of the config file.
Be sure to set permissions of 600 (u=rw) on this file,
and set owner to root.

Generate a 32-character hex string with `openssl`
to use as the proxy authentication token.
(This changed from v0.7.2.)
Set it as the `proxy_auth_token` attribute of the config file.

Place **jupyterhub_config.py** in **/etc/jupyter/hub**
and call JupyterHub with

    # jupyterhub -f /etc/jupyter/hub/jupyterhub_config.py
