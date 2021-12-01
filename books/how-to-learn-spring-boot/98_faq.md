---
title: "FAQ"
---

# Maven と Gradle のどちらを選択すべき?

Maven より Gradle の方が後発です。が、必ずしも Gradle の方が優れていてこちらを選択すべきだ、というわけではありません。
Gradle が出始めたころは Maven のように XML で設定を書くのは時代遅れだ、みたいな風潮もあり Gradle が持て囃されていましたが、その勢いのまま Maven を駆逐するには至らず、 Gradle は Gradle なりの欠点もあるよね、ということで現状 Java では主要ビルドシステムは 2 つ並立している状態です。

Spring Boot を含む Spring プロジェクトでは比較的最近[^port-to-gradle] Maven から Gradle に移行しましたが、 [WildFly プロジェクト](https://github.com/wildfly/wildfly)のように Maven を使い続けているプロジェクトもあります。

個人的には、

- Eclipse(Spring Tools 4 for Eclipse) の Gradle サポートは Maven に比べて不完全である[^buildship]
- Gradle は下位互換性を破壊するバージョンアップがたまにあるので Gradle 自身のバージョンを意識する必要がある
- 昔から Maven を使い続けているが、Gradle に移行する動機が特に無い

という理由で Maven を主に利用しています。

ただ、業務で Spring Boot アプリケーション開発を行う場合、先行プロジェクトに合わせましょう、みたいな話になると思いますので、自分の好みを反映させる機会はほぼ無いと思います。
ですので、最終的にはどちらも理解して使えるようになる必要はあるかと考えます。

[^port-to-gradle]: Spring Boot は `2020-01` リリースの `2.3.0.M1` より。 ref: [Port the build to Gradle #19608](https://github.com/spring-projects/spring-boot/issues/19608)
[^buildship]: Eclipse は Buildship という機能で Gradle プロジェクトを扱いますが、現時点では Annotation Processor 設定が Gradle と Eclipse で同期できません(ref: [Annotation Processor configuration from Gradle Eclipse Plugin #329](https://github.com/eclipse/buildship/issues/329))。このため、私が Gradle でプロジェクトを管理する必要が出たときは次のような手段を採用しています: [Gradle で Spring Boot プロジェクトを作成して STS(Eclipse)でインポートする手順](https://yukihane.github.io/blog/202007/26/spring-boot-gradle-eclipse/)
