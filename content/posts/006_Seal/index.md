---
title: "Androidでyt-dlpが使えるOSS Seal"
date: 2023-07-16T00:00:00+09:00
draft: false
pinned: false
tags:
- Seal
- OSS
---
yt-dlpはyoutubeから動画/音声をダウンロードすることが出来るツールです。元々youtube-dlというライブラリが本家で、yt-dlpはそのフォークにあたります。  

そんなyt-dlpをAndroidで使うことが出来る「Seal」というOSSアプリの使い勝手が良かったので紹介。

<!--more-->
尚、yt-dlp自体の使い方やコマンドについては既に従来研究が沢山あるので本記事では細かく触れません。  気になる人は下記のリンク参照。  

[https://gigazine.net/news/20220101-yt-dlp/](https://gigazine.net/news/20220101-yt-dlp/)  
[https://ikuriblog.com/how-to-download-videos-with-yt-dlp/](https://ikuriblog.com/how-to-download-videos-with-yt-dlp/)  

※youtube-dlは過去にyoutubeからDMCA違反を受けgithubのリポジトリを一時的に消したり、更新ペースが悪いことやダウンロード速度に何故か制限をかけていることから現在はyt-dlpを使うのが主流らしい。  

## 何故Sealを使うと良いの？
Androidで動画をダウンロードする従来研究なんて沢山ありますが、SealはAndroidでyt-dlpを使えること、OSSであることがウリです。

よくわからんサイトにURLを入れて変換してもらったのをダウンロードするとか、playに公開されている胡散臭い広告が出てくるプロプライエタリなアプリを使いたくないよねという。  

## Sealのダウンロード
F-droidで配布されています。  
[ここ](https://f-droid.org/en/packages/com.junkfood.seal/)からアプリ無しでダウンロード出来るけど、個人的にはF-droidアプリからダウンロードして、自動で更新できるようにするのがおすすめ。   

## 使い方
Sealを単純に起動するとこの画面が表示されます。  

{{< figure src="Seal_001.png" width=300px  height=300px >}}

URLを入力欄に直打ちして下の方にあるダウンロードっぽいボタンを押せばダウンロードすることも出来るけど、基本的にはyoutubeアプリ等から共有インテントをSealに投げてダウンロードする方が楽です。  

共有をすると「Seal」と「Seal Quick Download」みたいなのが表示されますが、その場で設定したいなら前者、テンプレートを用意していてそれをそのまま使いたいのなら後者でダウンロードすると良いです。

共有から2-3ポチで動画がダウンロード出来ます。楽でいいね

## カスタムコマンド

Sealはこのまま使っても便利だけど、デフォルトで用意されているテンプレートでは少々物足りないと感じます。例えば音声保存したいけどmp3で中途半端なkbpsで保存されるとか、形式をちゃんと指定したいとか人によっては不満を感じることでしょう。

そこで、yt-dlpのカスタムコマンドを自分で入力してテンプレートとして保存することで、自分好みの設定でダウンロードが出来るようになります。本記事では当方が使っているmp3最高音質でダウンロード出来るカスタムコマンドを例に、コマンド設定する方法を共有して締めくくります。

カスタムコマンドの設定方法ですが、Sealの設定項目からカスタムコマンドの項目へ進み、下記のようにカスタムコマンドを入力して保存してください。

{{< figure src="Seal_002.png" width=300px  height=300px >}}

コマンドは下記。コピペ用
```bash
--no-mtime 
-x 
--audio-format mp3 
--audio-quality 320K
```
コマンドの説明はしません。これを無心で入れてテンプレートを保存してください。以降、音声ダウンロードの際にはこのカスタムテンプレートを選択すればOKです。ちなみにSealではファイルの保存先/URLはアプリ側でよしなにやってくれるのでここで入力しなくてOK。(ファイルの保存先はアプリ側の設定で用意されていて、GUIで設定出来る。)

{{< figure src="Seal_003.png" width=300px  height=300px >}}

## 最後に
今後も気が向いたらOSSアプリの記事書くと思います