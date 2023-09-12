---
title: "自分的Hugoのお勉強"
date: 2023-06-26T00:00:00+09:00
draft: true
pinned: false
tags:
- Hugo
---
Hugoで記事書くにあたっての備忘録。
この記事は随時更新されるでしょう。
<!--more-->

## 画像を記事に貼る
単に貼るだけなら下記の様にshortcodeで書けばよい 。
```markdown
{{</* figure src="/path/to/image" width=300px  height=300px */>}}
```
書き方と関係無いけど個人的には画像をどう管理するかも悩ましい。
自分はstatic/images/post/ポスト名 に画像を入れて管理しようと考えている。

## Shortcodeをそのまま書く
既にしれっとやってるけど、先述のshortcodeをそのままマークダウンに書くと画像が表示されてしまう。コードそのものを表示したい場合は/**/でshortcodeを囲う。
上の場合ならこのように囲えば良い。
```markdown
</* figure src="/path/to/image" width=300px  height=300px */>
```

