---
title: 自分用 kubectl チートシート for CKAD
date: 2021-03-25T16:00:52.000Z
categories:
  - Kubernetes
id: "26006613708476233"
draft: true
---
CKAD の対策にあたって、kubectl のチートシートが欲しくなったので、まとめる。

- Pod:
  - `kubectl run {pod name} --image={image name}:{image version} --restart=Never`
  - https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#run
- Deployment:
  - `kubectl create deploy {deployment name} --replicas={number of replicas} --image={image name}:}{image version}`
  - https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#-em-deployment-em-
- Service:
  - `kubectl expose pod {pod name} --port={service's port number} --target-port={container's port number} --name {service name} --type (ClusterIP|NodePort|LoadBalancer|ExternalName)`
  - `kubectl create service (clusterip|nodeport|loadbalancer|externalname)~~` もある。
  - https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#-em-service-em-
- ConfigMap
  - `kubectl create configmap {configmap name}`
    - `--from-literal={Key}={Value}`
    - `--from-file`
  - https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#-em-configmap-em-
- Secret
  - `kubectl create secret {secret name}`
