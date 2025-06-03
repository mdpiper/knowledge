# Kubernetes

## Explore with *minikube*

[minikube](https://minikube.sigs.k8s.io/docs/) is local Kubernetes
intended for learning, testing, and development.

I installed *minikube* on ***solaria*** through Homebrew:
```bash
brew install minikube
``` 
This also installs the *kubernetes-cli* package,
so I get `kubectl`.

*minikube* needs a [driver](https://minikube.sigs.k8s.io/docs/drivers/) to work.
[Docker](https://minikube.sigs.k8s.io/docs/drivers/docker/#Standard%20Docker) is one of the driver choices.
I have Docker Desktop installed, so I started it.

Start a cluster with two nodes,
using two CPUs and 2 Gi of memory:
```bash
minikube start --kubernetes-version stable --nodes 2 --cpus 2 --memory 2000 --cni calico
```

Many messages follow, ending with:
```
🏄  Done! kubectl is now configured to use "minikube" cluster and "default" namespace by default
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

