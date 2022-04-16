---
title: "Fastlane の scan で iOS Simulator のリージョンや言語指定を出来るようにする"
emoji: "🍵"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: [swift, xcode]
published: true
---

GitHub Actions で iOS アプリのテストを回そうとして、言語周りでハマった時の備忘録です。

### 経緯

個人で作ってる iOS アプリで CI で継続的にテストしたいという事で設定をしようとしたら、日本向けのアプリなのにタイムゾーンが UTC になってしまったり、言語設定が英語となってしまって、CI 上でだけテストが大量に失敗するという事に陥りました。解決するのに色々と設定を試したことをまとめておきます。

### 発生原因

普段開発に利用している Mac で Xcode を利用してテストをした時は、日本時間で日本語表示にしていました。これは、Mac 上の iOS Simulator の設定(一般 → 言語と地域)で使用言語が日本語で、地域が日本としていたためです。

![](/images/e3dcddcc04e2a9/ios-simulator.png)

Xcode で XCTest や XCUITest を実行する時には Schemes の中で下記のように設定されてます。そのため、iOS Simulator で設定している言語と地域が利用されます。そして、CI環境ではその部分の設定していないので、デフォルト設定の英語圏の設定が利用されてしまいます。

App Language: System Language
App Region: System Region  


![](/images/e3dcddcc04e2a9/xcode-scheme.png)

また、特に何も設定をしない場合には GitHub Actions 上では Timezone が GMT となります。下記のように DateFormatter で Locale を `.current` で指定している場合には、Date の表示の時に GMT の時間で表示されてしまう事で、日本時間から９時間ずれてしまいます。

```
let dateFormatter = DateFormatter()
dateFormatter.locale = .current
```

### 解決方法

上述の Schemes 設定で日本仕様にすればよいみたいです。

![](/images/e3dcddcc04e2a9/xcode-scheme-japan.png)

Timezone を変更するには 環境変数 TZ を指定すればよいようです。
環境変数は Schemes の Arguments の Environment Variables で変更できます。

![](/images/e3dcddcc04e2a9/xcode-scheme-environment.png)

また、Test Plan 形式で設定を管理するようにした場合は、Configurations から設定を変えられます。多言語でのテストをするときは Schemes を言語後とに複数作成する必要がなくなるので、Test Plan 形式の方が便利そうですね。

![](/images/e3dcddcc04e2a9/xctestplan.png)
