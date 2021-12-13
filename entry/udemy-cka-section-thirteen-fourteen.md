---
title: 『Kubernetes Certified Administrator (CKA) with Practice Tests』記録 - セクション13・14
date: 2021-10-07T09:01:58.000Z
categories:
  - Kubernetes
id: "13574176438042469753"
draft: false
---
9月は忙しくあまり進められていなかったが、引き続き『Kubernetes Certified Administrator (CKA) with Practice Tests』を進めていく。
今回は Section 13: Troubleshooting と Section 14: Other Topics。トラブルシューティングは試験としても 30 % のボリュームを占める部分なので丁寧に進めていきましょう。

また、これで講座としては終了で、残るは Mock Exam(模擬試験)のみ。

## Section 13: Troubleshooting

- Application Failure
  - アプリケーションのトラシューは Service と Pod の状態を見ようね
  - Service: matchLabel が正しいかとか
  - Pod: Pod がクラッシュしてないかとか、k logs で見よう。restart が走ってる場合は k logs -f とか、k logs --previous とかでエラーを確認しよう。
  - see also: https://kubernetes.io/docs/tasks/debug-application-cluster/debug-application-introspection/
  - see also: https://kubernetes.io/docs/tasks/debug-application-cluster/debug-application/
- Practice Test - Application Failure
  - Webapp Pod, Database Pod 構成における各種トラブルシューティング。
  - Webapp 側の接続先設定を直すのか、Database 側を直すのか曖昧な部分がちらほらあって手間取った
- Control Plane Failure
  - kubeadm でクラスタを構築した場合、Control Plane のコンポーネントは Pod で動いているので `k get po -n kube-system` で確認できる
  - service として動いている場合は `service kube-apiserver status`, `service kubelet status` など
  - service のログは `k logs xxx -n kube-system` や `sudo journalctl -u xxx`
  - see also: https://kubernetes.io/docs/tasks/debug-application-cluster/debug-cluster/
- Practice Test - Control Plane Failure
  - kube-scheduler や replicaset controller のトラブルシューティングをした
  - 主に static pod のマニフェスト修正
- Worker Node Failure
  - `k get nodes` で `status` が `NotReady` なノードがあった場合
  - `k describe node node-name` で確認
  - ノード側のリソース使用率の確認: `top` コマンドや `df` コマンドで
  - kubelet の確認: `service kubelet status` や `sudo journalctl -u kubelet`
  - 証明書の確認: `openssl x509 -in /var/lib/kubelet/xxx.crt -text` で Issuer や有効期限を見る
- Practice Test - Worker Node Failure
  - kubelet が停止しているケース、`/var/lib/kubelet/config.yaml` が誤っているケース、`etc/kubernetes/kubelet.conf` が誤っているケースについて
- Network Troubleshooting
  - うーむ、CNI プラグインのインストールって問われるんだろうか

## Section 14: Other Topics

これは試験に役立つ Additional なトピックについて。  jsonpath と kubectl のコマンド。

- Pre-Requisites - JSON PATH
  - YAML, JSON PATH のトレーニングと、-ojson 時の出力を元にした所望のフィールドにアクセスする方法について。
- Advanced Kubectl Commands
  - `-o=jsonpath=~~` や `-o=custom-columns=COLUMN:JSONPATH`, `--sort-by=JSONPATH` などについて。


さあ、残すは模試のみ!
