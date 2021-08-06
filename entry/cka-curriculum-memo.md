---
title: CKA Exam Curriculum v1.21 と Udemy『Kubernetes Certified Administrator (CKA) with Practice Tests』の対応
date: 2021-08-06T14:37:34.000Z
categories:
  - Kubernetes
id: "26006613794533820"
draft: false
---
[https://www.udemy.com/course/certified-kubernetes-administrator-with-practice-tests/:embed:cite]

Udemy 講座『Kubernetes Certified Administrator (CKA) with Practice Tests』を利用していて、講座の構成が現在のカリキュラムに合っていない部分があったので、2021年8月現在利用されているカリキュラム、v1.21 との対応表を作成した。  
参照したカリキュラムは以下。

[https://github.com/cncf/curriculum/blob/master/CKA_Curriculum_v1.21.pdf:embed:cite]


今現在こちらの Udemy 講座を受講中のため、不足や誤りがあるかもしれないが、その場合は適宜修正する。

- 25% - Cluster Architecture, Installation & Configuration
  - Manage RBAC  
  https://www.udemy.com/course/certified-kubernetes-administrator-with-practice-tests/learn/lecture/14296110#overview
  - Use Kubeadm to install a basic cluster  
  https://www.udemy.com/course/certified-kubernetes-administrator-with-practice-tests/learn/lecture/20666292#overview
  - Manage a HA Kubernetes cluster  
  https://www.udemy.com/course/certified-kubernetes-administrator-with-practice-tests/learn/lecture/14296190#overview
  - provision underlying infrastructure to deploy a Kubernetes cluster  
  https://www.udemy.com/course/certified-kubernetes-administrator-with-practice-tests/learn/lecture/14296184#overview
  - Perform version upgrade on a Kubernetes cluster using kubeadm  
  https://www.udemy.com/course/certified-kubernetes-administrator-with-practice-tests/learn/lecture/14296062#overview
  - Implement etcd backup and restore  
  https://www.udemy.com/course/certified-kubernetes-administrator-with-practice-tests/learn/lecture/14296066#overview
- 15% - Workloads & Scheduling
  - Understand deployments and how to perform rolling update & rollback  
  - Know how to scale applications  
  https://www.udemy.com/course/certified-kubernetes-administrator-with-practice-tests/learn/lecture/14296008#overview
  - Use ConfigMaps & Secrets to configure applications  
  ConfigMap: https://www.udemy.com/course/certified-kubernetes-administrator-with-practice-tests/learn/lecture/14412322#overview  
  Secret: https://www.udemy.com/course/certified-kubernetes-administrator-with-practice-tests/learn/lecture/14296016#overview
  - Understand the primitives used to create robust, self-healing, application deployments  
  https://www.udemy.com/course/certified-kubernetes-administrator-with-practice-tests/learn/lecture/14296030#overview  
  CKAD のコースを見た方がいいかも。
  - Understand how resource limits can affect pod scheduling  
  https://www.udemy.com/course/certified-kubernetes-administrator-with-practice-tests/learn/lecture/14298678#overview
  - Awareness of manifest management and templating tools  
  これは Udemy 講座にはない？kustomize のことを指しているのなら↓。
  https://kubernetes.io/docs/tasks/manage-kubernetes-objects/kustomization/
- 20% - Services & Networking
  - Understand host networking configuration on the cluster nodes
  - Understand connectivity between Pods  
  https://www.udemy.com/course/certified-kubernetes-administrator-with-practice-tests/learn/lecture/14296150#overview
  - Understand ClusterIP, NodePort, LoadBalancer service types and endpoints  
  https://www.udemy.com/course/certified-kubernetes-administrator-with-practice-tests/learn/lecture/14295512#overview
  - Know how to use Ingress controllers and Ingress resources  
  https://www.udemy.com/course/certified-kubernetes-administrator-with-practice-tests/learn/lecture/14296170#overview
  - Know how to configure and use CoreDNS  
  https://www.udemy.com/course/certified-kubernetes-administrator-with-practice-tests/learn/lecture/14296164#overview  
  https://www.udemy.com/course/certified-kubernetes-administrator-with-practice-tests/learn/lecture/14296166#overview
  - Choose an appropriate CNI  
  https://www.udemy.com/course/certified-kubernetes-administrator-with-practice-tests/learn/lecture/14296152#overview
- 10% - Storage
  - Understand storage classes, persistent volumes  
  StorageClass: https://www.udemy.com/course/certified-kubernetes-administrator-with-practice-tests/learn/lecture/21795216#overview  
  PV: https://www.udemy.com/course/certified-kubernetes-administrator-with-practice-tests/learn/lecture/14296126#overview
  - Understand volume mode, access modes and reclaim policies  
  https://kubernetes.io/ja/docs/concepts/storage/persistent-volumes/#%E3%83%9C%E3%83%AA%E3%83%A5%E3%83%BC%E3%83%A0%E3%83%A2%E3%83%BC%E3%83%89
  - Understand PVC primitive  
  https://www.udemy.com/course/certified-kubernetes-administrator-with-practice-tests/learn/lecture/14296130#overview
  - Know how to configure applications with persistent storage  
  https://www.udemy.com/course/certified-kubernetes-administrator-with-practice-tests/learn/lecture/14296132#overview
- 30% - Troubleshooting
  - Evaluate cluster and node logging  
  - Understand how to monitor applications  
  - Manage container stdout & stderr logs  
  https://www.udemy.com/course/certified-kubernetes-administrator-with-practice-tests/learn/lecture/14295996#overview
  https://www.udemy.com/course/certified-kubernetes-administrator-with-practice-tests/learn/lecture/14296002#overview
  - Troubleshoot application failure  
  https://www.udemy.com/course/certified-kubernetes-administrator-with-practice-tests/learn/lecture/14296310#overview
  - Troubleshoot cluster component failure  
  ControlPlane: https://www.udemy.com/course/certified-kubernetes-administrator-with-practice-tests/learn/lecture/14296312#overview  
  WorkerNode: https://www.udemy.com/course/certified-kubernetes-administrator-with-practice-tests/learn/lecture/14296314#overview
  - Troubleshoot networking  
  https://www.udemy.com/course/certified-kubernetes-administrator-with-practice-tests/learn/lecture/24452872#overview

カリキュラムのほとんどをカバーできていることが確認できたため、引き続き進めていこう。  
なお現在(2021/08/06)は Section 7: Security を進めている。TLS Certificates は今は範囲に含まれないっぽい。
