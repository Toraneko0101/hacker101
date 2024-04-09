# 08-コンポーネントが画面に表示されるまで

## 1.レンダーがトリガーされる

### 初回レンダーの場合
- アプリが開始されるときは、何かしらの手順を踏み、（一回目の）レンダーが行われるよう引き金を引く必要がある
- 引き金、つまりトリガーはしばしばフレームワークにより隠蔽される
- 自力でトリガーを引く場合は以下の様に書ける

=== "index.js"

    ```javascript
    import App from "./App.js";
    import {createRoot} from "react-dom/client";

    const root = createRoot(document.getElementById("root")) //(1)
    root.render(<App />) //(2)
    // (3)
    ```

    1. コンポーネントを配置したい箇所を定めたら、その親となる DOMノード[^1] を`createRoot`でrootとする。
    2. rootを作成したらroot.renderを行う。これがトリガーとなり、引数に設定したReactコンポーネントが呼び出され(レンダーされ)、Reactは`<App \>`以下のDOMの管理を行えるようになる。
    3. 上記の結果、`<App \>`コンポーネントは、ID属性がrootであるDOMノードの子要素として配置される。
    ```html
    <html>
      <body>
        <div id="root">
        <!-- ここに<App />が配置される -->
        </div>
      </body>
    </html>
    ```

=== "App.js"

    ```javascript
    const App = () =>{
      return(
        <>
          <div>hoge</div>
        </>
      );
    }

    export default App;
    ```

### 再レンダリングの場合
- 前の項で述べたように、`setState`関数を使ってstateを更新すると、それが引き金となり、再度`root.render()`が行われる
- 具体的にはコンポーネントのstateが`setState`で更新された時、レンダー命令がqueueに追加される仕組みになっている。そのためFIFO(First In First Out)により、最初にトリガーされたレンダーが、最初に実行される。

## 2.Reactがコンポーネントをレンダーする
### 概要
- `root.render()`により、レンダーがトリガーされると、Reactはコンポーネントを呼び出し、画面に表示する内容を把握する。（把握するだけでまだ表示はしない）
- レンダーとは、Reactがコンポーネントを呼び出すことを意味する

### 初回レンダー時
- Reactはrootコンポーネント(上記例でいうと`<App />`)を呼び出す
  
### 再レンダー時
- どのstateが更新されたかによって変わる。たとえば`<Hoge />`内のstateが更新された場合、Reactは`<Hoge />`を呼び出す

### 共通点
- このプロセスは再帰的に発生する
- つまり、`<App />`のreturn内に、`<Hoge />`が存在する場合、そのコンポーネントもReactはレンダーする。そうしてネストされたコンポーネントを辿り、全ての葉（子がいないコンポーネント）に行きつくまで、Reactは再帰的に画面に表示される内容を把握し続ける

=== "index.js"

    ```javascript
    import App from "./App.js";
    import {createRoot} from "react-dom/client";

    const root = createRoot(document.getElementById("root"));
    root.render(<App />); // (1)
    ```

    1. 1.`<App />`がレンダーされる

=== "App.js"

    ```javascript
    const App = () =>{
      return(
        <>
          <Hoge /> //(1)
          <Hoge /> //(2)
          <Hoge /> //(3)
        </>
      );
    }

    export default App;
    ```

    1. 2.`<Hoge />`がレンダーされる
    2. 2.`<Hoge />`がレンダーされる
    3. 2.`<Hoge />`がレンダーされる


### 内容の把握とは?
- 初回時には、作成すべき全てのDOMノードをReactが理解すること(表示はしない)
- 再レンダー時には、前回のレンダーからの差分をReactが計算することを意味する

## 3.DOMを作成,あるいは更新する

### 初回レンダー時
- Reactは`appendChild()`というDOM APIを使用し、DOMツリーを作成する

### 再レンダー時
- step2で計算した差分を活用し、ReactはDOMツリーに対し、必要最低限の変更を行う。
- 差分がない場合、Reactは一切DOMツリーを触らない。よって再描画は行われない

### 差分検知の仕組み・効率性と仮想DOMについて
- 時間がない場合、読まなくてもよい
[仮想DOMについて理解する](./09_dom.md)

## 4.ブラウザによる再描画
- ブラウザは初回時はDOMツリーを用い、初回時以外は差分に則り画面を再描画する
- 仮想DOMを用いているため、すべては再描画されず、パフォーマンスの低下を防ぐことができる

[^1]: Document Object Modelの略。JS側からHTMLを操作するためのインターフェースであり、データ構造。Webブラウザは、Webサーバから受け取ったHTMLドキュメントを解析し、DOMと呼ばれるデータ構造に変換している。

