---
title: 『Kubernetes Certified Administrator (CKA) with Practice Tests』記録 - セクション7
date: 2021-08-11T08:35:26.000Z
categories:
  - Kubernetes
id: "26006613796206440"
draft: false
---
引き続き、『Kubernetes Certified Administrator (CKA) with Practice Tests』を進めていく。
今回は Section 7: Security。扱う内容が多岐にわたり、ボリュームも大きいが、セキュリティ周りのトピックは新設された CKS に移動したため、今の CKA だとほとんど関係ない・・・？

## Section 7: Security

- Kubernetes Security Primitives
  - k8s のセキュリティの概観について。
  - ノード（ホストするマシン）はパスワード認証でなく 鍵認証であるべき
  - Authentication
    - ID/Password
    - ID/Token
    - 証明書
    - LDAP
    - Service Accounts
  - Authorization
    - RBAC
    - ABAC
    - Node
    - Webhook
  - コンポーネント間の通信は TLS 化されている
  - Network Policies
- Authentication
  - クラスタにアクセスする人: クラスタ管理者、開発者、プログラム（bot）、エンドユーザー、etc...
  - クラスタ管理者と開発者はUser
    - kube-apiserver は API を処理する前に認証を行っている
    - 認証はパスワードファイル、トークンファイル、証明書、3rd party IDaaS
    - パスワードファイル
      - 非推奨
      - csv ファイルで管理されている
      - `{password},{username},{user id},{group(optional)}` 形式で記載されたファイルを kube-apiserver に読ませる
      - kube-apiserver の引数に `--basic-auth-file=/path/to/users.csv` と指定する
      - `curl -v -k https://master-node:6443/api/v1/pods -u "user1:password123"`
    - トークンファイル
      - 非推奨
      - パスワードファイルとほぼ同じ。
      - `{token},{username},{user id},{group(optional)}` 形式
      - `--token-auth-file` に指定する。
      - `curl -v -k https://master-node:6443/api/v1/pods -H "Authorization: Bearer xxxx"`
  - Bot user は ServiceAccount
- TLS
  - TLS Basics
    - 共通鍵暗号、公開鍵暗号、証明書(ルート証明書・サーバー証明書・クライアント証明書)、認証局などについて。
  - TLS in Kubernetes
    - k8s におけるホスト間の通信、ユーザーとkube-apiserver間の通信、そしてコンポーネント同士の通信は暗号化されている
    - これらはサーバ証明書とクライアント証明書によって成り立っている
      - kube-apiserver, etcd cluster, kubelet はサーバ証明書を持っている
      - kubectl を実行するユーザや、kube-apiserver と通信する kube-scheduler, kube-controller-manager, kube-proxy はクライアント証明書を持っている
        - kube-apiserver は etcd cluster, kubelet から見たときにクライアントなので、クライアント証明書も持つ（サーバ証明書と同一になることもある）
    - これらの証明書を発行するための認証局、ルート証明書も必要となる。
  - TLS in Kubernetes - Certificate Creation
    - 証明書を作る方法について。以下のツールがよく利用される。
      - easyrsa
      - openssl(この講座ではこちらを利用する)
      - cfssl(これは hard way で使ったな)
    - 認証局(CA)の作成
      - 認証局の秘密鍵を作成: `openssl genrsa -out ca.key 2048`
      - 証明書署名要求(CSR)を作成: `openssl req -new -key ca.key -subj "/CN=KUBERNETES-CA" -out ca.csr`
      - 証明書の発行: `openssl x509 -req -in ca.csr -signkey ca.key -out ca.crt`
    - クライアント証明書の作成(ex. Admin User)
      - 秘密鍵: `openssl genrsa -out admin.key 2048`
      - CSR: `openssl req -new -key admin.key -subj "/CN=kube-admin/O=system:masters" -out admin.csr`
      - 発行: `openssl x509 -in admin.csr -CA ca.crt -CAkey ca.key -out admin.crt`
      - `CN`(コモンネーム) がユーザーとして扱われ、`O`(組織) がグループとして扱われる。
        - 各種コンポーネントのクライアント証明書においては、`CN`, `O` は指定の値にする必要がある。
        - kubelet の場合は `system:node:{nodeName}`, kube-controller-manager の場合は `system:kube-controller-manager`...といった形に。
  - View Certificate Details
    - 証明書の場所は kubeadm で構築したクラスタの場合は static pod のマニフェストに引数として書いてある
    - `*.crt` ファイルの確認: `openssl x509 -in /path/to/certificate.crt -text -noout`
    - ログの確認
      - service log: `journalctl -u xxx.service -l`
      - pod log: `kubectl logs xxx`
      - kube-apiserver/etcd cluster が落ちている場合は Docker のログを直接: `docker logs xxx`
- Certificates API
  - ユーザーのクライアント証明書を作成するためのAPIが備わっている
  - クライアント証明書を作成したいユーザは秘密鍵とCSRを作成し、CSR から CertificateSigningRequest リソースを作成する。

```yaml
apiVersion: certificates.k8s.io/v1
kind: CertificateSigningRequest
metadata:
  name: foo
spec:
  groups:
  - system:authenticated
  usages:
  - digital signature
  - key encipherment
  - server auth
  request:
    {base64 encoded csr}
  signerName: kubernetes.io/kube-apiserver-client
```

- KubeConfig
  - クラスタに対してAPIを実行するとき、APIエンドポイントの情報やクライアント証明書の情報が必要となる。curl で叩くときはこれらを指定しているが、kubectl から API を実行するときは kubeconfig ファイルを利用する。
  - デフォルトでは kubectl `~/.kube/config` を見に行く。
    - `$KUBECONFIG` を指定することで `$HOME/.kube/config` 以外の kubeconfig を読み取り先にできる
  - kubeconfig は Clusters, Users, Contexts からできている。
    - Clusters: クラスタのAPIエンドポイント、CA証明書の情報を持つ
    - Users: ユーザとクライアント証明書の情報を持つ
    - Contexts: Cluster と User の組み合わせ
    - kubectl が利用するコンテキストは current-context フィールドに指定される。
  - kubeconfig の確認: `kubectl config view`
  - current-context の変更: `kubectl config use-context {context name}`
- API Groups
  - kubernetes API はいくつかのグループに分かれている
- Authorization
  - クラスタの利用者（管理者、開発者、Botユーザー...）ごとに権限を適切に分けたい
  - 認可方式(Authorization Mode)は以下がある
    - Node Authorization
      - kubelet は Node 上のリソースを管理（どのリソースを作るか、あるリソースがどのような状態か）するため、kube-apiserver にアクセスする。
      - これらの操作は Node Authorizer によって処理される
      - kubelet に指定した証明書では、グループを `system:nodes`, 名称を `system:node:xxx` とした。これらの名称を持つユーザは Node Authorizer で認可される。
    - ABAC(Attribute Based Access Control)
      - ABAC では、JSON 形式のポリシーファイルを作成し、許可される操作をユーザ毎に定義する。
      - これはユーザが追加される毎にこのファイルを編集し、かつ kube-apiserver の再起動が必要となる。
      - 不便なので、RBAC を使う。
    - RBAC(Role Based Access Control)
      - RBAC では、ロールを定義し、ユーザにロールを割り当てる。
      - ロールではどの操作が許可されているか定義されている。
    - Webhook
      - 認可を外部に任せる場合は、こちらを利用する。
    - AlwaysAllow
    - AlwaysDeny
      - 上記は認可をせずに全て許可/拒否をするモード。
  - Authorization Mode は、kube-apiserver の引数(`--authorization-mode`)に指定する。
    - デフォルトは `AlwaysAllow`
    - カンマ区切りで複数指定することもでき、複数指定した場合は指定した順番に認可が処理される。
- RBAC
  - ロールは Role リソースで作成する。
    ```yaml
    ```
    - resourceName フィールドでさらに認可するリソースの名称で絞ることが可能
  - ユーザをロールに紐付けるには、RoleBinding リソースを作成する。
  - Role は namespace-wide なリソースであるため、Role が作成された namespace 内のリソースにのみ認可される。cluster-wide に認可するためには ClusterRole/ClusterRoleBinding を使用する。
  - 自分があるリソースに対する操作が可能か確認するコマンド: `kubectl auth can-i {verb} {resource} [--as {user name}]`

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  name: developer
rules:
- apiGroups: [""]
  resources: ["pods"]
  verbs: ["list", "get", "create", "update", "delete"]
- apiGroups: [""]
  resources: ["ConfigMap"]
  verbs: ["create"]
---
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: devuser-binding
subjects:
- kind: User
  name: dev-user
  apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: Role
  name: developer
  apiGroup: rbac.authorization.k8s.io
```

- Cluster Roles and Cluster Role Bindings
  - Pod, Deployment, Job, Service, Secret, PVC... は namespace-wide。Node, PV, CSR, Namespace, そして ClusterRole, ClusterRoleBinding は cluster-wide なリソース。
  - 各リソースのスコープを確認したい場合は、`kubectl api-resources --namespaced=true/false` で。
  - Cluster Admin や Storage Admin のロールを作る場合は ClusterRole を作成する。
- Images Securely
  - Pod の実行に使用される Docker イメージについて。
  - `image: nginx` と指定された場合、`docker.io/nginx/nginx` から取得される。レジストリのエンドポイントやユーザ名を明示的に指定することで異なるレジストリから取得できる。
  - プライベートレジストリの場合、ログインが必要となる。認証情報は Secret で持つことができ、`kubectl create secret docker-registry xxx --docker-server=private-registry.io --docker-username=foo --docker-password=bar --docker-email=foobar@example.com` といった形で作成する。
  - これを Pod の `imagePullSecrets` セクションに指定することで認証を行える。
- Security Contexts
  - これは CKAD のときもやった。
  - runAsUser と capabilities について。
- Network Policies
  - これも CKAD のときにやった。

以上、Section 7: Security。  
現在の試験範囲に含まれないトピックが多かったが、いずれも重要な要素なので勉強する意味はある（と言い聞かせないと困るくらいには時間がかかったセクションであった）。
