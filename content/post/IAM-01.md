---
title: "IAM入門 認証"
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

ちなみに認証と認可は以下の通り。
- 認証：確認相手が「誰」であるかを確かめること。
- 認可：確認相手が「何」をできるかを定めること。

今回は「誰」つまり認証について基本的な事項をまとめていきます。

## 3.認証
AWSでの認証は多くの機能がありますが、ここではまず以下に示す基本的なものだけを扱います。

![IAMユーザ](/img/iam/iam-01.jpg "IAMユーザの基本")

### ルートユーザ
ルートユーザは正しくはIAMで管理するものではありません。AWSアカウントを作成した際に自動的に作られるユーザを指します。

このユーザは以下のような特徴があります。
- AWSアカウントを作成したEメールアドレスおよびパスワードでログインする
- すべてのリソースに関してすべての権限を有する
- IAMで設定する権限設定では制御できない

多くの場合ルートユーザの使用は制限する、またセキュリティを高くする（MFAを適用する）などがベストプラクティスとして挙がっています。詳しくは別途記載仕様と思います。

### IAMユーザ
IAMユーザはAWSアカウント内で作成するユーザで、このユーザを用いてAWSの各種マネージドサービスなどを利用します。どのサービスや機能を利用できるかは認可で設定します。

基本的には1人1ユーザとするのが望ましいです。

### パワーユーザ（IAMユーザ）
IAMユーザの中でも特別扱いされるのがパワーユーザです。正確には「パワーユーザ」という権限を付与したIAMユーザです。

パワーユーザはルートユーザからIAMの操作権限を外したもの、になります。

### IAMグループ
IAMグループとはIAMユーザをまとめて管理する機能です。グループにまとめることで、別途まとめます認可（権限）の付与に利用することで運用効率が上がります。

IAMグループは私の経験では会社の部署（経理部、営業部、システム部、など）のような単位でまとめることが多いイメージです。

### 実際の使い方
経験談となりますが以下のような使い方をしています。

- IAMグループとIAMユーザ（パワーユーザ）を作る
  - IAMグループは前述の通り部署ごと、その中で管理者としてパワーユーザを1つ作成
- 必要に応じて各IAMグループに一般利用者のIAMユーザを作成

部署内での管理についてはパワーユーザを持つIAMユーザに委ね、利用するユーザや権限は別で管理する、といったイメージです。

## まとめ
今回は基本的なIAMの認証で登場するユーザ、グループについてまとめました。これだけでは重要性が見えてきませんが、次回説明予定の認可を加えることでぐっと深みが出てきます。

