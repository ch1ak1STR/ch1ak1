---
title: "SearXNGをホストした"
date: 2023-06-25T00:00:00+09:00
draft: false
pinned: false
tags:
- SearXNG
---
他人のを使いたく無かったので自分でSearXNGをホストしました。
この記事ではSearXNGの簡単な説明、ホストの手順、設定の仕方を解説します。
作成した[インスタンス](https://searxng.leadershouse.tokyo) 


<!--more-->
## SearXNGの簡単な説明
SearXNGとは複数の検索サイトを横断して検索し、結果を出力するメタ検索エンジン。
GoogleやDuckDuckgoをそのまま使うのと比べると下記利点があります。

- 沢山のサイトの検索結果を同時に閲覧出来る
- データ収集の懸念が無い
- 細かい設定が可能

## ホストした理由
SearXNGは個々人でインスタンスを公開出来るので、他人が公開しているインスタンスを使ってネットサーフィンすることが出来ます。が、インスタンスによっては特定の検索サイトの結果が技術的な問題で取得できない等が多々あったり、地理的に日本からの接続が安定しているインスタンスがあまりないため自分でホストすることにしました。

## ホストに必要な手順
地味に日本語記事でSearXNGの構築記事が無いので書こうかと。
とはいえ公式ドキュメントが充実しており、導入は難しくないですが。

SearXNGには3つのインストールオプションがあります。
- Dockerでのインストール
- スクリプトでのインストール
- 1つ1つ手順を踏んで行うインストール？

自分はスクリプトでのインストールを選択しました。 
※1つ1つ手順を踏んで行うインストールでは、SearXNGが何を必要として動くのかの理解しやすいみたいなメリットがあるとか書いてありましたが、面倒くさそうなのでやめました。

まずSearXNG本体のダウンロード及びインストールスクリプトを走らせます。
```bash
$ cd ~/Downloads
$ git clone https://github.com/searxng/searxng.git searxng
$ cd searxng
$ sudo -H ./utils/searxng.sh install all
```

その後、使っているwebサーバに設定を書いてあげる。nginxでは下記。
```conf
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
```

これだけで動作するようになる。後はお好みでSSLにするなりなんなりと。

## SearXNGの簡単な設定
トップページ右上の設定ボタンから設定可能です。
とりあえずこれ知っとけば困らないよっていう設定のみ抜粋。

- 一般：自動補完 
-> 検索バーに出てくる検索候補/検索予測のこと。Googleがおすすめ。
- ユーザーインターフェース：無限スクロール
-> AutoPaglizeみたいに検索結果のページを自動読み込みする奴。
- プライバシー：HTTPメソッド
-> 検索結果のURLに検索内容が含まれるかどうかの差。POST推奨。
- プライバシー：トラッカー URL リムーバー
-> URLから余計な引数を消すのでON推奨。
- 検索エンジン：
-> 検索結果に含みたいエンジンを選ぶ。startpageは入れる理由無いので外す。
- クッキー：このURLで違うブラウザに設定を復活:
-> SearXNGはCookieを使用して設定を保存しているので別のブラウザで開くと当然設定が引き継がれないが、ここで表示されているURLを引き継ぎ先のPCで開くと設定が引き継がれる。

ちなみに、当方の設定を使用したい場合は[このURL](https://searxng.leadershouse.tokyo/preferences?preferences=eJx1V02P4zYM_TXNxegA3T0UPeRUoNcWaO8CLTE2x7Ko0Uccz68vFduxPN49rDd6kijqiXzkaEjYcSCM1w4dBrAXC67L0OEVcuKLZQ0Wr-9wKUPNo7eY8GqyHsq_ji80ymLlAz_m619gI15GTD2b6z9___vfJcINI0LQ_fXbJfU44jVSMXIJGLNNUbFTDieVoF23GyYlk2zvGK4MMnzj0F2WbSqm2a6-aXQJgwJLnRvl97ofzB2cRqPWcxf0I2OYFTmVKImBBSR3I0dJrOrA1m4OUITWigF0HTmh5o8OOqUiawLbjGgIfvn2J_ihGSkEDkrdyGJ8YuJiI98mJg5YTYgjdMeGolIr0YJONJAv5mqwpdQKs5iUoiRjrfWv6a7UnQxysSWuxhjwJh5pQrmpYDP62obOwRJ-QYKsnWvMIH4Kf2rMkXQZm64x-GSE2B08xcATGaVYnjCsnhtIB8f3mGieMSEGlv-PcxNCMbLbWkgbwcsG-ZbTRn7fiNlWJXmJ-XCl3x7VATcTuDi4EX4LiE3kW5ogYGMooJYXmVdOb4HcQKArA52EBbQb52ywxdCtw465Exe9hbk8btyPqWdGvlO59OuhOs_G1AT30AYon9Uujabdb0gOqs3k5Cdxjj_GNpPvFHvebQykB4iVg5bkyDA3hbZIhwnX3Q_vZ7mNCd9CXL2DTzsH0tVhktigPbh1gfPj9msG2E2zRxfQc3WaB_Gro7gFtc_tm8H7OvqYwKXalSVzyys05fN6NRihXGgDIo6ykXQTdc8WQp0RMUFIvkhTZTfxMHNioWwot9i8SxOloiRfU3yGnvmYsO0hg0QaH-BMOGZwSY6WeTjluuicYOW7Ah-ZE35dFTkHfUJFDCOl-QTz_OWOhnT6ZHdMle_ff3_s9zXZoNuDJuKng7FeL_mHONSIg3shaAdCbucOxy1YPGJIucUqWp8syhGDpMU0YVtNhTyKwFbAxA8a2ElMNHF27OYRK__e_Zuf3FF9RpGpg2wG8bAGvASrXQLpJ_BZpiS49vhf3qHPaVeBl6ELumOJeNqTnMqPpni3pVCQ6KzsQ3jQvY7SVqJHw-j3HGslL4_lwHVnR5_oIZyeyMJnxWubDHXdMTVLJGGtzXgnSSPxtQZZSlBo-rxJ4q7gh7qQ5pGd3Lt6rpsVEQq1tKKjWrNknAqXC6uU9kMWOa0PWAX2RMCKHyhYsRMJK_4DkejX856Hb1LJNxEd10kLUh840kNbzqZSQ5AagpuWF2ZBykypihGlr6mO2eZE9YQbWKvrcT4mqVhJ6t9WBL0p2bQv8r0ol3vNUpDWrYW50tjcSsodtsyeaok90rVAJ2YX-ESi3MxQOklkRE-wZdlrsaSOxPWRLhFkPbAE383ytOVHHHKbXcpbymWPIccXqdIEkpG6La1LqqpJdlEqbuwrr59SfbzfzPmLIr2QV8cDZCWAy4NUy-40ItfiRENZAaV9eEkQ26Uc-b5qUkbRrVHajCYFcNHKC9U9EwfjaKiAlMIbbbq2t53eZhGVeC20Pt7W0VvPMYlMo_TGcvmlxh0WMKildZ6CtLSn6Yj2Jv3vjU8zQrDSPerhNCNMKAm7Aef4UryfeFeeYxn84ORneAjHevlzY5bW3EpDdvYkQJEdJe2r3GMs0XKRmi3Zcf0fuEPJlQ==&save=1) を開けば使用することが出来ます。