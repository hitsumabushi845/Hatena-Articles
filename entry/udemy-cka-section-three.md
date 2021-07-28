---
title: 『Kubernetes Certified Administrator (CKA) with Practice Tests』記録 - セクション1~4
date: 2021-07-26T08:46:02.000Z
categories:
  - Kubernetes
id: "26006613790836794"
draft: false
---
[https://qiita.com/hitsumabushi845/items/a6ae8935eaf509fb5381:embed:cite]

というわけで、CKAD を取得したので引き続き CKA の取得へ向けて勉強していく。
CKAD と同様に Mumshad Mannambeth 氏の『Kubernetes Certified Administrator (CKA) with Practice Tests』を利用する。

[https://www.udemy.com/course/certified-kubernetes-administrator-with-practice-tests/:embed:cite]

CKAD を取得したので、今回は CKA 特有の項目について勉強を行い、時間をかけすぎてしまった CKAD よりも短期での取得を目指す。

# Section 1: Introduction, Section 2: Core Concepts

これらのセクションはセクション 1 が CKA の説明、セクション 2 は CKAD と同様の内容であるので、今回はスキップ。

# Section 3: Scheduling

このセクションでは、スケジューリング、つまり Pod などのリソースをどのノードにデプロイするかといった項目について説明される。
いくらかは CKAD で触れたものなので、"Practice Test を行う -> 分からなかったところがあったら説明の動画に戻る" といったステップで行う。
"分からなかったところ" の判定は "公式ドキュメントで説明されている場所が分からない", "公式ドキュメントを見ても分からない" といった場合に限定する。これは実際の試験で公式ドキュメントを参照できることから。

- nodeName  
  - もっともプリミティブな指定方法。ノード名を指定することで、そのノードへ Pod をスケジューリングできる。
- Labels and Selectors
  - スケジューリングの観点では主に Deployments や ReplicaSets で登場する。
- Taints and Tolerations
  - Taints が設定されたノードには、Tolerations が指定されていない場合はスケジューリングされない。
- Node Affinity
  - label を用いて、スケジューリングするノードを指定できる
- Resource Requirements and Limits
  - cpu や memory の request, limit について。
  - アプリケーションが使用するメモリよりも少ない memory limit を指定すると OOMKilled が発生する。
- DaemonSets
  - 各ノードに1個 Pod 配置されることが保証されるリソース。
  - kube-proxy やオーバレイネットワークを提供する Pod は DaemonSet で提供される。
  - ほか、Monitoring プロセスが動く Pod などに使用される。
- Static Pods
  - kubelet は単体で Pod を動かすことができる(kubelet が直接管理する Pod)
  - `/etc/kubernetes/manifests` に Pod のマニフェストファイルを配置すると、Pod が作成される。なお、このパスは kubelet の設定(起動時の `--pod-manifest-path` または設定ファイルの `staticPodPath` )で変更できる。
  - kubelet はこのディレクトリを監視していて、ここにマニフェストが配置されれば Pod を動かすし、削除されたら Pod を削除する。
  - この static pod は、主に controlplane のコンポーネントを管理するために利用される。controlplane が controlplane 自身に依存しないよう、kubelet が管理するようにする。
- Multiple Schedulers
  - k8s のスケジューラは基本的に kube-scheduler が使われるが、複数のスケジューラを共存できる
  - kube-scheduler を複数立てることもでき、その名前は `--scheduler-name` で変更できる
  - 特定のリソースにおいてスケジューラを指定する場合は、`spec.schedulerName` に指定する

# Section 4: Logging and Monitoring

このセクションでは、ロギングとモニタリングについて説明される。
クラスタのコンポーネントのロギング・モニタリング、並びにアプリケーションのロギングとモニタリングについて。

- Monitor Cluster Components
  - k top コマンドについて。k top コマンドはノードや Pod のリソース利用率を確認できるコマンド。
  - 素の k8s では使えず、metrics-server を別途デプロイすることで利用可能となる。
  - 各ノードのリソース利用率を確認する場合は `k top node`、各 Pod の場合は `k top pod`。
- Managing Application Logs
  - k logs コマンドについて。
  - 複数のコンテナからなる Pod のログを取る場合は、コンテナの名前を指定する。

セクション4はここまで。かなり短いセクションであった。

次回は Section 5: Application Lifecycle Management に取り組む。
目次を見る感じ結構 CKAD と被っていそうなのでサクッとできるとよい。 
