# Kubernetes

See also my notes on [helm](./helm.md) and [jupyterhub](./jupyterhub.md).

## Explore with *minikube*

[minikube](https://minikube.sigs.k8s.io/docs/) is local Kubernetes
intended for learning, testing, and development.

I installed *minikube* on ***solaria*** and ***terminus*** through Homebrew:
```bash
brew install minikube
``` 
This also installs the *kubernetes-cli* package as a dependency.

I've also had [success](https://github.com/csdms/jupyterhub-management/blob/main/z2jh/minikube.yml) installing these packages through *conda*.

*minikube* needs a [driver](https://minikube.sigs.k8s.io/docs/drivers/) to work.
[Docker](https://minikube.sigs.k8s.io/docs/drivers/docker/#Standard%20Docker) is one of the driver choices.
I have Docker Desktop installed, so I started it.
Note that `kubectl` is installed through Docker Desktop (though possibly also through *kubernetes-cli*?).

Start a cluster with two nodes,
using two CPUs and 2 Gi of memory:
```bash
minikube start --kubernetes-version stable --nodes 2 --cpus 2 --memory 2g --cni calico --namespace z2jh
```
I gave the cluster a namespace I can refer to later.

Many messages follow, ending with:
```
🏄  Done! kubectl is now configured to use "minikube" cluster and "z2jh" namespace by default
```

Check that the cluster is initialized:
```bash
kubectl get node
```
which prints:
```
NAME           STATUS   ROLES           AGE   VERSION
minikube       Ready    control-plane   35m   v1.33.1
minikube-m02   Ready    <none>          35m   v1.33.1
```
There's also `minikube status` which returns similar information.

Note that `kubectl get ns` still reports the namespace as "default", not "z2jh":
```
NAME              STATUS   AGE
default           Active   107m
kube-node-lease   Active   107m
kube-public       Active   107m
kube-system       Active   107m
```
I'm not sure why.

Stop the cluster:
```bash
minikube stop
```

Delete all clusters:
```bash
minikube delete --all
```

*References*:

* minikube start: https://minikube.sigs.k8s.io/docs/start

## Google Kubernetes Engine (GKE)

Login through CloudBank.
Google set up a new mark.piper@cloudbank.org profile for me.
Use it to login through https://console.cloud.google.com/.

Enabled Kubernetes Engine API.

Initially, I installed Google Cloud CLI (which includes `gcloud`) locally through conda
(see [gke.yml](https://github.com/csdms/jupyterhub-management/blob/main/z2jh/gke.yml)).
(Note that it's also available through Homebrew.)
However, this didn't quite work: there was a problem with authentication.
It seems to be better to install [from source](https://cloud.google.com/sdk/docs/install).
I placed the distribution in my `~/Applications` directory.
I activate it in a zsh shell (setting paths and command completion)
with a [script](https://github.com/csdms/jupyterhub-management/blob/main/z2jh/activate_gcloud)
I adapted from Google:
```bash
source activate_gcloud
```
The script has to be in the path (e.g., `~/bin`) for this syntax to work.

Ran `gcloud init` for authentication and configuration.

* Project: nsf-2148762-333330
* Zone: us-central1-c
* Region: us-central1
* Council Bluffs, Iowa
* Low CO2

Configuration saved in `~/.boto`.
Do this on all local machines where I'll be using `gcloud`.
The config is persistent, so I don't have to run `gcloud init` again.

Kubernetes clients like `kubectl` require an authentication plugin, *gke-gcloud-auth-plugin*.
Install it with:
```bash
gcloud components install gke-gcloud-auth-plugin
```
Check that it has been installed correctly:
```bash
gke-gcloud-auth-plugin --version
```

Also install `kubectl` through `gcloud` to match versions:
```bash
gcloud components install kubectl
```
It's likely `kubectl` is already installed elsewhere on the path,
such as through Docker.
A `rehash` may be needed after install.

Check the install:
```bash
which kubectl
kubectl version
```
This gives:
```console
/Users/mpiper/Applications/google-cloud-sdk/bin/kubectl
Client Version: v1.32.4-dispatcher
Kustomize Version: v5.5.0
Server Version: v1.33.2-gke.1043000
```
(Note that I ran this while I had a cluster running;
"Server Version" wouldn't have a response otherwise.)

Create a k8s cluster with two nodes.
```bash
gcloud container clusters create \
  --machine-type n1-standard-2 \
  --num-nodes 2 \
  --zone us-central1-c \
  --preemptible \
  --cluster-version latest \
  milwaukee
```
where
* *n1-standard-2* is a basic machine type with 2 vCPUs and 7.5 GB of memory (more on the [N1 standard](https://cloud.google.com/compute/docs/general-purpose-machines#n1_machine_types) machine type)
* preemptible nodes are [heavily discounted](https://cloud.google.com/compute/docs/instances/preemptible)
* *milwaukee* is the cluster name

This command took about 5 minutes to run.
It successfully created a cluster,
returning this kubeconfig entry:
```console
NAME       LOCATION       MASTER_VERSION      MASTER_IP      MACHINE_TYPE   NODE_VERSION        NUM_NODES  STATUS
milwaukee  us-central1-c  1.33.2-gke.1043000  34.46.137.217  n1-standard-2  1.33.2-gke.1043000  2          RUNNING
```

List all running clusters:
```bash
gcloud container clusters list
```
This prints the above kubeconfig entry.
I can also view my [Clusters](https://console.cloud.google.com/kubernetes/list/overview) on the web.

View details about the running cluster:
```bash
gcloud container clusters describe milwaukee
```

To enable `kubectl` with the cluster, get credentials from the server:
```bash
gcloud container clusters get-credentials milwaukee --location=us-central1-c
```
Check that the credentials work:
```bash
kubectl get namespaces
```
which gives:
```console
NAME                          STATUS   AGE
default                       Active   4m26s
gke-managed-cim               Active   2m27s
gke-managed-system            Active   2m12s
gke-managed-volumepopulator   Active   2m8s
gmp-public                    Active   110s
gmp-system                    Active   110s
kube-node-lease               Active   4m26s
kube-public                   Active   4m27s
kube-system                   Active   4m27s
```

When I install an application (such as a JupyterHub) into this cluster,
I can view it with
```bash
kubectl get pod
```
I can also view my
[Workloads](https://console.cloud.google.com/kubernetes/workload/overview) on the web.
(Check the "proxy" workload to get the public IP address of the running Hub.)

*References*:

* GKE instructions from z2jh: https://z2jh.jupyter.org/en/stable/kubernetes/google/step-zero-gcp.html
* [Kubernetes Engine API](https://console.cloud.google.com/apis/api/container.googleapis.com/overview)
* [Google Cloud CLI](https://cloud.google.com/sdk/docs/install)
* [Available regions and zones](https://cloud.google.com/compute/docs/regions-zones/#available)
* [Machine families resource and comparison guide](https://cloud.google.com/compute/docs/machine-resource)
  * [General-purpose machine family for Compute Engine](https://cloud.google.com/compute/docs/general-purpose-machines)
* [Managing clusters](https://cloud.google.com/kubernetes-engine/docs/how-to/managing-clusters)

### Connect to remote cluster

If the cluster *milwaukee* is not present locally,
but it is running in the cloud,
connect to it with:
```bash
gcloud container clusters get-credentials milwaukee --location=us-central1-c
```

Check with:
```bash
gcloud container clusters list
kubectl config get-clusters
```

### Delete a cluster

Delete the cluster *milwaukee*:
```bash
gcloud container clusters delete milwaukee
```

*References*:

* [Deleting a cluster](https://cloud.google.com/kubernetes-engine/docs/how-to/deleting-a-cluster)

## Managing multiple clusters

Say I have two k8s clusters, *milwaukee* and *madison*,
and I want to use `helm` to install an application onto one of them.
`helm` doesn't have the facility to choose clusters;
instead, we have to set the current context through `kubectl`,
then `helm` will install into it.

See what contexts are available through `kubectl`:
```bash
kubectl config get-contexts
```
This returns, for example:
```console
CURRENT   NAME              CLUSTER           AUTHINFO           NAMESPACE
*         bblah-madison     bblah-madison     bblah-madison     
          bblah-milwaukee   bblah-milwaukee   bblah-milwaukee
```
(The names are typically much longer and mangled depending on the cloud provider.)

Show that *madison* is the current context:
```bash
kubectl config current-context  # madison
```

Switch the current context to *milwaukee*:
```bash
kubectl config use-context bblah-milwaukee
```
and check the result:
```bash
kubectl config current-context  # milwaukee
```

For more detail, try:
```bash
kubectl config view
```
This shows all the info in the kubeconfig file.

`helm` can now be used to install an application into *milwaukee*.
