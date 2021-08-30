---
title: 『Kubernetes Certified Administrator (CKA) with Practice Tests』記録 - セクション8・9
date: 2021-08-30T09:10:51.000Z
categories:
  - Kubernetes
id: "26006613802911673"
draft: false
---
引き続き、『Kubernetes Certified Administrator (CKA) with Practice Tests』を進めていく。  
今回は Section 8: Storage と Section 9: Networking。Storage は CKAD と同内容だと思うので、サクッと行う。

## Section 8: Storage

- Practice Test: PV & PVC
  - "Configure a volume~" という設問で、PV作るのか単にVolumeを作るのかわからず困惑したりした。
- Storage Class
  - おっとこいつは CKAD のときはスキップしていたのだった。
  - 主に Dynamic Provisioning を利用するために使われる。Dynamic Provisioning とは、PVC を作成したときに動的に PV を作成するもの。特にクラウドなど、自由にストレージを用意できる環境で利用され、各種クラウドプロバイダ向けの provisioner が用意されている。
  - クラウドストレージはそのストレージタイプやレプリケーションの有無など、様々なタイプのストレージを構成できるため、これらを Storage Class として管理することで所望の仕様のストレージを簡便に用意できる。

## Section 9: Networking

このセクションが最もボリュームが大きい。

- Prerequisite - Switching Routing
  - スイッチとルータについて。
  - スイッチは同一ネットワーク内の通信、ルータは異なるネットワーク間の通信を担う。
  - `ip link`: ネットワークインターフェースの設定の確認・変更を行う
  - `ip addr add x.x.x.x/x dev yyy`: ネットワークインターフェースにIPアドレスを追加する
  - `ip route add x.x.x.x/x via y.y.y.y`: ルーティングテーブルにルートを追加する
  - 複数のネットワークインターフェースを持つホストをルータとして使用する場合、接続される端末はそのホストをゲートウェイとして設定すれば良いが、ホスト側でネットワークインターフェース間のフォワーディングを許可しなければならない。
    - `/proc/sys/net/ipv4/ip_forward` に `0` が設定されている場合はフォワーディングが許可されていない。`1` にすると許可される。
    - この値は再起動でリセットされるため、`/etc/sysctl.conf` の `net.ipv4.ip_forward` にも同様に設定が必要。
  - `ip` コマンドで変更された設定も永続化するためには `/etc/network/interfaces` に記載が必要。
- Prerequisite - DNS
  - DNS について。
  - `/etc/hosts` ファイルに名前とIPを書いたら名前解決されるけど、管理する端末が多くなったらメンテナンスできなくなるので、DNS を構成したほうがいい。
  - DNS サーバの設定は `/etc/resolv.conf` ファイルに行う。
  - DNS レコード
    - A レコード: IPv4 アドレスとドメインの対応
    - AAAA レコード: IPv6 アドレスとドメインの対応
    - CNAME レコード: ドメインの別名
    - ほか
  - `ping`, `nslookup`, `dig` コマンド
- Prerequisite - CoreDNS
  - Linux ホストを DNS サーバとして構成するためのもの（のひとつ）
  - 実行可能バイナリとしても、Docker コンテナとしても構成できる
  - CoreDNS は Corefile ファイルから構成を読み取る。例えば、ここに `hosts /etc/hosts` と書くとホストの `/etc/hosts` ファイルから DNS を構成できる。
- Prerequisite - Network Namespaces
  - Docker コンテナは namespace によってホストから分離されている
    - コンテナ内からコンテナ外（ホスト）のプロセスは見えない
  - 同様に、ホストのネットワークインターフェイスは見えないので、コンテナにはバーチャルインターフェースを割り当てる
  - `ip netns add <name>`: ネットワークネームスペースを作る
  - `ip netns exec <namespace> <command>`: ネームスペース内でコマンドを実行
    - `ip -n <namespace> <command>` も同様
  - namespace は作るだけでは箱を切るだけなので、インターフェースなども存在しない
  - ns 間の直接接続は virtual cable(pipe) で行える
    - `ip link add <veth-a> type veth peer name <veth-b>`: インターフェースの作成
    - `ip link set <veth-a> netns <namespace-a>`: ネームスペースへの割り当て
    - `ip link set <veth-b> netns <namespace-b>`
    - `ip -n <namespace-a> addr add x.x.x.x dev <veth-a>`: IP アドレスの設定
    - `ip -n <namespace-b> addr add y.y.y.y dev <veth-b>`
    - `ip -n <namespace-a> link set <veth-a> up`: インターフェースの起動
    - `ip -n <namespace-b> link set <veth-b> up`
    - `ip netns exec <namespace-a> ping y.y.y.y` とかで確認
  - 2ネットワーク（namespace）間であればよいが、複数nsあった場合は？
    - virtual switch を作成する
      - linux bridge や open vswitch など
    - linux bridge
      - `ip link add <v-net-n> type bridge`: ブリッジの作成
      - `ip link set dev <v-net-n> up`: ブリッジの起動
      - `ip link add <veth-a> type veth peer name <veth-a-bridge>`: インターフェースの作成
      - `ip link set <veth-a> netns red`: インターフェースの割り当て
      - `ip link set <veth-a-bridge> master <v-net-n>`: ブリッジへの接続
      - `ip -n <namespace-a> addr add x.x.x.x dev <veth-a>`: IPアドレスの割り当て
      - `ip -n <namespace-a> link set <veth-a> up`: 起動
- Prerequisite - Docker Networking
  - `docker run --network none <image>`: None network(ネットワークインターフェースをアタッチしない)
  - `docker run --network host <image>`: ホストのネットワークを利用する（ネットワーク的に独立しない）
  - `docker run <image>`: コンテナ毎にIPアドレスが払い出され、バーチャルブリッジが作成されてネットワークとして独立する（デフォルト）
  - `docker network ls`: でネットワークモードを確認できる。`ip link` するとバーチャルブリッジが見える
    - `ip link` でもみえる
  - コンテナが作成されるとネームスペースが作成され、ブリッジと接続される
  - ポートマッピング
    - ホスト外からコンテナが公開するポートにアクセスできない
    - `docker run -p 8080:80 nginx` といった形で、ホストのポートとコンテナのポートをマッピングすることでアクセスできる
- Prerequisite - CNI
  - ここまで確認してきた、ネームスペースの作成、ブリッジの作成、virtual cable の作成・割り当て、IPアドレスの割り当て...といったプロセスは、Docker も行うが他のネットワークソリューションも同様に行う。
  - コンテナが作成された時のネットワーク接続性の確保や、コンテナが削除された時のリソース開放を担うインターフェース: CNI(Container Network Interface)
  - CNI を実装しているランタイム: rkt, kubernetes, Mesos,...
  - 3rd party プラグイン: Calico, Weave, Cilium,...
  - Docker は CNI を実装していない（CNMという別のもの）
  - k8s が Docker コンテナを立ち上げるときは、Docker 自体は `--network=none` で起動し、別途 CNI プラグインがネットワークの構成を担っている
- Cluster Networking
  - k8s のノード(master/worker)は1つ以上のネットワークインターフェイスを持ち、同じネットワークに接続されている必要がある
  - いくつかのポートが開放されていなければならない
    - 6443: kube-apiserver
    - 10250: kubelet
    - 10251: kube-scheduler
    - 10252: kube-controller-manager
    - 30000 - 32767: services
    - 2379: etcd
    - 2380: etcd client(master node/etcd cluster が冗長化されている場合)
    - https://kubernetes.io/ja/docs/setup/production-environment/tools/kubeadm/install-kubeadm/#%E5%BF%85%E9%A0%88%E3%83%9D%E3%83%BC%E3%83%88%E3%81%AE%E7%A2%BA%E8%AA%8D
- Practice Test - Explore Kubernetes Environment
  - Node の IP アドレス, MAC アドレスの確認
  - デフォルトゲートウェイの確認(ip route)
  - 各種コンポーネントのポート確認(netstat -natulp | grep xxx)
- Pod Networking
  - Pod 間の通信をどうするか。複数のワーカーノードに分かれて配置される Pod は、それぞれがノードを跨いで通信できなければならない。
  - これも同様に、仮想ネットワーク同士をルートテーブルでつなげることで、仮想的なもう一段大きいネットワークを構成する
- CNI in Kubernetes
  - k8s では、kubelet が CNI plugin を呼び出す
  - kubelet の `--network-plugin`, `--cni-bin-dir`, `--cni-conf-dir` で指定される
    - `ps -aux | grep kubelet` で確認できる
- CNI weave
  - weave ってやつがあるやで〜程度の説明
- Practice Test - Explore CNI Weave
  - kubelet の引数を見たりして、CNI を確認する
- Practice Test - Deploy Network Solution
  - Weave のデプロイをする
- IP Address Management - Weave
  - Pod に払い出す IP をどう管理するか。重複しないように払い出す必要がある
  - CNI Plugin の責務
  - CNI 組み込みの管理手法: DHCP or host-local
  - CNI の設定ファイル、ipam セクションで設定される
  - weave はデフォルトでは 10.32.0.0/12 のレンジの IP アドレスを払い出す

```json
{
  //略
  "ipam": {
    "type": "host-local",
    "subnet": "10.244.0.0/16",
    "routes": [
      { "dst": "0.0.0.0/0" }
    ]
  }
}
```

- Service Networking
  - Service は cluster-wide に作成され、いかなるノードに作成された Pod も Service を介して Pod にアクセスできる
  - Pod では、namespace や interface を作成して IP アドレスを割り当てていたが、Service の場合はそうではなく、namespace, interface は作成されない。あくまで仮想的なもの。
  - Service が作成されると、規定の IP レンジから IP が払い出され、kube-proxy が forwarding rule を作成する 
    - つまり、Service とは各ノードの kube-proxy がトラフィックを振り分ける仕組み
    - 規定の IP レンジは kube-api-server の `--service-cluster-ip-range` で指定されている（デフォルト: 10.0.0.0/24）
  - kube-proxy の振り分け方法は3種類: userspace, iptables(default), ipvs
  - `iptables -L -t nat | grep <service-name>`: iptables から service を確認
  - NodePort Service では、全ノードで同じポートからの外部アクセスを受け付ける
- DNS in Kubernetes
  - クラスタ内のリソース(Pod や Service など)への名前解決
  - kubernetes クラスタには DNS が含まれていて、名前解決が行える
  - Service が作成されると、Service 名とその IP アドレスのレコードが登録される
    - 同一 namespace であれば、この Service には Service 名だけで名前解決が可能
    - 異なる namespace の場合は、`<service name>.<namespace>` で名前解決できる
    - 全ての Service は `.svc` サブドメイン配下に登録されるため、`<service name>.<namespace>.svc` でも指定でき、同様に全てのリソースは `cluster.local` 配下に登録されるため、`<service name>.<namespace>.svc.cluster.local` も有効(FQDN)
  - Pod に対するレコードは、デフォルトでは作成されない
    - 登録される場合は、IP アドレスの `.` を `-` に変えたものが名称になる(ex: `10-244-2-5`, `10-244-2-5.default.pod.cluster.local`)
- CoreDNS in Kubernetes
  - kubernetes が DNS をどう実装しているか
  - v1.12 以前では kube-dns、v1.13 以降では CoreDNS が基本
  - CoreDNS は ReplicaSet として kube-system にデプロイされる
  - CoreDNS は `/etc/Corefile` で設定され、ConfigMap として挿入される
  - 様々なプラグインを利用でき、kubernetes plugin もある
    - `pods insecure`: pod の DNS レコードを作成する
  - CoreDNS が構築されると、この Pod に接続するための Service も作成され、これが Node の resolv.conf に nameserver として設定される
    - resolv.conf への設定は kubelet が行い、config.yaml の clusterDNS に設定する

```
.:53 {
  errors
  health
  kubernetes cluster.locacl in-addr.arpa ip6.arpa {
    pods insecure
    upstream
    fallthrough in-addr.arpa ip6.arpa
  }
  proxy . /etc/resolv.conf
  cache 30
  reload
}
```

- Ingress
  - これは CKAD でやったのと同じなので Practice Test だけやって終わり
  - Ingress が GA になり微妙に API が変わっていることだけ注意しよう
    - まあ `k create ingress` で作ってしまっても良い
- Ingress - Annotations and rewrite-target
  - Ingress のアノテーション、特に Nginx ingress controller の rewrite-target について


長かった。あとすこしで全講座がおわる！  
忘れている内容もままありそうなので、Mock Exam や Curriculum をもとに復習していこう。
