---
title: 『Kubernetes Certified Application Developer (CKAD) with Tests』記録 - セクション4・セクション5
date: 2021-05-03T18:08:15.000Z
categories:
  - Kubernetes
id: "26006613712885760"
draft: false
---
前回の記録から間が空いてしまったが引き続き、『Kubernetes Certified Application Developer (CKAD) with Tests』を進めていく。  
今回は Section 4: Multi-Container PODs と Section 5: Observability。

これらのセクションは結構短い。

# Section 4: Multi-Container PODs

マルチコンテナ Pod のパターンについて、アプリケーションコンテナとロギングを扱うコンテナの2つのコンテナを例に説明。このセクションは以下の説明と Practice test のみ。

- Sidecar pattern  
アプリケーションコンテナのログをログサーバなどのサービスに送信するなど、補助的な機能をもつコンテナを配置する。
- Adapter pattern  
アプリケーションコンテナのログをフォーマットするなど、外部とのインターフェースを提供するコンテナをもつパターン。アダプタコンテナがインターフェースを担うことで、アプリケーションコンテナ自身のログの柔軟性を高めることが出来る。
- Ambassador pattern  
アプリケーションコンテナのログの送信先を扱うような、外部のサービスとの接続を仲介するコンテナを持つパターン。このコンテナがあることで、アプリケーションコンテナからは常にこのコンテナ(localhost)に接続すればよく、アンバサダーコンテナを差し替えることで接続先を柔軟に切り替えられる。

Practice Test は、Elasticsearch に Sidecar コンテナからログを送信し、Kibana からログを閲覧できるようにするハンズオンだった。といっても既存の Pod に指定のコンテナを追加するだけだが。

# Section 5: Observability

## Readiness Probes 

Pod が作成されると、Pending -> ContainerCreating -> Running といったように、Status が遷移する。
コンテナが起動した後、"Running" であることを Kubernetes はどう知るのか？  
Jenkins を Kubernetes で立ち上げると、Web 画面は利用可能でないが Pod は "Running" になる。
Service は "Running" な Pod にルーティングするので、アプリケーションとして利用可能でないのにルーティングされてしまう可能性がある。

そこで、アプリケーションが利用可能であること(リクエストを受け付けることができること)を Readiness Probes を用いて検証する。
これは HTTP API や TCP, コマンド実行で検証する。
この検証に失敗した場合、Service はその Pod へトラフィックをルーティングしない。

これは `spec.containers[*].readinessProbe` に設定する。

```yaml:HTTP
    readinessProbe:
      httpGet:
        path: /api/ready
        port: 8080
```

```yaml:TCP
    readinessProbe:
      tcpSocket:
        port: 3306 
```

```yaml:command
    readinessProbe:
      exec:
        command:
        - cat
        - /app/is_ready
```

## Liveness Probes

Docker では、コンテナの作成に失敗したらそのコンテナは失敗したままだが、Kubernetes では Pod の作成に失敗したら、再作成を行う。
Pod の作成に成功したこと(Pod 内のコンテナが正常に動作していること)を検証するための機構が Liveness Probe。
この検証に失敗した場合、コンテナを再起動する。

こちらも同様に、HTTP API call や TCP, コマンド実行で検証を行う。

書き方は、おおむね Readiness Probe の `readinessProbe` を `livenessProbe` に変更したもの。

## Container Logging

`kubectl logs` コマンドについて。Pod 名を指定して Pod のログを見たり、加えてコンテナ名を指定してコンテナのログを絞って見たり。ここは普段よく使っているので流す。

## Monitor and Debug Applications

kubernetes のノード監視について。通常の運用では Prometheus や Datadog などを使ってメトリクスを記録していくが、CKAD では公式に開発されている Metrics Server を用いるようだ。過去は heapster というコンポーネントだったが、今は metrics-server らしい。

こいつはメモリ上に記録するため、メトリクスを過去に遡って見ることはできないが，`kubectl top` コマンドでノードや Pod のメトリクスを確認できるようになる。
