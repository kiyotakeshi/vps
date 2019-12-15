# WordPress構築の要件

- [vultrで構築](https://my.vultr.com/deploy/)
    - 残クレジットが10ドルほどある

- WordPressで記事を投稿できる状態にする
    - もしよければ何本か記事を書いてみる

- 常時SSL化に対応する
    - [Let's encrypt](https://letsencrypt.org)を使用

- ドメインはお好きなものを取っていただければ
    - [お名前.comのアカウントを使っていただければ](https://navi.onamae.com/top)
    - DNSの登録はドメイン取得業者(レジストラ)のままでも良いし、AWS,GCPに変えてもOK

- 最低限のセキュリティに配慮
    - rootユーザでログインできないようにする
    - sshは22番ポートで行わない(22番は閉じる)
    - firewalldは有効にする

## マシン要件(vultr)

- 東京リージョン
- centos8
- 1ヶ月5ドルのプラン(1CPU,1GB RAM,1000GB Bandwidth)

## ミドルウェア要件

- phpは7系を使用
    - centos8はデフォルトで7系だったはず
  
- webサーバは、 **nginxを使用**

- DBはお好きなものを(MySQLが無難かな)


## 参照するサイト

- [さくらのナレッジ](https://knowledge.sakura.ad.jp/11101/)
    - apacheで書かれている情報は、nginxにして修正していただければ
    - ただし、phpMyAdminは攻撃されやすいため、今回は使用しない(LAN環境で使うならあり)

## 手順書について

- 構築周りの手順書(Markdown)を残してくれたらうれしい(初めての構築なのでなくてもOK)

- 以下の手順書は存在、参考にしていただければ
    - [初期セットアップ](./vpsの初期セットアップ.md)
    - [sshポートの変更(初期セットアップより詳細に記載)](./sshポートの変更.md)
