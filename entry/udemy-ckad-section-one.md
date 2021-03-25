---
title: 『Kubernetes Certified Application Developer (CKAD) with Tests』記録 - セクション1・セクション2
date: 2021-03-20T04:08:00.000Z
categories:
  - Kubernetes
id: "26006613705795273"
draft: false
---
今年上半期は Certified Kubernetes Application Developer(CKAD) の取得を目指して勉強していく。  
先人の受検ログを見ると、Udemy にある Mumshad Mannambeth 氏の『Kubernetes Certified Application Developer (CKAD) with Tests』を活用している人が多かったので、先日のセールのタイミングで購入。

[https://www.udemy.com/share/1013BQBUEddV9bRXo=/:embed:cite]

同氏による CKA のコースも合わせて購入した。

まずはセクション1と2を完了したので、記録。

# Section 1: Introduction

このセクションでは、コースの概観や CKAD の説明が行われる。

このコースでは CKAD の取得を目的として、出題範囲に従ってレクチャーが展開される。  
Workloads APIs や Service APIs 等の基礎、Kubernetes のアーキテクチャといった基礎の部分は前提知識となるため、全くの初心者は以下のコースから取り組むことが推奨されていた。

[https://www.udemy.com/course/learn-kubernetes/:embed:cite]

上記のコースの存在は知らなかったが、すでに[『Kubernetes 完全ガイド』を写経しながら通読した](https://github.com/hitsumabushi845/training-kubernetes-perfect-guide)こと、Kubernetes the Hard Way をやったこと[^1]からスキップしてよいと判断し、このまま進めていく。

特筆すべき点は、ここで CKAD/CKA の受験料が 15% オフになるバウチャーコードが提供されること。
通常 $300 の受験料が $255 になるので、セールのタイミングで購入しておくと実質無料になる。

イントロダクションはサクッとこんな感じ。次セクションから具体的な解説に入るようだ。
完全ガイドを通読したとはいえ、忘れてしまっているところも結構あるので復習も兼ねて進めていこう。

[^1]: これは kubeadm 使用/非使用でそれぞれ行った。kubeadm を使用したクラスタ構築の記録は Qiita に書いた。[https://qiita.com/hitsumabushi845/items/4a24109cf1408c0e30de:embed:cite]

# Section 2: Core Concepts

このセクションでは、Pod, ReplicaSet, Deployment, Namespace, Service といった基本的なリソースについての説明とハンズオンテストが提供される。

- Recap - Kubernetes Architecture
- Recap - Pods  

この2つはわりと日頃触れてるのでサクッと流す。  
ここではじめて Practice Test が入る。Practice Test は、KodeKloud(Katacoda) を使って作られており、ブラウザ上で実際に k8s クラスタを触りながら確認テストができる。  
これが学習体験としてけっこう楽しい。

<figure class="figure-image figure-image-fotolife" title="Practice Test のイメージ">[f:id:hitsumabushi845:20210325011608p:plain]<figcaption>Practice Test のイメージ</figcaption></figure>

- Recap - ReplicaSets

ReplicaSet は普段使わないので、思い出しがてらちょっとしっかりやる。
ReplicationController の存在を（ほぼ）はじめて知る。ReplicaSet には selector を定義できるが、ReplicationController はできない。  

Deployment と違い、ローリングアップデートもできない。  
例えば `kubectl edit` などで定義を書き換えても配下の Pod は再作成されない。  
というかそもそも、ReplicaSet の上位に存在して、ReplicaSet を管理することでローリングアップデートやロールバック（など）を可能にしたリソースが Deployment。

- Recap - Deployments

というわけで Deployment。  
Deployment 固有のフィールドとか結構忘れてるなーと思ったけど、このセクションではそれは扱わないっぽい。ここでは基礎のみ復習して、後段のセクションで詳細を扱うようだ。

- Recap - Namespaces

Namespace リソースそのものは簡単なのでサクッと。  
...と思ったら急に Service とか DNS が出てきてびっくりした。Namespace をまたいだ名前解決の話もここだったようだ。

- Certification Tip: Imperative Commands 

yaml を使わずに kubectl コマンドだけでリソースを作る方法について。  
CKAD の試験は時間がタイトなので、この手法をよく使うらしい。  

Practice Test では Pod と Deployment の作成は難なくできたが、Service の作成が慣れておらず手間取った。  

セクション2はここまで。次はセクション3: Configuration。
