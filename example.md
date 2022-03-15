---
theme: ./
---

<img src="/title.png">

toyama satoshi / @toyamarinyon

---

# 自己紹介

- 🧑‍💻 **私** - フリーランスのエンジニア

- 📰 **最近の仕事** - ROUTE06 という会社で複数のマイクロサービスや SaaS を組み合わせたプロダクト開発に参加している

<div class="flex space-x-2">


<div>
<img src="/route06.png" class="h-64 shadow" />
<a href="https://speakerdeck.com/route06/zhu-shi-hui-she-route06-rutositukusu-hui-she-shao-jie" class="text-xs">会社紹介</a>

</div>

<div>
<img src="/route06_a.png" class="h-64 shadow" />
<a href="https://prtimes.jp/main/html/rd/p/000000006.000056964.html" class="text-xs">ROUTE06、三菱商事の部品調達に関するマーケットプレイスの立ち上げを支援 / PRTimes</a>
</div>

</div>

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

# ５分後のゴール 🥅


Pact を使ってコンシューマ駆動テストを書くことでどのような課題が解決できるかをざっくりとイメージいただき、機会があれば使ってみたいなと思ってもらえれば幸いです

---

# デモサービスを作って、コンシューマ駆動テストをやってみました

- デモ用の EC サイト [Hello Pact Store](https://store.sat0shi.dev/) を作りました
- 子ども向けのヘアゴムやピンを売っている EC サイトのイメージ
- アクセスすると、api を実行して、db にある商品をブラウザに表示します
- 商品名を日本語と英語で表示できます

<img src="/flow.png" class="h-64" />

---

# 自分で動かしたい ✋

- このデモは、こちらのレポジトリを Clone してお手元で再現できます
  <a href="https://github.com/toyamarinyon/sat0shi-store">https://github.com/toyamarinyon/sat0shi-store</a>

---

# デモサービスにコンシューマ駆動テストをやってみる

- コンシューマ駆動テストは、 Consumer (Web サービス) と Provider(API サービス) の間で Contract (契約) を結び、Contract に違反していないことを Consumer, Provider それぞれがテストします
- Contract (契約)の内容は、リクエストとレスポンスのセットです

> - /api/products?query=en に GET リクエスト送ったら、products フィールドに配列で英語の商品情報が入った json が返ってくる
> - /api/products?query=jp に GET リクエスト送ったら、products フィールドに配列で日本語の商品情報が入った json が返ってくる

<img src="/cdc_0.png" class="h-70" />

---

# 誰が Contract を作るのか

- Consumer (Web サービス) が作ります
- まず Provider のモックを作り、期待するリクエスト、レスポンスを定義してテストをします
- テストに成功すると Contract が作成されます
- 次に、Contract から Consumer のモックを作成し、Provider が Consumer の期待したレスポンスを返せるかをテストします
- Consumer から 契約を作ってテストするので、 コンシューマ駆動テストという名前になっています

<img src="/cdc_1.png" class="h-60" />

---

# Scheme は逆方向のイメージ

- Scheme は、Provider の仕様をもらって、Consumer がそれを満たすように実装する
- コンシューマ駆動テスト は、Consumer が実際に使っている API をファイルに書き出して、Provider が意図せずに壊すこと防ぐ

> Schemas are abstract, contracts are concrete. / Matt Fellows

<img src="/cdc_2.png" class="h-70" />

---

# Pact でコンシューマ駆動テストを書く - Consumer

<div class="flex">
<section>

では、実際にコンシューマ駆動のテストを書いてみます

コンシューマ駆動テストのためのツールはいくつかありますが、今回は対応言語が多く、開発も盛んな Pact を使います

</section>

<img src="/step1.png" class="h-65" />
</div>

---

# Pact でコンシューマ駆動テストを書く - Provider

<div class="flex">
<section>

次に Provider (API サービス) をテストします

- Provider のテストでは、先ほど作成したファイルから Consumer のモックを作成し、Consumer が期待する通りの動作をするかをテストします

</section>

<img src="/step2.png" class="h-65" />
</div>

---

# コンシューマ駆動テストの恩恵を感じる

- Provider が /api/products のレスポンスのフィールド名をうっかり変えてしまった
- Consumer のテストは通る
- Provider のテストは失敗する 👍
- こんなことは実際ないと思いますが、Provider に実施した変更の影響範囲を把握できておらず、本番リリース後に不具合が見つかった、という経験がある人は少ないないのかなと思います

---

# Pact, コンシューマ駆動テストが解決する課題

Consumer が使っている API を具体的に明示することで、Provider が意図せずに壊すこと防ぐ

---

# Contract の管理

実際に運用していく場合、Contract をレポジトリに含めるのはあまり現実的ではないですね

便利な SaaS [PACTFLOW](https://pactflow.io/) があります

---

# Learn More

この発表を作る中で見つけた良さそうな資料を紹介します

## 国内の事例

- [Cookpad](https://techlife.cookpad.com/entry/2016/06/28/164247)
- [freee](https://developers.freee.co.jp/entry/introduction-of-pact)
- [ubie](https://note.com/sys1yagi/n/n93a3d8f4a64a)

<br>

## コンシューマ駆動テスト・Pact についての説明

- [Consumer-driven Contract Testing (CDC)](https://microsoft.github.io/code-with-engineering-playbook/automated-testing/cdc-testing/)

  End to End テストの問題点とそれを コンシューマ駆動テストでどのように解決するか分かりやすく説明されていました

- [How Pact contract testing works](https://pactflow.io/how-pact-works/#slide-1)

  Pact の動きがアニメーションで説明されていて、イメージしやすいです

---

# Learn More

## もっと詳しく

PACTFLOW のブログに色々な記事があるのでおすすめです。2つ紹介します。

- [Schemas are not contracts](https://pactflow.io/blog/schemas-are-not-contracts/)

  スキーマは Contract の代わりにならないということについて説明されています。若干ポジショントークな気はしますが、理解できるところもありました。

- [Proving E2E tests are a Scam](https://pactflow.io/blog/proving-e2e-tests-are-a-scam/)

  マイクロサービスを End to End でテストするのが現実的でないことを丁寧に説明しています。