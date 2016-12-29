---
layout: post
title: kindleを使った天気予報/予定表の壁紙
tags: [IoT, kindle]
---

## TL;DR

* 古いkindle(paper white, 第5世代)を天気予報とGoogleカレンダーのイベントを表示する壁紙に

  <div class="post-images">
    <div class="post-image">
      <a href="/images/posts/kindle-wallpaper/kindle-wallpaper.jpg"><img src="/images/posts/kindle-wallpaper/kindle-wallpaper.jpg"/></a>
      <div class="post-image-info">両面テープの磁石で壁や冷蔵庫に貼り付け</div>
    </div>
    <div class="post-image">
      <a href="/images/posts/kindle-wallpaper/kindle-wallpaper-wood-frame.jpg"><img src="/images/posts/kindle-wallpaper/kindle-wallpaper-wood-frame.jpg"/></a>
      <div class="post-image-info">ダンボールに木目調のシート(3M製)を貼って壁掛けにも</div>
    </div>
    <div class="clear"></div>
  </div>

* 充電は1ヵ月に1度くらい(にならないかなー、と願望。これから測定します)

## 動機

* 天気予報を録画から見るの面倒。iPhoneもwithingsの体重計も天気予報の精度が悪い。
* kindleだとバッテリーをほぼ食わないから壁紙に最適
* 家族も喜ぶ
* 新しいkindleも買える!

## アーキテクチャ

* raspberry-pi: 天気予報、カレンダー取得、壁紙作成
  * 天気予報は、livedoorのAPIを使用。元となるデータは、日本気象協会っぽい。
* kindle papwer white(5th generation): raspberry-piから壁紙取得して表示
  * 1日に1度(15時)、次の日の天気予報とカレンダーを取得して、壁紙に設定して再起動
  * 前提: jailbreakしていること(下のソースコードのメモを参照)
* ソースコード: [https://github.com/ichusrlocalbin/kindle-wallpaper-jp](https://github.com/ichusrlocalbin/kindle-wallpaper-jp)
