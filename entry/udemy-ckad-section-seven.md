---
title: 『Kubernetes Certified Application Developer (CKAD) with Tests』記録 - セクション7
categories:
  - Kubernetes
draft: true
---
引き続き、『Kubernetes Certified Application Developer (CKAD) with Tests』を進めていく。  
今回は Section 7: Services & Networking。

# Section 7: Services & Networking

## Services

k8s 内で動いている Web アプリケーションに対して、クラスタ外から単に Pod の IP アドレスにアクセスしてもアプリケーションへアクセスできない。  
これは、Pod IP は異なるネットワークであり、クラスタ外からは見ることができないものだからだ。

クラスタ外から到達できる IP は基本的にノードの IP となる。そのため、ノードへのアクセスを所望の Pod に配分するリソースが必要となる。  
この際作成するサービスは、例えば NodePort サービスが挙げられる。これはノードの特定のポートに来たリクエストと、指定した Pod や Deployment との間のやりとりを仲介するリソース。

ほかに、

- ClusterIP  
  クラスタ内でのみ疎通性のあるロードバランシング/ディスカバリを行うリソース。
- LoadBalancer  
  外部疎通性のある IP を払い出し、ノードに対するロードバランシングを提供するリソース。クラウドプロバイダが提供する LB を作成する。

などがある[^1]。

[^1]: ほか、ExternalIP や ExternalName など。

NodePort Service はクラスタ内に作成された ClusterIP Service に対してノードの特定ポートを利用した外部疎通性を確保しているもので、トラフィックの経路としては `Node の特定ポート -> ClusterIP Service -> Target Pod` といった流れとなる。  
同様に、LoadBalancer Service は NodePort Service に対する外部疎通性を確保するものである。

そのため、NodePort を作成したら内部的には ClusterIP も併せて作成され、LoadBalancer を作成したら NodePort も作成される。  
このような `ClusterIP <- NodePort <- LoadBalancer` といった関係は、`Pod <- ReplicaSet <- Deployment` や、`Job <- CronJob` といった親子関係に似ている。

## Services - ClusterIP

Deployment によって冗長化された Pod に対して、他の Pod からリクエストを送る場合、内部的なロードバランシングが必要となる。  
この場合に利用されるのが ClusterIP Service であり、先述のとおりこれはクラスタ内でのみ利用可能な Service。

## Ingress Networking

提供するサービスごとにコンテナを分けるといったマイクロサービス的なデプロイをした場合、サービス全体を外部公開した際にパスベースのルーティングを行いたいケースがある。  
NodePort や LoadBalancer はあくまで L4 のロードバランシングを行うため、パスベースルーティングといった L7 のロードバランシングを行うためには、リバースプロキシのようなものが必要になる。

Ingress リソースは L7 のロードバランシングを提供する。  
Pod や Service といったリソースのように、マニフェストファイルをもとに L7 ロードバランサを提供することができ、K8s 内で一貫した外部疎通性の確保が可能となる。

Ingress リソースは、デフォルトのクラスタでは提供されていない。多くのマネージド k8s では、クラウドプロバイダが提供する外部の LB と連携した Ingress Controller がデプロイされている。  
そうでないクラスタ(オンプレミスのクラスタや Oracle Cloud Infrastructure の k8s などが該当する)では、Nginx controller を利用することとなる。 

前者の外部 LB を利用した方法では、外部 LB がそれぞれの NodePort へトラフィックを転送する。  
後者の場合は、クラスタ内に Nginx Ingress controller がデプロイされるため、別途 LoadBalancer や NodePort で外部疎通性を確保することとなる。LoadBalancer サービスは Nginx controller へトラフィックを転送し、Nginx controller が各 Pod へトラフィックを転送する。

## Network Policies

Pod 間の通信ルールを設定するリソース `NetworkPolicy` について。  
Kubernetes では、NetworkPolicy を設定しない場合は全ての通信が許可される。

NetworkPolicy では、Pod への通信である ingress rule と、Pod からの通信である egress rule を設定できる。
NetworkPolicy を設定した場合、そこで許可されていないトラフィックはすべて拒否される。

マニフェストの例は以下の通り。

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: test-network-policy
spec:
  podSelector:
    matchLabels:
      role: db
  policyTypes:
  - Ingress
  - Egress
  ingress:
  - from:
    - ipBlock:
        cidr: 172.17.0.0/16
        except:
        - 172.17.1.0/24
    - namespaceSelector:
        matchLabels:
          project: myproject
      podSelector:
        matchLabels:
          role: frontend
    ports:
    - protocol: TCP
      port: 6379
  egress:
  - to:
    - ipBlock:
        cidr: 10.0.0.0/24
    ports:
    - protocol: TCP
      port: 5978
```

## Developing network policies

上記マニフェストの各種ルールについて。

- ipBlock  
  指定した CIDR から/へ の通信を許可する。
- namespaceSelector  
  指定した namespace から/へ の通信を許可する。
- podSelector  
  指定した Pod から/へ の通信を許可する。

from/to にリストのアイテムとして指定されたルールは、それぞれが or 条件として処理される。アイテム内に複数のルールを指定した場合は、and 条件として処理される。
