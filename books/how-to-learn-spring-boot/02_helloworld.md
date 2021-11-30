---
title: "Hello, world!"
---

# Guides ページについて

Spring Boot で Hello, world! を実装してみましょう。
前述の通り、 [Windows10 に Java/Spring Boot 開発環境をセットアップする](https://zenn.dev/yukihane/articles/fb52d049da587c) の手順でセットアップした環境で行います。

さて、Spring Boot の公式サイトで "getting started" 的なものはどこにあるかというと、 [Guides](https://spring.io/guides) のページです。
これらのうち、今回は [Building a RESTful Web Service](https://spring.io/guides/gs/rest-service/) を見ていきます。
ちなみに、このページの URL は

- https://spring.io/guides/gs/rest-service/

ですが、ドメインを変更して

- https://spring.pleiades.io/guides/gs/rest-service/

にアクセスすると日本語訳サイト[^japanese]で見ることもできます。

[^japanese]: [柏原 真二 (cypher256)さん](https://spring.pleiades.io/)が運営されているサイトで、おそらく非公式です。

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

# ゼロからコード作成する

## Spring Initializr

前節で 3 つの経路を説明しましたが、ここではそのうちのひとつめ、ゼロからコードを作成していきます。
(残りの 2 つもこの経路の途上にありますので、本節の説明で全て賄っています。)
ガイドページの説明の "Starting with Spring Initializr" 節以降を見ていくことになります。

Spring Initializr とは https://start.spring.io/ のことで、このページに必要事項を入力していくことで、Spring Boot プロジェクトのひな形が作成できるようになっています[^initializr]。

[^initializr]: なお、同機能は [セットアップ](https://zenn.dev/yukihane/articles/fb52d049da587c)を済ませた IDE からや [Spring Boot CLI](https://docs.spring.io/spring-boot/docs/current/reference/html/cli.html) というコマンドラインツールからも実行できます。

各項目を見ていきましょう。
