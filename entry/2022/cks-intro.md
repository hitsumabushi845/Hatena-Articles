---
title: KodeKloud『Kubernetes Certified Security Specialist (CKS)』記録 - Introduction/Understanding the Kubernetes Attack Surface
date: 2022-01-19T10:02:04.000Z
categories:
  - Kubernetes
id: "13574176438054418818"
draft: true
---
Certified Kubernetes Security Specialist(CKS) の取得を目指して勉強していく。  
CKA/CKAD の際にもお世話になった KodeKloud 上で CKS のコースが提供されているので、こちらをベースに勉強を進める。

[https://kodekloud.com/courses/certified-kubernetes-security-specialist-cks/:embed:cite]

ちなみに、KodeKloud はサブスクリプション型のサービスで、月額（または年額一括払い）で提供されている全てのコースを利用できる。  
当然年額一括払いの方が月単価は下がるが、長くても数ヶ月で利用を終了しそうなので月額払いを選択した。

まずは Introduction ~ Understanding the Kubernetes Attack Surface を完了したので、記録。

# Introduction

このコースと CKS 試験について、概要を説明するセクション。

## Course Introduction

コース全体の説明。CKA/CKAD と同様にハンズオン形式のコースとなる。  
コースはカリキュラムに従って以下の内容で提供される。

- Understanding Kubernetes Attack Surface
- Cluster Setup & Hardening
- System Hardening
- Minimizing Microservices Vulnerabilities
- Supply Chain Security
- Monitoring, Logging & Runtime Security
- Mock Exams

ちなみに試験のカリキュラムは以下。

[https://github.com/cncf/curriculum/blob/master/CKS_Curriculum_%20v1.22.pdf:embed:cite]

## Exam Information

CKS試験の概要について。

## Certification Details

試験関連のリンク集。

# Understanding the Kubernetes Attack Surface

## The Attack

投票アプリケーションへの不正な介入のデモを通して「セキュリティ対策をなにもしていない K8s クラスタがあったとき、どのようなことができてしまうか」について見ることができる。  
当初攻撃者はアプリケーションとそのドメインしか情報がない状況であるが、そこからいかにして侵入していくか見ることができおもしろい。

## The 4C's of Cloud Native security

[https://kubernetes.io/docs/concepts/security/overview/:embed:cite]

クラウドネイティブセキュリティにおける多層防御の考え方について。  
4C とは以下の4つを指す。

- Cloud
  - インフラのセキュリティ。
  - クラウドプロバイダーのセキュリティやファイアウォールなど。etcd へのアクセスや暗号化もここに含まれる。
- Cluster
  - クラスタ自身のセキュリティ。
  - クラスタの認証認可やクラスタ全体の NetworkPolicy など。
- Container
  - 実行されるコンテナのセキュリティ。
  - コンテナの脆弱性スキャンや特権ユーザを拒否するなど。
- Code
  - 実行されるアプリケーションコードのセキュリティ。
  - 通信をTLSで暗号化することや、ライブラリの脆弱性など。
  - 基本的にこのコースでは扱う対象ではないが、Secret や mTLS による Pod 間通信の保護といったトピックは提供される。

# 感想

やはり CKA の取得を前提とする認定資格なだけあって、CKA/CKAD に比べてより実践的な内容と感じた。  
今回取り組んだセクションはまだ概要といった内容ではあったが、"The Attack" の節で示された攻撃の例を見るとセキュリティの重要性について腑に落ちて理解できる。

次回以降、具体的な対応方法についてハンズオンを含めて扱うようなので、楽しみ。
