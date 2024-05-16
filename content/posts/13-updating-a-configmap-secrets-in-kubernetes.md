+++
author = "Zakatell Kanda"
categories = ["technology", "software", "kubernetes", "configmap", "secrets"]
date = 2018-03-06T02:45:18Z
description = ""
draft = false
showDate = true
slug = "updating-a-configmap-secrets-in-kubernetes"
tags = ["technology", "software", "kubernetes", "configmap", "secrets"]
title = "updating a configmap/secrets in kubernetes"

+++

At work, we started using Kubernetes for stateless application. We frequently change the values of configmap/secrets especially in staging environments, however, this is still not baked in to k8s.

How to do it?

You have to replace a config to update it.

For example, if you create a configmap like this:

```sh
kubectl create configmap site-config --from-file=nginx.conf
```

If you want to update that, you have to add the command above with 

* `--dry-run -o yaml | kubectl replace -f -`

So it would become like this:

```sh
kubectl create configmap site-config --from-file=nginx.conf \
    --dry-run -o yaml | kubectl replace -f -
```

But we are not done yet, in order for those configs to be visible in the running containers that need them, we have to do a new deployment.

The easiest way to do that is to patch the container. Say our deployment service is called `nginx`.

```sh
kubectl patch deployment nginx -p "{\"spec\":{\"template\":{\"metadata\":{\"annotations\":{\"date\":\"`date +'%s'`\"}}}}}"
```

This should issue a new deployment rollout and the changes in configmap are now propagated on the new containers.

I hope this helps you on your journey to Kubernetes, it's a different way of thinking than previous ops but its a step forward in my opinion.
