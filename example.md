---
theme: ./
---

<img src="/title.png">

toyama satoshi / @toyamarinyon

---

# 自己紹介

- 🧑‍💻 **私** - フリーランスのエンジニア

- 📰 **最近の仕事** - ROUTE06 という会社で複数のマイクロサービスや SaaS を組み合わせたプロダクト開発に参加している

<img src="/route06.png" class="h-64 ml-32 mb-2" />

---

# 登壇の背景

## End-to-End テストをたくさん書いた

- 👍 テストが通ると、自信を持ってリリースできるようになった
- 👍 依存しているサービスの仕様変更にテストが失敗したことで気付けた
- 🤔 遅い、不安定、信頼できなくなっていった

<br>

## 依存しているサービスをモックにした

- 👍 速く、安定したテストになった
- 🤔 依存しているサービスの仕様変更に気付けない不安が残っている

<br>

## いい塩梅はないものか色々調べてみた

- O'Reilly の「マイクロサービスアーキテクチャ」に"コンシューマ駆動テスト"というテスト手法と、それを実現する"Pact"というツールが紹介されていた
- 良さそう

---

# 登壇の背景

- 利用事例はそれなりにあった

<div class="grid grid-cols-3 gap-4">
<div>

<img src="/pact_cookpad.png" />

</div>
<div>

<img src="/pact_freee.png" />

</div>

<div>

<img src="/pact_ubie.png" />

</div>
</div>

- もしかすると私と同じような人もいるかもしれないので、紹介します

---

# 10分後のゴール

- コンシューマ駆動テストが解決する課題や、その実現方法の大枠をイメージいただき、使ってみたいなと思ってもらえれば幸いです

---

# デモ

- デモ用の EC サイト [Hello Pact Store](https://store.sat0shi.dev/) を作りました
- 子ども向けのヘアゴムやピンを売っている EC サイトのイメージ
- アクセスすると、api を実行して、db にある商品をブラウザに表示します
- 商品名を日本語と英語で表示できます

<img src="/flow.png" />

---

# テストしたいこと

- 英語サイトにアクセスしたら、英語の商品が表示される
- 日本語サイトにアクセスしたら、日本語の商品が表示される

<br>

# テスト方法

上記のテストを 3 つの方法で試してみて、メリットデメリット比較します

- End to End
- モック
- コンシューマ駆動テスト・Pact

<br>

# 自分で動かしたい ✋

- 以降のデモは、こちらのレポジトリを Clone してお手元で再現できます

---

# End to End

ローカルに全てのサービスを立ち上げてテスト

- 👍 成功すると信頼できる
- 🤔 遅い、不安定
- 🤔 複雑なサービスを全てローカルで立ち上げられないこともある

<img src="/e2e.png" />

---

# モック

API サービスをモックにして Web サーバーだけ立ち上げてテスト、モックは Cypress の intercept を使って実装

- 👍 速い、安定
- 🤔 API サービスの仕様変更に気づかずにリリースする可能性がある
  <img src="/mock.png" />

---

# コンシューマ駆動テスト

コンシューマ駆動テストは、 Contract（契約） と呼ばれるモックを使って、サービスを Consumer(クライアント) と Provider(サーバー) に分離してテストする手法です。

- デモでは Consumer が Webサービスになり、Provider が APIサービスになります。

先ほどの cy.intercept を使ったモックとの違いは、順を追って説明します。

<img src="/pact_1.png" class="h-64" />

---

# コンシューマ駆動テスト - ステップ 1. Consumer

まずは Consumer (Webサービス) からテストします

- Consumer のテストでは、 Provider に期待する挙動を モック として定義します

> - /api/products?query=en に GET リクエスト送ったら、products フィールドに配列で英語の商品情報が入ったjsonが返ってくる
> - /api/products?query=jp に GET リクエスト送ったら、products フィールドに配列で日本語の商品情報が入ったjsonが返ってくる

- テストを実行すると モック の呼び出し結果がファイルに出力されます

<img src="/pact_2.png" class="h-99" />

---

# コンシューマ駆動テスト - ステップ 2. Provider

次に Provider (APIサービス) をテストします

- Provider のテストでは、先ほど出力されたファイルから Consumerのモックを作成し、Consumer が期待する通りの動作をするかをテストします

> - /api/products?query=en に GET リクエスト送ったら、products フィールドに配列で英語の商品情報が入ったjsonが返ってくる
> - /api/products?query=jp に GET リクエスト送ったら、products フィールドに配列で日本語の商品情報が入ったjsonが返ってくる

- 期待通りに動作すればテストが通ります

<img src="/pact_3.png" class="h-56" />

---

# コンシューマ駆動テスト - ステップ 3. Provider 変更

- 商品名のフィールドを name から title に変更することになった
- Consumer のテストは通る
- Provider のテストは失敗する 👍
- 移行計画を立てて再度実施


---

# 未来の話

- いろいろな事情でサービスがたくさんできた
- End to End テストは現実的ではない
- 注文サービスについて考えてみる

<img src="/feature.png" class="h-80" />

---

# 注文サービス

- Web サービスと admin サービスがConsumer
- 在庫管理サービスがProvider

<img src="/order.png" />


---

# 複数のマイクロサービスや SaaS を組み合わせたプロダクトを上手にテストする方法

- End-to-End テストは、成功すると信頼できるが、時間がかかる。よく壊れる
- モックを使ったテストは、早いけど、どこまで信頼していいか悩む

---

# Navigation

Hover on the bottom-left corner to see the navigation's controls panel

### Keyboard Shortcuts

|                                                      |                             |
| ---------------------------------------------------- | --------------------------- |
| <kbd>space</kbd> / <kbd>tab</kbd> / <kbd>right</kbd> | next animation or slide     |
| <kbd>left</kbd> / <kbd>shift</kbd><kbd>space</kbd>   | previous animation or slide |
| <kbd>up</kbd>                                        | previous slide              |
| <kbd>down</kbd>                                      | next slide                  |

---

layout: image-right
image: 'https://source.unsplash.com/collection/94734566/1920x1080'

---

# Code

Use code snippets and get the highlighting directly!

```ts
interface User {
  id: number;
  firstName: string;
  lastName: string;
  role: string;
}

function updateUser(id: number, update: Partial<User>) {
  const user = getUser(id);
  const newUser = { ...user, ...update };
  saveUser(id, newUser);
}
```

---

layout: center
class: "text-center"

---

# Learn More

[Documentations](https://sli.dev) / [GitHub Repo](https://github.com/slidevjs/slidev)
