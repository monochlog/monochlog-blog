---
title: "IAM入門"
date: 2021-11-26T09:26:48+09:00
lastmod: 2021-11-26T09:26:48+09:00
draft: false
keywords: []
description: ""
tags: []
categories: []
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
今回はAWS IAMについて詳細をまとめていきます。AWSのサービスの中でも共通的で目立たないマネージドサービスですが、セキュリティ面からみると最も重要といっても過言ではない機能であり、奥が深いため、正確な理解をしていきましょう。

## 2.IAMとは
まずは公式からの引用です。
>AWS Identity and Access Management (IAM) は、AWS リソースへのアクセスを安全に管理するためのウェブサービスです。IAM により、誰を認証 (サインイン) し、誰にリソースの使用を承認する (アクセス権限を持たせる) かを制御します。

かいつまむと、「誰」と「権限」を定義するサービスです。多くの場合認証と認可という表現がされます。それらを定義していく機能になります。

今回は「誰」つまり認証について基本的な事項をまとめていきます。

## 3.認証
AWSでの認証は多くの機能がありますが、ここではまず以下に示す基本的なものだけを扱います。

![IAMユーザ](/img/iam/iam-01.jpg "IAMユーザの基本")

