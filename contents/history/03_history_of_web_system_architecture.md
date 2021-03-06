# Webシステムアーキテクチャの歴史 {#history-of-Web-system-architecture}
## ブラウザ〜サーバ {#browser-server}

<!-- textlint-disable -->

<img src="../../assets/images/drawio/history/03_history_of_web_system_architecture_1.png" alt="03_history_of_web_system_architecture_1" height="360" />

<!-- textlint-enable -->

BrowserとServerだけの構成というシンプルなWebシステムアーキテクチャだ。
Serverから静的コンテンツを返すだけや、CGIを使った動的コンテンツを返すこともできる。

## ブラウザ〜サーバ〜API {#browser-server-api}

<!-- textlint-disable -->

<img src="../../assets/images/drawio/history/03_history_of_web_system_architecture_2.png" alt="03_history_of_web_system_architecture_2" height="360" />

<!-- textlint-enable -->

データベースが登場。
Serverから直接データベースを参照する構成や、データベースからAPIを提供する構成がある。
前者は、直接参照するためレスポンスタイムが後者よりも短い。
後者は、APIというインタフェースを挟むことで、データベースの変更が前者よりも容易。

**レスポンスタイム短縮する常套手段** として、**キャッシュレイヤ** を挟むことである。
たとえば、APIとデータベースの間にキャッシュサーバを置くことで、大幅にレスポンスタイムを短縮することが期待できる。
しかし、キャッシュは麻酔であり、多用は厳禁である。なぜなら、さまざまな副作用があるからだ。
副作用の1つに、データ鮮度の低下が見込まれる。キャッシュは用法用量を守ることが大切だ。

## ブラウザ〜BFF〜API {#browser-bff-api}

<!-- textlint-disable -->

<img src="../../assets/images/drawio/history/03_history_of_web_system_architecture_3.png" alt="03_history_of_web_system_architecture_3" height="360" />

<!-- textlint-enable -->

### BFF {#bff}

APIが複数登場すると、APIをまとめるレイヤが必要となる。
そこで、**[BFF（Backend For Frontends）](https://philcalcado.com/2015/09/18/the_back_end_for_front_end_pattern_bff.html)** というレイヤが登場する。

<!-- textlint-disable -->

![<a href="https://philcalcado.com/2015/09/18/the_back_end_for_front_end_pattern_bff.html">The Back-end for Front-end Pattern (BFF) - philcalcado.com</a>](https://philcalcado.com/img/2015-09-back-end-for-front-end-pattern/sc-bff-1.png)

<!-- textlint-enable -->

これは、文字どおりフロントエンドのためのバックエンドである。
BFFの機能例として、データをフロントエンド向けに加工したり、HTMLを構築したりする。
フロントエンドでは、UI/UXに興味・関心がある。**UI/UXを表現するデータは、ビジネスロジックを返却するAPIだけでは不十分** である。
複数のAPIより取得したデータをUI/UX向けに加工してフロントエンドに提供することが多い。
ブラウザからBFFまでをフロントエンドとし、BFFより後ろをバックエンドとする。
BFFにより、フロントエンドとバックエンドの境界が明確になる。

### Microservices {#microservices}

バックエンドは、 **[マイクロサービス化](https://martinfowler.com/articles/microservices.html)** に進むケースがある。

<!-- textlint-disable -->

![<a href="https://martinfowler.com/articles/microservices.html">Microservices - martinfowler.com</a>](https://martinfowler.com/articles/microservices/images/decentralised-data.png)

<!-- textlint-enable -->

マイクロサービスという単語を出すと、モノリスという単語が対比としてよく挙げられる。
モノリスとは、一枚岩という意味で解釈されることが多い。
モノリスなアプリケーションとは、機能的に区別できるサービスすべてが一枚のコンポーネントにまとまっていることである。
それのメリットは、次のような点があると考えている。

* 開発が容易
  * 依存する機能がすべて含まれているため
    * 大規模なシステムになると、設計次第で開発が困難になる
* トラブルシュートが簡単
  * 1つのコンポーネントにすべてのログが記録されているため
* パフォーマンスが安定
  * 外部通信が少ないため

モノリスなアプリケーションにもデメリットが2つあると考えている。

* 開発サイクルが伸びる
* システムや組織的なスケーラビリティ性がボトルネック

それぞれ説明する。

1つ目は、システム規模が拡大するにつれて、開発サイクルが伸びることだ。
その結果、**ユーザへの価値提供が遅くなってしまう** 。
<!-- textlint-disable -->
理由としては、開発者側の負担である。
<!-- textlint-enable -->
さまざまな機能を1つのコンポーネントに含めるということは、**開発者はそれをすべて理解しなければならない** 。
そのコストは、規模が拡大するにつれて、増大する。
その問題をモノリスアプリケーションで解決する1手段として、モジュールを適切に分離することだ。
モジュラモノリスアーキテクチャが、まさにそれだ。

2つ目は、**システムや組織的なスケーラビリティ性がボトルネック** になることだ。
サービスが成長すると、それに伴いシステムや組織もスケールしなければならない。
しかし、モノリスなアプリケーションでは、**スケールのボトルネック** となってしまう。

システム的な例えとして、ECサイトのモノリスアプリケーションの中で、特定機能の負荷が高くなったする。
その場合、サーバの改善（スケールアウトorスケールアップ）をするか、アプリケーションチューニングをするかなどになる。
本来は、**特定機能だけスケールすればよい** のに、必要以上にコストがかかってしまう。

組織的な例えとして、システム保守する人を雇ったとしても、すぐに戦力にならない。
なぜなら、**理解すべき領域が広すぎる** ため、学習コストが高くなる。
この問題の解決としては、アプリケーションに関する資料の整備や、啓蒙活動がある。

そこで、マイクロサービスの話になる。

Unixの哲学にあるように『ただ1つのことをして、それをうまくやる』に従い、ただ1つの機能を提供するサービスを構築する。
そのサービスを複数組み合わせて、達成すべき課題を解決する。それが、マイクロサービスだ。
各サービス間は、軽量なプロトコルを用いて通信することで、連携する。

マイクロサービスのメリットは、次のような点があると考えている。

* 強制的なモジュラリティの向上
  * 各サービスは、それぞれ独立するため
* サービスごとにスケールすることが容易
  * 各サービスは、それぞれ独立するため
* 対象サービスの理解が容易
  * 1つのサービスのみに集中できるため

しかし、デメリットももちろん存在する。銀の弾丸は存在しない。

* 開発が困難
  * モノリスに比べて考えなければいけないことが多い
* トラブルシュートが困難
  * ユーザーアクセスの追跡が難しいため
* パフォーマンスが不安定
  * 外部通信が頻繁に行われるため

## ブラウザ〜マイクロフロントエンド〜サーバ〜API {#browser-micro-frontends-server-api}
<!-- textlint-disable -->

<img src="../../assets/images/drawio/history/03_history_of_web_system_architecture_4.png" alt="03_history_of_web_system_architecture_4" height="360" />

<!-- textlint-enable -->

そして、次がマイクロフロントエンドだ。簡単に説明すると、
マイクロサービスの考えをフロントエンドにも拡張したアーキテクチャだ。
それは、[こちらのページ](../microfrontends/10_overview_of_micro_frontends.md)より紹介する。
