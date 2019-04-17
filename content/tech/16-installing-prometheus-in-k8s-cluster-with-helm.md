+++
author = "Zakatell Kanda"
date = 2018-09-04T04:10:21Z
description = ""
draft = false
slug = "installing-prometheus-in-k8s-cluster-with-helm"
title = "installing prometheus in k8s cluster with helm"

+++

Prerequisite:
A cluster with RBAC enabled.
Have enough capacity in the cluster
Install Helm:

```sh
kubectl create serviceaccount tiller --namespace kube-system
kubectl create clusterrolebinding tiller-rb --clusterrole=cluster-admin --serviceaccount=kube-system:tiller
helm init --service-account tiller
```

Get the values.yaml from [here](https://github.com/helm/charts/blob/master/stable/prometheus/values.yaml)
Edit some things that you want to tweak, for us, we change the CPU limit to a lower value.
Then just install Prometheus:

```sh
helm install -f values.yaml --name prometheus stable/prometheus
```

Ideally, this should be installed in another namespace but we will keep it simple for now. After the installation you will have this new pods running in your default workspace:

- alertmanager
- kube-state-metrics
- node-exporter
- pushgateway
- prometheus-server

The server will automatically discover your cluster and you will have a whole bunch of metrics available, it can be overwhelming so don't pay attention too much about it.

In the next post: We will configure alertmanager to send alerts for basic things like CPU, Memory and Disk usage. At least this is our initial goal why we want to use Prometheus.