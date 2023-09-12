---
title: "onionドメインの取得"
date: 2023-06-27T00:00:00+09:00
draft: true
pinned: false
tags:
- Tor
---
SearXNG用にonionドメインを取りました。
<!--more-->

## onionドメインの意味
匿名性が高いことから違法性の高いサービスに使われておりイメージが悪い人もいるだろうが、onionドメインは通常のドメインと比べてトラフィックの追跡を困難にしてくれるので安全で、検閲回避にも使われています。

また、ドメインを運用するにあたってグローバルIPを持たなくてよいというメリットもあります。

## onionドメインの取得
Ubuntu Jammy amd64前提です。まずはTorのインストールから。
```bash
$ sudo apt install apt-transport-https
$ sudo sh -c 'echo "deb [arch=amd64 signed-by=/usr/share/keyrings/deb.torproject.org-keyring.gpg] https://deb.torproject.org/torproject.org $(lsb_release -sc) main" >> /etc/apt/sources.list.d/tor-project.list'
$ wget https://deb.torproject.org/torproject.org/pool/main/d/deb.torproject.org-keyring/deb.torproject.org-keyring_2022.04.27.1_all.deb
$ sudo apt install ./deb.torproject.org-keyring*.deb
$ sudo apt update && sudo apt install tor
```
正しくインストールされたか確認
```bash
$ tor --version
$ sudo systemctl status tor
```
問題なければ/etc/tor/torrc を書き換える。
```conf
HiddenServiceDir /var/lib/tor/hidden_service/
HiddenServicePort 80 127.0.0.1:80
```
編集後、torを再読込してドメインを取得できているか確認。
```bash
$ systemctl reload tor
$ cat /var/lib/tor/hidden_service/hostname
```
私の環境では kd7pxzi2i4jxi3r3srh7jhymvwrddvmntndpitdzfedyxcgxnjymkxad.onion が取れました。

## nginxでの使い方
こんなふうに書いてやれば良い。
```nginx
server {
        listen 80;
        server_name kd7pxzi2i4jxi3r3srh7jhymvwrddvmntndpitdzfedyxcgxnjymkxad.onion;
        ...
}
```
これでTorからアクセスすると接続できる。
{{< figure src="20230627-112759.png" width=300px  height=300px >}}

で、せっかく使えるようになったものの、onionドメインのhttps化についてまだ勉強が足りておらずhttpsで運用できないこと、弊サーバのhttp都合がちょっと悪くてすぐにはこのドメインを運用できない状態なので、本格的な運用は後日対応とさせてください・・・