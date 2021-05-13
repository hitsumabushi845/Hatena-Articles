---
title: 『Kubernetes Certified Application Developer (CKAD) with Tests』記録 - セクション6
categories:
  - Kubernetes
draft: true
---

引き続き、『Kubernetes Certified Application Developer (CKAD) with Tests』を進めていく。  
今回は Section 6: POD Design。

# Section 6: POD Design

## Labels, Selectors and Annotations

リソースを分類する方法について。  
ラベルは `metadata.labels` に設定でき、設定したラベルにしたがって `kubectl get pods --selector key=value` といったように絞り込むことができる。

ReplicaSet や Deployment では、ラベルをもとに指定したリソースの数を判断している。
また、Service では、ターゲットとなるリソースをラベルから指定できる。

上述の通り、ラベルはリソースを分類・振り分けするためのフィールドだが、アノテーションはそのほかの情報を付与するためのフィールド。
... と説明されているが、場合によってはアノテーションも分類・振り分け用途に使う気がする...


## Rolling Updates & Rollbacks in Deployments

Deployment のローリングアップデートとロールバックについて。  
Pod では、使用しているコンテナイメージを更新するためには Pod の再作成が必要になる。
一方、Deployment では配下の Pod についてコンテナイメージの更新をローリングアップデートで行える。

Deployment では、アップデート戦略(`StrategyType`)のデフォルトが `RollingUpdate` であるので、 ローリングアップデートは `kubectl set image` でイメージを変更するか、マニフェストを更新して `kubectl apply` をするだけでよい。  
`StrategyType` を `Recreate` にすると、Deployment であっても再作成により更新が行われる。

変更のロールバックには `kubectl rollout undo` が使用される。リビジョンを指定しなかった場合は1つ前にもどる。  
リビジョンは `kubectl rollout history` で確認できる。

ここのハンズオンは結構楽しい。Recreate と RollingUpdate の挙動について実際に手を動かして確認できた。  

## Jobs, CronJobs

Job について。  
Pod などのリソースは常に動作していることが期待されるものだが、Job はバッチ処理など、特定の実行単位があり、終了する処理を行うリソース。

Pod でも終了する処理を実行することは可能だが、終了してしまうがゆえにヘルスチェックに失敗し、コンテナの再起動が走ってしまう。
また、Job では、同一の処理を並列で実行させたり、成功数を規定できたりする。

CronJob は、指定した時間に Job を作成するリソース。`spec.schedule` に cron と同じフォーマットで時間を指定できる。

apiVersion が、Job は `batch/v1`, CronJob は `batch/v1beta1` なのに注意。

