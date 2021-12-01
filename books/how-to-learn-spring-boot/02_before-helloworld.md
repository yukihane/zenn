---
title: "Hello, world! を実装する…前に"
---

本章では、 Spring Boot で Hello, world! 実装で触れることになるドキュメントなどの説明を行います。
とにかく動かしてみたい、という場合は本章をスキップして次章へ進んでください。

# Guides ページについて

さて、Spring Boot の公式サイトで "getting started" 的なものはどこにあるかというと、 [Guides](https://spring.io/guides) のページです[^getting-started]。
これらのうち、次章では [Building a RESTful Web Service](https://spring.io/guides/gs/rest-service/) を見ていきます。
ちなみに、このページの URL は

- https://spring.io/guides/gs/rest-service/

ですが、ドメインを変更して

- https://spring.pleiades.io/guides/gs/rest-service/

にアクセスすると日本語訳サイト[^japanese]で見ることもできます。

また同様に、 Spring Boot 本体のリファレンスは

- https://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/

ですが、これも URL を `https://spring.pleiades.io/` に変更して

- https://spring.pleiades.io/spring-boot/docs/current/reference/htmlsingle/

で日本語訳サイトに飛べます。
他の Spring プロジェクトのドキュメントも同じルールで日本語訳を参照できます。

[^getting-started]: Spring Boot のリファレンスを読み進めた場合、[4.4.Developing Your First Spring Boot Application](https://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/#getting-started.first-application) などで Guides へのリンクを目にすることになります。
[^japanese]: [柏原 真二 (cypher256)さん](https://spring.pleiades.io/)が運営されているサイトで、おそらく非公式です(Spring プロジェクトが翻訳/改変文書をどういう扱いにしているのか私が理解していないので本書ではこれ以上言及しませんが、英語に苦手意識を持っている方は適宜参照してください)。

# Guides ページの読み進め方

それぞれのガイドでは、読み進めるために 3 つの経路が用意されています。

- ゼロからコードを作成していく
- ゼロからではなく、初期セットアップだけ済んだコードを使って実装していく
  - 慣れてしまうと最初のプロジェクト構築は同じことの繰り返しなので、そこだけスキップします
- 完全に実装されたコードを動作させてみる

画面右にある "Get the Code" の GitHub リンク先に本ガイドで作成するコードが格納されています。

![Get the Code](https://storage.googleapis.com/zenn-user-upload/a79187ba9790-20211130.png)

リンクをたどってリポジトリのページへ飛ぶと、 `initial` と `complete` というディレクトリが用意されています。
前述した 3 つの経路のうち、"初期セットアップだけ済んだコードを使って実装していく"場合、 `initial` ディレクトリのコードを利用します。 "完全に実装されたコードを動作させてみ"たい場合、 `complete` ディレクトリのコードを実行します。

# Spring Initializr

前節で 3 つの経路を説明しましたが、そのうちのひとつめ、ゼロからコードを作成する場合には Spring Initializr を利用すると簡便にひな形作成ができます。
(ガイドページでは "Starting with Spring Initializr" 節で説明されています)

Spring Initializr とは https://start.spring.io/ のことです[^initializr]。

[^initializr]: なお、同機能は [セットアップ](https://zenn.dev/yukihane/articles/fb52d049da587c)を済ませた IDE からや [Spring Boot CLI](https://docs.spring.io/spring-boot/docs/current/reference/html/cli.html) というコマンドラインツールからも実行できます。

この URL を開くと、入力項目がいくつか表示されます。
各項目を見ていきましょう。

| 名称             | 説明                                                                                                                                                                                                                                                                                        |
| ---------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Project          | Maven/Gradle はビルドツールです。<br>どちらのビルドツールを用いて作成するプロジェクトを管理するかを選択します。<br>今回は Maven を用いて説明を進めます。<br>なお Guides リポジトリには両方格納されています。<br>Maven/Gradle については "**FAQ**" 章 でも触れています。                     |
| Language         | 利用する言語を選択します。今回は Java を用いて説明を進めます。                                                                                                                                                                                                                              |
| Spring Boot      | 利用する Spring Boot のバージョンを選択します。<br>特に理由がないのであれば最新安定版を指定します。                                                                                                                                                                                         |
| Project Metadata | 今回作成するプロジェクトの情報を記入します。<br>Group と Artifact がプロジェクトの識別子になります[^maven]。                                                                                                                                                                                |
| Packaging        | 最終成果物を `.jar` 形式で出力するか `.war` 形式で出力するかを選択します。<br>`.jar` は組み込み Tomcat[^servlet] を利用してスタンドアローンで動作させる場合、 `.war` は Jakarta EE に準拠したサーブレットコンテナにデプロイして実行する場合に選択します。<br>今回は前者の方式で実行します。 |
| Java             | 利用する Java のバージョンを指定します。<br>今回は`17`を用いて説明します。                                                                                                                                                                                                                  |
| Dependencies     | 利用するライブラリを指定します。この選択肢についてはこの後説明します。                                                                                                                                                                                                                      |

**Dependencies** では利用するライブラリを選択します。 **ADD** ボタンを押すと追加可能なライブラリ一覧が表示されます。
画面上にも記載されていますが、 `Ctrl` ボタンを押しながらクリックすることで連続してアイテムを選択することが可能です。
(特に入門段階で)よく使うライブラリを下表に示します。

| 名称(カテゴリ)                                   | 概要                                                          | リファレンス                                                                                                                                                                                                                                                                                                                                                                                                                                       |
| ------------------------------------------------ | ------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Spring Boot DevTools (Developer Tools)           | 開発効率化の機能を提供する                                    | [6.8.Developer Tools](https://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/#using.devtools)                                                                                                                                                                                                                                                                                                                                        |
| Spring Configuration Processor (Developer Tools) | 独自コンフィグのメタデータを自動生成する                      | [B.3.Generating Your Own Metadata by Using the Annotation Processor](https://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/#configuration-metadata.annotation-processor)                                                                                                                                                                                                                                                            |
| Lombok (Developer Tools)                         | Java 言語の機能を補完する                                     | [Lombok features](https://projectlombok.org/features/all)                                                                                                                                                                                                                                                                                                                                                                                          |
| Spring Web (Web)                                 | Spring Framework のウェブレイヤー。 Spring MVC とも呼ばれる。 | [Spring Framework Documentation: Web Servlet](https://docs.spring.io/spring-framework/docs/current/reference/html/web.html)                                                                                                                                                                                                                                                                                                                        |
| Spring Session (Web)                             | Spring Web だけでは不足するセッション機能を提供する           | [Spring Session](https://docs.spring.io/spring-session/reference/)                                                                                                                                                                                                                                                                                                                                                                                 |
| Thymeleaf (Template Engines)                     | HTML を動的生成する                                           | [Using Thymeleaf](https://www.thymeleaf.org/doc/tutorials/3.0/usingthymeleaf_ja.html) および [Thymeleaf + Spring](https://www.thymeleaf.org/doc/tutorials/3.0/thymeleafspring.html)                                                                                                                                                                                                                                                                |
| Spring Security (Security)                       | 認証と認可の機構                                              | [Spring Security Reference](https://docs.spring.io/spring-security/reference/index.html)[^security]                                                                                                                                                                                                                                                                                                                                                |
| Spring Data JPA (SQL)                            | DB アクセス、OR マッパ                                        | [Spring Data JPA - Reference Documentation](https://docs.spring.io/spring-data/jpa/docs/current/reference/html/) および [Hibernate ORM User guide](https://docs.jboss.org/hibernate/orm/5.6/userguide/html_single/Hibernate_User_Guide.html)                                                                                                                                                                                                       |
| H2 Database (SQL)                                | インメモリデータベース(主に開発時に利用)                      | [H2 Dataqbase Feaures](https://www.h2database.com/html/features.html)                                                                                                                                                                                                                                                                                                                                                                              |
| Validation (I/O)                                 | 入力データや永続化データの検証                                | [Spring Framework Documentation Core: 3.7.Java Bean Validation](https://docs.spring.io/spring-framework/docs/current/reference/html/core.html#validation-beanvalidation)、 [Web Servlet](https://docs.spring.io/spring-framework/docs/current/reference/html/web.html) の Validation 説明箇所、 および [Hibernate Validator Reference Guide](https://docs.jboss.org/hibernate/validator/6.2/reference/en-US/html_single/#validator-gettingstarted) |
| Spring Boot Actuator (OPS)                       | アプリケーションのメトリクス収集                              | [Spring Boot Reference Documentation: 13.Production-ready Features](https://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/#actuator)                                                                                                                                                                                                                                                                                                |

より詳しく知りたい場合には、"リファレンス" 列に記載したリンク先のドキュメントを読んでください。
一瞥するとわかる通り、 Spring プロジェクト以外のものも多く含まれ、参照すべきドキュメントも多岐に渡ります。
"**はじめに**" 章冒頭でも触れましたが、Spring Boot は、チュートリアルの範囲で実装する分にはこのようなドキュメントを意識せずとも実装できるように工夫されています。
しかし一歩でも外へ踏み出そうとした場合、どこに自分の欲しい情報が存在しているのかを把握しておくことは重要です。

[^maven]: 詳細は[Maven Getting Started Guide > How do I make my first Maven project?](http://maven.apache.org/guides/getting-started/index.html#how-do-i-make-my-first-maven-project) の `groupId`, `artifactId` の説明箇所を参照してください。この部分は Gradle も Maven の仕様に従っていますので同じです。
[^servlet]: デフォルトの場合。Tomcat の他、Jetty, Undertow, Netty を選択できます。 ref: [Spring Boot Reference Documentation > 8.Web](https://docs.spring.io/spring-boot/docs/2.6.1/reference/htmlsingle/#web)
[^security]: 本文章執筆時点で、 Spring Security だけリファレンスの URL 命名規則が変わっています。もしかすると他の Spring プロジェクトもこの規則に追随するかもしれません。ちなみに古いバージョンの Spring Security リファレンスの URL は https://docs.spring.io/spring-security/site/docs/5.5.3/reference/html5/ などです。
