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

Installed Google Cloud CLI (includes `gcloud`) locally through conda
(see [gke.yml](https://github.com/csdms/jupyterhub-management/blob/main/z2jh/gke.yml)).
Also available through Homebrew.

Ran `gcloud init` for authentication and configuration.

* Project: nsf-2148762-333330
* Zone: us-central1-c
* Region: us-central1
* Council Bluffs, Iowa
* Low CO2

Configuration saved in `~/.boto`.
Do this on all local machines where I'll be using `gcloud`.

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

*References*:

* GKE instructions from z2jh: https://z2jh.jupyter.org/en/stable/kubernetes/google/step-zero-gcp.html
* [Kubernetes Engine API](https://console.cloud.google.com/apis/api/container.googleapis.com/overview)
* [Google Cloud CLI](https://cloud.google.com/sdk/docs/install)
* [Available regions and zones](https://cloud.google.com/compute/docs/regions-zones/#available)
* [Machine families resource and comparison guide](https://cloud.google.com/compute/docs/machine-resource)
  * [General-purpose machine family for Compute Engine](https://cloud.google.com/compute/docs/general-purpose-machines)

