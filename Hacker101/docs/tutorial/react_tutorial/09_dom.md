# 09-仮想DOMについて理解する

## 注意点
- このページはReactのレンダリング処理について、より理解するためのものです
- 仮想DOMの観点から考えると、08について理解が進むと思われます
- 興味がない場合、読む必要はありません

## 用語

### DOM
- Document Object Modelの略。
- Webブラウザは、Webサーバから受け取ったHTMLドキュメントを解析し、要素やテキスト、属性をオブジェクトに変換する。
- オブジェクトはいくつかのプロパティを持ち、互いを参照しあう
- オブジェクト同士のつながりを表現したものをDOMと呼ぶ
- つまりDOMはブラウザがWebページを解釈したものといえる
### DOMツリー
オブジェクト同士のつながりをツリー状に表現したもの。ツリーとしての表現が一般的なのでDOMツリーを単にDOMと呼んだりする

### DOM API
- JS側からHTMLを操作するためのインターフェース
- 例として`document.getElementById()`等がある

### Node
- 多くのDOM APIオブジェクトの継承元
- 例として`document`オブジェクト, `element`オブジェクト

### Documentオブジェクト
- ブラウザ固有の操作を行うためのオブジェクト
- DOM(Document object model)を操作するためのメソッドが含まれている
- DOMに対してDOM APIを用い、HTMLドキュメントを操作する。これを __リアルDOM__ と呼ぶ

=== "例"

    ```html
    <script>
    // 1. `document.getElementById`で`root`という`id`を持つ、`Node`をDOM全体から検索する
    const target = document.getElementById("root");

    // 2. 取得した`Node`に対して`innerHTML`を用い、中身を書き換え
    target.innerHTML = "変更後のテキスト";
    </script>
    ```

## リアルDOM操作とその問題点

### 概要
- WebブラウザはHTMLドキュメントをレンダリングすることで画面を描画する。この際、以下の手順が踏まれる
- なお、レンダリング処理はDOM操作イベント(例として`addEventListener()`)等によって発火する
### レンダリング手順
1. HTMLの解析
2. レンダリングツリーの構築（DOMツリーやCSSツリーから構築される）
3. レイアウトの処理
4. 描画

### なぜリアルDOMのレンダリングコストは高いか
1. 関連のない部分も再描画されるため(JQuery等を用いた場合、これは顕著)
2. 子ノードを複数持つ、親ノードが変更された場合、一部の子に変化がなくとも親ノード全体を再構築する必要があるため

## 仮想DOMについて

### 概要
- バーチャルなDOMで、本来は軽量なJSオブジェクト
- これはブラウザ上ではなく、メモリ上に保存される

### レンダリング手順(差分検知)
1. UIが変更される
2. 新たな仮想DOM構造体が生成される
3. メモリに保存されている、変更前の仮想DOM構造体と比較
4. 差分検知がメモリ上で素早く行われる
5. 抽出した差分をリアルDOMに適用
6. ブラウザは必要な分のレンダリングを行う
<br>
⇒ __関係ない部分はレンダリングされない__

### 効率性以外もよい
- DOMノードは、レンダー間で違いがない場合変更されないため、通常よりチラつきを減らすことができる。
- Reactの場合、仮に親コンポーネントが再レンダーされたとして、その場合、 __子も再レンダーされる(子の関数が呼び出される)が、子のDOMに変化がない場合、子要素は再描画されない。__

### リアルDOMと仮想DOMの相性
- 仮想DOMを用いているにもかかわらず、リアルDOMの操作を直接行うと、関係性が壊れることがある
- そのためリアルDOMを操作するjQueryはReactと相性が悪い

