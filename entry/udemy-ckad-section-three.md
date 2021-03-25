---
title: 『Kubernetes Certified Application Developer (CKAD) with Tests』記録 - セクション3
categories:
  - Kubernetes
draft: true
---

引き続き、『Kubernetes Certified Application Developer (CKAD) with Tests』を進めていく。  
今回は Section 3: Configuration。

# Section 3: Configuration 

- Pre-Requisite - Commands and Arguments in Docker

Docker コンテナのコマンドと引数について。  
Dockerfile の CMD, ENTRYPOINT について説明されている。

Docker をちゃんと触ったことないので助かる（そんな状態で Kubernetes に手を出してるのはアレだが...）

- Commands and Arguments in Kubernetes

Pod manifest における `spec.containers[*].command`, `spec.containers[*].args` フィールドについて、Dockerfile の例と比較して。  
`command` は `ENTRYPOINT` を、`args` は `CMD` を上書きする。

Practice Test では、Pod の manifest に指定されているイメージの Dockerfile と比較して、これらのフィールドを操作した。

- Environment Variables

コンテナ内の環境変数について（`spec.containers[*].env`）。  
後段の ConfigMap への布石。

- ConfigMaps

`env` で指定するような key-value pair を外出しできるやつ。  
`kubectl create configmap` で作成したり、

```shell
> kubectl create configmap \
    app-config --from-literal=APP_COLOR=blue \
               --from-literal=APP_MODE=prod
```

yaml を書いたり。

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
    name: app-config
data:
    APP_COLOR: blue
    APP_MODE: prod
```

`spec.containers[*].envFrom[*].configMapRef` で、ConfigMap を環境変数として注入できる。

- Secrets