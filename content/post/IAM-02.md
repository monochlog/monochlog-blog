---
title: "IAM入門 ポリシー編"
date: 2021-11-26T10:50:47+09:00
lastmod: 2021-11-26T10:50:47+09:00
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
前回の[IAM入門 認証](/post/iam-01)でまとめましたユーザなどへの権限を定義する認可についてまとめていきます。

## 2.IAMとは
改めてIAMとは何かというと公式から引用します。
>AWS Identity and Access Management (IAM) は、AWS リソースへのアクセスを安全に管理するためのウェブサービスです。IAM により、誰を認証 (サインイン) し、誰にリソースの使用を承認する (アクセス権限を持たせる) かを制御します。

かいつまむと、「誰」と「権限」を定義するサービスです。多くの場合認証と認可という表現がされます。それらを定義していく機能になります。

ちなみに認証と認可は以下の通り。
- 認証：確認相手が「誰」であるかを確かめること。
- 認可：確認相手が「何」をできるかを定めること。

今回は「何」ができるか、つまり認可について基本的な事項をまとめていきます。

## 3.IAMでの認可とは
IAMで認可を設定する機能を「IAM ポリシー」と呼びます。
まずは認証の機能であるIAMユーザとIAMグループとのかかわりを見てみましょう。

今回も基本的な部分でまとめています。話を簡単にするため、今回は認可の中でもアイデンティティベースのポリシー、つまりユーザやグループに対する認可についてのみ扱います。

![認証と認可](/img/iam/iam-02.jpg "認証と認可")

IAM ポリシーはIAM ユーザやIAMグループに対して実施可能なアクションなどを定義し、権限を付与、割り当てて利用します。

グループに割り当てを行うことで、まとめて権限を管理することができるため、基本的にはグループへの割り当てを行うほうが良いといえます。

## 4.ポリシーの構成
AWSポリシーはほとんどJSON形式で定義されています。構成イメージを公式ドキュメントから引用します。各要素についてまとめていきます。

![ポリシー構成](/img/iam/iam-03.jpg)

[JSON ポリシードキュメント構造](https://docs.aws.amazon.com/ja_jp/IAM/latest/UserGuide/access_policies.html)

### Optional top-level elements
jsonファイル全体に適用されるポリシー言語のバージョンを指定します。
基本的には以下の通りです。

```json
"Version": "2012-10-17"
```
### Statement
具体的な権限を記述するためのコンテナ的な位置づけです。
1つのポリシーには複数のStatementを含めることができます。

### Sid（オプション）
SidはStatement iDの略で、複数のStatementがある場合に区別をしやすくするための記述です。
```json
"Sid": "FirstStatement",
```
### Effect
Allow　または　Denyを指定し、権限の許可か拒否かを定義します。
```json
"Effect": "Allow"
```
### Principal（オプション、一部のみ必須）
Principalは「このルールを誰に割り当ててよいか」を指定します。
指定はARNを指定します。

前段のイメージ図の通りポリシーは色々なユーザやグループに割り当てるため、この指定は必須ではありません。

用途としては「特定のグループだけS3のバケットを利用できるようにしたい」みたいな場合に、「特定のグループ」を指定する利用方法になります。
```json
"Principal": {"AWS": ["arn:aws:iam::ACCOUNT-ID-:root"]},
```
### Action
Actionでは権限を与えたい、または拒否したい具体的な操作を指定します。

```json
"Action": ["iam:ChangePassword"]
```

アクション自体は多くの種類があるため、覚えるものでなく都度参照して合致するものを選択する、という使い方がよいと思います。


### Resource（オプション、一部のみ必須）
このActionなどで操作してよい対象（リソース）を指定します。指定方法はPrincipalと同様にARNを用います。
例えば「EC2の中でも特定のインスタンスだけ」にしたい場合に「特定のインスタンス」を指定する利用方法になります。
指定しない場合は、このポリシーをアタッチ（割り当て）しているリソースのみが対象になります。

よく現場で利用する方法としてはワイルドカードで以下のようにすることがあります。
```json
"Resource": "*"
```
### Condition(オプション)
ポリシーでアクセス許可を与える場合の条件を指定します。よく用いるのはMFAが有効になっているユーザに対しては権限を与えたい、という感じですね。
この場合の起債は下記の通りとなります。
```json
"Condition": {"Bool": {"aws:MultiFactorAuthPresent": "true"}}
```

## 5.ポリシー作成
ここまでポリシーの中身をマニュアルチックに見てきましたが、これらを全部把握して構築するのは大変です。
そのため、AWSではポリシーを作る方法やテンプレートが複数用意されています。

1. 管理ポリシーとインラインポリシー
2. ビジュアルエディタ
3. ポリシーのテスト


### 管理ポリシーとインラインポリシー
手作業ですべてのポリシーを作成することは現実的ではないです。そのため、AWSが作成し、管理しているポリシーがあり、それをAWS管理ポリシーといいます。一方でユーザが自由に作成したものをカスタマ管理ポリシーと呼びます。

また管理者の違いではなく、記載方法として特定のプリンシパルエンティティ（ロール、ユーザ、グループなどの総称）と1対1で定義するポリシーもあります。

これらの特徴をまとめます。

|種類|利用方法|概要|
|:-|:-|:-|
AWS管理ポリシー|プリンシパルエンティティにアタッチする|AWSが管理をしており、典型的なテンプレートとして利用が可能。アップデートなどの変更も実施される。
カスタマー管理ポリシー|プリンシパルエンティティにアタッチする|各カスタマーで自身で作成したポリシー
インラインポリシー|特定のプリンシパルエンティティに埋め込む|厳密な利用者を定義したい場合に作成する専用のポリシー

## 6.ポリシーのベストプラクティス
ここまで見てきた内容についてBlackBeltにあるベストプラクティスをまとめます。
- 最小限の権限を付与する。
- はじめはAWS管理ポリシーを用いる。なれたらAWS管理ポリシーをカスタマイズしたカスタマー管理ポリシーを作成する。
- ポリシーはユーザではなくグループ単位に割り当てる
  - 管理を容易にすることができる。
- インラインポリシーではなく、カスタマー管理ポリシーを使用する
  - カスタマイズが可能、再利用性が高い、ポリシーの管理を集約できるなどのメリット
- 安全性を向上させるためにCondition要素を利用する
  - 許容するIPアドレス、日時、MFAデバイス認証の要求、SSLの要求などを指定する


[20190129 AWS Black Belt Online Seminar AWS Identity and Access Management (AWS IAM) Part1](https://www.slideshare.net/AmazonWebServicesJapan/20190129-aws-black-belt-online-seminar-aws-identity-and-access-management-iam-part1)

## 7.まとめ
今回はIAMの中でも権限を定義するポリシーに注目してまとめてきました。ポリシーを使うことで特定の権限をグループ単位に付与、または拒否できるなどを行うことでAWS利用者へのコントロールを適切にしていきましょう。

次回はIAMロールについてまとめていきます。
