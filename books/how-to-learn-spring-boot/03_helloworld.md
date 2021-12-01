---
title: "Hello, world! を実装する"
---

本章では、前章で説明した [Guides: Building a RESTful Web Service](https://spring.io/guides/gs/rest-service/) に沿って最初の Spring Boot アプリケーションを実装してみます。

"**はじめに**" 章に記載した通り、 [Windows10 に Java/Spring Boot 開発環境をセットアップする](https://zenn.dev/yukihane/articles/fb52d049da587c) の手順でセットアップした環境で行います。

# Spring Initializr でひな形を作成する / Starting with Spring Initializr

ウェブブラウザで https://start.spring.io/ を開き、次の通り設定します:

| 設定項目名   | 設定値                                                                           | 備考                                                                                      |
| ------------ | -------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------- |
| Project      | `Maven`                                                                          |                                                                                           |
| Language     | `Java`                                                                           |                                                                                           |
| Spring Boot  | `2.6.1`                                                                          | 最新安定版を選択します                                                                    |
| Group        | `com.example`                                                                    | デフォルト値                                                                              |
| Artifact     | `rest-service`                                                                   |                                                                                           |
| Name         | `rest-service`                                                                   | デフォルト値                                                                              |
| Description  | `Demo project for Spring Boot`                                                   | デフォルト値                                                                              |
| Package name | `com.example.restservice`                                                        | 今回の場合デフォルト値をそのまま採用するとコンパイルエラーになるので注意(詳細後述)        |
| Packaging    | `Jar`                                                                            |                                                                                           |
| Java         | `17`                                                                             |                                                                                           |
| Dependencies | `Spring Boot DevTools`, `Lombok`, `Spring Configuration Processor`, `Spring Web` | 原文では `Spring Web` だけですが、前章で説明した通り、他の 3 つは常に設定しておきましょう |

**Package name** は、デフォルトでは `groupId` と `artifactId` を連結したものになります。
今回は `artifactId` にハイフン "`-`" が入っていますのでデフォルト値をそのまま採用するとパッケージ名デフォルト値にもハイフンが含まれてしまいます。
しかし、Java の仕様上パッケージ名にハイフンは利用できません[^java-letters]。
つまりデフォルト値は採用できません。

上記の設定が完了したら、画面下部の **GENERATE** ボタンを押してひな形生成・ダウンロードします。

[^java-letters]: ref: [The Java® Language Specification Java SE 17 Edition: 7.4.1.Named Packages](https://docs.oracle.com/javase/specs/jls/se17/html/jls-7.html#jls-7.4.1)

# プロジェクトひな形を IDE にインポートする

次の手順を実行します:

1. `ConEmu` を起動し、次のコマンドを実行し、ダウンロードしたひな形の展開とバージョン管理を行います:
   ```
   cd ~/Documents/repos/
   unzip ~/Downloads/rest-service.zip
   cd rest-service/
   git init
   git commit --allow-empty -m init
   git add .
   git commit -m 'auto-generated files'
   ```
1. `Spring Tools 4 for Eclipse` を起動します。
1. **File > Import** メニューを選択します。
1. **Maven > Existing Maven Project** を選択します。
1. 先ほど展開したひな形ディレクトリ(例: `C:\Users\yuki\Documents\repos\rest-service`)を選択します

上記の操作が完了すると STS 上にプロジェクトがインポートされます。
初回は関連ライブラリやソースコードのダウンロードも行われますので少し時間がかかります。

# 実装する

## リソース表現クラスを作成する / Create a Resource Representation Class

クライアントから要求されたリソースを表現するクラスを定義します。

1. `Package Explorer` 上の `com.example.restservice` パッケージを右クリックし、 **New > Class** を選択します。
   ![クラス作成](https://storage.googleapis.com/zenn-user-upload/ca543112fc91-20211201.png)
1. **Name** に `Greeting` と入力し **Finish** ボタンを押して `Greeting` クラスを作成します。
1. 作成された `Greeting.java` には次のように書きます:

   ```java
   package com.example.restservice;

   import lombok.Data;

   @Data
   public class Greeting {
       private final long id;
       private final String content;
   }
   ```

### 補足説明

#### Lombok の効能

上記で作成したクラスは、原文の `Greeting` クラスと同一です。
参考に原文の `Greeting` クラス実装を示します:

```java
package com.example.restservice;

public class Greeting {

	private final long id;
	private final String content;

	public Greeting(long id, String content) {
		this.id = id;
		this.content = content;
	}

	public long getId() {
		return id;
	}

	public String getContent() {
		return content;
	}
}
```

なぜこの 2 つの実装が同一になるかというと、Lombok の [`@Data` アノテーション](https://projectlombok.org/features/Data) によって、次のコードが自動で生成されるためです:

- 初期化されていない `final` フィールドを引数にとるコンストラクタの生成([`@RequiredArgsConstructor`](https://projectlombok.org/features/constructor))
- フィールドに対応する getter メソッドの生成([`@Getter`](https://projectlombok.org/features/GetterSetter))

今回の例のように、 Lombok を活用すればコードを簡潔に書くことが可能になりますので積極的に利用していきましょう[^record]。

[^record]: 今回の実装例に限って言うと、 Java16 で正式導入された `record` を用いることで Lombok を利用した場合と同じくらいに簡潔に記述することができます。参考: [Spring MVC で Java17 record を試してみる](https://yukihane.github.io/blog/202110/18/java17-record-on-spring-mvc/)

## リソースコントローラを作成する / Create a Resource Controller

前節と同様の手順で新しいクラス `GreetingController` を作成し、次の通り実装します:

```java
package com.example.restservice;

import java.util.concurrent.atomic.AtomicLong;

import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestParam;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class GreetingController {

    private static final String template = "Hello, %s!";
    private final AtomicLong counter = new AtomicLong();

    @GetMapping("/greeting")
    public Greeting greeting(@RequestParam(value = "name", defaultValue = "World") final String name) {
        return new Greeting(counter.incrementAndGet(), String.format(template, name));
    }
}
```

この `GreetingController` 実装は原文と同じです。

この実装は次のような処理を行いますが、 Spring について詳しく知らなくても、何となくそうだろうなあ、ということはわかるかなと思います。

- `/greeting` パスのリクエストをハンドルする実装を行っていて、このパスは `name` というパラメータを受け取ります。
- レスポンスにカウンターの数値と文字列を要素に持つ `Greeting` を返します。

### 補足説明

#### コントローラクラスについて知るためのドキュメント

`@RestController` や `@GetMapping` は(パッケージ名 `org.springframework.web...` から推測できる通り) Spring MVC が提供するアノテーションです。
したがって、コントローラクラスに関して参照すべきドキュメントは、前章で記載した通り(Spring Boot リファレンスでなく) Spring Framework Documentation: Web Servlet であり、 [1.3.Annotated Controllers](https://docs.spring.io/spring-framework/docs/current/reference/html/web.html#mvc-controller) 節に説明があります。

また、 Javadoc やソースコードを参照することで新しい情報が得られることもあります。
例えば `@RestController` の [Javadoc](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/web/bind/annotation/RestController.html) や[ソースコード](https://github.com/spring-projects/spring-framework/blob/main/spring-web/src/main/java/org/springframework/web/bind/annotation/RestController.java)を見ると、 `@RestController` をクラスに付与するのは `@Controller` と `@ResponseBody` の両方を付与することと同じ意味だ、ということがわかります。

なお、Javadoc や ソースコードは IDE 上でも確認できます。 STS(Eclipse) では `F3` キー、あるいは該当型を `Ctrl` を押しながらクリックすることで定義箇所にジャンプすることができます。
この機能と Javadoc ビューで 整形された Javadoc を参照できます。
Javadoc ビューなどの "ビュー" は、 **Window > Show View** メニューから開くことができます。

![Eclipse の Javadoc ビュー](https://storage.googleapis.com/zenn-user-upload/adcad447b5b3-20211201.png)

### `@RestController`

前述の通り、 `@RestController` は `@Controller` と `@ResponseBody` を両方指定する代わりのショートハンド(省略記法)です。
では `@Controller` と `@ResponseBody` とは何でしょうか。
それも Javadoc に説明があります。

- [`@Controller`](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/stereotype/Controller.html)
- [`@ResponseBody`](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/web/bind/annotation/ResponseBody.html)

リンク先を読むと、取り敢えず `@Controller` については次のようなことがわかります。

- `@Controller` は、このクラスが"コントローラ"の役割を担うコンポーネント(`@Component`)であることを示すために用います。

  - パッケージ名に含まれる `stereotype`[^stereotype] からも推測できますが、 `@Component` アノテーション(これ自体については後述します)と機能的な差は無く、分類のためのアノテーションです。

`@ResponseBody` についてはいまいちよくわかりません。

そこで、またリファレンスで説明されている箇所を調べることになります。

[Spring Framework Documentation: Web Servlet](https://docs.spring.io/spring-framework/docs/current/reference/html/web.html#spring-web) 内を "`@ResponseBody`" で検索すると [**1.3.3. Handler Methods > Return Values**](https://docs.spring.io/spring-framework/docs/current/reference/html/web.html#mvc-ann-return-types) そして [`@ResponseBody`](https://docs.spring.io/spring-framework/docs/current/reference/html/web.html#mvc-ann-responsebody) に所望の説明があることがわかります。
この箇所の説明で次のことがわかります:

- `@ResponseBody` を付与したメソッドは、 `HttpMessageConverter`(この型自体の説明は後述) を介してシリアライズしたレスポンスボディを返します。

では逆に `@ResponseBody` を付与しなければどうなるか、については前出の [**Return Values**](https://docs.spring.io/spring-framework/docs/current/reference/html/web.html#mvc-ann-return-types) 節を読めばわかりますが、戻り値の型によって振る舞いが異なります。(今回の話題から逸れるので詳細は割愛します)

[^stereotype]: 一般に流通している "[ステレオタイプ](https://ja.wikipedia.org/wiki/%E3%82%B9%E3%83%86%E3%83%AC%E3%82%AA%E3%82%BF%E3%82%A4%E3%83%97)" とはニュアンスが違います。 UML 用語のステレオタイプです(参考: [英語 Wikipedia](<https://en.wikipedia.org/wiki/Stereotype_(UML)>), [Google 検索結果](https://www.google.com/search?q=UML+%E3%82%B9%E3%83%86%E3%83%AC%E3%82%AA%E3%82%BF%E3%82%A4%E3%83%97))。

### 今回実装したコントローラクラスのまずい点

通常、コントローラクラスはステートレスになるよう(状態を持たないよう)実装します。
しかし、今回のコントローラはリクエスト回数を保持するフィールド(`counter`)を持っており、ステートフルです。
このような実装は、次のような問題を引き起こします:

- インメモリで状態を管理しているため、再起動などで状態がリセットされてしまいます。
- コントローラクラスは(デフォルトでは)すべてのリクエストを 1 つのインスタンスで処理します。そのためスレッドセーフティについて考慮する必要が出ます。
  - 今回、単純に `long` 型で回数を保持せず `AtomicLong` を利用しているのはスレッドセーフティ確保のためです。
- アプリケーションサーバをクラスタ化した場合、状態を共有できない。

サンプルコードだから許される実装であり、通常はこのような実装は行いません。
