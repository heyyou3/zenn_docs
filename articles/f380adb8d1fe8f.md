---
title: "Hexagonal Architecture(ヘキサゴナルアーキテクチャ) とは"
emoji: "🔌"
type: "tech"
topics:
- "アーキテクチャ"
- "ヘキサゴナルアーキテクチャ"
- "ポートアンドアダプタ"
  published: true
  published_at: "2022-07-18 14:48"
---

:::message
以下の点に同意して読んでいただければと思います。

- 現在の筆者の理解度を整理する目的で書いた記事のため、間違った解釈があるかもしれません
    - ご指摘いただければと思います
- 概念的な話は解釈が分かれやすいです。なので、この記事だけではなく提唱者の記事も含めて読むことをおすすめします
  :::

# 本記事の要約

- ヘキサゴナルアーキテクチャは[MVC(S)](https://ja.wikipedia.org/wiki/Model_View_Controller#:~:text=Model%2DView%2DController%20(MVC,%E3%82%A2%E3%83%BC%E3%82%AD%E3%83%86%E3%82%AF%E3%83%81%E3%83%A3%E3%81%AE%E4%B8%80%E7%A8%AE%E3%81%A7%E3%81%82%E3%82%8B%E3%80%82)) や [レイヤードアーキテクチャ](https://ja.wikipedia.org/wiki/%E5%A4%9A%E5%B1%A4%E3%82%A2%E3%83%BC%E3%82%AD%E3%83%86%E3%82%AF%E3%83%81%E3%83%A3) が持つ課題を解決するために生まれた
- ヘキサゴナルアーキテクチャの目的
    - ユースケースを実現するためのロジックが完全に独立した状態で実行できるようにする
    - 外部技術を抽象化し、テスト容易性を高くする
- Driver, Driven, Application という境界を設けることで責務を分離できる

# Hexagonal Architecture について

![](https://storage.googleapis.com/zenn-user-upload/44c70f4f2bed-20220712.png)

ヘキサゴナルアーキテクチャは **Ports & Adapter(ポートアンドアダプタ)** とも呼ばれます。

## Hexagonal Architecture はなぜ生まれたのか
ヘキサゴナルアーキテクチャは 2005 年に [Alistair Cockburn](https://en.wikipedia.org/wiki/Alistair_Cockburn) 氏によって提唱されたソフトウェアアーキテクチャです。
ヘキサゴナルアーキテクチャが生まれる前の代表的なソフトウェアアーキテクチャといえば [MVC(S)](https://ja.wikipedia.org/wiki/Model_View_Controller#:~:text=Model%2DView%2DController%20(MVC,%E3%82%A2%E3%83%BC%E3%82%AD%E3%83%86%E3%82%AF%E3%83%81%E3%83%A3%E3%81%AE%E4%B8%80%E7%A8%AE%E3%81%A7%E3%81%82%E3%82%8B%E3%80%82)) や[レイヤードアーキテクチャ](https://ja.wikipedia.org/wiki/%E5%A4%9A%E5%B1%A4%E3%82%A2%E3%83%BC%E3%82%AD%E3%83%86%E3%82%AF%E3%83%81%E3%83%A3)が存在していました。
特に MVC(S) は Rails や Rails ライクなフレームワークに採用され、脚光を浴びている期間が多かったように感じます。
ただ、MVC(S) 、レイヤードアーキテクチャにはそれぞれ問題点がありました。

:::message
「レイヤードアーキテクチャ」という概念の中にクリーンアーキテクチャ、ヘキサゴナルアーキテクチャ、オニオンアーキテクチャが存在するという考え方もありますが、本記事では区別して考えます
:::

**MVC(S) の問題点**
- Controller 層が肥大化しやすい
- Service 層が肥大化しやすい
- ビジネスロジックが View と密結合関係に陥りやすい

**レイヤードアーキテクチャの問題点**
- ドメインロジックが外部技術に依存してしまう

更に Cockburn さんはビジネスロジックが View と密結合関係に陥ることにより、以下の問題を抱えていました。

**Cockburn 氏が抱えていた問題**

- View と密結合している箇所のテスト容易性が低い
- 別のユーザインタフェースに切り替えることがほぼ不可能
    - 例: GUI -> CLI またはその逆
- プログラム A の一部機能をプログラム B で使用したいがそれができない

> ・First, the system can’t neatly be tested with automated test suites because part of the logic needing to be tested is dependent on oft-changing visual details such as field size and button placement;
> ・For the exact same reason, it becomes impossible to shift from a human-driven use of the system to a batch-run system;
> ・For still the same reason, it becomes difficult or impossible to allow the program to be driven by another program when that becomes attractive.
> https://alistair.cockburn.us/hexagonal-architecture/

各アーキテクチャの問題点と Cockburn さんが解決したかった問題を解決するアプローチとしてヘキサゴナルアーキテクチャは誕生しました。

## Hexagonal Architecture で実現できること
ヘキサゴナルアーキテクチャを適用することで実現できることは以下になります。

**ユースケースを実現するためのロジックを完全に独立した状態で実行できる**
> The ultimate benefit of a ports and adapters implementation is the ability to run the application in a fully isolated mode.
> https://alistair.cockburn.us/hexagonal-architecture/

****

**外部技術を抽象化するため、テスト容易性が高くなる**
>On the data side, the application can be configured to run decoupled from external databases using an in-memory oracle, or ‘’mock’’, database replacement; or it can run against the test- or run-time database. The functional specification of the application, perhaps in use cases, is made against the inner hexagon’s interface and not against any one of the external technologies that might be used.
>https://alistair.cockburn.us/hexagonal-architecture/


## Hexagonal Architecture の概念
全体概念図

![](https://storage.googleapis.com/zenn-user-upload/5bc42b452e41-20220718.png)

### Driver(呼び出す側)
![](https://storage.googleapis.com/zenn-user-upload/da1e2b58c5c6-20220717.png)

外部から「アプリケーション」を呼び出す側です。
Driver ポートにリクエストデータを送信し、レスポンスデータを受信、受信したデータを Primary Actor が扱いやすいデータに加工します。

#### Primary Actor
##### 概要
イベントを発火する存在です。
WEB System を操作するユーザ、CLI や API を呼び出すバッチプログラム、Lambda を呼び出す API Gateway などを指します。

##### 責務

- Primary Actor Adapter の実行

#### Primary Actor Adapter
##### 概要
Primary Actor との「会話」をおこないます。
Presentation や View という言葉で言い換えることができると思います。
Driver Port を呼び出し、DI(依存性注入)をおこないます。
Driver Port から受信したデータを Primary Actor に扱いやすいもしくは見えやすい形に変換し、Primary Actor に届けます。

##### 責務

- Driver Port の呼び出し、DI(依存性注入)
- Primary Actor が扱いやすいデータを Primary Actor に提供する

### Driven(呼び出される側)
![](https://storage.googleapis.com/zenn-user-upload/c720121c5c27-20220717.png)

Driven Port から呼び出される側です。
Domain, Port を経て、ビジネスロジックが反映されたデータを外部技術用データに変換、伝播します。
外部技術は Adapter からのリクエストに応えて、レスポンスデータを返却します。

#### Secondary Actor
##### 概要
システムを作成するとなった場合、外部技術を利用しないというのは中々ないかと思います。
Secondary Actor はクラウドサービス(AWS, GCP, Azure)やシステムに関わる外部技術を指します。

##### 責務

- データの永続化
- データの提供
- サービス固有の振る舞い
    - メール送信
    - キューイング
    - etc...

#### Secondary Actor Adapter
##### 概要
Driven Port からインターフェース経由で呼び出され、Secondary Actor との「会話」をおこないます。
インフラ層、Client、Repository という言葉と言い換えられるかと思います。
Domain, UseCase, Driven Port を経て、ビジネスロジックが反映されたデータを外部技術に合わせたリクエストデータに変換し、外部技術にリクエストします。
外部技術からのレスポンスデータは言語のプリミティブ型、もしくは Domain モデルに変換して Driven Port に渡します。

##### 責務

- Driven Port から呼び出すために外部技術処理を抽象化
- DI 時に差し込むための外部技術処理の具象化
- 外部技術固有のエラーを Driven Port 用のエラーに変換し、返却する

### Port
![](https://storage.googleapis.com/zenn-user-upload/03d3be7c05cf-20220718.png)

Port はアダプタとインターフェース経由でやりとりします。
Port は以下の種類で区別できます。

- 呼び出す側(Driver)
- 呼び出される側(Driven)
- ユースケース

Port はユースケースに基づいた Driver Port と Driven Port を提供します。
各種類の関係性としてはユースケース図に基づいて3種類のポートが作成されます。

![](https://storage.googleapis.com/zenn-user-upload/1c75f6907773-20220718.png)

#### Driver Port
##### 概要
ユーザからのユースケース呼び出しに応えるための差込口(Port)を提供します。
ユーザからの入力を検査、ドメインオブジェクトに変換する役割を持つため、ポートの中では一番多くの責務を持つことになります。
また、Primary Actor Adapter へのレスポンスとして、Driver Port 固有の戻り値を返却することになります。

##### 責務

- Primary Actor から渡された入力データの検査
- Primary Actor から渡された入力データをドメインオブジェクトに変換
- Primary Actor Adapter への Result 生成、返却
- ユースケースの呼び出し(インターフェース経由)

#### Driven Port
##### 概要
ユースケースを実現するために必要な情報をインターフェース経由で Secondary Actor Adapter に問い合わせたり、ビジネスロジックが反映されたデータを Secondary Actor Adapter 経由で Secondary Actor に書き込んだりします。
Secondary Actor Adapter 側で発生したエラーのハンドリングも責務として持ちます。

##### 責務

- Secondary Actor Adapter の呼び出し
- Secondary Actor Adapter で発生したエラーのハンドリング

#### UseCase
##### 概要
ユースケースモデリングに基づいて、ビジネスロジック(業務ロジック)を実行します。
ユーザからの入力データや外部へのリクエストは Driver Port, Driven Port に任せておけばよいため、ユースケースはビジネスロジックのみとなり、記述量は少なく済むことがほとんどだと思います。

##### 責務

- ビジネスロジックの実行
- Driven Port の呼び出し

### Domain
##### 概要
「アプリケーション」のコアです。
ヘキサゴナルアーキテクチャの概念では完全に独立しているため、Adapter や Port との依存関係はありません。
「ありえない」状態のドメインオブジェクトの生成を防ぐことに注力します。
ReadOnly 属性と検査用の Private メソッドのみを保持することになると思います。

##### 責務

- ユースケースで使用されるドメインオブジェクトの属性を保持する
- ユースケース上、「ありえない」ドメインオブジェクトの生成を防ぐ
- 依存関係を持たない

# 所感
[オニオンアーキテクチャ](https://jeffreypalermo.com/2008/07/the-onion-architecture-part-1/)よりも概念理解がしやすく、責務分離しやすいと感じました。
また、Alistair Cockburn 氏が積極的に活動していることもあってか解説記事も多く、実装に迷った場合に参照できる情報の多さも魅力の一つだなと。
実装に境界があり、ユースケースやドメインに注力でき、[ドメイン駆動設計](https://ja.wikipedia.org/wiki/%E3%83%89%E3%83%A1%E3%82%A4%E3%83%B3%E9%A7%86%E5%8B%95%E8%A8%AD%E8%A8%88)とも相性が良いです。
この記事を書く上で参考にさせていただいたサイトをいくつか貼らせていただきます。
個人的には [AWS Summit 2022 ヘキサゴナルアーキテクチャを利用したLambda 関数のドメインモデルの実装 Live GitHub Repository](https://github.com/aws-samples/aws-lambda-domain-model-sample) がおすすめで、Driver, Driven を Input, Output と表現してサンプルコードが実装されています。
Driver, Driven を Input, Output に変換するとヘキサゴナルアーキテクチャではエンジニアには馴染み深く、イメージしやすい単語[^イメージしやすい単語]が増えるため、人に説明する際も理解してもらいやすそうです。
同じ理由で Hexagonal Architecture よりも Ports And Adapter の方がイメージしやすいため、Ports And Adapter という呼び名の方が好みです。
今後は Ports And Adapter を導入できそうなら積極的に導入し、導入が難しい場合はエッセンスを切り出して活用するなどしていきたいなと思います。

一応、素振り用のリポジトリも作ってみたので、良ければご覧になってください。
https://github.com/heyyou3/control_text

[^イメージしやすい単語]: Port, Adapter, Input, Output, UseCase

# 参考サイト

- [ヘキサゴナルアーキテクチャ提唱記事](https://alistair.cockburn.us/hexagonal-architecture/)
- [ヘキサゴナルアーキテクチャ提唱記事(日本語版)](https://blog.tai2.net/hexagonal_architexture.html)
- [cockburn 氏おすすめのヘキサゴナルアーキテクチャ学習サイト](https://jmgarridopaz.github.io/)
- [Netflixでの実装事例](https://netflixtechblog.com/ready-for-changes-with-hexagonal-architecture-b315ec967749)
- [AWS Summit 2022 ヘキサゴナルアーキテクチャを利用したLambda 関数のドメインモデルの実装 Live 資料](https://speakerdeck.com/fatsushi/hekisagonaruakitekutiyawoli-yong-sitalambda-guan-shu-falsedomeinmoderufalseshi-zhuang-live)
- [AWS Summit 2022 ヘキサゴナルアーキテクチャを利用したLambda 関数のドメインモデルの実装 Live GitHub Repository](https://github.com/aws-samples/aws-lambda-domain-model-sample)