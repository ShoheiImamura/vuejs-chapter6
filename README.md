---

theme : "black"
transizion : "default"

---

# Vue.js入門 6章
## 単一ファイルコンポーネントによる開発

--

## はじめに

--

### 注意事項
- このリポジトリは、 [秋葉原 Vue.js 入門 輪読会6章 Vue.js の基礎(初心者歓迎！) ](https://weeyble-js.connpass.com/event/133656/) の発表資料として用意したリポジトリです。
- 書籍の要約は正体、担当者の理解で書いているところは斜体で記載しています
- 対象は、6 章の前半(6.1~6.4)のみです。6.5以降は作っていません。
- 権利関係で問題があればご指摘ください
- vscode-reveal で見てもらうことを想定しています
  - Windowsは改行コードをLFにして見てください

--

### 自己紹介
- 今村昌平と申します
- 普段はWebシステムの受託会社で SE として勤務してます
  - プログラムを書くことは少ない
  - 元々獣医してましたが、やめて色々あって、1年半前に IT 業界に転身しました
- 個人開発する予定なので、リリースしたら使っていただけると嬉しいです
 - (手順書共有サービス)

--

---

## 全体の流れ

### 6章までの復習

### 6章前半
- ツール(Vue CLI)のインストール
- 単一ファイルコンポーネントとは
- 単一ファイルコンポーネントの仕様
- 単一ファイルコンポーネントのビルド

### 6章後半
- 単一ファイルコンポーネントの動作を体験する
- 単一ファイルコンポーネントの機能

--

## これまで学習した内容の確認
- Vue.js の特徴
- コンポーネント化
- ルーターを用いたページ遷移

--

### Vue.js の特徴

- Vue.js 自体はビューだけを取り扱うシンプルなライブラリ
  - ユーザに表示されるWebページ内容の画面をうまく処理する
- Vue.js 本体と、関連するライブラリも Vue.js 公式プロジェクトとして開発、管理されるためいくつかのライブラリを組み合わせて総合的なフレームワークとして使うこともできる
- 学習コストが低い  
  - 文法が想像しやすいライブラリ、
    - HTMLベースの平易なテンプレートを使用する
  - 開発環境の準備が楽、事前知識の必要が薄い
- UI を構造化し、コンポーネントとして利用する
  - システム全体をコンポーネントの集合体として扱う
- リアクティブなデータバインディング
  - HTML 内の DOM 要素とVue.js 内のデータを結びつける
  - Vue.js 内のデータ更新を検出すると、結び付けられたDOM要素を即座に更新する。

--

### Vue.js の設計思想

フレームワークはどんなときにでも、どんな規模でも、段階的に柔軟に使えるべきである

設計思想とは、トレードオフが発生したときの優先順位(今村意見)

--

### プロジェクト進行時の要求変化に対応できるフレームワーク

- 6 章では、ビルドシステム(build system)を扱う

--

### Vue.js を支える技術

- コンポーネントシステム
- リアクティブシステム
- レンダリングシステム

--

### Vue.js のエコシステム

#### Vue.js 公式で提供
- Vue Router, Vuex, VueLoader, VueCLI, VueDevTool
#### サードパーティが提供
- Nuxt.js, Weex, Onsen UI
#### その他
- Awesome Vue, Vue Curated

---

### Vue.js の導入

- script 要素で直接読み込む
  - グローバル変数 Vue が定義される

```html
<!DOCTYPE html>
<title>はじめてのVue.js</title>
<!-- script 要素で直接読み込みます -->
<script src="https://unpkg.com/vue@2.5.17"></script> 

```

--

### データバインディング

- HYML の `DOM 要素`と、Vue.js の`データ`を結びつける
- Vue.js の`データ`更新時に、HTML の `DOM 要素`も更新を反映する

```html
<!-- DOM要素 -->
<div id="app">
    <p>{{message}}</p>
</div>

<script>
    new Vue({
        el: '#app', // ここで DOM要素 指定
        data: {
            message: 'こんにちは！' // ここで、Vue.js のデータを定義
        }
    })
</script>
```

--

### Vueインスタンス

- グローバル変数 `Vue` を用いて、`Vueインスタンス`を生成する
  - コンストラクタとして用いる(引数はオブジェクト)
- new 演算子を用いる
- インスタンスをDOM要素にマウント（適用）することで、Vue.js の機能が要素内で使える

```js
// インスタンスを生成します
var vm = new Vue({
    // ...
})
```

--

### Vueインスタンスのオプション

| オプション名 | 内容                                 |
|--------------|--------------------------------------|
| data         | UIの状態・データ                     |
| el           | Vue インスタンスを、マウントする要素 |
| filters      | データを文字列と整形する             |
| methods      | イベントが発生したときなどの振る舞い |
| computed     | データから派生して算出される値       |

--

### Vue インスタンスの適用

- オプションオブジェクトの el プロパティで指定した DOM 要素がマウント対象になる
- el プロパティを指定せず、 $mount メソッドでマウントすることも可能

```js
// el プロパティで DOM 要素を指定
var vm = new Vue({
    el: "#app",
})
```

```js
var vm = new Vue({
    //
})
// 要素が生成されてからマウントする
vm.$mount(el)
```

--

### データ定義

- data プロパティは、データを指定する
- data プロパティの値が変わると、Vue.js は自動検知する

```js
var vm = new Vue({
    data: { // data プロパティ
        items: [
            {name:'鉛筆', price:300, quantity: 0},
            {name:'消しゴム', price:400, quantity: 0}
        ]
    }
})
```

--

### データ変更検知

- 変更検知時の処理を $watch メソッドを用いて記述できる

```js
vm.$watch( "監視対象の値を返す関数", "コールバック関数", [options] )
```

```js
vm.$watch(
    function(){return this.items[0].quantity}, // 鉛筆の個数を監視します
    function(quantity){console.log(quantity)}  // 個数が変化すると、コンソールに出力します
)
```

--

### テンプレート構文

- Vue.js でデータの変更をビューに反映させるためのテンプレート側の記述方法
  - `{{` と `}}` の値にデータや式を挿入する (Mustache記法)
  - `ディレクティブ` を利用する

--

### Mustache 記法

- Vue インスタンスのデータを HTML のテキストコンテンツとして出力する

```html
<p>{{ items[0].name }}</p>
```

- 式を記述することもできる

```html
<p>{{ items[0].price * items[0].quantity }}</p>
```

--

### ディレクティブ

- Vue.js 独自の構成
  - `v-` から始まる
- HTML に追加することで、式の変化に応じた DOM 操作を行う

--

| ディレクティブ           | 説明                                                                                   |
|--------------------------|----------------------------------------------------------------------------------------|
| v-if="引数"              | 引数が真のとき DOM 追加、偽のとき DOM 削除                                             |
| v-show="引数"            | 引数が真のとき表示、偽のとき非表示                                                     |
| v-bind:属性名="属性値"   | data に応じて、属性値が変化する                                                        |
| v-for="要素 in 配列"     | 配列あるいはオブジェクトのデータを繰り返しレンダリングする                             |
| v-on:イベント名="属性値" | イベントに応じて、式を実行する                                                         |
| v-model="属性値"         | データが更新されたら、ビューに反映する<br>かつ、ビューが更新されたら、データに反映する |

--

### メソッド(methods)

- methods プロパティで定義する
- Vue インスタンスのメソッドとして呼び出せる
  - v-on ディレクティブの属性値に設定されることが多い

```js
var vm = new Vue({
    // コンストラクタオプションの一つ
    methods: {
        メソッド名: function(){
            // 処理を記述します
        }
    }
})
```

---

### コンポーネント化(3章)
- 再利用性が高まり、開発効率をあげられる
- 既に使用されているコンポーネントを再利用することで、品質を保てる
- コンポーネントを適切に区切り、疎結合にした場合、保守性が高まる
- カプセル化されて開発で意識すべき範囲を限定できるようになる

--

### コンポーネントの定義（カスタムタグ方式）

- Vue.component() API で定義する

```js
Vue.component('コンポーネント名','オプション')
```

- オプションはコンポーネント自体の構成情報をオブジェクトで定義する
  - Vue インスタンスで使用されるオプションや、template, props などが使える

| オプション名 | 用途                         |
|--------------|------------------------------|
| template     | コンポーネントのテンプレート |
| props        | 親から子へのデータの受け渡し |

--

### コンポーネントの実装

- コンポーネント名をタグ名として使う（`カスタムタグ`）

```html
<ul id="example">
  <list-item></list-item>  <!--ここにコンポーネントを流し込む-->
</ul>

<script>
Vue.component(
    'list-item', // コンポーネント名（タグ名）
    {temlate:'<li>foo</li>'} // コンポーネントのオプション（テンプレート）
)

new Vue({el:'#example})
</script>
```

--

### 親コンポーネントから子コンポーネントへのデータの伝搬

- 親コンポーネント内でデータを定義する
- カスタムタグ内で v-bind 経由で子コンポーネントに渡す
- 子コンポーネント内に props オプションで親からもらうデータを定義する
- 子コンポーネント内で親からもらったデータを利用する

```html
<div id="app">
  <item-desc v-bind:item-name='myItem'></item-desc>
</div>

<script>
Vue.component(
    'item-name', // コンポーネント名
    {
        props: {itemName:{type: String}},  // 親から itemName を取得
        template: '<p>{{ itemName }}は便利です</p>' // 取得した itemName を表示
    }
)

new Vue({
    el: '#app',
    data: {MyItem: 'pen'}
})
</script>
```

---

### Vue Router (4章)
- ルートとは、`URL` と `ビューの情報` のセット
- Vue Router では、URL 遷移を伴うような動作を簡単に実現できる
    - ブラウザの戻るボタン等にも対応できる

--

### ルーターのインストール

- unpkg.com の CDN サービスを利用すると簡単にインストール可能

```html
<script src="https://umpkg.com/vue"></script>
<script src="https://umpkg.com/vue-router"></script>
```

--

### ルート定義

- path と component のセット

```js
// ルート定義
{
    path: '/top',
    component: { template: '<div>トップページです。</div>'}
}
```

--

### ルーターインスタンス

- route オプションにルート定義を配列で登録する

```js
// ルーターインスタンス
var router = new VueRouter({
    route: ['ルート定義1', 'ルート定義2', ... ]
})

// Vueインスタンス作成時に router オプションでルーターインスタンス指定する
var app = new Vue({
    router: router
}).$mount('#app')
```

--

### ページ遷移の実現方法

- `<router-link>`タグを用いて宣言的に記述
- `router.push` を利用してプログラム上で遷移を実行

--

## 中規模以上のプロジェクトにおける課題
- JavaScript のグローあるなスコープにおけるコンポーネントの管理
- エディタにおいてシンタックスハイライトが効かない JavaScript による文字列テンプレート
- コンポーネントに適用する CSS の名前空間管理
- コンポーネントのビルド処理

--

## 単一ファイルコンポーネントは上記の課題を解決します

--

# Vue.js入門 6章

単一ファイルコンポーネントによる開発

---

## 6.1 ツールのインストール
- ツールの利用目的の確認
- Node.js のインストール
    - npm のインストール
- Vue CLI のインストール

--

### ツールの利用目的の確認
- Vue.js 入門 の 6章以降では、コマンドラインを利用した様々なツールを利用します
- そのための準備をします
- Vue.jsで提供されるツールは、Node.js で開発され、npm によって配信されています。

--

### Node.js のインストール
- [Node.js日本語公式サイト](https://nodejs.org/ja/) からインストール
    - LTS を選択

--

## Vue CLI のインストール
- Vue.js 公式のコマンドラインツール
- Vue.js 向けのアプリケーション開発環境のセットアップを行う
    - モジュール化
    - ビルド(バンドルツール/プリプロセッサ)
    - 静的構文チェック(リント)
    - 単体テスト/E2Eテスト

---

## 6.2 単一ファイルコンポーネントとは

- 1つのコンポーネントを1つのファイルとして作成します
- `.vue` 拡張子ファイル内に定義します
- HTML ベースの構文で定義します
  - 1ファイルに、`<template>`, `<script>`, `<style>` ブロックを１つずつ配置します
- SPF (=Single File Components) と呼びます

```html
<template></template>
<script></script>
<style></style>

```

--

### 単一ファイルコンポーネントの例

```html
// template ブロック
<template>
    <div id="app">
        <h1 class="greeting">あいさつ： {{ msg }}</h1>
    </div>
</template>

// script ブロック
<script>
import Content from './content.vue'
export default {
    components: {
        Content
    },
    data () {
        return { msg: 'こんにちは！' }
    }
}
</script>

// style ブロック
<style>
.greeting { color: #42b983; }
</style>
```

--

### 単一ファイルコンポーネントの特徴

- 学習コストが少ない
  - 従来の Web 標準の技術( `HTML`, `JavaScript`, `CSS` )で構成
- 一貫性と保守性が高い
  - 1ファイルに 1つのコンポーネントのみ定義する
  - コンポーネントの定義が明確
- UI の再現性が高い
  - スタイルも含めてコンポーネント化できる

---

## 6.3 単一ファイルコンポーネントの仕様

- 基本的には、`HTML`と同じ仕組み

--

### `<template>`ブロック
- テンプレートを書き込むブロック
- 言語は `HTML`
  - `Pug`などの言語も使用できる
- Vue.js で提供する文法がそのまま利用可能
  - `Mustache`、`v-if`

--

### `<script>`ブロック

- UI の振る舞いをスクリプトで制御するブロック
- 言語は `JavaScript`
  - 構文は、Webブラウザの環境に依存する
- `<script>` タグは、1 ファイルに 1 つのみ

--

### `<script>`ブロック（例）
```html
<script>
// ES2015 の import 構文を利用
import MyModal from 'my-modal'
// ... 何かしらの処理
</script>
```

--

### `<style>`ブロック
- UI の見た目を制御するブロック
- 言語は `CSS`
- `<style>` タグは、1ファイルに複数可能

--

### `<style>`ブロック（例）
```html
<style>
.theme {
  color: #34495e;
  /* 何らかのスタイル */
}
</style>
```

--

### スタイルのカプセル化
- 他のコンポーネントで定義された `CSS` との干渉を防ぐ
  - コンポーネント単位のスタイルのカプセル化
  - `スコープ付きCSS/CSSモジュール`を使う
  - グローバルな（カプセル化されてない）スタイルと併記可能

--

### スタイルのカプセル化（例）

```html
<!-- カプセル化されたスタイル -->
<style scoped>
.message { color : #42b983; }
</style>

<!-- グローバルなスタイル -->
<style>
.theme { color : #34495e; }
</style>
```

- `<style>` タグに `scoped` をつけてカプセル化する


---

## 6.4 単一ファイルコンポーネントのビルド

- `.vue` ファイルは、ブラウザで読み込めない
- `.vue` ファイルは `HTML`, `JavaScript`, `CSS` に変換する必要がある

| ブラウザで動作する                | 動作しない |
|-----------------------------------|------------|
| `HTML`<br> `JavaScript`<br> `CSS` | `.vue`     |

--

### `.vue` ファイルのビルド（変換）
- バンドルツールと解析用のミドルウェアライブラリを使う
- バンドルツールは、Webページ表示に必要なファイルを全てJavaScript ファイルに束ねる
  - JavaScript、HTML、CSS を全てJavaScript ファイルにする

- 本章以降の説明では、webpack と Veu Loader を利用する前提ですすめる

---

## 6.5 単一ファイルコンポーネントの動作を体験する

--

### Vue CLI の serve コマンド

- webpack と Vue Loader を利用
- webpack の設定無しで Vue.js アプリケーションとしてビルドできる

--

### serve コマンドを利用したビルドの実行1

- /users/vuejs-primer/sfc/hello.vue として保存
```html
<template><p class="message">メッセージ：{{msg}}</p></template>

<script>
export default {
    data(){return {msg: 'こんにちは!'}}
}
</script>

<style>.message{color: #42b983;}</style>
```

--

### serve コマンドを利用したビルドの実行2
- ビルドして表示するコマンドを実行

```shell
# 単一ファイルコンポーネントの同じディレクトリに移動
$ cd /users/vuejs-primer/sfc/
# ビルドして表示
$ vue serve hello.vue --open
```

--

### serve コマンドを利用したビルドの実行3

- hello.vue ファイルをビルド
- 1つの JavaScript ファイルにバンドル
  - `Vue.js の本体`とその他`依存 JavaScript ライブラリ`

--

### --open オプション
- ブラウザでビルド結果を表示
  - VueCLI がローカルでHTTPサーバを起動
  - ビルドした単一ファイルコンポーネントを表示する
    - エンドポイント http://localhost:8080

--

### HTMLの確認

- レンダリングされた単一ファイルコンポーネント
```html
<head>
    <meta charset="utf-8">
    <title>Vue CLI App</title>
    <!-- 挿入されたスタイル -->
    <style type="text/css">
    .message { color: #42b983;}
    </style>
</head>
<body>
    <!-- 描画されたテンプレート -->
    <p class="message">メッセージ：こんにちは！</p>
    <!-- JavaScriptにバンドル化されたファイルを script 要素で読み込む -->
    <script type="text/javascript" src="/app.js"></script>
</body>
```

--

### 6.5.1 動作を押さえる
- JavaScript ファイルへのバンドル化
- HTMLとして描画されたテンプレート
- 挿入されたスタイル
- `script`, `template`, `style` の3つの要素は JavaScript に変換された後 JavaScript, HTML, SCC として動作

| .vueファイル | バンドル後 | 描画       |
|--------------|------------|------------|
| `<script>`   | JavaScript | JavaScript |
| `template`   | JavaScript | HTML       |
| `<style>`    | JavaScript | CSS        |

--

### JavaScript ファイルへのバンドル化
- バンドルしたファイルは、script 要素で読み込むことで、動作する

--

### HTML として描画されたテンプレート
- `<template>`ブロックのテンプレートが、Body 要素以下に `HTML`として描画される
- 実際には HTML ファイルには記載されていない
  - 単一ファイルコンポーネント→コンポーネントオブジェクト→JavaScriptで配置

--

### 挿入されたスタイル
- `CSS`もJavaScript化される
- `<style>`ブロックで定義したスタイルは、 JavaScript によって head 要素内に `<style>` として挿入されている

---

## 6.6 単一ファイルコンポーネントの機能

- 外部ファイルのインポート
- スコープ付き CSS
- CSS モジュール

--

### 外部ファイルのインポート
- src 属性で外部ファイルをインポートできる
  - src にはインポート対象ファイルのパスを指定
- 既存の資産を流用できる

```html
<template src="./template.html"></template>
<script src="./script.js"></script>
<style src="./style.css"></style>
```

--

### 6.6.2 スコープ付きCSS
- `<style>` ブロックの適用範囲を単一ファイルコンポーネントのみに制限する
- CSS 同士の干渉を防ぐための記述方法を意識する手間、必要性を省略できる
  - 通常 BEM 等の記法が用いられる

--

### スコープ付き CSS のレンダリング

- コンポーネント要素、スタイル要素にカスタムデータ属性が付与される
  - `data-v-` ではじまるハッシュ値（一意なスコープID）で構成

```html
<html>
  <head><style>
      .message[data-v-3bcf9374] {color: #42b983;}
  </style></head>
  <body>
    <p data-v-3bcf9374 class="message">こんにちは！</p>
  </body>
</html>
```

--

### 複数の`<style>`ブロックの定義

- カプセル化されたスタイルとカプセル化されていないスタイルを同時に持つことができる

```shell
vue serve root.vue --open
```

--

### スタイルの注意事項
- class 属性を指定すると、カプセル化が有効にならない場合がある
  - 子コンポーネントの class 属性は、コンポーネントのルート要素にも追加される
  
```html
<!-- Bar.vue の内容を更新して確認 -->
<div id="root">
    <h1 class="header">Rootコンポーネント</h1>
    <p>これはRootコンポーネントです。</p>
    <div data-v-2a9ec0b0="" class="foo">
        <h1 data-v-2a9ec0b0="" class="header">Foo</h1>
        <p data-v-2a9ec0b0="">これはFooコンポーネントです。</p>
        <div data-v-0143bf16="" class="bar">
            <h1 data-v-0143bf16="" class="header">Bar</h1>
            <p data-v-0143bf16="">これはBarコンポーネントです。</p>
            <div data-v-2a9ec0b0="" data-v-0143bf16="" class="foo header">
                <h1 data-v-2a9ec0b0="" class="header">Foo</h1>
                <p data-v-2a9ec0b0="">これはFooコンポーネントです。</p>
            </div>
        </div>
    </div>
</div>
```

--

### CSSモジュール

--

### 多言語実装のサポート

--

### column カスタムブロック

--

### column カスタムブロックの定義
