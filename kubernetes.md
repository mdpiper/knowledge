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
