---
title: "自分的neovimのお勉強"
date: 2023-08-15T09:00:00+09:00
description:
draft: false
tags:
- neovim
- VScode
- Linux
- Windows
---
neovim運用の備忘録。
この記事は随時更新されるでしょう。

<!--more-->

##  neovimとは
まんま引用で済ませます[[1]](https://neovim.io/charter/)
>Neovim is a refactor, and sometimes redactor, in the tradition of Vim (which itself derives from Stevie). It is not a rewrite but a continuation and extension of Vim. Many clones and derivatives exist, some very clever—but none are Vim. Neovim is built for users who want the good parts of Vim, and more.

## vimでよくね？
機能と方向性についでは人の好みによってvimや他の派生エディタで良いと思うが、自分はVScodeでvimを使いたかったのでneovimを使うことにした。

VScodeでもvimを使えるようにするプラグインがあるけど、どうやらvimのエミュレータみたいなものらしく互換性に問題があるとか不安定とか、一部vimrc非対応とかあまり気持ちの良さそうなものではないので辞めた。

## neovimの導入

### Linux
Linuxの場合導入は簡単。

Debian系
```bash
sudo apt update && sudo apt upgrade -y 
sudo apt install neovim
```
Fedoraとか
```bash
sudo dnf update 
sudo dnf install neovim
```

### Windows
帰って来たら書く

## neovimの設定

### Linux 
とりあえず設定ファイルを作る
```bash
mkdir -p ~/.config/nvim
touch ~/.config/nvim/init.vim
```
今後、機能追加をする際はこのファイルに色々書き込んでいくことになる。

## 筆者用備忘録

## 参考文献
[1][neovim Vision](https://neovim.io/charter/)