---
title: "ファイルストレージ"
date: 2021-10-28T23:28:29+09:00
lastmod: 2021-10-28T23:28:29+09:00
draft: false
keywords: []
description: ""
tags: ["ストレージ"]
categories: ["ITリテラシ"]
author: "Mono"

# You can also close(false) or open(true) something for this content.
# P.S. comment can only be closed
comment: true
toc: true
autoCollapseToc: true
postMetaInFooter: false
hiddenFromHomePage: false
# You can also define another contentCopyright. e.g. contentCopyright: "This is another copyright."
contentCopyright: false
reward: false
mathjax: false
mathjaxEnableSingleDollar: false
mathjaxEnableAutoNumber: false

# You unlisted posts you might want not want the header or footer to show
hideHeaderAndFooter: false

# You can enable or disable out-of-date content warning for individual post.
# Comment this out to use the global config.
#enableOutdatedInfoWarning: false

flowchartDiagrams:
  enable: false
  options: ""

sequenceDiagrams: 
  enable: false
  options: ""

---

<!--more-->

## 1.目的

クラウドが普及して、もう一般的になっています。その中でストレージのいわゆる種類がいくつかあります。
ストレージの種類とは、オブジェクト、ファイル、ブロックなどのストレージのことを扱います。
また、多くの場合、クラウドはクラウド、オンプレはオンプレで整理されがちなので、その辺をまとめて置きたいと思います。

## ストレージの種類
先に結論をまとめましょう。今回の整理範囲はこんな感じ。  
<div  align="center"><a  target="_blank" title="WS000000" href="https://livedoor.blogimg.jp/tetratetto1985-14mqzmex/imgs/b/5/b585dc36.jpg"><img  class="pict" hspace="5" alt="WS000000" border="0" height="88" width="480" src="https://livedoor.blogimg.jp/tetratetto1985-14mqzmex/imgs/b/5/b585dc36-s.jpg"></a></div>  
<div  align="center"><a  href="https://livedoor.blogimg.jp/tetratetto1985-14mqzmex/imgs/a/b/ab89e902.jpg" title="WS000001" target="_blank"><img  src="https://livedoor.blogimg.jp/tetratetto1985-14mqzmex/imgs/a/b/ab89e902-s.jpg" width="480" height="270" border="0" alt="WS000001" hspace="5" class="pict"></a></div>

では行きましょう。
## ローカルストレージ
### 概要
ローカルストレージとは、そのままになりますが、ローカルにあるストレージです。
そのまんまでが、言い換えると「CPUと同じ筐体などに設置されたり、物理的に距離の近い」ストレージと言えます。

PCであれば分かりやすく、PC内に入っているHDD,SSDのことですね。基本的には自分（OSから見て自分自身）が利用するためのデータ領域です。
このようなストレージをDAS（Direct Attached Storage）といいます。

### 使用イメージ
<div  align="center"><a  href="https://livedoor.blogimg.jp/tetratetto1985-14mqzmex/imgs/8/0/80cdace5.jpg" title="WS000002" target="_blank"><img  src="https://livedoor.blogimg.jp/tetratetto1985-14mqzmex/imgs/8/0/80cdace5-s.jpg" width="480" height="297" border="0" alt="WS000002" hspace="5" class="pict"></a></div>

### データ領域のアクセス方法（プロトコル）とデータの単位
データ領域、つまりHDDやSSDへのアクセス方法（プロトコル）はACHIやNVMeといった規格があります。
その時のデータの受け渡しのサイズは4KBであることが多いです（特にLinux）。Windowsもクラスターサイズという表現になりますが4KBでアクセスすることが多いです。


このようにブロック単位でアクセスするのでローカルストレージも分類的にはブロックストレージになります。  

## ブロックストレージ
### 概要
ブロックストレージとは、OSがブロック単位でアクセスするストレージです。

と言ってしまうと、先ほどのローカルストレージと違いがないですね。実はその通りで、データのアクセス単位という見方をすると、
ローカルストレージとは差がありません。
ただストレージの種類として「ブロックストレージ」という場合は、多くの場合は「SAN(Storage Area Network)」（もしくはそれと同等の仕組み）を指すことが多いです。

<div  align="center"><a  href="https://livedoor.blogimg.jp/tetratetto1985-14mqzmex/imgs/a/7/a7b77f22.jpg" title="WS000003" target="_blank"><img  src="https://livedoor.blogimg.jp/tetratetto1985-14mqzmex/imgs/a/7/a7b77f22-s.jpg" width="480" height="280" border="0" alt="WS000003" hspace="5" class="pict"></a></div>


### データ領域のアクセス方法（プロトコル）とデータの単位
アクセス方法（プロトコル）はiSCSIやFCoE(Fiber Channel over Ethernet)といった規格があります。
物理的なケーブルの種類の差分はありますが、ブロック単位でのアクセスであるため、ローカルストレージと同等の扱いができます。


### 使い道
なぜローカルストレージと分別しているか、というといくつかメリット（もちろんデメリットも）があります。<br />
<ui>
    <li>メリット</li>
    <ol>
        <li>ディスクを束ねることで管理がしやすい。過不足に応じて再割り当てなどが可能</li>
        <li>NWとは異なる接続（ケーブル）となるため、負荷を分散可能</li>
        <li>OSが直接ファイルシステムを管理することでアクセス速度が速い</li>
        <li>バックアップ対象の集約による効率化や、OS領域のシステムバックアップなどが簡単になる</li>
    </ol>
    <li>デメリット</li>
    <ol>
        <li>NW同等に利用台数が増えるとFC用のスイッチが必要になる。</li>
        <li>OSやハードがSANに対応している必要があり、設定にも専門的な知識が必要</li>
        <li>NASに比べると効果となる。（FC関連が高い）</li>
    </ol>
</ui>

私の個人的な意見としてはバックアップの効率化、容易化はオンプレにおいてはすごく重要です。

ローカルディスクしかないサーバを何台もイメージバックアップ用のディスクでブートしLTOに取得する、というような
マンパワーに依存したシステムを経験すると有難みがすごく分かります。

### 補足
クラウドでこのような位置づけに該当するものはAWSならEBS、Azureならマネージドディスクと呼ばれます。
これらは仮想化をされているのでSANと同じものではありませんが、ブロックストレージです。
仮想化およびサーバから独立しているため、イメージバックアップや割付の変更ができたりとSANのメリット（もしくはそれ以上）の
恩恵を受けることができます。

## ファイルストレージ
### 概要
ファイルストレージとは、OSがファイル単位でアクセスするストレージです。そのまんまですね。オブジェクトでも同じことを言います。
もう少しかみ砕くと、ファイルの読み書きを行う場合に、「ファイル単位」でデータのアクセスが完結する。というイメージです。

これを具体的に理解するには少しOSの動きを理解する必要があります。
OSはブロックストレージを「ファイルシステム」として認識しています。「ファイルシステム」が何かというと、
具体的にはNFTSとか、XFSを指すのですが、イメージでいうと、「この装置はディスクで、容量1TB、使用率は20％」みたいなことが
分かるということです。
これは「ストレージの管理は自分である」という風に言えます。

一方で、ファイルストレージはストレージの管理（物理的な管理）は自分ではない、と言えます。

<div  align="center"><a  href="https://livedoor.blogimg.jp/tetratetto1985-14mqzmex/imgs/b/7/b7dbc0cf.jpg" title="WS000004" target="_blank"><img  src="https://livedoor.blogimg.jp/tetratetto1985-14mqzmex/imgs/b/7/b7dbc0cf-s.jpg" width="480" height="293" border="0" alt="WS000004" hspace="5" class="pict"></a></div>


### データ領域のアクセス方法（プロトコル）とデータの単位
ファイルストレージはアクセスする単位が「ファイル」で、OSが認識できるものは「フォルダ（ディレクトリ）とファイル」しか
ありません。全体容量や使用率はどれくらいかは分かりません
  （マウント等すればアクセス可能な範囲は見れますが、アクセスできない領域は分かりません）

### 使い道
主にオンプレではNAS(Network Attached Storage)として構築されます。
もちろんこれによりメリット、デメリットがあります。
<ui>
    <li>メリット</li>
    <ol>
        <li>OSがストレージの管理をしなくてよい。（ディスクの不足に対する追加、など）</li>
        <li>NW（TCPIP）で構築するため構築難易度が低い</li>
        <li>SANに比べ安価に構築できる</li>
    </ol>
    <li>デメリット</li>
    <ol>
        <li>性能はNWに依存する</li>
    </ol>
</ui>

## オブジェクトストレージ
### 概要
オブジェクトストレージとは、ファイルなどを「オブジェクト」という単位で扱い、アクセスするストレージです。<br /><br />
このオブジェクトとは、AWSのS3が始まりのようですが、ファイル＋メタデータ（例えばファイルID）で管理しています。
このメタデータ（のファイルID）を指定してファイルにアクセスを行います。
<br /><br />
これまでのストレージと異なり、ファイルへたどり着くにはメタデータで指定するため、ディレクトリ階層表現は不要で、「フラットな構成」
が特徴です。
<br /><br />
少し仕組みの裏側を記載すると、AWSなどのクラウドの中身では、ブロックストレージでディレクトリ共有しているものが基本です。<br /><br />
このディレクトリ共有を大量に作成し、仮想的に1つのストレージとして見せている、というイメージを持ってもらえればと思います。<br /><br /><div  align="center"><a  href="https://livedoor.blogimg.jp/tetratetto1985-14mqzmex/imgs/4/5/4515e375.jpg" title="WS000005" target="_blank"><img  src="https://livedoor.blogimg.jp/tetratetto1985-14mqzmex/imgs/4/5/4515e375-s.jpg" width="480" height="275" border="0" alt="WS000005" hspace="5" class="pict"></a></div><br />

### データ領域のアクセス方法（プロトコル）とデータの単位
オブジェクトストレージへはアクセスする単位は「オブジェクト」となります。アクセス方法はHTTP(S)であり、いわゆるブラウザからアクセスするか
アプリケーション(Restful API)などを介して利用します。<br /><br />
基本的にはアプリケーションがアクセスするものの認識でOKです。<br /><br />

### 使い道
こちらはオンプレではあまり実装されないです。例えばですが、windowsではDFSを用いて実現することは可能です。<br /><br />
ただ以下のメリットを損なってしまっていると思います。
<ui>
    <li>メリット</li>
    <ol>
        <li>OSがストレージの管理をしなくてよい。（ディスクの不足に対する追加、など）</li>
        <li>大容量（PBなど）、大多数ファイルを扱うことができる。</li>
        <li>メタデータのインデックス作成により高速なアクセスが可能</li>
    </ol>
    <li>デメリット</li>
    <ol>
        <li>性能はNWに依存する</li>
        <li>同一オブジェクトを頻繁に更新する処理には不向き</li>
    </ol>
</ui>

## まとめ
長くなりましたが、ストレージの種類についてアクセス方法やその特徴を整理しました。
いずれのストレージも一長一短あるため、利用目的に応じたものを活用する用にしましょう。