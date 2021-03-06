---
title: "IAM入門 ロール編"
date: 2021-11-28T16:22:41+09:00
lastmod: 2021-11-28T16:22:41+09:00
draft: false
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
前回の[IAM入門 認可(ポリシー編)](/post/iam-02)をまとめてきましたが、今回はIAMロールについてまとめていきます。

## 2.IAMとは
改めてIAMとは何かというと公式から引用します。
>AWS Identity and Access Management (IAM) は、AWS リソースへのアクセスを安全に管理するためのウェブサービスです。IAM により、誰を認証 (サインイン) し、誰にリソースの使用を承認する (アクセス権限を持たせる) かを制御します。

かいつまむと、「誰」と「権限」を定義するサービスです。多くの場合認証と認可という表現がされます。それらを定義していく機能になります。

ちなみに認証と認可は以下の通り。
- 認証：確認相手が「誰」であるかを確かめること。
- 認可：確認相手が「何」をできるかを定めること。

今回は「何」ができるか、つまり認可について基本的な事項をまとめていきます。
特にポリシーとの違いも意識しながら理解しましょう。

## 3.IAMロールとは
IAMロールはIAMアイデンティティの1つで、割り当てられたアイデンティティがアクセス許可や拒否といった権限を持つことができます。
ここまではポリシーと同じですが、違いとしてロールは特定のユーザやアカウントに紐づくものではなく、必要な時に引き受ける、という意味合いがあります。
またその利用対処もリソースや他AWSアカウントが対象となります。

またポリシーのようにインラインポリシーとしてjson形式で個別の権限を記載することもできますが、多くはすでに定義しているポリシーを割り当てます。つまり、ポリシーのグルーピングすることができます。

分かりづらいため、イメージ図を見てみましょう。
なお、ここでは同一AWSアカウント内に閉じた場合で整理しています。マルチアカウント、クロスアカウントはまたの機会にします。

![ポリシーとロール](/img/iam/iam-04.jpg)

表.ポリシーとロールの違い
|分類|割り当て先|用途|定義方法|
|-|-|-|-|
ポリシー|ユーザまたはグループ（同一AWSアカウント内）|権限の割り当て|AWS管理ポリシーなどのjson
ロール|リソースまたは他アカウントのユーザ、グループ|権限の割り当て|ポリシーの割り当て

つまるところ以下のイメージがよいと思います。

　ポリシー：自AWSアカウントのグループの権限を割り当てるためのもの  
　ロール：ポリシーをまとめるためのもの  
　　　　　自AWSアカウントのリソースへの権限を割り当てるもの  
　　　　　他AWSアカウントのユーザ、グループへの権限を割り当てるもの

## 4.IAMロールの認証
前述の通り、ロールは自分のユーザやグループではなく、リソースや他アカウントのユーザ、グループに権限を付与するものです。

このとき、ユーザは基本的にログインIDやMFAによって認証がされていますが、リソースや他アカウントは認証をしていません。

そこで利用するのがAWS STS(Security Token Service)です。ユーザがIDとパスワードで認証するように、IAMロールを利用するリソースはSTSに一時的なセキュリティ認証情報（アクセスキー、シークレットアクセスキー、セキュリティトークン）を発行してもらい、権限を行使します。

![IAMロールの認証](/img/iam/iam-05.jpg)

アプリケーションは準備されているAPIを利用して上記の認証を行い、ロールを利用した処理を行います。APIについては別途にしましょう。

## 5.まとめ
ロールについてポリシーの都の違いやをメインに整理しました。次回はクロスアカウントや他IDプロバイダのユーザへの権限付与など応用的な内容をまとめます。     