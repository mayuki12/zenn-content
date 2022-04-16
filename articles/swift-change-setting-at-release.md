---
title: "Swift でアプリのリリース時だけ設定値を変える"
emoji: "🍵"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: [swift, xcode]
published: true
---

何番煎じか分からない小ネタの備忘録です。

### 経緯

Admob の広告を表示する際には【広告ユニット ID】を設定する事になるかと思います。
広告ユニットIDを開発中に利用するのはダメなので、デモ用のIDを利用する事になりますが、
リリース直前だけパラメータを変えるのは面倒なので、勝手に置き換えるようにしたくなりますね。


### 対応方法

Xcode Project の Build Setting で Active compilation conditions の設定を利用します。デフォルトでは下記のようになっているかと思います。

![](/images/swift-change-setting-at-release/xcode-debug-setting.png)

上記の設定の場合は Swift のコード上で下記のように条件分岐させれば良いようです。

``` swift: adview.swift
#if DEBUG
    static let id = "demo-adunit-id"
#else
    static let id = "release-adunit-id"
#endif
```

私はアプリのリリース時のみ変えるということを明示的にしたいので、下記のように設定しています。

![](/images/swift-change-setting-at-release/xcode-release-setting.png)

``` swift: adview.swift
#if RELEASE
    static let id = "release-adunit-id"
#else
    static let id = "demo-adunit-id"
#endif
```

こういう設定は環境変数で使い分けをしたいのですが、ビルド時にスクリプト実行するなど面倒なことしないといけないので、この方法を使ってます。
