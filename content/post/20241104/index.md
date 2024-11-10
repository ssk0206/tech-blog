---
title: SwiftのSendableとは何ですか？
description: SwiftのSendableは、簡単に言うと「安全にスレッド間でやりとりできる型」を示すプロトコルです。マルチスレッドプログラム、つまり複数のスレッドが同時に動作するプログラムで使うときに役立ちます。
slug: 20241104
date: 2024-11-04 00:00:00+0000
# image: cover.jpg
categories:
  - 備忘録
tags:
  - Swift
weight: 1 # You can add weight to some posts to override the default sorting (date descending)
---

Swift の Sendable は、簡単に言うと「安全にスレッド間でやりとりできる型」を示すプロトコルです。マルチスレッドプログラム、つまり複数のスレッドが同時に動作するプログラムで使うときに役立ちます。

## なぜ Sendable が必要なのか？

Swift は UI を扱うこともあり、マルチスレッドを強く意識しなければなりません。
マルチスレッドでのプログラミングでは、複数のスレッドが同時に同じデータにアクセスして操作することで、予期しないバグやエラーが発生することがあります。こういったバグを避けるために「このデータはスレッド間で安全に扱える」と明示する仕組みが必要です。これが Sendable の役割です。

## 具体的にはどういうこと？

Sendable を使うと、Swift が「この型はスレッド間で安全に送れる」と判断してくれます。たとえば、Int や String のような基本型はスレッド間で安全にやりとりできますが、複雑なデータ構造や、スレッドセーフでない型はそうとは限りません。Sendable を型に適用することで、その型がスレッドセーフかどうかをコンパイラがチェックしてくれるようになります。

Sendable は基本的にマーカープロトコルのため、それ自体が何かするということはありません。ただ、安全に送信できることを示すものです。

## Sendable の使い方

具体的には、独自の型を作成するときに、その型が Sendable を満たしているかを宣言できます。たとえば以下のように使います。

```swift
struct Data: Sendable {
    let value: Int
}
```

上記のコードでは、MyData が Sendable プロトコルに準拠しているため、この型のインスタンスはスレッド間で安全にやりとりできます。

Swift で struct が Sendable プロトコルに準拠するためには、以下の条件を満たす必要があります。

### Sendable に準拠している例

#### Sendable な struct の例

```swift
struct UserProfile: Sendable {
    let id: Int            // IntはSendable
    let name: String       // StringもSendable
    let isActive: Bool     // BoolもSendable
}
```

この UserProfile 構造体は、全てのプロパティが Sendable 準拠の型なので、Sendable に自動で準拠しています。

#### Sendable な class の例

```swift
final class UserSettings: Sendable {
    let theme: String         // StringはSendable
    let notificationsEnabled: Bool  // BoolもSendable

    init(theme: String, notificationsEnabled: Bool) {
        self.theme = theme
        self.notificationsEnabled = notificationsEnabled
    }
}
```

### Sendable ではない struct の例

```swift
struct NonSendableStruct {
    var id: Int
    var data: [String: Any]  // AnyはSendableではない
}
```

```swift
class NonSendableClass {
    var count: Int = 0      // varで可変プロパティ
    var closure: (() -> Void)?  // クロージャもSendableではない
    func increment() {
        count += 1
    }
}
```

この NonSendableClass は、Sendable に準拠できません。理由は以下のとおりです：

• count が var で宣言されているため、状態が変更される可能性があります。
• closure プロパティがクロージャ型で、これはスレッドセーフでない可能性があるためです。

### @unchecked Sendable を使った例

一部のプロパティがスレッドセーフでない場合や、スレッドセーフであることを自己責任で保証できる場合には、@unchecked Sendable を使うことができます。

#### @unchecked Sendable を使用した struct の例

```swift
struct MyStruct: @unchecked Sendable {
    var name: String
    var cache: [String: Any]   // AnyはSendableではないが、スレッドセーフを保証したい
}
```

ここで、cache プロパティに Any 型を使っていますが、@unchecked Sendable を使うことで Sendable として扱えるようになります。ただし、この場合はスレッドセーフを保証する責任がプログラマ側にあるため、慎重に設計する必要があります。

```swift
final class MyClass: @unchecked Sendable {
    var count: Int = 0
    var settings: [String: String] = [:]

    func updateCount() {
        count += 1
    }
}
```
