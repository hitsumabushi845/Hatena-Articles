---
title: 『Kubernetes Certified Administrator (CKA) with Practice Tests』記録 - セクション5
date: 2021-07-28T11:36:59.000Z
categories:
  - Kubernetes
id: "26006613791515244"
draft: true
---
引き続き、『Kubernetes Certified Administrator (CKA) with Practice Tests』を進めていく。
今回は Section 5: Application Lifecycle Management。

目次によるとこの章では以下の項目を扱うようだ。

- Rolling Updates and Rollbacks
- Commands and Arguments
- Configure Environment Variables in Applications
- Configuring ConfigMaps in Applications
- Configure Secrets in Applications
- Multi-Container Pods
- Multi-Container Pods Design Patterns
- InitContainers
- Self Healing

ほぼほぼ CKAD で扱った内容のようなので、Practice Test で確認しつつ軽く進めていく。

## Section 5: Application Lifecycle Management

- Rolling Updates and Rollbacks
  - Deployment のローリングアップデートについて。
  - StrategyType に関してもすこし。RollingUpdate だと maxSurge/minSurge に基づいてアップデートされて、Recreate だと全 Pod を落として再作成。
- Commands and Arguments
  - Dockerfile を併せて参照して、commands/args について。
  - k run で指定する場合、`--commands` をつけると `spec.containers[].command` に、つけないと `spec.containers[].args` に指定される。
- Configure Environment Variables in Applications
- Configuring ConfigMaps in Applications
  - 環境変数の設定と ConfigMap からの指定（envFrom）について。
- Configure Secrets in Applications
  - secret と secret からの環境変数の指定について。
- Multi-Container Pods
  - elastic stack を用いて、Sidecar コンテナから elasticsearch にログを流すサンプル。
- Multi-Container Pods Design Patterns
  - これは CKA の範囲じゃないよ！とのこと
- InitContainers
- Self Healing
