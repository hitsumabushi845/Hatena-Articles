---
title: 2021年の目標をふりかえる
date: 2021-12-31T02:19:05.000Z
categories:
  - 雑記
id: "13574176438047875011"
draft: true
---

2021年も1年お疲れ様でした。

[https://hitsumabushi845.hatenablog.com/entry/2021/01/02/092252:embed:cite] 

で掲げた目標について、結果を確認していきましょう。

## 資格をとる

> 今年受検したいと思っているものは以下の通り。  
> - CKA or CKAD  
> - TOEIC  

今年は以下の認定資格を取得した。

- Certified Kubernetes Application Developer
  - 受検ログ: https://qiita.com/hitsumabushi845/items/a6ae8935eaf509fb5381
- Certified Kubernetes Administrator
  - 受検ログ: https://qiita.com/hitsumabushi845/items/1af69cc5db65a1607d70
- AWS Certified Solutions Architect - Associate
  - 受検ログ: https://qiita.com/hitsumabushi845/items/98c51cef2a11bf20f85f

今年はあまり英語を使う機会がなかったので、TOEIC の受検はとりやめた。
その代わり、業務で AWS を触る機会があったので、SAA を取得した。

おおむね目標通り。考えてみれば IT 系の資格は今年のこれらがはじめての取得。Credly にバッヂが増えていくのは気分がいいので、来年もコンスタントに資格を取っていきたい。

## こまめにブログを書く

> このブログでは特定の技術に関する話題**以外の**、技術以外の話やポエムなどを書いていきたいと思っている。  
> 特定の技術要素については、せっかく Organization に属しているので Qiita に書くつもりでいる。
> こまめにと言っても、せいぜい月2程度のペースでゆるくやっていきたい。  

今年、Qiita には 16 件の記事を投稿した。
このブログには主に CKAD/CKA の学習ログを投稿しており、合算すると月2のペースはクリアできたと思う。

来年もこれくらいのペースで投稿できるとよい。

## Terraform を勉強する

> 2021年から本業・副業ともに Terraform を扱うようなので、ゆるく勉強する。  
> 仕事で使うのでまあ勉強できるだろうと高をくくっている。
> 特に到達目標はないが、仕事が回る程度に扱えればよいと思う。

中盤でのふりかえりでも述べたが、業務で扱う中でなんとなく雰囲気は掴めた気がする。  
ちなみに Terraform を使った業務では GCP と OCI を主に扱っている。OCI は [Resource Manager](https://www.oracle.com/jp/devops/resource-manager/) という IaC のサービスがあって、Terraform HCL をそのまま使えるので便利。

## golang を勉強する

> そろそろ新しい言語を勉強したい。  
> golang は、複数のプラットフォームに向けてコンパイルできるということで、かねてより興味は持っていたが、  
> Kubernetes に触れるようになったことでより勉強するモチベーションが上がっている。
> とりあえず、[Gopher 道場](https://gopherdojo.org/) を進めていこう。

最近は k8s のカスタムコントローラや Admission Webhook を実装しており、そのため Go に入門した感じがある。
しかし完全に雰囲気で進めてしまっているので、来年はもう少し体系的に勉強していきたい。

読みたいと思っている書籍はこれ。

[https://www.oreilly.co.jp/books/9784873118222/:embed:cite]

## おわりに & 来年へ向けて

今年掲げた目標はおおむね目標通りに推移したように思う。

今年初頭の自分をふりかえってみると、k8s マッタクワカランだったところから今は CKA/CKAD も取得して、CR や Admission Webhook もなんとなく実装できるようになった。  
AWS/GCP/OCI についてもそれぞれ雰囲気程度には扱えるようになり、昨年までのオンプレおじさんから一転クラウドおじさんになった気がする。

というわけで、来年も引き続き勉強を続けていきたい。来年はもう少し本を読むようにしたいなぁ。
2022年の目標はまたお正月に書きましょう。
