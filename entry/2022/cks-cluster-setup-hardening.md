---
title: KodeKloud『Kubernetes Certified Security Specialist (CKS)』記録 - Cluster Setup & Hardening
categories:
  - Kubernetes
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

## Lab: Kubectl Proxy & Port Forward

## Kubernetes Dashboard

## Securing Kubernetes Dashboard

## Lab: Securing Kubernetes Dashboard

## Verify platform binaries before deploying

## Lab: Verify platform binaries
