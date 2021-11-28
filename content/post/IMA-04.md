---
title: "IAM クロスアカウント  "
date: 2021-11-29T13:27:12+00:00
lastmod: 2021-11-29T13:27:12+00:00
draft: true
keywords: []
description: ""
tags: ["AWS IAM"]
categories: ["AWS"]
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
## 1.はじめに
今回はIAMの中でも自AWSアカウントではなく、他AWSアカウントや他のIDプロバイダ（facebookやgoogleなど）のユーザの活用という話になります。

結構細かい内容が多いですが、基本的にBlackBeltの説明をベースに嚙み砕いていくことにします。

## 2.マルチアカウント
そもそも、マルチアカウントにする理由です。

BlackBeltにあるユースケースを見ながら説明していきます。
![マルチアカウントのユースケース](/img/iam/iam-06.jpg)

### アカウントを分ける理由
システム開発を行っていくうえで、経験はあるかもしれません。

本番環境は多くの場合リリースをしてから多数のユーザが利用しており、気軽にシステム停止やデプロイなどができないことが多いです。また開発は複数の機能改修などが変更して行われており、頻繁なデプロイがあります。

このとき1つのアカウント（またはIAMユーザ）で実施していた場合、操作ミスなどにより意図しない環境への操作を行ってしまう可能性があります。

そこで、アカウントを分ける、という考え方になります。

### アカウント間でアクセスを許可する理由
環境単位での権限を分けるためにアカウントを分けた場合、作業の度にログインをし直す手間や、検証環境で試験した資材を本番環境に持ち込むことができない、という制約もついてしまいます。
また、単純に分けただけでは各アカウントでIAMユーザを作り、かつ、権限を管理する、という手間も雪だるま式に増えていってしまいます。

そこで信用可能なアカウントやユーザやリソースに対しては自アカウントのリソースへの操作を許可する権限を与える、という仕組みをとります。

これがロールを用いたクロスアカウントの基本的な考えになります。

##クロスアカウント
さて、改めてBlackBeltにあるユースケースをに戻ります。
マルチアカウントを用いたクロスアカウント方針、、と記載すると似た言葉が続きますが、ほぼ同義だと思って構いません。

![マルチアカウントのユースケース](/img/iam/iam-06.jpg)

このユースケースが示していることを噛み砕いていこうと思います。

### ジャンプアカウント
このジャンプアカウントはユーザを作成し、管理する専用のアカウントです。このアカウント内だけにIAMユーザを作成することを指しています。

またジャンプアカウントから他のアカウントへのアクセスが同時ユーザから出ている通り、1人1人が１つのアカウントだけを利用し、他のアカウントにはロールを付与されて操作をする、という形をとります。

ちなみに各ユーザは「Swtich Role」を行うことで再ログインなどを不要で必要な時に必要な権限に切り替えることが可能です。

### 他のアカウント 