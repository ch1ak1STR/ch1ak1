---
title: "自分的githubのお勉強"
date: 2023-06-27T00:00:00+09:00
draft: true
pinned: false
tags:
- github
---
github運用の備忘録。
この記事は随時更新されるでしょう。
<!--more-->

## ローカル環境をgithubに載せる
```bash
git remote add origin https://github.com/hoge/fuga.git
git push -u origin master
```

## n個のコミットを1つにまとめる
```bash
git rebase -i HEAD~n
```
統合したいコミットをsquashにし、コミットメッセージを消す