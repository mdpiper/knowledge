# JupyterHub

My experience with installing, setting up, and running
[JupyterHub](https://jupyterhub.readthedocs.io/en/stable/).

The configuration files for Jupyter at CSDMS can be found
[here](https://github.com/csdms/jupyterhub-management).

See also my notes on [kubernetes](./kubernetes.md) and [helm](./helm.md).

## Zero to JupyterHub (z2jh)

[Zero to JupyterHub with Kubernetes](https://z2jh.jupyter.org/en/stable/index.html) is a way to set up a JupyterHub on a cloud or local environment and leverage its scalable nature to support a large group of users.

A running k8s cluster (call it "csdms") is required for the following commands.
If more than one cluster is present,
set the activate cluster with
```bash
kubectl config use-context <long prefix>_csdms  # only needed to switch clusters
```
where the long context name is returned from `kubectl config get-contexts`.

Add the repository of JupyterHub Helm charts:
```bash
helm repo add jupyterhub https://hub.jupyter.org/helm-chart/
helm repo update
```

Elsewhere, I've set up a [Helm chart configuration file](#customizing-a-z2jh-deployment) to customize my JupyterHub
(see [config.yaml](https://github.com/csdms/jupyterhub-management/blob/main/z2jh/config.yaml)).
Install JupyterHub
from a Helm chart
into the active k8s cluster
with my configuration values
using:
```bash
helm upgrade --cleanup-on-fail --install experiment jupyterhub/jupyterhub --namespace default --create-namespace --version "4.2.0" --values config.yaml
```
where

* `experiment` is my choice for the [Helm release name](https://helm.sh/docs/glossary/#release), and
* `jupyterhub/jupyterhub` is the chart name,
* `"4.2.0"` is the [version of JupyterHub Helm chart](https://hub.jupyter.org/helm-chart/) to use.

This command takes a little while to run and the prompt is unresponsive.

Once the install is complete, verify the pods are running:
```bash
kubectl get pod --namespace=default
```

If a pod keeps restarting, diagnose the issue with:
```bash
kubectl logs --namespace=default --previous <name of pod>
```

> [!NOTE]  
> I actually used this command! See the subsection below.

Check for the public IP address:
```bash
kubectl --namespace=default describe service proxy-public
```
I don't have this locally on ***solaria***.
Instead, forward the port:
```bash
kubectl --namespace=default port-forward service/proxy-public 8080:http
```

The Hub is running at
http://localhost:8080!

*References:*

* [Zero to JupyterHub with Kubernetes](https://z2jh.jupyter.org/en/stable/index.html)
* [Setup JupyterHub](https://z2jh.jupyter.org/en/stable/jupyterhub/index.html#setup-jupyterhub)
* The [GitHub repository](https://github.com/jupyterhub/zero-to-jupyterhub-k8s) with sources for the JupyterHub Helm chart

### Customizing a z2jh deployment

Use a configuration file (yaml) to customize the options presented in a Helm chart.

Here's the `config.yaml` I used to load the [CSDMS JupyterHub image](https://hub.docker.com/repository/docker/csdms/jupyterhub/general) instead of the default JupyterHub image.
```yaml
# JupyterHub Helm chart configuration

hub:
  db:
    type: sqlite-memory

singleuser:
  storage:
    type: none
  image:
    name: csdms/jupyterhub
    tag: 0.1.0
  cmd: null
```

*References:*

* [Customizing your Deployment](https://z2jh.jupyter.org/en/stable/jupyterhub/customizing/extending-jupyterhub.html) [z2jh.jupyter.org]
* [Configuration Reference](https://z2jh.jupyter.org/en/stable/resources/reference.html) [z2jh.jupyter.org]

### Local persistent user storage issue

The Hub pod kept crashing when I tried to run the example above. It's apparently a permissions issue; a database file can't be written, probably because I'm running Docker as root on ***solaria***.
A description of the problem and a possible fix are given in the reference below.

I modified my `config.yaml` to not create persistent user storage.
I then ran a `helm upgrade`:
```bash
helm upgrade --cleanup-on-fail experiment jupyterhub/jupyterhub --namespace csdms --version "4.2.0" --values config.yaml
```
Now the Hub pod starts.

*References:*

* https://discourse.jupyter.org/t/sql-operationalerror-with-jh-default-config/12207/1

## Jupyter Docker Stacks

These are a set of Docker images containing Project Jupyter applications and other software.
I'm particularly interested in the [scipy-notebook](https://jupyter-docker-stacks.readthedocs.io/en/latest/using/selecting.html#jupyter-scipy-notebook) image as a base for building a JupyterHub that includes CSDMS software.

Download an image:
```sh
docker pull quay.io/jupyter/scipy-notebook:python-3.12.11
```

Run a JupyterHub through a container:
```sh
docker run -p 8888:8888 quay.io/jupyter/scipy-notebook:python-3.12.11
```

Visiting `http://<hostname>:8888/?token=<token>` in a browser loads JupyterLab, where:

* `hostname` is the name of the computer running Docker
* `token` is the secret token printed in the console

Note that Project Jupyter moved from Docker Hub to [quay.io](https://quay.io) for hosting images.
I made a quay.io account, but I haven't used it yet; still using Docker Hub (for now, while it's free for my low-volume open-source project).

*References:*

* Documentation: https://jupyter-docker-stacks.readthedocs.io
* Repository: https://github.com/jupyter/docker-stacks
* Images: https://quay.io/organization/jupyter

## Installing JupyterHub on ***siwenna***

JupyterHub requires `python>=3.4`.
Use the Python 3 installation in **/home/csdms/anaconda3**.

    PATH=/home/csdms/anaconda3/bin:$PATH

(Note that I've set "mpiper:csdms" as the user and group owners,
and set `g+w` recursively.)

Install JupyterHub through `conda`.

    conda install jupyterhub -c defaults -c conda-forge

The current version is 0.8.1.


### Create an environment for the CSDMS stack

Create a minimal py27 environment for the CSDMS stack.

    conda create -n pymt python=2.7

Activate it:

    source activate pymt

Install the CSDMS stack, as well as `notebook` and `ipykernel`.

    (py27)$ conda install csdms-stack notebook ipykernel -c csdms-stack -c defaults -c conda-forge

Register the Python 2 kernel in the Python 3 distro.

	(py27)$ python -m ipykernel install --prefix=/home/csdms/anaconda3 --name 'pymt'

    Installed kernelspec pymt in /home/csdms/anaconda3/share/jupyter/kernels/pymt

For reference, view the two kernelspec files.

```bash
$ cd anaconda3/share/jupyter/kernels
$ cat python3/kernel.json
{
 "argv": [
   "/home/csdms/anaconda3/bin/python",
   "-m",
   "ipykernel_launcher",
   "-f",
   "{connection_file}"
 ],
 "display_name": "Python 3",
 "language": "python"
}

$ cat pymt/kernel.json
{
 "display_name": "pymt",
 "language": "python",
 "argv": [
   "/home/csdms/anaconda3/envs/pymt/bin/python",
   "-m",
   "ipykernel_launcher",
   "-f",
   "{connection_file}"
  ]
}
```

*References:*

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
	# PATH=/home/csdms/anaconda3/bin:$PATH
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

    # PATH=/home/csdms/anaconda3/bin:$PATH
    # jupyterhub -f /etc/jupyterhub/jupyterhub_config.py


### Login issue (resolved)

At first,
I was able to login once,
but subsequent logins were denied.
This problem is discussed in the JupyterHub issue listed below.
I found that setting

    c.PAMAuthenticator.open_sessions = False

in the JupyterHub config worked (fingers crossed).

Disabling SELinux is also a solution,
but not a very good one.
I had done this earlier, when ***siwenna*** was CentOS 6,
which is why I think I didn't encounter this problem earlier.

*Reference:*

* https://github.com/jupyterhub/jupyterhub/issues/323


### JupyterHub as a service

Ideally, we want JupyterHub to run as a service.
We can then control it with

    sudo systemctl [start|stop|status] jupyterhub

instead of having to log in as root.

Using the reference listed below,
we created a [jupyterhub.service](https://github.com/csdms/jupyterhub/blob/master/jupyterhub.service) file,
placed it in **/lib/systemd/system**,
and started a Hub with `systemctl`.

Automatically start a Hub on boot with

    sudo systemctl enable jupyterhub

*Reference:*

* https://github.com/jupyterhub/jupyterhub/wiki/Run-jupyterhub-as-a-system-service


### Cull inactive servers

Following the example in the JupyterHub docs (and referenced below),
I placed the `cull_active_servers` script in **/etc/jupyterhub**
and added it as a service.

*Reference:*

* https://github.com/jupyterhub/jupyterhub/tree/master/examples/cull-idle


### Updating TLS certificates

I use [Let's Encrypt](https://letsencrypt.org/)
to provide the certs (for running HTTPS)
on ***siwenna*** (see [my notes](./centos.md)).
These certs expire, and must be renewed, every 90 days.
After renewing the certs,
I need to restart JupyterHub.

    sudo systemctl restart jupyterhub


### Using JupyterLab with JupyterHub

Install JupyterLab through `conda`.

    conda install jupyterlab -c defaults -c conda-forge

The current version is 0.32.1.

To use JupyterLab by default,
edit the **jupyterhub_config.py** file:

    c.Spawner.default_url = '/lab'

Install the `jupyterlab-hub` extension with

    jupyter labextension install @jupyterlab/hub-extension

Update the config file to call jupyter-labhub

    c.Spawner.cmd = ['jupyter-labhub']

This adds the "Hub" menu to JupyterLab,
which allows you to view the server and logoff.

*References:*

* https://jupyterlab.readthedocs.io/en/stable/user/jupyterhub.html
* https://github.com/jupyterhub/jupyterlab-hub


### Think about

* It would be great to use the
[OAuthenticator](https://github.com/jupyterhub/oauthenticator)
so that people could log in with, e.g.,
their GitHub or Google (or CSDMS!) credentials.
* Can a maximum number of users be set?


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
