---
title: "PUNK APIでクラフトビールの情報を取得しながらAPI利用を学ぶ🍻" # 記事のタイトル
emoji: "🍺" # アイキャッチとして使われる絵文字（1文字だけ）
type: "tech" # tech: 技術記事 / idea: アイデア記事
topics: ["javaScript","Node.js","PUNKAPI","Beer","axios"] # タグ。["markdown", "rust", "aws"]のように指定する
published: true # 公開設定（falseにすると下書き）
---

Punk IPAというクラフトビールがありますよね。APIもある模様ですね！

注意ですが、 **この記事を読むとビールのIPAとAPIがゲシュタルト崩壊していきます。**

@[tweet](https://twitter.com/pokiiio/status/1404355279827857409?ref_src=twsrc%5Etfw)

<!-- <blockquote class="twitter-tweet"><p lang="ja" dir="ltr">IPA飲みながらAPI叩かねば！</p>&mdash; ポキオ (@pokiiio) <a href="https://twitter.com/pokiiio/status/1404355279827857409?ref_src=twsrc%5Etfw">June 14, 2021</a></blockquote> <script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script> -->

JavaScript初心者、APIやaxiosの利用が初めての人向け解説記事です。if文やfor文、配列やオブジェクトなどの基本構文についてはスキップして話をしています。[こちらのaxiosサンプル集](https://protoout.studio/posts/public-apis-api-get)で見つけたAPIを使ってみました。

## PUNK APIのページを見てみる

[ブリュードッグのPunk IPA](https://whisk-e.co.jp/products/punkipa/)をもじってる感がすごいAPIですね。公式ではなく、サードパーティが作ってるジョークAPIだと思います。

説明文を見る限り、ブリュードッグのビール情報を取得するっぽいですね。

> ![](https://i.gyazo.com/c8561d4e24cc738b2f986e226a7ba5d4.gif)

[こちら](https://punkapi.com/)がAPIのページらしいです。

> Version 1 has now been shutdown. Upgrade to V2 it's easier & faster!

とのことなので、どうやら最近はv1を終えて、v2に移行していくみたいですね

> https://punkapi.com/documentation/v2


## まずはAPIを叩いてみる

> `https://api.punkapi.com/v2/beers`

これがビール取得のAPIエンドポイント（URL）みたいですね

> ![](https://i.gyazo.com/ae1f1c5abec43963522b7a171e3b7fb4.png)

```js
const axios = require('axios');

const main = async () => {
  try {
    const res = await axios.get('https://api.punkapi.com/v2/beers');
    console.log(res.data);
  } catch (error) {
    console.error(error);
  }
}

main();
```

実行してみると以下のような感じでクラフトビールの一覧と情報が取得できました。

```bash
省略・・・

  {
    id: 24,
    name: 'The End Of History',
    tagline: "The World's Strongest Beer.",
    first_brewed: '06/2011',
    description: 'The End of History: The name derives from the famous work of philosopher Francis Fukuyama, this is to beer what democracy is to history. Complexity defined. Floral, grapefruit, caramel and cloves are intensified by boozy heat.',
    image_url: 'https://images.punkapi.com/v2/24.png',
    abv: 55,
    ibu: null,
    target_fg: 1000,
    target_og: 1112,
    ebc: null,
    srm: null,
    ph: 4.4,
    attenuation_level: 100,
    volume: { value: 20, unit: 'litres' },
    boil_volume: { value: 25, unit: 'litres' },
    method: {
      mash_temp: [Array],
      fermentation: [Object],
      twist: 'Nettles: 25g at end, Juniper: 25g at end'
    },
    ingredients: {
      malt: [Array],
      hops: [Array],
      yeast: 'Wyeast 3522 - Belgian Ardennes™'
    },
    food_pairing: [
      'Roasted wood pigeon with black pudding',
      'Pan seared venison fillet with juniper sauce',
      'Apricot coconut cake'
    ],
    brewers_tips: "You'll have to get this one all the way down to -70°C. Taxidermy is not optional.",
    contributed_by: 'Sam Mason <samjbmason>'
  },
  {
    id: 25,
    name: 'Bad Pixie',
    tagline: 'Spiced Wheat Beer.',
    first_brewed: '10/2008',
    description: '2008 Prototype beer, a 4.7% wheat ale with crushed juniper berries and citrus peel.',
    image_url: 'https://images.punkapi.com/v2/25.png',
    abv: 4.7,
    ibu: 45,
    target_fg: 1010,
    target_og: 1047,
    ebc: 8,
    srm: 4,
    ph: 4.4,
    attenuation_level: 79,
    volume: { value: 20, unit: 'litres' },
    boil_volume: { value: 25, unit: 'litres' },
    method: {
      mash_temp: [Array],
      fermentation: [Object],
      twist: 'Crushed juniper berries: 12.5g, Lemon peel: 18.8g'
    },
    ingredients: {
      malt: [Array],
      hops: [Array],
      yeast: 'Wyeast 1056 - American Ale™'
    },
    food_pairing: [
      'Poached sole fillet with capers',
      'Summer fruit salad',
      'Banana split'
    ],
    brewers_tips: 'Make sure you have plenty of room in the fermenter. Beers containing wheat can often foam aggressively during fermentation.',
    contributed_by: 'Sam Mason <samjbmason>'
  }
```

## Punk IPAをAPI経由で取得してみる

APIのリクエストパラメータに`beer_name`というKeyがあります。

しかも詳細の箇所をみると**`Punk`を指定するとPunk IPAの情報が取れるよ**みたいなことが書かれています。確信犯。

> ![](https://i.gyazo.com/c3951f7803f9ccc65df807193d877f69.png)

URLはこういう指定になります。

```
https://api.punkapi.com/v2/beers?beer_name=punk
```

JavaScriptからだとこんな書き方ですね。

```js
const axios = require('axios');

const main = async () => {
  try {
    const res = await axios.get('https://api.punkapi.com/v2/beers?beer_name=punkipa');
    console.log(res.data);
  } catch (error) {
    console.error(error);
  }
}

main();
```

実行すると確かにPunk APIの情報一覧が取得できました。

```
・・・省略

  {
    id: 192,
    name: 'Punk IPA 2007 - 2010',
    tagline: 'Post Modern Classic. Spiky. Tropical. Hoppy.',
    first_brewed: '04/2007',
    description: "Our flagship beer that kick started the craft beer revolution. This is James and Martin's original take on an American IPA, subverted with punchy New Zealand hops. Layered with new world hops to create an all-out riot of grapefruit, pineapple and lychee before a spiky, mouth-puckering bitter finish.",
    image_url: 'https://images.punkapi.com/v2/192.png',
    abv: 6,
    ibu: 60,
    target_fg: 1010,
    target_og: 1056,
    ebc: 17,
    srm: 8.5,
    ph: 4.4,
    attenuation_level: 82.14,
    volume: { value: 20, unit: 'litres' },
    boil_volume: { value: 25, unit: 'litres' },
    method: { mash_temp: [Array], fermentation: [Object], twist: null },
    ingredients: {
      malt: [Array],
      hops: [Array],
      yeast: 'Wyeast 1056 - American Ale™'
    },
    food_pairing: [
      'Spicy carne asada with a pico de gallo sauce',
      'Shredded chicken tacos with a mango chilli lime salsa',
      'Cheesecake with a passion fruit swirl sauce'
    ],
    brewers_tips: "While it may surprise you, this version of Punk IPA isn't dry hopped but still packs a punch! To make the best of the aroma hops make sure they are fully submerged and add them just before knock out for an intense hop hit.",
    contributed_by: 'Sam Mason <samjbmason>'
  }
]
```

色々とPunk IPAの情報が出てきましたが、少し長いので最後のものだけ記事に表示させてます。この`id:192`を次の手順で使ってみます。

## Punk IPAの情報を1件だけ取得する

一覧の取得が出来ましたが、このAPIは1件だけ取得することも出来るみたいです。

ドキュメントページを読み進めると`Single Beer`と`Random Beer`のAPIを発見できます。

どうやらビールの情報を一つ取得したり、ランダムでビール情報を取得したり出来る模様です。

> ![](https://i.gyazo.com/ff691ab4f00710ced44faa63fe610833.png)


1件だけ取得するSingle BeerはIDを指定して1件取得できるらしいです。
先程のPunk IPAの`id: 192`の項目を指定してみます。

URLは`https://api.punkapi.com/v2/beers/192`になりますね。

```js
const axios = require('axios');

const main = async () => {
  try {
    const res = await axios.get('https://api.punkapi.com/v2/beers/192');
    console.log(res.data);
  } catch (error) {
    console.error(error);
  }
}

main();
```

```bash
$ node punkapi-test.js 
[
  {
    id: 192,
    name: 'Punk IPA 2007 - 2010',
    tagline: 'Post Modern Classic. Spiky. Tropical. Hoppy.',
    first_brewed: '04/2007',
    description: "Our flagship beer that kick started the craft beer revolution. This is James and Martin's original take on an American IPA, subverted with punchy New Zealand hops. Layered with new world hops to create an all-out riot of grapefruit, pineapple and lychee before a spiky, mouth-puckering bitter finish.",
    image_url: 'https://images.punkapi.com/v2/192.png',
    abv: 6,
    ibu: 60,
    target_fg: 1010,
    target_og: 1056,
    ebc: 17,
    srm: 8.5,
    ph: 4.4,
    attenuation_level: 82.14,
    volume: { value: 20, unit: 'litres' },
    boil_volume: { value: 25, unit: 'litres' },
    method: { mash_temp: [Array], fermentation: [Object], twist: null },
    ingredients: {
      malt: [Array],
      hops: [Array],
      yeast: 'Wyeast 1056 - American Ale™'
    },
    food_pairing: [
      'Spicy carne asada with a pico de gallo sauce',
      'Shredded chicken tacos with a mango chilli lime salsa',
      'Cheesecake with a passion fruit swirl sauce'
    ],
    brewers_tips: "While it may surprise you, this version of Punk IPA isn't dry hopped but still packs a punch! To make the best of the aroma hops make sure they are fully submerged and add them just before knock out for an intense hop hit.",
    contributed_by: 'Sam Mason <samjbmason>'
  }
]
```

無事に1件だけ取得することが出来ました。

## ビールの名前と画像だけ欲しい

データの加工タイムです。1件だけ取得できましたが、まだ情報量が多いですね。自分が利用したい、必要な情報だけ取得しましょう。

ビールの名前`Punk IPA 2007-2010`と画像の`https://images.punkapi.com/v2/192.png`を取得してみます。

> ![](https://i.gyazo.com/23c5e683d6a87aad21a30e1e63b75844.png)

基本的には`res.data`の後にKey名をドット(.)で繋げれば取得できます。
PUNK APIのこの場合、

* ビールの名前: `name`のKey
* ビールの画像: `image_url`のKey

というKey名になっているので、以下のように書くことで名前と画像だけを抽出出来ます。

```js
//省略

const res = await axios.get('https://api.punkapi.com/v2/beers/192');
console.log(res.data.name); //名前
console.log(res.data.image_url); //画像取得

//省略
```

実行してみるとどうでしょう。

```bash
$ node punkapi-test.js 
undefined
undefined
```

...あれ。`undefined`はデータが格納されてないということで、上手く取得できてないです。

ここはAPIあるあるです。`res.data`に格納されるデータが配列形式になっている場合があります。その場合は`[0]`などで配列の何番目を利用するかを指定する必要があります。

配列（`[]`）かオブジェクト（`{}`）かの見分け方はコンソールなどに表示された最初の記号が`[`なのか`{`なのかで見分けると良いと思います。

> ![](https://i.gyazo.com/619257463e0ea05d2be8b9481708b732.png)

実はドキュメントページの例にも書いてありましたね。

> ![](https://i.gyazo.com/a5c5ed0f81d484a2430cbf78789489d5.png)

1件だけ取得する`Single Beer`のAPIが配列（複数）で返ってくるのも仕様的にどうなんだろ？と思いつつ、仕方ないので書き換えてみます。

以下のように配列の`0番目`を指定し、以降は`.name`や`.image_url`などドットとKey名で繋げていきます。

```js
//省略

const res = await axios.get('https://api.punkapi.com/v2/beers/192');
console.log(res.data[0].name); //名前
console.log(res.data[0].image_url); //画像取得

//省略
```

これで名前と画像URLだけを取得できました。

```bash
$ node punkapi-test.js 
Punk IPA 2007 - 2010
https://images.punkapi.com/v2/192.png
```

## もっと深い階層のデータを取得してみる

yeastのkeyにある値を取得したい場合はどうすれば良いでしょうか。

> ![](https://i.gyazo.com/6dd606beccf8b0303d38c3bf5f5ce557.png)

`yeast`は入れ子構造になっていて、`ingredients`のKeyの子階層になっていることが分かります。ドットで接続していくことで子階層の深いデータにアクセスすることが出来ます。

```js
//省略

const res = await axios.get('https://api.punkapi.com/v2/beers/192');
console.log(res.data[0].ingredients.yeast);

//省略
```

```bash
$ node punkapi-test.js 
Wyeast 1056 - American Ale™
```

`yeast`と同階層の`malt`や`hops`にアクセスする場合は、`[Array]`となっていて表示上省略されています。一度`res.data[0].ingredients`をconsole.logで表示してみましょう。

```js
//省略
console.log(res.data[0].ingredients);
//省略
```

```bash
$ node punkapi-test.js 
{
  malt: [ { name: 'Extra Pale', amount: [Object] } ],
  hops: [
    {
      name: 'Ahtanum',
      amount: [Object],
      add: 'start',
      attribute: 'bitter'
    },
    {
      name: 'Chinook',
      amount: [Object],
      add: 'start',
      attribute: 'bitter'
    },

//省略
```

`malt`も`hops`も下の階層は配列になっていて、その中にオブジェクトが格納されていることが分かります。

`hops`以下の`Chinook`の情報を取得してみます。`Chinook`の箇所はhops以下の配列の2番目になります。配列の指定は0からスタートなので、2番目の配列は1を指定します。

```js
//省略
console.log(res.data[0].ingredients.hops[1].name);
console.log(res.data[0].ingredients.hops[1].add);
console.log(res.data[0].ingredients.hops[1].amount);
console.log(res.data[0].ingredients.hops[1].attribute);
//省略
```

```bash
$ node punkapi-test.js 
Chinook
start
{ value: 15, unit: 'grams' }
bitter
```

という感じで配列とオブジェクトの操作を行えました。

## Punk IPAのビールの名前と画像だけの一覧が欲しい

1件だけ扱うではなく、一覧を作成してみましょう。ここでfor文を利用してデータを複数回処理します。

```js
const axios = require('axios');

const main = async () => {
  try {
    const res = await axios.get('https://api.punkapi.com/v2/beers?beer_name=punkipa');
    // console.log(res.data);
    for (let i = 0, len = res.data.length; i < len; i++) {
      console.log(res.data[i].name);
      console.log(res.data[i].image_url);
    }
  } catch (error) {
    console.error(error);
  }
}

main();
```

```bash
$ node punkapi-test.js 
Punk IPA 2010 - Current
https://images.punkapi.com/v2/106.png
Punk IPA 2007 - 2010
https://images.punkapi.com/v2/192.png
```

ちなみにPunkだけで調べるとPunk IPA以外のPunkと名のつくビールが表示されました。Punk IPA以外にも〇〇Punkみたいな名称、色々あるんですね。

## まとめ

Punk APIはネーミング的にジョークAPIだと思ってましたが、APIの作りは比較的分かりやすく、扱いやすい印象でした。

途中でも書きましたが、Single Beerが配列で返ってくるのはどうなんでしょうね苦笑

皆さんもぜひPUNK APIを使って知らないクラフトビール（このAPIだとブリュードッグのものだけかもですが）と出会ってみて下さい。

そろそろ緊急事態宣言も空けてビール飲みに行けますかねぇ。
