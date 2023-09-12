---
title: "WEBサーバのPHPのバージョンの上げ方(nginx対応)"
date: 2023-08-23T09:00:00+09:00
description:
draft: false
tags:
- PHP
- nginx 
---
<!--more-->
めっちゃ恥ずかしい話だが、自鯖のPHPのバージョンが7.4でとっくにdeprecatedだったので上げました。
## この記事 is 何

- PHPのバージョンアップのやり方/注意点がわかる
- nginxを使う(≒fpmを使う)場合の注意点がわかる

## 従来研究
実は本内容について検索してもあんまりちゃんとした記事は出てこない。出てくるのは精々下記に分類されるもの。

1. レンタルサーバ内のPHPのバージョンアップ
2. WEBサーバのデフォルトPHPのバージョンアップ
3. apacheに対応したPHPのバージョンアップ

### 1 について
レンタルサーバ毎にGUIでこうこう操作すれば良いぜーと書いてある記事。

勿論、私みたいに自宅に鯖置いて鯖缶している人ばかりではないのでこの手の記事の需要については否定しない。当然レンタルサーバ前提の記事なので自宅の鯖環境では役に立たないし、PHPのバージョンアップを勝手によしなにやってくれるので、技術的背景がわからない。

### 2 について
WEBサーバで使われるPHPのデフォルトバージョンを上げる方法が書いてあるタイプ。

大体はupdate-alternatives --config phpを実行して切り替えて、古いPHPを消してはい終わりみたいな記事。勿論記事のタイトルに違わないがこれだけでは大体の環境でエラーが出るし、大半の人がエラーのハンドリング出来ないイメージ。

### 3 について

ほぼ2の延長の話で、apacheのconfやapacheのPHPモジュールを付け加えたみたいなのが加筆されている。

apacheを意識しているが他モジュールが意識されてないので大体同様にエラーが出るイメージ。とはいえwordpressみたいにそんな沢山のPHPモジュールを使ってないものであればこれだけで何とかなることも多いのだが。

## じゃあどうすればいいの
筆者は下記全部をやるのがPHPのバージョンアップ対応だと思っている。

① 既存環境で使用しているPHPとPHPモジュール全てについてもバージョンアップ
② 必要に応じてwww.confやnginx.confやphp.ini等に差分加筆

既存のPHP環境をPHPバージョンアップ先に引き継げば問題がないという前提。具体的には「PHPのバージョンアップで別のPHPモジュールをアプリ側から要求された」等は考えないものとする。

本記事は①-②について解説する。

### 準備
<span style="color: red; ">**システム全体のスナップショットを取るなりしてバックアップを取るのを強く勧めます。**</span>

作業前の環境を復元できれば何でもいいです。Ubuntuとかだったらtimeshiftとか楽ですかね。
```bash
sudo apt install timeshift
sudo timeshift --create --coomment "BEFORE PHP VERSION UP"
# 復元する時は下記コマンド
sudo timeshift --restore 
```

問題が起きて手詰まりになったら前環境を復元して復旧させましょう。

以降この記事ではUbuntu前提で進めるが、考え方はどのOSも同じなので適宜コマンドを読み替えていってもらえれば。

### PHP/PHPモジュール全てバージョンアップ

まずはPHPのバージョンアップのためにリポジトリを登録する。

```bash
sudo LC_ALL=C.UTF-8 add-apt-repository -y ppa:ondrej/php
sudo apt update
```

その後自分が使用しているPHPのパッケージを全部洗いだす。

下記コマンドのXを現在のPHPのバージョン、Yを上げたいバージョンに書き換えて実行する。

```bash
dpkg -l | grep phpX | cut -f3 -d' ' | xargs | sed 's/X/Y/g' 
# 例えば、PHP8.0からPHP8.2にしたい場合はこうなる。
dpkg -l | grep php8.0 | cut -f3 -d' ' | xargs | sed 's/8.0/8.2/g'
# ※ 自分の今のPHPのバージョンはこれで調べられる。
php -v
```

このコマンドを実行すると、自分が今使用しているパッケージ情報を上げたいバージョンのパッケージ名にしたものが出力される。

次に、上記コマンドで得た結果を使用してインストールのシミュレートを行う。

```bash
sudo apt install --simulate (dpkg -l | grep phpX | cut -f3 -d' ' | xargs | sed 's/X/Y/g' の結果全部)
```

上記インストールのシミュレートで「Package 'php8.0-XXX' has no installation candidate」のようなエラーが出た場合、そのパッケージをインストール対象から取り除き、再度シミュレートをする。

エラーが出なくなるまでこれを繰り返す。エラーが出なくなったら、上記コマンドの--simulateを外してインストールする。

インストール後、php -vで自分のPHP環境を確認し、自分の意図したバージョンになっていればOK。なっていなければ下記コマンドで明示的に設定する。

```bash
update-alternatives --config php
```

### 他設定ファイルの書き換え
PHPは前の章で切り替わったが、PHPを使用する他アプリの設定も書き換えなければならない。この記事ではnginxの設定と一部の設定ファイルについて解説する。

といっても、下記コマンドで既存環境のphpの設定ファイル群を新しいphpの設定ファイル群にコピーするだけだが。

```bash
# Xは移行前のPHPのバージョン、Yは移行後のPHPのバージョンに書き変える
rm /etc/php/Y/fpm/pool.d/*
rm /etc/php/Y/fpm/cli/*
cp -rL /etc/php/X/fpm/pool.d/* /etc/php/Y/fpm/pool.d/
cp -rL /etc/php/X/fpm/cli/* /etc/php/Y/fpm/cli/
```

大体上記コマンドで間に合うと思うが、必要ならほかの設定ファイルも適宜コピーしていけばよい。他、nginxでphp-fpmを使うにあたってsockを使っている場合は適宜nginx.confやpool内のlistenを見直して合わせること。

※私はsockを使わず9000ポートを使っているため、既存環境をコピーするだけで良いので上記を変更せずに済んでいる。

最後に、nginx再起動、古いphp-fpmの自動起動を止め、新しいphp-fpmを自動起動するようにすれば完了。

```bash
# Xは移行前のPHPのバージョン、Yは移行後のPHPのバージョンに書き変える
sudo nginx -s reload
sudo systemctl stop phpX-fpm
sudo systemctl disable phpX-fpm
sudo systemctl enable phpY-fpm
sudo systemctl start phpY-fpm
```

上記を行い、PHPアプリに問題があれば適宜confを書き換えて、下記コマンドを打って再読込して直ったか確認する。

```bash
sudo nginx -s reload
sudo systemctl reload phpY-fpm
```