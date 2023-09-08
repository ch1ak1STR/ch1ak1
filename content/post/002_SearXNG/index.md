---
title: "メタ検索エンジン「SearXNG」をホストした"
date: 2023-06-25T00:00:00+09:00
draft: false
pinned: false
tags:
- SearXNG
- nginx
---
<!--more-->
# Abstract

自分のサーバにSearXNGを用意したので、SearXNGの紹介とついでに構築手順を解説する。
- SearXNGの概要
- ホストした理由
- ホストの手順
- SearXNGの簡単な設定(管理者側)
  - SearXNGのバージョンアップ方法
  -
- SearXNGの簡単な設定(ユーザ側)
- 今後について
- 参考文献

# SearXNGの概要
SearXNG[https://docs.searxng.org/]とは複数の検索サイトを横断して検索し、結果を出力するメタ検索エンジンです。
GoogleやDuckDuckgoをそのまま使うのと比べると下記利点がある。

- 沢山のサイトの検索結果を同時に閲覧出来る
- データ収集の懸念が無い
- 細かい設定が可能

{{< figure src="002_1.png" = full >}}  
※ 自分のSearXNGで「ch1ak1」と検索した結果。

SearXNGはOSSなので自分で用意し、利用したりインターネットに公開することもできる。今回、自分のサーバでSearXNGを公開したのでお披露目、SeraXNGの構築手順を解説する。~~(地味に日本語記事でSearXNGの構築記事が無いので、需要もあるかも？)~~
記事を書き直した時点で既に出来ていた。(Dockerを使用するケース)[SearXNGでメタ検索エンジンを所有する](https://riq0h.jp/2023/08/20/091355/)

# ホストした理由
SearXNGは個々人でインスタンスを公開出来るので、他人が公開しているインスタンスを使ってネットサーフィンすることも出来る。が、インスタンスによっては特定の検索サイトの結果が技術的な問題で取得できない等が多々あったり、地理的に日本からの接続が安定しているインスタンスがあまりないため自分でホストすることにした。

作成した[SearXNGインスタンス](https://searxng.leadershouse.tokyo) 

# ホストの手順

※この記事ではファイアウォールがどうとかSSL対応がどうとかnginxのディレクティブ全体がどうとかまでは書かない。SearXNG固有の内容について書く。

先述の通り、筆者調べでは日本語文献のSearXNGの構築記事は存在しないが、公式ドキュメントが充実しており導入は難しくない。

SearXNGには3つのインストールオプションがある。
1. Dockerでのインストール
2. スクリプトでのインストール
3. 1つ1つ手順を踏んで行うインストール

Docker使いたい人は1、使いたくない人は2を選択するのが良いと思われる。

自分は2を選択しているので、以下はスクリプトによるインストール方法を紹介。

といっても、SearXNG本体をダウンロードしてインストールスクリプトを走らせるだけですが。
{{< codeblock "shell">}}
$ cd ~/Downloads
$ git clone https://github.com/searxng/searxng.git searxng
$ cd searxng
$ sudo -H ./utils/searxng.sh install all
{{< /codeblock >}}

その後、使っているwebサーバに設定を書く。nginxでは下記。(ほぼ[公式サイトの例](https://docs.searxng.org/admin/installation-nginx.html#nginx-s-searxng-site))
{{< codeblock "nginx.conf">}}
location / {
    uwsgi_pass unix:///usr/local/searxng/run/socket;
    include uwsgi_params;
    uwsgi_param    HTTP_HOST             $host;
    uwsgi_param    HTTP_CONNECTION       $http_connection;
    # see flaskfix.py
    uwsgi_param    HTTP_X_SCHEME         $scheme;
    uwsgi_param    HTTP_X_SCRIPT_NAME    /searxng;
    # see limiter.py
    uwsgi_param    HTTP_X_REAL_IP        $remote_addr;
    uwsgi_param    HTTP_X_FORWARDED_FOR  $proxy_add_x_forwarded_for;
}
{{< /codeblock >}}

後はお好みでファイアウォール、ポート開け、serverディレクティブ書いたりSSLにするなりなんなりと。

# SearXNGの簡単な設定(管理者側)

せっかく建てたので、管理していく上で必要なことや設定とかを雑に書く。

## SearXNGのバージョンアップ方法

下記コマンドで更新する。

{{< codeblock "shell">}}
sudo -H /usr/local/searxng/searxng-src/utils/searxng.sh instance update
{{< /codeblock >}}

## SearXNG全般の設定

[3][公式ドキュメント](https://docs.searxng.org/admin/settings/settings.html#settings-yml-location)によると、SearXNGの設定ファイルは```/etc/searxng/settings.yml```にあるとのこと。

設定項目の詳細はドキュメントを読んでもらった方がよいと思う。

が、```use_default_settings```について自分はぱっと見よくわからなかったので触れておくと、
- true -> デフォルト設定+settings.ymlを読む
- false -> settings.ymlのみを読む
だと思われる。要するに、ちょっと変更入れる人はtrueで、抜本的に変更入れる人はfalse、が良いと思われる。

設定を書いて、反映させたくなったら下記コマンドで再起動。
{{< codeblock "shell">}}
sudo service uwsgi reload searxng
{{< /codeblock >}}

# SearXNGの簡単な設定(ユーザ側)
トップページ右上の設定ボタンから設定可能です。
とりあえずこれ知っとけば困らないよっていう設定のみ抜粋。

- 一般：自動補完 
  - 検索バーに出てくる検索候補/検索予測のこと。Googleがおすすめ。
- ユーザーインターフェース：無限スクロール
  - AutoPaglizeみたいに検索結果のページを自動読み込みする奴。
- プライバシー：HTTPメソッド
  - 検索結果のURLに検索内容が含まれるかどうかの差。POST推奨。
- プライバシー：トラッカー URL リムーバー
  - URLから余計な引数を消すのでON推奨。
- 検索エンジン：
  - 検索に使いたいエンジンを選ぶ。
- クッキー：このURLで違うブラウザに設定を復活:
  - SearXNGはCookieを使用して設定を保存しているので別のブラウザで開くと当然設定が引き継がれないが、ここで表示されているURLを引き継ぎ先のPCで開くと設定が引き継がれる。

ちなみに、当方の設定を使用したい場合は[このURL](https://searxng.leadershouse.tokyo/preferences?preferences=eJx1V02P4zYM_TXNxegA3T0UPeRUoNcWaO8CLTE2x7Ko0Uccz68vFduxPN49rDd6kijqiXzkaEjYcSCM1w4dBrAXC67L0OEVcuKLZQ0Wr-9wKUPNo7eY8GqyHsq_ji80ymLlAz_m619gI15GTD2b6z9___vfJcINI0LQ_fXbJfU44jVSMXIJGLNNUbFTDieVoF23GyYlk2zvGK4MMnzj0F2WbSqm2a6-aXQJgwJLnRvl97ofzB2cRqPWcxf0I2OYFTmVKImBBSR3I0dJrOrA1m4OUITWigF0HTmh5o8OOqUiawLbjGgIfvn2J_ihGSkEDkrdyGJ8YuJiI98mJg5YTYgjdMeGolIr0YJONJAv5mqwpdQKs5iUoiRjrfWv6a7UnQxysSWuxhjwJh5pQrmpYDP62obOwRJ-QYKsnWvMIH4Kf2rMkXQZm64x-GSE2B08xcATGaVYnjCsnhtIB8f3mGieMSEGlv-PcxNCMbLbWkgbwcsG-ZbTRn7fiNlWJXmJ-XCl3x7VATcTuDi4EX4LiE3kW5ogYGMooJYXmVdOb4HcQKArA52EBbQb52ywxdCtw465Exe9hbk8btyPqWdGvlO59OuhOs_G1AT30AYon9Uujabdb0gOqs3k5Cdxjj_GNpPvFHvebQykB4iVg5bkyDA3hbZIhwnX3Q_vZ7mNCd9CXL2DTzsH0tVhktigPbh1gfPj9msG2E2zRxfQc3WaB_Gro7gFtc_tm8H7OvqYwKXalSVzyys05fN6NRihXGgDIo6ykXQTdc8WQp0RMUFIvkhTZTfxMHNioWwot9i8SxOloiRfU3yGnvmYsO0hg0QaH-BMOGZwSY6WeTjluuicYOW7Ah-ZE35dFTkHfUJFDCOl-QTz_OWOhnT6ZHdMle_ff3_s9zXZoNuDJuKng7FeL_mHONSIg3shaAdCbucOxy1YPGJIucUqWp8syhGDpMU0YVtNhTyKwFbAxA8a2ElMNHF27OYRK__e_Zuf3FF9RpGpg2wG8bAGvASrXQLpJ_BZpiS49vhf3qHPaVeBl6ELumOJeNqTnMqPpni3pVCQ6KzsQ3jQvY7SVqJHw-j3HGslL4_lwHVnR5_oIZyeyMJnxWubDHXdMTVLJGGtzXgnSSPxtQZZSlBo-rxJ4q7gh7qQ5pGd3Lt6rpsVEQq1tKKjWrNknAqXC6uU9kMWOa0PWAX2RMCKHyhYsRMJK_4DkejX856Hb1LJNxEd10kLUh840kNbzqZSQ5AagpuWF2ZBykypihGlr6mO2eZE9YQbWKvrcT4mqVhJ6t9WBL0p2bQv8r0ol3vNUpDWrYW50tjcSsodtsyeaok90rVAJ2YX-ESi3MxQOklkRE-wZdlrsaSOxPWRLhFkPbAE383ytOVHHHKbXcpbymWPIccXqdIEkpG6La1LqqpJdlEqbuwrr59SfbzfzPmLIr2QV8cDZCWAy4NUy-40ItfiRENZAaV9eEkQ26Uc-b5qUkbRrVHajCYFcNHKC9U9EwfjaKiAlMIbbbq2t53eZhGVeC20Pt7W0VvPMYlMo_TGcvmlxh0WMKildZ6CtLSn6Yj2Jv3vjU8zQrDSPerhNCNMKAm7Aef4UryfeFeeYxn84ORneAjHevlzY5bW3EpDdvYkQJEdJe2r3GMs0XKRmi3Zcf0fuEPJlQ==&save=1) を開けば使用することが出来ます。

# 参考文献
- [1] [SearXNG 公式ドキュメント](https://docs.searxng.org/)
- [2] [SearXNGでメタ検索エンジンを所有する]
- [3] [SearXNG 公式ドキュメント nginx記述例](https://docs.searxng.org/admin/installation-nginx.html#nginx-s-searxng-site)
