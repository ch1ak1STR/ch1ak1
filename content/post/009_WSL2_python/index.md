---
title: "WSL2で機械学習用python環境を作る"
date: 2023-08-12T09:00:00+09:00
description:
draft: false
tags:
- WSL2
- python
- AI
- 機械学習
---
<!--more-->
WSL2の勉強ついでに機械学習用のpython環境をWSL2で用意したのでメモ。
# 必要なもの
- Windows10 version:2004(Build 19041以上) もしくは Windows11

# WSL2の導入
公式[[1]](https://learn.microsoft.com/ja-jp/windows/wsl/install)ではwsl --installを使用した導入方法がお勧めされているが、自分の環境ではこのコマンドで完結しなかったので別の手順[[2]](https://learn.microsoft.com/ja-jp/windows/wsl/install-manual)を紹介する。

Windows+Xを同時押し後Aを押してPowerShellを管理者権限で実行します。  ウィンドウが立ち上がったら下記コマンドを1つずつ実行します。
{{< codeblock "PowerShell" >}}
dism.exe /online /enable-feature /featurename:Microsoft-Windows-Subsystem-Linux /all /norestart
dism.exe /online /enable-feature /featurename:VirtualMachinePlatform /all /norestart
{{< /codeblock >}}
実行後、Windowsを再起動して下さい。

再起動し終えたら[WSL 2 Linux カーネル](https://github.com/microsoft/WSL2-Linux-Kernel)をダウンロードしてインストール後、再度PowerShellを開いて下記コマンドを実行します。
{{< codeblock "PowerShell" >}}
wsl --set-default-version 2
{{< /codeblock >}}
WSL2にインストールしたいOSを選択してインストールします。ここではUbuntuを選択します。
{{< codeblock "PowerShell" >}}
wsl --update
wsl --install -d Ubuntu-22.04
{{< /codeblock >}}

WSL2自体のセットアップは以上です。

# WSL2で機械学習用のpython環境を構築する

## python環境構築の方針
pythonをただインストールするだけならapt install pythonとでもすれば良いのですが、機械学習目的でpythonを取り扱う場合pythonのバージョンや環境を単一で運用していくのは不可能です。

というのも特定のpythonバージョン、特定のライブラリバージョンでないと動作しないとか振る舞いが変わってくるなどが往々にしてある上、環境を無秩序に作って置くと後々整理がしんどいです。

そのため、今回は複数のpython環境の管理も可能な形でインストールをしていきます。

## pythonバージョン管理手順
{{< codeblock "PowerShell" >}}
wsl -d Ubuntu-22.04
{{< /codeblock >}}
入ったら、続けて下記コマンドを入力してとりあえずpipコマンドを使えるようにします。最後にバージョンが表示されればOK。
{{< codeblock "Shell" >}}
cd ~ ; apt update && apt upgrade -y
apt install -y python3-pip
pip3 -V
{{< /codeblock >}}

リポジトリを追加します
{{< codeblock "Shell" >}}
apt install -y software-properties-common
add-apt-repository ppa:deadsnakes/ppa
{{< /codeblock >}}
>Press [ENTER] to continue or Ctrl-c to cancel adding it.   

が表示されたらEnterします。

その後、インストール出来るpythonのバージョンを下記コマンドで確認します。
{{< codeblock "Shell" >}}
apt list python3.*
{{< /codeblock >}}

リストの中で、自分がインストールしたいバージョンを選択して、下記コマンドでインストールします。自分は3.10と3.12が必要だったので下記内容としています。
{{< codeblock "Shell" >}}
apt install -y python3.10 python3.10-venv
apt install -y python3.12 python3.12-venv
{{< /codeblock >}}
その後、priorityを定義します。
{{< codeblock "Shell" >}}
update-alternatives --install /usr/bin/python python /usr/bin/python3.10 10
update-alternatives --install /usr/bin/python python /usr/bin/python3.12 20
{{< /codeblock >}}
以降は下記コマンドを使って、切り替えたいバージョンを選択することでpythonのバージョンを切り替えることができます。
{{< codeblock "Shell" >}}
update-alternatives --config python
{{< /codeblock >}}
python -V でバージョンが選択したバージョンになっていればOK。

## pythonライブラリ管理手順

上記手順でpythonのバージョンを切り替えることはできるものの、これだけではライブラリのバージョンや状況を切り替えることができません。

そこで、「venv」を使います。自分の開発するディレクトリに移動して、そこで下記コマンドを打ちます。

{{< codeblock "Shell" >}}
python -m venv test_venv
{{< /codeblock >}}
すると、そこにtest_venvという名前のvenvフォルダが作成され、そこにvenv環境で構築した環境がまるごと入ります。

venv環境を有効にするにはそのまま下記コマンド。
{{< codeblock "Shell" >}}
source  ./test_venv/bin/activate
{{< /codeblock >}}
実行した後、ターミナル左端に入った環境の名前が表示されていれば成功。

この状態でインストールしたpythonライブラリはvenv環境に全て入ることになっているはずです。

venv環境を抜ける際は下記コマンドで抜けれる。
{{< codeblock "Shell" >}}
deactivate
{{< /codeblock >}}

これで、ライブラリも切り替えられるようになった。

以後、ツール毎にpython環境を切り替える際はvenvとバージョンを切り替えればよい。

## 参考文献/従来研究
- [1] [WSL を使用して Windows に Linux をインストールする](https://learn.microsoft.com/ja-jp/windows/wsl/install)
- [2] [以前のバージョンの WSL の手動インストール手順](https://learn.microsoft.com/ja-jp/windows/wsl/install-manual)
- [3] [Ubuntu 22.04にPython3.xを複数インストールして切り替える方法（PPA・apt）](https://qiita.com/murakami77/items/b612734ff209cbb22afb)