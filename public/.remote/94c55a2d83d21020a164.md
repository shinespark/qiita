---
title: リモート会議中、家族に「入ってこないで！」を伝えるPhilips Hue + macOSアプリをつくったぞ!!
tags:
  - macOS
  - Swift
  - PhilipsHue
  - 個人開発
  - ClaudeCode
private: false
updated_at: '2026-07-10T11:34:18+09:00'
id: 94c55a2d83d21020a164
organization_url_name: kddiagile
slide: false
ignorePublish: false
posting_campaign_uuid: 783b7a849caf11eefd91
agreed_posting_campaign_term: true
---

皆さんこんにちは。
出社用に [Moonlander](https://www.zsa.io/moonlander) をもう1枚買ったのですが、思ってたよりかさばってしまうため、持ち運びしやすい分割格子配列キーボードを探している [shinespark（yuzu）](https://qiita.com/shinespark) です。

今日は**リモート会議中に家族がうっかり部屋に入ってきてしまうことを防ぐためのアプリ**をご紹介します。

## こんなことってありませんか？

- リモート会議中に、家族が部屋に入ってきて、画面に映り込みそうになる
- リモート会議中に、子どもが帰宅して、帰宅直後のテンションの高い声がマイクに入ってしまう
- 「会議中は気をつけて」と伝えてはいるものの、**いつも骨伝導イヤホンを付けっぱなし**。家族からは会議中かどうかが分からない（シュレディンガーの猫状態）

<img width="200" src="https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/94332/c9fa8cdd-b26e-4681-9c08-97d5dd501bbc.png">

いわゆる **ON AIRライト** が欲しいパターンです。

これまで私自身は困っていなかったのですが、最近社外の方とのミーティングも増えてきたため、自分でもライトを設置したいなと思うようになりました。

## 要件

自分なりの要件を整理してみました。

- バッテリー管理コストが低い
- 自席からワイヤレスで更新できる
- かさばらない
- 点灯と実態のズレ（点いているけど会議中ではない・点いていないけど会議中）が極力少ない

順にみていきます。

<img width="400" src="https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/94332/934ba1c0-922d-4a52-a4ef-b5dd7ed9a52d.png">

### バッテリー管理コストが低い

まずは、市販のON AIRライトの購入を検討したのですが、USB給電式や乾電池式が主でした。
ドア付近に設置するユースケースを考えると、USB給電式は**ケーブルが煩雑**になるのでできる限り避けたいです。

とはいっても、乾電池式やモバイルバッテリーを接続して使うタイプは、電池交換や充電の手間がかかるので、運用コストが悩みどころです。
知らず知らずのうちに**バッテリーが切れてた！** というパターンも避けたいため、できる限りバッテリー管理が不要なものが望ましいです。

### 自席からワイヤレスで更新できる

また、リモート会議中であるかどうかは、都度ドア付近に移動して切り替えたりすることは手間なので、ワイヤレスで更新できることが望ましいです。
リモート会議がはじまってから、「あ、点けてなかった」と、**ドア付近まで行ってライトを点ける**ことがないようにしたいです。

### かさばらない

かさばらないという点も重要です。
自室のドア付近、高所などに設置することを考えると、**落下や破損のリスク**があるため、できる限り小さいものが望ましいです。

DIYで設置スペースを頑張って作るという手もありますが、現在進行形で毎日会議が行われているため、できればDIYせずなるはやで設置完了したいです。

### 点灯と実態のズレが極力少ない

そして、最も重要なのは、点灯と実態のズレが極力少ないことです。
実態との齟齬があると、結局この**システムの信頼性が損なわれます**。
うっかりON AIRライトが点きっぱなしになってたことで、**晩ごはんに呼ばれない**などのインシデントが発生するリスクもあります。

## 方針: Philips Hueを使うことにした

**E-Inkの値札** をドアに貼ることも検討しました。
が、調べた限りでは、スマホアプリ経由でのタッチで更新するタイプが多く、PC経由でワイヤレスで更新可能なものは個人向けで入手することが難しそう or 高価なため、断念しました。

悩んだ結果、**Philips Hue**を使うことにしました。

<img width="400" src="https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/94332/cd6f62a2-a58b-4beb-8e11-6ec4a9654ba6.jpeg">

ゲーム用に既にPhilips Hueのライトを複数持っており、Hue Bridge経由でPCやゲーム機からワイヤレスでライトの色を変えることができます。

- Philips Hueのライトが自宅照明として設置済み
  - バッテリー管理不要
  - かさばらない
- API経由でライトを操作可能
  - ワイヤレスで更新可能
  - 点灯と実態のズレが少ない

おお、良さそうです。
ということで、Philips Hueを使ったON AIRライトをmacOSアプリとして開発することにしました。

## Huemdall: カメラ使用を検知してPhilips Hueを光らせるmacOSアプリ

できたものがこちらです。

![logo.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/94332/df172ee8-7bc8-4265-a5b0-16137a096bd5.png)
[shinespark/huemdall - GitHub](https://github.com/shinespark/huemdall)

**Huemdall**は、Macのカメラ利用を検知して、Philips Hueのライトを「ON AIR色」(デフォルトは赤)に変えるメニューバー常駐アプリです。

カメラの利用が終わると、ライトを元の状態に復元します。

「カメラを使っているかどうか」だけを見ているので、Zoom, Google Meet, Teams など、アプリを問わず動作します。会議アプリごとの連携設定は一切不要です。

名前の由来は **Hue（ヒュー） + Heimdall（ヘイムダル）**。
Heimdallは、北欧神話の**光の神**で、アースガルズの**見張り番**らしいです。**ピッタリ！**[^1]

[^1]: 開発当初はアイコンのイメージから「Gigantes」という別の名前でしたが、まんま過ぎたので名前を変えることにしました。

### インストール

[Releases](https://github.com/shinespark/huemdall/releases) から最新の `Huemdall-<version>.zip` をダウンロードして、`Huemdall.app` をアプリケーションフォルダに入れるだけです。

### 使い方

使い方はカンタンです。
アプリを起動して、メニューバーのアイコンから「セットアップ...」を開き、
「ブリッジを検索...」からHue Bridgeへ接続します。
![SCR-20260710-ecot.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/94332/5969db1d-0c17-48df-ad17-1c9698fdc36f.png)
![SCR-20260710-ectz.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/94332/dda0f8cc-c75c-420d-9bcc-d3bb7522f2d7.png)

Hue Bridgeとのペアリングが完了したら、ON AIR時に色を変えたいライトを選んで、ON AIRの色を選ぶだけです。
（シーンで設定することもできます。）

![SCR-20260710-ednq.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/94332/e635e05f-2d99-4367-a0f5-b2bc938c8ab8.png)
![SCR-20260710-edww.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/94332/1aa79433-8bb1-4ad2-9960-5d0b7319d3d9.png)

後はカメラを使い始めると、自動でON AIR色に変わります。

https://x.com/shnsprk/status/2075288374597029965?s=20

カメラを使い終わると、ライトは元の状態に戻ります。

任意のキーボードショートカットからON AIRにすることもできます。

![SCR-20260710-eeab.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/94332/10315fba-fc66-461a-a172-a4edbcc8595e.png)

もちろん、メニューバーのアイコンから手動でON AIRにすることもできます。

![SCR-20260710-efkk.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/94332/c85b1dad-8e20-44f2-aee6-4ad222fe5fd4.png)

ON AIR中かどうかでメニューバーのアプリアイコンが変化するので、自席から離れずON/OFF制御することができます。

### その他の実例

Hue ライトはいくつか持っているので、実際に試してみました。

https://x.com/shnsprk/status/2075396299843907849?s=20

https://x.com/shnsprk/status/2075401783892218008?s=20

https://x.com/shnsprk/status/2075401904973307980?s=20

https://x.com/shnsprk/status/2075402032912236696?s=20

しばらくは室内照明の1つの色を変化させる設定で運用してみる予定です。

## カメラの検知技術やアーキテクチャ

ここからは技術の話です。

### カメラの使用中を検知する仕組み

カメラの使用中の検知には、macOSのCoreMediaIOのデバイスプロパティ `kCMIODevicePropertyDeviceIsRunningSomewhere` を使っています。
これは `いずれかのプロセスがこのデバイスを使用中か` をBoolで返してくれるプロパティです。
デバイスが使用中かどうかしか返さないため、カメラ許可は不要で、実際の映像フレームには一切触れることはありません。

```swift
private func isRunningSomewhere(_ device: CMIOObjectID) -> Bool {
    var address = Self.propertyAddress(
        CMIOObjectPropertySelector(kCMIODevicePropertyDeviceIsRunningSomewhere))
    guard CMIOObjectHasProperty(device, &address) else { return false }
    var value: UInt32 = 0
    // ...CMIOObjectGetPropertyData で value にUInt32を読むだけ
    return value != 0
}
```

監視はポーリングではなく、`CMIOObjectAddPropertyListenerBlock` によるイベント駆動です。変化した瞬間だけ通知を受け取るため、CPU負荷は少ないです。
外付けのWebカメラなどもあるため、すべてのカメラデバイスのORを取って「いずれかがrunning」ならONと判定しています。

### 見送り: マイクの使用中の検知

ちなみに「マイク検知」も検討しましたが、**見送りました**。

リモート会議中に突然家族に入って来られると困るパターンとしては、社外との会議、面談、面接などが考えられます。
私の主観ですが、カメラとマイクの使用中では、カメラの使用中の方が入って来て困るパターンと連動しています。

また、マイクの検知を考慮した場合には、以下のような考慮も必要になります。

- OS側ではなく、マイクデバイス側のミュート機能の考慮
- 音声入力に使われるパターン
- カメラとマイクのどちらを優先してON AIRと判断するか

考慮すべきポイントが格段に増えてしまうため、今回は見送ることとしました。

### Claude Code Fable 5 で 5日 で開発

今回はちょうどFable 5が使えるタイミングだったので、大いに活用しました。
Proプランを契約しているのですが、システム自体はシンプルだったため、特に使用制限に引っかかることなく完成まで漕ぎ着けられました。

Hue API v2 の話も書きたいのですが、長くなってきたので別の機会にします。

## 〆

ということで、本記事ではカメラ使用を検知してPhilips Hueを光らせるmacOSアプリ [shinespark/huemdall - GitHub](https://github.com/shinespark/huemdall) と、macOSでのカメラの検知方法の仕組みについて紹介させていただきました。

Philips Hue はゲームとの相性がよいので、ゲームの没入感を高めたり、スケジュール機能を利用して保育園のお迎えの時間に照明の色を変えてリマインドしたり[^2]など、便利に使っています。

[^2]: 今はお迎えが不要になったため、業務時間の定時時刻に色を変える程度です。

https://x.com/shnsprk/status/1931110330626908263?s=20

ちなみに、Hue は一般的なE26口金の照明であればそのまま使えるので、興味があればぜひ買って試してみてください。
Huemdallを使ってみて何かあれば、IssueやPRを頂けると嬉しいです。

それでは良いリモートワークライフを！
