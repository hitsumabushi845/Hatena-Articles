---
title: 『Kubernetes Certified Administrator (CKA) with Practice Tests』記録 - セクション5, 6
categories:
  - Kubernetes
id: "26006613791515244"
draft: true
---
引き続き、『Kubernetes Certified Administrator (CKA) with Practice Tests』を進めていく。
今回は Section 5: Application Lifecycle Management。

Section 5 についてはほぼ CKAD で扱った内容のようなので、Practice Test で確認しつつ軽く進めていく。
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
  - Init Containers について。
- Self Healing
  - ReplicaSets や Deployments はアプリケーションがクラッシュした際などに自己修復する。
  - このへんは Liveness probe や Readiness probe で細かく設定できるがこれは CKAD の範囲なので関係ない。

## Section 6: Cluster Maintenance

この章から CKA 特有の項目となるので、しっかり進めていく。
本章では、以下の内容を扱う。

- クラスタのアップグレード
- OS のアップグレード
- バックアップとリストア

それぞれ Practice Test とともに学習していく。

- OS Upgrades
  - ノードの OS アップグレードのためには、まずノード上の Pod を退避させなければならない。
  - `kubectl drain {node name}`
    - pod の evict と node を NoSchedule にする
    - node のメンテナンス時に使う。
  - `kubectl cordon {node name}`
    - node を NoSchedule にする（evict はしない）
  - `kubectl uncordon {node name}`
    - node を NoSchedule からもどす。k drain/k cordon のあとにつかう。
- Kubernetes Software Versions
  - kubernetes のバージョンについて。
  - 機能は alpha -> beta -> GA となる
- Cluster Upgrade Process
  - kubernetes は常に最新の3つのマイナーバージョンがサポートされる。
  - アップデートは一足飛びに行わず、積み上げ型で行うことが推奨される。（1.9 から 1.11 に直接上げるのでなく、1.9 -> 1.10 -> 1.11 とアップデートすること）
  - アップグレードは master node の更新 -> worker node の更新 といった順序で進む。master node の更新中はクラスタに対する操作はできないが、実行中のリソースは引き続き worker node 上で動作しているため、アプリケーションは利用可能。
  - worker node のアップグレード戦略は以下の通り。
    1. Recreate
       - 既存のノードを全消しして作り替える。
       - 消えてる間はアプリケーションは利用不可（ダウンタイムが発生する）
    2. RollingUpdate
       - node を一部ずつ更新していく。
       - 既存のpodは別のnodeにevictされるのでアプリケーションは利用可能（ダウンタイムが発生しない）
       - Recreate に比べ、Node を一部ずつ更新するため時間がかかる。
    3. Create, Move and Terminate
       - まずアップグレード済みのノードを作成し、リソースをそちらに全部移動して既存のnodeを削除する。
       - クラウドのようにマシンリソースを手軽に確保出来る場合に行える戦略。
  - `kubeadm upgrade plan`
    - アップグレードの可否やアップグレード可能なバージョンの確認ができる。
  - `kubeadm upgrade apply vx.xx.x`
    - controlplane のコンポーネントのアップグレードができる。
    - kubelet がある場合、kubelet は更新されないので個別で apt install する必要がある。
  - `kubeadm upgrade node`
    - 2つめ以降の node のアップグレードを行う。
  - ref: https://kubernetes.io/docs/tasks/administer-cluster/kubeadm/kubeadm-upgrade/
- Backup and Restore Methods
  - 基本的に、全てのリソースはマニフェストによって定義されるため、マニフェストファイルを GitHub などのリポジトリに保管することが推奨される。これが1つのバックアップとなる。
    - もし管理されていなかったとしてもクラスタに対して `kubectl get all -A -oyaml > all-resources.yaml` などとすることで取得は可能。
  - ETCD クラスタのバックアップ
    - スナップショットの取得: `etcdctl snapshot save /path/to/snapshot.db`
    - スナップショットからの復元: `etcdctl snapshot restore /path/to/snapshot.db --data-dir /var/lib/etcd-from-backup`
      - 別の場所にリストアして、etcd の読み込み先を変更する。
      - data-dir を変更しているため、`etcd.service` ファイルも修正して、`systemctl daemon-reload` する。
      - static pod として動いている場合は、`/etc/kubernetes/manifests/etcd.yaml` を修正する。

この章はこれまでに馴染みのない領域が多いので、後に改めて復習する。
