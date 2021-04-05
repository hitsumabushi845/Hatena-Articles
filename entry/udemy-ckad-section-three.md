---
title: 『Kubernetes Certified Application Developer (CKAD) with Tests』記録 - セクション3
date: 2021-03-25T16:00:52.000Z
categories:
  - Kubernetes
id: "26006613708476232"
draft: false
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

```shell-session
$ kubectl create configmap \
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

```shell-session
$ kubectl create secret generic \
    <secret-name> --from-literal=<key>=<value as plain>
$ kubectl create secret generic \
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
コンテナ単位での securityContext では、capabilities を設定でき、ユーザの権限を操作できる。これは Pod 単位ではできない。

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

Taint と Toleration は Pod を特定の Node にスケジューリングさせないための仕組み。  
これらは、スケジューリング**させない**ための仕組みであって、Pod を特定の Node にスケジューリング**させる**ための仕組みは後述する Node Selector や Node Affinity が担う。

Taint は Node に、Toleration は Pod に設定する。
Node に設定された Taint と合致する Toleration が設定された Pod がスケジューリングされる。

Taint は `kubectl taint nodes {node name} key=value:taint-effect` で設定できる。
key, value が Taint の条件となり、taint-effect でその振る舞いを設定できる。  
`taint-effect` には `NoSchedule`, `PreferNoSchedule`, `NoExecute` がある。

Toleration は、manifest の `spec.tolerations` に設定される。

Master Node に Pod がスケジューリングされないのもこれのおかげ。

## Node Selectors

Pod を特定の Node にスケジューリングさせるための仕組み。
manifest の `spec.nodeSelector` に、Node に設定したラベルを指定する。

Node にラベルを設定するには、`kubectl label nodes {node name} key=value` を実行する。

Node Selector では、指定されたラベルへの完全一致のみサポートしているが、OR条件やNOT条件などを利用したいケースがあり、これは Node Affinity でサポートされる。

## Node Affinity

Node Selector より柔軟な設定が可能なもの。
`spec.affinity.nodeAffinity` に設定され、`requiredDuringSchedulingIgnoredDuringExecution` と `preferredDuringSchedulingIgnoredExecution` の2種類が設定できる[^1]。

[^1]: 将来的に `requiredDuringSchedulingRequiredDuringExecution` が提供される予定。

required~ は必須条件。マッチする Node がない場合はスケジューリングされない。  
preferred~ は推奨条件。マッチする Node がない場合は他の Node へスケジューリングされる。  
`IgnoredDuringExecution` とあるように、実行中の Pod に対してはこの設定は影響せず、例えば nodeAffinity を設定した Pod が実行されている Node から、その nodeAffinity の条件を満たさなくなるようにラベルを削除したとしても、その Pod は削除されない[^2]。

[^2]: 当然、`requiredDuringSchedulingRequiredDuringExecution` の場合は削除される。

かなり柔軟に設定できるため、フィールドがいろいろあって混乱する。
この辺をブックマークして、試験中に見れるようにしておこう。  
https://kubernetes.io/ja/docs/concepts/scheduling-eviction/assign-pod-node/  
https://kubernetes.io/ja/docs/tasks/configure-pod-container/assign-pods-nodes-using-node-affinity/

## Taints & Tolerations vs Node Affinity

特定の Node に特定の Pod **だけ**をスケジューリングさせたい場合は、Taints/Tolerations と Node Affinity を併用しようねという話。

---

Section 3 はここまで。ちょっと忘れちゃいそうな点がままあるので、折に触れて復習していこうと思う。
