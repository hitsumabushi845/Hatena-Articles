---
title: 『Kubernetes Certified Application Developer (CKAD) with Tests』記録 - セクション3
date: 2021-03-25T16:00:52.000Z
categories:
  - Kubernetes
id: "26006613708476232"
draft: true
---
引き続き、『Kubernetes Certified Application Developer (CKAD) with Tests』を進めていく。  
今回は Section 3: Configuration。

# Section 3: Configuration 

## Pre-Requisite - Commands and Arguments in Docker

Docker コンテナのコマンドと引数について。  
Dockerfile の CMD, ENTRYPOINT について説明されている。

Docker をちゃんと触ったことないので助かる（そんな状態で Kubernetes に手を出してるのはアレだが...）

## Commands and Arguments in Kubernetes

Pod manifest における `spec.containers[*].command`, `spec.containers[*].args` フィールドについて、Dockerfile の例と比較して。  
`command` は `ENTRYPOINT` を、`args` は `CMD` を上書きする。

## Environment Variables

コンテナ内の環境変数について（`spec.containers[*].env`）。  
後段の ConfigMap への布石。

## ConfigMaps

あんまり触れてなかったのでちゃんと書く。
`env` で指定するような key-value pair を外出しできるやつ。  
`kubectl create configmap` で作成したり、

```shell
> kubectl create configmap \
    app-config --from-literal=APP_COLOR=blue \
               --from-literal=APP_MODE=prod
```

yaml を書いたり。

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
    name: app-config
data:
    APP_COLOR: blue
    APP_MODE: prod
```

`spec.containers[*].envFrom[*].configMapRef` で、ConfigMap を環境変数として注入できる。

ほか、ボリュームとして注入してファイルとして扱うなど。

## Secrets

ConfigMap は平文で格納されるが、Secret は Base64 でエンコードされた状態で格納されるやつ。
`kubectl create secret generic` や `kubectl create secret tls` で作成するか、yaml を書くか。

```shell
> kubectl create secret generic\
    <secret-name> --from-literal=<key>=<value as plain>
> kubectl create secret generic \
    <secret-name> --from-file=<path-to-file>
```

```yaml
apiVersion: v1
kind: Secret
metadata:
    name: <secret-name>
data:
    <key>: <base64 encoded value>
```

`spec.containers[*].envFrom[*].secretRef` で、Secret を環境変数として注入できる。
注入したあとはデコードされた値が利用できる。

## Docker Security

Docker のプロセス分離について。Namespace でプロセスを分離している。
コンテナ内の実行ユーザーについて。デフォルトは root、`--user=xxx` を `docker run` 時に指定したり、Dockerfile の `USER` フィールドに指定することで異なるユーザで実行できる。
ユーザの権限を操作する場合は、`--cap-add`, `--cap-drop` や `--privileged` を使う。

## Container Security

k8s におけるコンテナは Pod で抽象化されているため、Pod 単位でのセキュリティ設定が可能。
Pod 単位で設定した場合は、Pod 内の全てのコンテナに反映される。

```yaml
spec:
  securityContext:
    runAsUser: 1000
  containers:
  ~~
```

コンテナ単位で設定する場合は、`containers` 以下に設定する。

## Service Account

KSA は認証/認可やRBACに関わってくるけど、CKAD では KSA の使い方だけ分かってればいいらしい。
k8s のアカウントは User Account と Service Account に大別される。

Service Account はアプリケーションが k8s クラスタを操作するためのアカウント。
例えば Prometheus が k8s の API を叩いてメトリクスを取得する際などに使う。

```
> kubectl create serviceaccount <serviceaccount name>
> kubectl create sa <serviceaccount name>
```

describe すると、Token というフィールドが見える。
これは k8s の API を叩くための認証トークンとクライアント証明書を持った Secret で、KSA を作成すると自動的に作成される。

`default` という名称の ServiceAccount は各 namespace にはじめから存在する。
そもそも Pod は ServiceAccount と紐付いている必要があり、マニフェストで指定しなかった場合は `default` が使用される。`default` SA は権限が弱く、基本的なクエリしか実行できない。
`default` をマウントさせたくない場合は、`spec.automountServiceAccountToken` を `false` にする。

異なる ServiceAccount を使用する場合は、Pod マニフェストの `spec.serviceAccount` に明示的に指定する。

## Resource Requirements

kube-scheduler はノードのリソース状況(CPU, Memory, Disk)を見ながら最適なノードに Pod をスケジューリングする。

ワークロードの使用するリソースの最低値・最大値を指定するには、`spec.containers[*].resources` フィールドを使用する。
`resources.requests` には要求値（最低値）、`resource.limits` に最大値を指定する。

`LimitRange` リソースをつかって、resource フィールドを指定しなかった場合のデフォルト値を指定できる。

なお、負荷に応じてワークロードをスケールさせる HPA とか VPA とかは CKAD では範囲外。

## Taints and Tolerations

## Node Selectors

## Node Affinity
