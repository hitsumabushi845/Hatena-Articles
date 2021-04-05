---
title: 『Kubernetes Certified Application Developer (CKAD) with Tests』記録 - セクション4・セクション5
categories:
  - Kubernetes
draft: true
---
引き続き、『Kubernetes Certified Application Developer (CKAD) with Tests』を進めていく。  
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

## Readiness and Liveness Probes 

## Liveness Probes

## Container Logging

## Monitor and Debug Applications