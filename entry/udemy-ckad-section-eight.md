---
title: 『Kubernetes Certified Application Developer (CKAD) with Tests』記録 - セクション8
date: 2021-06-10T15:48:28.000Z
categories:
  - Kubernetes
id: "26006613774485036"
draft: false
---
引き続き、『Kubernetes Certified Application Developer (CKAD) with Tests』を進めていく。  
今回は Section 8: State Persistence。ここまでで試験範囲は終わり。以降は Mock Exam(模試) となる。

# Section 8: State Persistence

## Volumes

コンテナが生成するデータ（ファイル）を永続化させるため、Pod に Volume をアタッチする。
以下に示すマニフェストでは、ノードの記憶領域をコンテナに `/opt` としてマウントしている。

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: sample-pod
spec:
  containers:
  - image: alpine
    name: alpine
    command: ["/bin/sh", "-c"]
    args: ["shuf -i 0-100 -n 1 >> /opt/number.out"]
    volumeMounts:
    - mountPath: /opt
      name: data-volume
  volumes:
  - name: data-volume
    hostPath:
      path: /data
      type: Directory
```

`hostPath` は特定のノード（Pod の配置されるノード）の記憶領域を利用するが、マルチノードの場合やノードのフェイルオーバーなど、ノードが替わった場合に一貫性が損なわれる。
そのため、通常は後述する Persistent Volume によりクラウドストレージ等を利用して永続化を図る。

volume の種別は他に、`emptyDir`, `downwardAPI`, `projected`などがある。
`emptyDir` は一時的な領域で、Pod が terminate されると削除される。`downwardAPI` は Pod の情報などをファイルとして保持するためのもの、`projected` は複数の volume をまとめるもの。

## Persistent Volumes

Persistent Volumes(PV) は、ストレージ空間を提供するプールのような役割をしている。
大量の Pod をデプロイする場合に、各マニフェストで逐一 Volume を管理するのではなく、PV によって利用されるストレージを作成・管理し、Pod からは Persistent Volume Claim(PVC) を用いて利用する。

```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: pv-sample
spec:
  accessModes:
  - ReadWriteOnce
  persistentVolumeReclaimPolicy: Retain
  storageClassName: manual
  capacity:
    storage: 1Gi
  hostPath:
    path: /tmp/data
```

## Persistent Volume Claims

先述した PV を Pod から利用するためのリソース。
`accessModes` や `resources` に指定した内容に合致する PV が紐付く。


```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: pvc-sample
spec:
  accessModes:
  - ReadWriteOnce
  resources:
    requests:
      storage: 500Mi
```

素の k8s では、PV を作って PVC を作って... という流れで利用するが、GKE などではデフォルトでいい感じの StorageClass (`kubernetes.io/gce-pd`)が設定されており、PVC を作るだけで PV が Dynamic Provisioning される。

## Storage Classes, Stateful Sets...

これらは CKAD の範囲ではないそうなので、今はスキップ。
