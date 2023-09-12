---
title: "iOS 15.0 ~ 15.4.1のiOS端末をDopamineで脱獄した"
date: 2023-08-20T09:00:00+09:00
description:
draft: true
tags:
- JailBreak
- Dopamin
- iOS
---
OSS縛りを辞めたので、こちらにも久しぶりに手を出したくiOS端末を脱獄(JailBrrak)してみました。
<!--more-->
今回の記事ではJailBreakってなんだよとかメリットはなんだよみたいな話はしません。

※JailBreakについて勉強するなら素晴らしい[サイト](https://tools4hack.santalab.me/)があります。

## 対応端末
今回は「Dopamine」という脱獄アプリを使ってJailBreakします。

iOSのバージョンが15.0 ~ 15.41かつ、A12チップ以降の端末ならOK。
***
- iPhone XR、XS、XS Max
- iPhone 11、11 Pro、11 Pro Max
- iPhone 12、12 mini、12 Pro、12 Pro Max
- iPhone 13、13 mini、13 Pro、13 Pro Max
- iPhone SE 2、SE 3
- iPad 8、9
- iPad Pro 12.9 (2018 / 2020 / 2021)、Pro 11 (2018 / 2020 / 2021)
- iPad Air 3 / 4 / 5
- iPad mini 5 / 6
***

## やり方

### TrollStoreのインストール

Dopamineをインストールするには、署名不要でアプリをインストール出来るようにする「TrollStore」が必要になります。

TrollStoreはsafariからこの[ページ](https://api.jailbreaks.app/troll)を開くだけでインストールできます。

インストール後、ホーム画面に「GTA Car Tracker」というアプリがインストールされます。そのアプリからTrollStoreをインストールします。

TrollStoreのインストール後、TrollStoreの設定画面からURL Scheme Enabledを有効にすれば完了です。

※TrollStoreのインストール後はGTA Car Trackerを削除しても大丈夫です。

### Dopamineのインストール

この[リンク](apple-magnifier://install?url=https://github.com/opa334/Dopamine/releases/latest/download/Dopamine.tipa)からDopamineをインストールできます。

※先述のURL Scheme Enabledを有効にしていないと拡大鏡のアプリが立ち上がるだけでうまくいかないです。

### DopamineでJailBreakする

Dopamineを起動して、下のJailBreakをタップ、Sileoを選択して少し待つ。

最後にDopamine環境で実行するsudoのためにパスワードを入力して終了。

## 参考文献
[1][Tools4Hackさん](https://tools4hack.santalab.me/)

[2][iOS 15.0〜15.4.1を「Dopamine」で脱獄する方法!! A12以降の全デバイスに対応](https://tools4hack.santalab.me/howto-ios150-1541-jailbreak-for-dopamine.html)  
-> ほぼこの記事のまんまです