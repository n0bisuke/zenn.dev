---
title: "Node.jsから周辺のBLEデバイスを探すサンプル （2021年4月版）" # 記事のタイトル
emoji: "㊗️" # アイキャッチとして使われる絵文字（1文字だけ）
type: "tech" # tech: 技術記事 / idea: アイデア記事
topics: ["javascript","bluetooth","ble","IoT","Node.js"] # タグ。["markdown", "rust", "aws"]のように指定する
published: true # 公開設定（falseにすると下書き）
---

こんにちは、n0bisukeです。 [#iotlt vol74](https://iotlt.connpass.com/event/207823/) で話をした内容をまとめます。

Qiitaで昔書いた記事がありますが、2021年になってだいぶ状況が変わってきたのでリライトしてみます。

## noble

Node.jsからBLEデバイスを扱う際にはマストと言っていいくらいよく使われていたモジュールです。


> 参考: [Node.jsから周辺のBLEデバイスを探すサンプル](https://qiita.com/n0bisuke/items/00503d2b18fc4f413c4e)

僕も2016年頃までは現役で使ってましたが、その頃から更新がされなくなり、macのOSアップデートがあった際に動作しなくなってしまっていました。

> 参考: [最近のNode.jsでBluetooth事情 #iotlt #abandonware](https://speakerdeck.com/n0bisuke2/zui-jin-falsenode-dot-jsdebluetoothshi-qing-number-iotlt-number-abandonware)

## アバンダンウェアプロジェクトのnoble

アバンダンウェアとは管理者がサポートしなくなったソフトウェアを指すらしいです。

> アバンダンウェア (Abandonware) とは、著作権者が既に販売をやめたりサポートしていないソフトウェア、あるいは様々な理由により、誰が著作権者であるか不明なソフトウェアを指すために用いられる語である。
> https://ja.wikipedia.org/wiki/%E3%82%A2%E3%83%90%E3%83%B3%E3%83%80%E3%83%B3%E3%82%A6%E3%82%A7%E3%82%A2

定かでは無いですが、こういったアバンダンウェアを復活させるような動きとして[abandonware](https://github.com/abandonware/)のプロジェクトがある模様です。

なんにしても更新がされなくなって久しいnobleを使えるようにしてくれてるのは嬉しいですね。

## 注意: mac以外の人は通常のnobleでも問題無いかも

しっかりと調べられて無いですが、nobleが動作しないという話題はmacで動作させたい場合に限る話かもしれず、Windowsやラズパイ上で動作させたい場合は通常のnobleでも動作するかもしれません。

（手元で検証できないのでかもしれないくらいな表現にしています。）

## 最近な環境でnobleを使っていく

### 環境

M1のMac Bookではないので最新ではないですが、OSやNode.jsは最新です。

* Mac Book Pro 2019
* macOS Big Sur
* Node.js 15.14.0

### 準備

```bash
$ npm init -y
$ npm i @abandonware/noble
```

これだけです。

### コード

```javascript

'use strict';

const noble = require('@abandonware/noble');
const knownDevices = [];

//discovered BLE device
const discovered = (peripheral) => {
    const device = {
        name: peripheral.advertisement.localName,
        uuid: peripheral.uuid,
        rssi: peripheral.rssi
    };
    knownDevices.push(device);
    console.log(`${knownDevices.length}:${device.name}(${device.uuid}) RSSI${device.rssi}`);
}

//BLE scan start
const scanStart = () => {
    noble.startScanning();
    noble.on('discover', discovered);
}

if(noble.state === 'poweredOn'){
    scanStart();
}else{
    noble.on('stateChange', scanStart);
}
```

### 実行

```bash
$ node app.js
1:<デバイス名称>(<UUID>) RSSI-75
2:<デバイス名称>(<UUID>) RSSI85
3:<デバイス名称>(<UUID>) RSSI-49
・
・
・
```

問題なく動いてくれました。感動です。

5年ぶりくらいにMacでこのコードが動きました。

## 所感

Node.jsでBLE制御ができるのはやはりやれることの可能性が広がります。

ラズパイで旧式のnobleは動くみたいですが、手元の母艦機(Mac)で試せないとデバッグがしんどくてNode.jsからBLE制御は少し諦めていた節がありました。

ここ数年できてなかったフラストレーションを発散できるかもしれません。

こういったJavaScriptでドローン制御などもまたやれそうです。 

> 参考: [ノンプログラマでもコピペでOK！JavaScriptを使ってDroneを飛ばそう](https://liginc.co.jp/187633)
