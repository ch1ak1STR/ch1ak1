---
title: "OSS縛りを辞めた"
date: 2023-08-11T09:00:00+09:00
description:
draft: true
tags:
- OSS
---
この記事ではOSS縛りして生きてきた感想と辞める理由をつらつら書きます。誰かの役に立つかどうかは不明です。
<!--more-->

今まで自分は必要が無ければプロプライエタリなものを使わないで生活やら開発が出来ればという考えで生きてきました。 

PCはQubesOSやFedoraを、AndroidはGrapheneOSを使うとか、個人開発もLinuxやAndroid等根本がOSSであるフィールドで開発をしてきたけど、そういうことに縛られるのはやめようと思います。

## OSS縛りって何
具体的にはプロプライエタリな物を使わないでOSSだけで生きていこうねっていうのを3年間くらいやってました。  

冒頭でも軽く書いたけど、PCやスマホのOSは当然OSSの物を使ったし、ソフトもなるべくはOSSで完結させていました。

皆が使っているコミュニケーションツール(LINE、Twitter、Teams、Slack、Discord)やゲームアプリとかは仕方なくプロプライエタリなものを使う程度。

代わりがどうしてもきかないもの以外はOSSで固めてきました。

ぶっちゃけ生きていくだけなら全然上記内容は出来るレベルだと思っている。ある程度ITリテラシーがいるかもしれないが。

## OSS縛りを始めた理由
主に2つある。

1つ目は自分が実際に降りかかったことであまり具体的に書けないが、「自分のデータは自分で持ちたい」という気持ちが芽生えましたっていう内容の出来事があったから。

2つ目はこの話すると99%の人にバカにされるのだがエドワード・スノーデンさんの暴露事件ですね。

事件を要約すると、「NSA(アメリカ国家安全保障局)が合衆国内で30億件/月、全世界で970億件/月のインターネットと電話回線の傍受が行なわれていた」ということを暴露した事件です。[[1]](https://ja.wikipedia.org/wiki/%E3%82%A8%E3%83%89%E3%83%AF%E3%83%BC%E3%83%89%E3%83%BB%E3%82%B9%E3%83%8E%E3%83%BC%E3%83%87%E3%83%B3)  

>スノーデンは、英紙ガーディアンにNSAの極秘ツールであるバウンドレス・インフォーマントの画面を示し、クラッパー国家情報長官が議会公聴会で否定した3月に合衆国内で30億件/月、全世界で970億件/月のインターネットと電話回線の傍受が行なわれていたことを明らかにした。  


>電話傍受にはベライゾン・ワイヤレスなどの大手通信事業者が協力しており、NSAは加入者の通話情報を収集していた。インターネット傍受はクラッキングではなく、アプリケーションプログラミングインタフェースのような形のバックドアによるもので、コードネーム「PRISM（プリズム）」と名付けられた監視システムによって行なわれていた。標的になった情報は電子メールやチャット、電話、ビデオ、写真、ファイル転送、ビデオ会議、登録情報などだった。 

>また、標的になった情報は通話者の氏名・住所・通話内容の録音のみならず、メタデータも収集しており、通話者双方の電話番号、端末の個体番号、通話に利用されたカード番号・通話時刻・所要時間、および基地局情報から割り出した通話者の位置情報も収集していた。通話履歴から人工知能の機械学習でテロ容疑者を割り出して逮捕もしくは無人攻撃機（ドローン）による殺害の標的（ディスポジション・マトリックス（英語版）、キルリスト）に指定する「SKYNET（英語版）」（スカイネット）の存在も暴露した。   

>通信傍受にはマイクロソフト、Yahoo!、Google、Facebook、PalTalk、YouTube、Skype、AOL、Appleなどが協力させられていたことは以前から指摘されていたが、スノーデンの持ち出した資料によってその一部が明らかとなった。

この暴露事件は2013年に起こったもので、当時の私はそもそもこんな事件があったことを知らなかったし、仮に知ったとしてもふーん私みたいなつまらん奴の情報は抜かれてもなとしか思わなかったでしょう。

のだが、理由1つ目の出来事が起きてしまった私にはあんまり他人事にも思えず、信用できるのはOSSだけなんかなという考えになりました。それで縛りを始めた。

## OSS縛りして良かった？
エンジニア的には良かったと思う。(後述するがエンジニア的に辞める羽目になるのが皮肉だが)

OSSに関心しかなかったので、開発する際にまず従来研究としてOSSを勉強する癖がついたし、セキュリティ/プライバシー技術への知見が深まった。

どうしてもプロプライエタリなサービスを使いたくなさ過ぎて自宅にサーバを設けて自分専用のサービスを作るとかちょっとイカれてることもしたが、良い経験だった。

一方でエンジニアの観点を抜きにすると、良かったかと言われると微妙なところだ。理由の1つ目の出来事が起きえないだろう...という安心感以外は特にないかも。

## OSS縛りを辞めた理由は？
先述したがエンジニア的に辞める羽目になる。というのも参入出来るフィールドに制限がかかるから。

例えば自分の今のスタンスではiOSアプリ開発とかwindowsアプリ開発とかと縁が無いし、個人開発で一切触れないことになる。

正直自分はたいそうなエンジニアではないし、今今の段階で自分で使用するツールや戦場に制限を課すのはありえないなあと思ってしまった。

自分はまだここ1本で食っていく！というエンジニアではないと思っているし、一生OSS個人開発やって慈善事業をする余裕がある程の金持ちなわけでもない。

※自分のgiteaに制作物を貯めこんでいてgithubにpushすらしてない立場の自分が言うのもあれだが、OSS開発は面白い話ばかりではない。[[2]](https://logmi.jp/tech/articles/326575)[[3]](https://www.itmedia.co.jp/news/articles/2201/26/news162.html)[[4]](https://japan.zdnet.com/article/35182496/)

## もうOSS関係はやらんの？
自分がやってて負担に感じないものや開発の障壁にならない物は取り組み続けようと思っています。例えばGrapheneOS使い続ける等はする。

一方でこのOSは~ツールは使わないみたいなのは諦めることにしました。開発フィールドを狭めたくない。個人利用はまあケースバイケースかな。

OSS開発もOSSであるべきなものはやり続けるが、一方でお金のための個人開発もやらないといけないなあと思っています。

そういうわけで今後自分の開発物についてソースを開示せずに展開するものもあるでしょうね。

## 参考文献
[1] [エドワード・スノーデン - wikipedia](https://ja.wikipedia.org/wiki/%E3%82%A8%E3%83%89%E3%83%AF%E3%83%BC%E3%83%89%E3%83%BB%E3%82%B9%E3%83%8E%E3%83%BC%E3%83%87%E3%83%B3)  
[2] [OSS開発者に向けられる「搾取の精神」まつもとゆきひろ氏が語る、正しい生産的関係に必要なのは “リスペクト”と“参加すること” ](https://logmi.jp/tech/articles/326575)  
[3] [オープンソース「cURL」の作者、大企業から「24時間以内にこの質問に答えるように」との無礼なメールを受け取る](https://www.itmedia.co.jp/news/articles/2201/26/news162.html)  
[4] [オープンソース開発者が悪に走る時--意図的なライブラリー改ざん騒動から考える](https://japan.zdnet.com/article/35182496/)  