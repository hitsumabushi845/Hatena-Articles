---
title: 『Kubernetes Certified Administrator (CKA) with Practice Tests』記録 - セクション10~12
date: 2021-09-17T09:17:36.000Z
categories:
  - Kubernetes
id: "13574176438012704404"
draft: false
---
引き続き、『Kubernetes Certified Administrator (CKA) with Practice Tests』を進めていく。  
今回は Section 10: Design and Install a Kubernetes Cluster と Section 11: Install "Kubernetes the kubeadm way"。

## Section 10: Design and Install a Kubernetes Cluster

- Design a Kubernetes Cluster
  - どういうクラスタを作るのか
  - 目的: 勉強用？開発用？本番用？
    - 勉強用なら minikube とか、小さめのクラスタをクラウドで作るとか
    - 開発用なら 1-Master, n-Worker
    - 本番用だと HA 構成がいるよね（multi-master nodeとか）
  - プラットフォーム: クラウド？オンプレ？
    - オンプレなら kubeadm
    - GCP なら GKE, AWS なら Kops, Azure なら AKS
  - ワークロード: どれくらいの規模のアプリケーションが動くのか？アプリケーションの内容は？CPU, メモリの利用は？トラフィックは？
- Choosing Kubernetes Infrastructure
  - クラスタをどこにホストするか
  - 手元のマシンなら minikube とか、kubeadm とか
  - プライベートなサーバにセットアップするなら、OpenShift, VMware Cloud PKS, Vagrant とかで
  - クラウド使うなら GKE, AKS, EKS とか
- Configure High Availability
  - Control plane が 1 台のみで構成されていると、そのマシンが壊れたときに困るので、HA 構成を取ろう
  - HA 構成の Control plane では、複数の Master Node がそれぞれ同じコンポーネントを持つ
  - API Server もそれぞれに居るため、API call を振り分けるため前段に LB を配置することが望ましい（nginx, HA proxy, etc...など）
  - Controller Manager, Scheduler も同様だが、こちらは各 Node で走っていると意図しない Pod の作成などが行われてしまうので、1 つだけ Active にし、他は Standby にする。
    - これは leader-election process によって制御される
    - kube-controller-manager/kube-scheduler の引数に `--leader-elect true` を指定すると、kube-controller-manager Endpoint のリース取得有無でリーダーが選出される
    - `--leader-elect-lease-duration duration`: リース期間
    - `--leader-elect-renew-deadline duration`: リース更新にかかる時間の最大値
    - `--leader-elect-retry-period duration`: リーダーシップの獲得を試みる間隔
    - https://speakerdeck.com/daikurosawa/leader-election-in-kubernetes-number-k8sjp
  - ETCD
    - ETCD の HA 構成は2種類ある
      - Stacked Topology: Master Node 内に ETCD を構築する
        - pros: Node が少なくすむ、管理が楽
        - cons: Node のトラブル時に etcd も影響を受ける
      - External ETCD Topology: 異なるノードに ETCD を分離して、独立に構築する
        - pros: リスクを低減する
        - cons: セットアップがつらい、Node が増える
        - kube-apiserver が見に行く etcd のエンドポイントは `--etcd-servers` で指定できる
- ETCD in HA
  - ETCD について
    - 分散型KVS
    - 分散
      - クラスタ構成をとれる
      - いかなるノードへのR/Wでもクラスタ全体での一貫性が保たれる
      - leader election が行われており、Write は常に leader から全ノードに対して行われる
    - leader election - RAFT
      - etcd のリーダー選出は RAFT というアルゴリズムで行われている
      - 最初に各ノードはランダムタイマーをもっていて、最初にタイマーが終わったノードが他のノードにリーダー選出をリクエストする
      - あとは投票みたいな感じでリーダーが選ばれる
    - 書き込まれているデータは過半数優先（過半数に記録されているデータが正）
      - "過半数" なので、クラスタは3-nodes以上で構成することが望ましい（2-nodes以下だとノードが落ちたら終了する）
      - さらに 奇数-nodes が望ましい（クラスタが分断されたときにどこかは必ず生きる）
  - etcdctl
    - `etcdctl put <key> <name>`
    - `etcdctl get <key>`
- Important Update: Kubernetes the Hard Way
  - Hard Way もやるといいよ的な話。

## Section 11: Install "Kubernetes the kubeadm way"

- Introduction to Deployment with Kubeadm
  - kubeadm での k8s クラスタ構築の概観
  - ステップ
    1. セットアップ対象のマシン/VMを用意する
    2. マシン/VM にコンテナランタイムをインストールする
    3. マシン/VM に kubeadm をインストールする
    4. Master Node を作成する
    5. Pod Network を作成する
    6. Worker Node をクラスタに参加させる
- Deploy with Kubeadm - Provision VMs with Vagrant
  - Vagrant でローカルにクラスタ用のVMを構築する方法について
- Demo - Deployment with Kubeadm
  - やってることは[以前 Qiita に書いたこと](https://qiita.com/hitsumabushi845/items/4a24109cf1408c0e30de)と同様だった。
- Practice Test - Deploy a Kubernetes Cluster using Kubeadm
  - 手順としては以前やった通りだが、1.22 での挙動でちょっと詰まったり、Practice Test 用の環境が 1.22 に即してなかったりでいろいろ詰まった。
  - [このへん](https://yasu7ri.hatenablog.com/entry/2018/03/31/231139#4-Cgroup-Driver%E3%81%AE%E8%A8%AD%E5%AE%9A)の Cgroup Driver のミスマッチなど。

## Section 12: End to End Tests on a Kubernetes Cluster

この範囲は CKA の範囲から外されたので講座はなし。
