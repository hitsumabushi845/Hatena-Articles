---
title: KodeKloud『Kubernetes Certified Security Specialist (CKS)』記録 - Cluster Setup & Hardening
date: 2022-02-25T08:48:54.000Z
categories:
  - Kubernetes
id: "4207112889915213590"
draft: true
---
引き続き、KodeKloud『Kubernetes Certified Security Specialist(CKS)』を進めていく。  
今回は Cluster Setup & Hardening のセクション。

# Cluster Setup & Hardening

Udemy の CKA コースにおける Security のセクションと重複する内容が全体の半分程度を占めていたので、この記事では CKS のコースで新規に登場した項目のみ記載する。  
実際には重複した内容も復習のためあらためて受講した。

## What are CIS Benchmarks

[https://www.cisecurity.org/benchmark/kubernetes/:embed:cite]

セキュリティを担保するための取り組みは多岐にわたる。サーバのセキュリティであればユーザの権限管理やファイアウォールの設定、ロギングによる記録、OS patchの適用など...
それらのベンチマークの一つが CIS benchmark。CIS は Center for Internet Security の略。
CIS は様々なプラットフォームに対するベンチマークを提供しており、Kubernetes 向けのベンチマークも含まれる。

CIS は CIS-CAT というツールも提供しており、CIS Benchmarks のアセスメントを行える。

## Lab: Run CIS Benchmarks Assessment Tool on Ubuntu

CIS-CAT ツールを Ubuntu マシン上で実際に動かしてみるハンズオン。
レポートを見ながら Fail した項目を修正するところまで試せる。

## CIS benchmark for Kubernetes

Kubernetes における CIS benchmark について。  
benchmark には、さまざまなコンポーネントについてどのような設定をすべきか記載されており、具体的な手順まで記載がある。

CIS-CAT Lite では Kubernetes のサポートはなく、Pro の利用が必要。

## Kube-bench

[https://github.com/aquasecurity/kube-bench:embed:cite]

Kube-bench は、Aqua security が提供する OSS ツール。  
CIS benchmark で要求されている内容を満たしているかチェックできるツールとなっており、Kubernetes 上で Pod (Job) として実行できる。  
そのほか、Docker container やバイナリ形式での実行もサポートされている。

## Lab: Kube-bench

Kube-bench のバイナリをダウンロード、実行し、結果を見ながら Fail した項目を修正するハンズオン。  
Kube-bench の出力の見方がわかるようになる。

## Article on Setting up Basic Authentication

パスワードファイル/トークンファイルでの認証を構成する具体的な方法について。  

## Kubelet Security

[https://kubernetes.io/docs/reference/command-line-tools-reference/kubelet-authentication-authorization/:embed:cite]

kubelet のセキュリティについて。  
kubelet も API を提供しており、デフォルトだと特に認証を必要とせず API を叩けてしまう。そこで、認可されていないユーザからのリクエストを拒否したり、証明書による認証をかけたりする方法について学ぶ。

また、kubelet は readOnlyPort を提供しており、ここもまた認証を必要とせずノードの情報を得られてしまうので、readOnlyPort を無効化する方法についても学ぶ。

## Lab: Kubelet Security

実際に認証なしに kubelet の API を叩けてしまうことなどを確認し、KubeletConfiguration を修正してこれらの API の実行を不可能にするハンズオン。  
KubeletConfiguration ファイルの場所を探すところから始まるので、段階を追って kubelet の堅牢化について学ぶことができる。

## Kubectl Proxy & Port Forward

`kubectl proxy` と `kubectl port-forward` について。後続の講座のための前振り。
`kubectl proxy` は kube-apiserver に対するプロキシをローカルに立てる。プロキシに対して curl で API を叩くときは認証情報がいらなくなる（proxy が kubeconfig をよしなにやってくれるっぽい）
`kubectl port-forward` はクラスタ内の Pod や Service のポートをローカルのポートにマッピングする。

## Lab: Kubectl Proxy & Port Forward

`kubectl proxy` と `kubectl port-forward` を使ってみるハンズオン。クラスタ内のリソースやクラスタのAPIに対して `localhost` でアクセスできることを確認する。

## Kubernetes Dashboard

[https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/:embed:cite]

Kubernetes Dashboard について。
GUI でクラスタのリソースを管理できるダッシュボード画面だが、本当になんでもできてしまうのでアクセス制御は慎重に行わなければならない。

デフォルトでは ClusterIP で公開されており、クラスタ外からアクセスするには `kubectl proxy` でプロキシすることが推奨される。これによりクラスタへの認証情報がない場合はダッシュボードにアクセスできない。
また、ダッシュボードへのログインを ServiceAccount の Bearer Token や kubeConfig ファイルで行う形にすることもできる。

## Lab: Securing Kubernetes Dashboard

Kubernetes Dashboard のデプロイ、`kubectl proxy` によるアクセスの確認、ServiceAccount, Role, RoleBinding の作成による認可の制御を確認するハンズオン。 

## Verify platform binaries before deploying

Kubernetes クラスタコンポーネントのバイナリをダウンロードしたらチェックサムを確認しようねという話。
悪意の第三者がダウンロードされるファイル悪意のあるプログラムが実行されるバイナリにすり替えることがあるので。

チェックサムは `shasum` とか `sha512sum` コマンドで確認できる。

## Lab: Verify platform binaries

GitHub からバイナリをダウンロードしてきて、チェックサムを確認するハンズオン。
複数の（ダウンロード済みの）ファイルから正しいファイルはどれ？みたいな設問もあってわかりやすい。

## Docker Service Configuration

Docker デーモンの設定について。  

`/var/run/docker.sock` に Docker の UNIX ソケットがあり、Docker CLI はこことやり取りしている。
デフォルトではシステムの外部からソケットにアクセスすることはできないため、Docker ホストとクライアントは同じである必要がある。  
Docker デーモン起動時に、`--host=tcp://{IP Address}:{Port}` を指定することで、指定のポートで受け付けることができる。（例: `--host=tcp://192.168.1.10:2375`）  
これにより、Docker ホスト外からのアクセスができるようになる。ホスト外から Docker CLI で指定のホストにアクセスするには、`DOCKER_HOST` 環境変数を指定する。（例: `export DOCKER_HOST="tcp://192.168.1.10:2375"`）  
しかし、もしホストがインターネットに公開されている場合は、誰もがアクセスできてしまうため、このオプションはデフォルトで無効化されている。公開する場合はネットワークのセキュリティを考慮する必要がある。

もし Docker ホストを公開する場合は、通信の TLS 暗号化を行う。通信の TLS 暗号化は、`--tls`, `--tlscert`, `--tlskey` オプションで設定する。  
（例: `--tls=true --tlscert=/var/docker/server.pem --tlskey=/var/docker/serverkey.pem`）

これらのオプションは設定ファイルに記述することもでき、`/etc/docker/daemon.json` ファイルに記述する。

```json
{
  "debug": true,
  "hosts": ["tcp://192.168.1.10:2376"],
  "tls": true,
  "tlscert": "/var/docker/server.pem",
  "tlskey": "/var/docker/serverkey.pem"
}
```

## Docker - Securing the Daemon

Docker デーモン、Docker ホストの保護について。

1. サーバ自体のセキュア化
  - パスワード認証を無効化し SSH 鍵認証を有効化する、ユーザを切る、不要なポートは閉じるなど
2. 通信の TLS 暗号化(上述)
  - Ref.: https://docs.docker.com/engine/security/protect-access/
3. Docker デーモンに証明書による認証を入れる
  - `daemon.json` に `"tlsverify": true`, `"tlscacert": /path/to/cacert.pem` を追加
  - クライアント側でも `DOCKER_TLS_VERIFY=true` 環境変数を設定しておき、`~/.docker` にクライアント証明書を配置しておく。

Cluster Setup & Hardening のセクションはここまで。
多層防御の考え方のもと、マシン、OS、Docker デーモン、Kubernetes コンポーネントなどなど様々な対象のセキュリティについて確認した。一通りさらったという印象なので、まだ整理がつかない部分もあるがひとまず先に進む。

実はこのセクションが一番ボリュームのあるセクションだったので、以降はもう少しスムーズに進められるといい。
