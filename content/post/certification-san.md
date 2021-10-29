---
title: "SAN対応自己証明書"
date: 2021-10-28T23:28:29+09:00
lastmod: 2021-10-28T23:28:29+09:00
draft: false
keywords: []
description: ""
tags: ["セキュリティ"]
categories: ["ITリテラシ","Linux"]
author: "Mono"

# You can also close(false) or open(true) something for this content.
# P.S. comment can only be closed
comment: ture
toc: ture
autoCollapseToc: ture
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
## はじめに
今回は自己証明書のうちでも、主流になっているSANへの対応方法をまとめます。
そもそもですが、SANとはsubject Alt Nameの略称です。

以前は1ドメイン、1証明書が必要であり、大量のサーバを構築する際にはその管理や適用作業にとても手間がかかりました。  

このSAN（サブジェクトの別名）においてはサブドメインを含めたドメインを指定できるようになり、マルチホスト、マルチドメインで1つの証明書をまとめることができるようになりました。

また、別の角度からですが、Chrome、Edgeブラウザ（chromiumベースブラウザ）バージョン58+ではCN（コモンネーム）という 従来ドメインを指定していた箇所だけでは評価しなくなっています。

私の周辺では利用ユーザが多いこともあり、証明書でのSAN対応は必須な状況です。

はい。前置きが長くなったので作成方法。

## 作成方法
`openssl genrsa -aes128 2048 > server.pem`

公開鍵の作成 (細かいところは適宜変えてください）  
`openssl req -new -key server.pem > server.csr`
~~~
Country Name (2 letter code) [AU]: JP
State or Province Name (full name) [Some-state]: Tokyo
Locality Name (eg, city) []: Minato-ku
Organization Name (eg, company) [Internet Widgits Pty Ltd]: Test Inc.
Organizational Unit Name (eg, section) []:test section
Common Name (eg, YOUR name) []:xx.xx.xx.xx(もしくはFQDN)
Email Address []:（任意 何も入力せずEnter）
A challenge password[]:（任意 何も入力せずEnter）
An optional company name []:（任意 何も入力せずEnter）
~~~

san用のファイルを作成  
`vi /etc/host/svrcrt/san`
~~~
subjectAltName = IP:xx.xx.xx.xx,DNS:*.test.local
~~~

サーバ証明書の発行(san含む)  
`openssl x509 -req -days 365 -sha256 -in server.csr -signkey server.pem -out server.crt -extfile san`  

パスフレーズの省略化  
`mv server.pem server.pem.back`
`openssl rsa -in server.pem.back -out server.pem`

以上です。

あとは作成したものを各アプリケーションなどで利用しましょう。
