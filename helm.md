# Helm

*Helm* is the package manager for Kubernetes.
Helm packages are called *charts*.

See also my notes on [kubernetes](./kubernetes.md) and [jupyterhub](./jupyterhub.md).



## Installation

I installed *helm* with Homebrew:
```bash
brew install helm
```

I've also installed *helm* (and *kubernetes*) through conda:
```bash
conda install -c conda-forge kubernetes-helm kubernetes
```

## Install a chart from a repository

For this example, I'll install [Grafana](https://grafana.com/) into an existing Kubernetes cluster.

Start by adding the Grafana chart repository to Helm,
making sure to get the latest list of charts available through it:
```sh
helm repo add grafana https://grafana.github.io/helm-charts
helm repo update
```

List the available Helm repositories I've added:
```bash
helm repo list
```

List the charts available to install through the Grafana chart repository:
```sh
helm search repo grafana
```

Install the chart for Grafana with the name *test*:
```sh
helm install test grafana/grafana
```

List deployed Helm releases:
```bash
helm list
```

To uninstall/delete the *test* deployment:
```sh
helm delete test
```

*References:*

* Helm Quickstart: https://helm.sh/docs/intro/quickstart/
* Grafana Helm chart: https://artifacthub.io/packages/helm/grafana/grafana
