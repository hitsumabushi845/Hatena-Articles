---
title: はてなブログを GitHub 管理に移行しました
categories:
  - 雑記
draft: true
---

表題の通り。
ブラウザ上のエディタで記事を編集するより、やっぱり使い慣れている VSCode で編集したく、いろいろ調べていたらよさげなツールを見つけたのでセットアップして利用している。

## 使っているツール

`gimonfu` というツール。

[https://github.com/basd4g/gimonfu:embed:cite]

Node.js 製の cli ツールで、これをローカルにインストールしても cli から記事の更新ができるのだが、
以下の記事で紹介されているように GitHub Actions を設定すると Git にファイルをプッシュするだけで自動的に post を投稿できる。

## というわけで

以下のリポジトリで管理するようになりました。
VSCode だと Markdown 系の拡張機能が使えるのでよい。

[https://github.com/hitsumabushi845/Hatena-Articles:embed:cite]
