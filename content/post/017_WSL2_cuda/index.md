---
title: "WSL2にcudaを導入する"
date: 2023-09-19T00:00:00+09:00
tags:
- WSL2
- Ubuntu
- cuda
---
<!--more-->
# Abstract
WSL2にcudaを導入の際割と嵌ったのでメモ。

# Windows側の準備
下記は既に対応済みとする。
- WSL2の導入
- WSL2内にUbuntu-22.04がある
  - ※Ubuntu-22.04じゃないとだめそう？

## NVIDIAドライバのインストール
まず、PCで使用しているGPUを調べる。<br><br>わかれば何でもいいけど、
個人的にはタスクマネージャの「パフォーマンス」タブでGPU画面の右上を見るのが一番早いかなと。<br><br>
{{< figure src="017_1.png" = full >}} 
※この画面の場合だと「RTX 3060 Ti」。<br><br>

GPUの情報がわかったら、[NVIDIA Driver Downloads](https://www.nvidia.com/download/index.aspx)
に先ほど判明したGPU情報とPCのプロセッサを打ち込み、「search」を押下してドライバをダウンロードしてインストールする。

{{< figure src="017_2.png" = full >}} 
※私の環境だと上記。

# WSL2側の準備
下記は既に対応済みとする。
- WSL2内にNVIDIA関係の古いパッケージが存在しないこと
  - 上手くいかない人は大体この点を見直すべき。
  - 不安な人は下記対応をすると安全。

1. 下記コマンドを実行する
```sudo apt-get --purge remove nvidia* cuda* cudnn* libnvidia* libcuda* libcudnn* && sudo apt-get autoremove && sudo apt-get autoclean && sudo apt-get update``` 
2. WSL2のUbuntu-22.04環境を新しく作り直す

## WSL2にCUDAをインストール
[CUDA Toolkit Downloads](https://developer.nvidia.com/cuda-downloads)に
自分のPCとGPUの情報を入力し、対応したcudaをインストールできるコマンドを教えてもらう。<br><br>

{{< figure src="017_3.png" = full >}} 
※私の環境だと上記。WSLだけど最初にLinux、OSでWSL-Ubuntuを選択するのがちょい嵌りポイントかも。

情報を入力し終えると、画面下部にcudaをインストールできるコマンドが表示される。これをWSL2で実行する。

{{< figure src="017_4.png" = full >}} 