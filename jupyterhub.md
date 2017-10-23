# JupyterHub

My experience in installing, setting up, and running
[JupyterHub](https://github.com/jupyterhub/jupyterhub).


## Installing JupyterHub on ***siwenna***

Use the Python 3 installation in **/usr/local/anaconda3**.
(Note that I've set "mpiper:csdms" as the user and group owners.)

    PATH=/usr/local/anaconda3/bin:$PATH

JupyterHub requires `python>=3.4`.

Install `npm` and `nodejs`.

    sudo yum install npm nodejs

This also installs the `node` executable.

Install HTTP proxy and dependencies.

    sudo npm install -g inherits@'2'
	sudo npm install -g configurable-http-proxy

Install JupyterHub.

    pip install jupyterhub

The current version is 0.8.
This also retrieves and installs some dependencies.

Update Jupyter Notebook.

    pip install --upgrade notebook

This installed a slew of dependencies.

Create a py27 environment with a full Anaconda.
This should include `notebook` and `ipykernel`.

    conda create -n py27 python=2.7 anaconda

Activate it:

    source activate py27

Register the Python 2 kernel in the Python 3 distro.

    (py27)$ python -m ipykernel install --prefix=/usr/local/anaconda3 --name 'python2'
    Installed kernelspec python2 in /usr/local/anaconda3/share/jupyter/kernels/python2

For reference, view the two kernelspec files.

```bash
$ cd anaconda3/share/jupyter/kernels
$ cat python3/kernel.json
{
 "argv": [
   "python",
   "-m",
   "ipykernel_launcher",
   "-f",
   "{connection_file}"
 ],
 "display_name": "Python 3",
 "language": "python"
}

$ cat python2/kernel.json
{
 "display_name": "Python 2",
 "language": "python",
 "argv": [
   "/usr/local/anaconda3/envs/py27/bin/python",
   "-m",
   "ipykernel_launcher",
   "-f",
   "{connection_file}"
  ]
}
```

**References**

* https://jupyterhub.readthedocs.io
* https://ipython.readthedocs.io/en/stable/install/kernel_install.html#kernels-for-different-environments


## Configuring and running JupyterHub on ***siwenna***

As a test, run JupyterHub as a (nonprivileged) single-user server.

    jupyterhub

From a browser running locally on ***siwenna***,
I can log in with my PAM credentials at http://127.0.0.1:8000
and get a Jupyter Notebook.
Note that I had to modify the firewall
to open port 8000.

To allow others to log in,
run JupyterHub as root.
Executing

    $ su -
	# PATH=/usr/local/anaconda3/bin:$PATH
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

	# jupyterhub --ip 128.138.87.12 --ssl-key /etc/letsencrypt/live/siwenna.colorado.edu/privkey.pem --ssl-cert /etc/letsencrypt/live/siwenna.colorado.edu/cert.pem --debug

allows me to log in
at https://siwenna.colorado.edu:8000,
and they can no longer access the site via HTTP.
Yay!

Use a JupyterHub configuration file instead of command line args.
Create a config file with

    # jupyterhub --generate-config

Install a cookie secret file in **/srv/jupyterhub** with

    # openssl rand -hex 32 > /srv/jupyterhub/cookie_secret

and add this to the `cookie_secret_file` attribute
of the config file.
Be sure to set permissions of 600 (u=rw) on this file.

Generate a 32-character hex string with `openssl`
to use as the proxy authentication token.
Set it as the `ConfigurableHTTPProxy.auth_token` attribute
of the config file.

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


## Configuring and running JupyterHub on ***diluvium***

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
