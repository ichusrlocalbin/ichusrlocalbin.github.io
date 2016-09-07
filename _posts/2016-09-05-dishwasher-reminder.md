---
layout: post
title: 食洗機のかけ忘れリマインダ
tags: [IoT, デザイン]
---

## TL;DR

* 食洗機のかけ忘れリマインダを作成

  <div class="post-images">
    <div class="post-image">
      <a href="/images/posts/dishwasher-reminder/flic.jpg"><img src="/images/posts/dishwasher-reminder/flic.jpg"/></a>
      <div class="post-image-info">食洗機かけるときにボタンを押す</div>
    </div>
    <div class="post-image">
      <a href="/images/posts/dishwasher-reminder/normal_light.jpg"><img src="/images/posts/dishwasher-reminder/normal_light.jpg"></a>
      <div class="post-image-info">通常時の玄関のライト</div>
    </div>
    <div class="post-image">
      <a href="/images/posts/dishwasher-reminder/warning_light.jpg"><img src="/images/posts/dishwasher-reminder/warning_light.jpg"></a>
      <div class="post-image-info">朝と夜にボタンを押していないと、食洗機をかけ忘れてないかという警告で青くなる</div>
    </div>
    <div class="post-image">
      <a href="/images/posts/dishwasher-reminder/warning_light_up.jpg"><img src="/images/posts/dishwasher-reminder/warning_light_up.jpg"></a>
      <div class="post-image-info">警告を見てかけ忘れがないか思い出す</div>
    </div>
    <div class="clear"></div>
  </div>

* 効果はこれから測定

## つくろうと思った背景

* デザイン思考を実践したいと思って授業を受けていた
* YDDの記事を読んでいた [https://speakerdeck.com/itosho525/effective-ydd](https://speakerdeck.com/itosho525/effective-ydd)
* 家庭の課題くらい解決できなくて、何が「デザイン思考の実践」じゃい、と思った
* 夏休みで作れる時間があった

## 食洗機のかけ忘れ防止のデザイン

### ヒアリングによる課題の明確化

* 食洗機をかけなければならないとは、いつも思う
* でも、かけた気になって、忘れる
* 洗剤も入れてスイッチを入れるだけの状態のときもある
* かけ忘れて、食洗機を開けたときに初めて、かけ忘れに気づく

### リマインダの場所: 玄関のライト

* 最初に試したこと: 食洗機やキッチンの壁にマグネットシール  
  かけたときに、シールをひっくり返す → そもそも、かけること自体を忘れるので、×
* 寝るとき、出かけるときに目に入る玄関のライトに決定

### 食洗機が動いた検知: ボタン

* ビルトインの食洗機なので、コンセントの間にガジェットを入れるのは、×
* [透明タッチスイッチ](http://bit-trade-one.co.jp/product/module/ad00018/) を利用しようと思ったけど、5Vなのと、回路が付くと電源と配線が煩わしいから、×
* 食洗機の出す光や音を検知しても考えたけど、一度でも失敗したら使ってもらえなさそうなので、×
* ヒアリングしたらボタン押すくらいなら良いと聞いたので、食洗機をかけたついでにボタンを押してもらうことに決定

### アラートを出すタイミング:

* 朝と夜に通知する。朝と夜、すでにかけていたら通知しない。具体的な条件は、次のとおり。
  * 現在、朝8時以降 and 朝6時以降にボタンが押されていない  
    or  
    現在、夜9時30分以降 and 夜6時以降にボタンが押されていない
* 当初、次のように、「ボタンが押されて9時間経過後」としていたけど、ヒアリングで定刻の方が良さそうだったので[変更](https://github.com/ichusrlocalbin/dishwasher-reminder-home-status/pull/1/files)した。
  * ~~長すぎると、朝かけた食洗機で昼に出かけるときのアラートが出ずに、忘れる危険~~
  * ~~アラートは気づかせるための契機であって、アラートが出ずに忘れるよりも、食洗機をかけてもアラートが出た方が良い~~
  * ~~でも、日常的に警告されると意識が薄れる。また、アラートは結構うざい。~~
  * ~~夜の11時にかけて8時に出かけることを考えて、とりあえず9時間で試行する。~~

### アラートの光: 青と普通の白色電灯

* 聞き取り調査の結果、白の点滅が、リマインダとして一番良かった。これを実現するためには、(白など固定の色(Discoモード以外の色)に設定後)、Discoモードの信号を2回連続で送る必要がある。
* 1回目のDiscoモード信号が赤で、2回目の信号を送る間に、この赤が入るため煩わしく、あきらめた。
* 2つライトがつけられたので、1つは白色灯を、残りにmilightを設置し、milightは固定の光にすることで、煩わしくない色、かつ、リマインダとして気づける色(ヒアリング結果より青)にした。

## アーキテクチャ

* flic.io:  押したらBLEでビーコンを発生するボタン
  * AWS IoT Buttonの方が良かったけど、日本には売ってくれなかった...
* raspberry pi 3:
  * ビーコン検知をherokuへ。一人ならiPhoneでも良かったけどどちらかが不在のときにも使えるように。
  * milgihtに状態に応じて光の色を通知
  * ソースコード: [https://github.com/ichusrlocalbin/dishwasher-reminder-flic](https://github.com/ichusrlocalbin/dishwasher-reminder-flic)
* heroku: ビーコン発生時刻を記録。raspberry pi 3でもいいけど、電源切れたとき用にcloudに記録。
  * ソースコード: [https://github.com/ichusrlocalbin/dishwasher-reminder-home-status](https://github.com/ichusrlocalbin/dishwasher-reminder-home-status)
* milight: 色をAPIで制御できる電球。philips hueと異なり、インターネットからは制御できない。でも安い。

## おまけ: 自作ビーコンボタン

* flic.io の配送が2～3週間かかるので、抑えられない気持ちを行動で表すために、自作した。
* ボタンのソースコードと回路図: [https://github.com/ichusrlocalbin/dishwasher-reminder-diy-ble-button-peripheral](https://github.com/ichusrlocalbin/dishwasher-reminder-diy-ble-button-peripheral)
* raspberry piのソースコード: [https://github.com/ichusrlocalbin/dishwasher-reminder-diy-ble-button-central](https://github.com/ichusrlocalbin/dishwasher-reminder-diy-ble-button-central)
* herokuのソースコード(flick.io版と同じ): [https://github.com/ichusrlocalbin/dishwasher-reminder-home-status](https://github.com/ichusrlocalbin/dishwasher-reminder-home-status)

  <div class="post-images">
    <div class="post-image">
      <a href="/images/posts/dishwasher-reminder/diy_beacon_button.jpg"><img src="/images/posts/dishwasher-reminder/diy_beacon_button.jpg" /></a>
      <div class="post-image-info">自作のビーコン発生ボタン</div>
    </div>
    <div class="post-image">
      <a href="/images/posts/dishwasher-reminder/diy_beacon_button_circuit.jpg"><img src="/images/posts/dishwasher-reminder/diy_beacon_button_circuit.jpg" /></a>
      <div class="post-image-info">回路図</div>
    </div>
    <div class="clear"></div>
  </div>
