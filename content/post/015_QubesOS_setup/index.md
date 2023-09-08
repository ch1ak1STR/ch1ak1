---
title: "QubesOSまた使い始めたので情報整理"
date: 2023-09-05T09:00:00+09:00
description:
draft: false
tags:
- QubesOS
---
<!--more-->
# Abstract

自分のラップトップにQubesOSを導入し、2か月位試用して結局辞めてしまったのだが、最近また使い始めたのでQubesOS関係の情報をまとめることにする。

- QubesOSに関する従来研究(日本語情報)
    - QubesOSの概要
    - QubesOSのセットアップ
- QubesOS使うにあたって調べた内容の整理
    - 現在のQubesOSの問題点:suspend/resume問題
    - 従来研究になかったQubesOSの情報
        - 一部USB機器がdom0で認識されない問題の解決
        - ネットワーク構成にVPNを経由させる方法
        - カスタムDNSの設定方法
- 今後について

# QubesOSに関する従来研究(日本語情報)

英語圏情報は沢山あるけど、日本語情報は乏しい。

## QubesOSの概要

QubesOSについて良く解説されていると思われる記事はこちら。

[普通のLinuxから難攻不落のQubes OSに乗り換えてみた](https://o-o.hateblo.jp/entry/qubes-os-1)

その他、下記サイトに情報が充実している。

[「山形浩生の「経済のトリセツ」」 さん](https://cruel.hatenablog.com/)  
[「ウラの裏」 さん](https://www.ura-no-ura.com/)  
[「/ft/'s blog」 さん](https://o-o.hateblo.jp/)  

ちなみに、山形浩生さんはエドワード・スノーデンさんの自伝を日本語訳した方でもあります。

## QubesOSのセットアップ

QubesOSのセットアップのやり方が書いてある記事は下記。

というか、/ft/'s blogさんを読むと全部わかる。すごい。

個人的にも重宝したセットアップ記事は下記。

- [Qubes OSインストールの仕方](https://o-o.hateblo.jp/entry/qubes-os-install-initial-setup)
- [Qubes OS基本的な使い方・アップデート方法](https://o-o.hateblo.jp/archive/2022)
- [Qubes OS 基本的な使い方 Part2](https://o-o.hateblo.jp/entry/qubes-os-basics2)
- [Qubes OSの日本語化 その1](https://o-o.hateblo.jp/entry/qubes-os-lang-jp)
- [Qubes OS: テンプレートに外部ソースからソフトウェアをインストール](https://o-o.hateblo.jp/entry/new-template-install-qubesos)

# QubesOS使うにあたって調べた内容の整理

このままじゃただの他所の記事紹介記事になってしまうのでここから自分で手を動かして調べた内容を書く。

## 現在のQubesOSの問題点:suspend/resume問題
ラップトップをスリープ(≒suspend)すると、復帰時にスリープ状態から帰ってこれず、電源ボタンを長押ししないといけなくなる問題。

[QubesOSの公式ドキュメント](https://www.qubes-os.org/doc/suspend-resume-troubleshooting/)でも触れられており、ドキュメントによると

> On some devices suspend-to-RAM does not work and a hard power-off is needed to recover, because the system does not go into deep sleep.   
> To get suspend to work, you need to add mem_sleep_default=deep kernel option.   
> For legacy boot, or UEFI/legacy in R4.1+, add it to the GRUB_CMDLINE_LINUX setting in /etc/default/grub, update the grub config, and reboot.   
> In R4.0 with UEFI boot, you need to add it to every kernel= line in /boot/efi/EFI/qubes/xen.cfg and reboot.  

とあるが、一部上記は有効であるとの報告がある一方、githubのissueやQubesOSのフォーラムでは効果がないと報告されている。筆者も効果がなかった。

- [Qubes won't resume from suspend #7968](https://github.com/QubesOS/qubes-issues/issues/7968)  
- [Qubes 4.1.1 resume from suspend fails - system doesn't wake up #7611 ](https://github.com/QubesOS/qubes-issues/issues/7611)

ぱっと調べた感じでは機器差分によるという説があり、すぐに解決される状況ではなさそう。

スリープ出来ないのが致命的な人にはQubesOSはお勧めできないかも。

## 従来研究になかったQubesOSの情報

普段使いで支障が出うる内容でなので取り急ぎまとめ。

### 一部USB機器がdom0で使えない問題の解決

筆者はラップトップ内臓のキーボードが認識されるもののタッチパッドがdom0で使えない事象に見舞われた。

```/etc/qubes-rpc/policy/```内のdevice設定に下記を追記して、dom0で使えるようにする。下記はタッチパッドの例。

{{< codeblock "/etc/qubes-rpc/policy/qubes.InputTablet" >}}
sys-usb dom0 allow,user=root
sys-usb dom0 allow
{{< /codeblock >}}

### ネットワーク構成にVPNを入れる方法

```sys-vpn```というキューブを作り、匿名化技術の王道ともいえる「VPN over Tor」構成の構築を例に紹介。期待値は下記。

{{< figure src="015_1.png" = full >}}

<span style="color: red; ">
**※わざわざ注意書きしなくてもいいと思うが上記手法の悪用は厳禁。尚、悪用されないように一部手順を省いておきます。省くといってもvpn over tor自体は出来ます。
</span>


まずsys-vpn用のqubeを作る。設定は下記。

sys-vpnは接続元をtor接続にしたいので、Networkingはsys-whonixにし、他qubeにネットワーク機能を提供するので「Provides network access to other qubes」にチェックを入れる。

{{< figure src="015_2.png" = full >}}  
{{< figure src="015_3.png" = full >}}

次はVPN over Torを利用するAppVMを作る。設定は下記。接続元はさっき作成したsys-vpn。

{{< figure src="015_4.png" = full >}}

この段階でVPN over TorのFirefoxで、IP確認出来るサイトにアクセスした際にホストがTorになっていれば設定は上手くいっています。（まだVPN接続してないので、Torネットワークなはず）

{{< figure src="015_5.png" = full >}}

Torであることを確認したら、sys-vpnのnetwork-managerからVPN接続します。

{{< figure src="015_6.png" = full >}}

自分は一旦自宅のopenvpnに接続しました。ここで再度VPN over TorのFirefoxで接続元を確認し、それがVPN接続先になっていれば設定完了です。

{{< figure src="015_7.png" = full >}}

### カスタムDNSの設定方法

テンプレート単位でカスタムDNSを設定するのはおそらく無理そう。(sys-dnsみたいなキューブを作ってよしなにやればいけるかもだが不明)

筆者は現状AppVMの```/etc/resolv.conf```に直書きするようにしている。

# 今後について

以前は開発もラップトップでバリバリやる用として用意した結果、工数が嵩みがちだったので泣く泣くQubesOSを辞めてしまったが今は雑に携帯してネットサーフィンや匿名活動およびセキュリティの勉強用と割り切って使っているので当分は使うと思われる。

今もbluetooth接続の仕方やsuspend/resume問題やまだまだQubesOSで出来とかないとなと思うネタはありますので日々勉強です。