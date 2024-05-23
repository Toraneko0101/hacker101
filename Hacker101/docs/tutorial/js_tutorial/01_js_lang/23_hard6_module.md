# クイズ(上級6:モジュール)

## Q123 モジュールについて知っていますか?

??? success
    ### モジュール
    - システムを構成する要素のこと
    - 単なる1つのファイル

## Q124 古いスクリプトで使われているモジュールシステムについて知っていますか?

??? success
    ### 2014年以前
    - 言語レベルのモジュール構文が存在していなかった
    - そのため幾つかのサードパーティ製のものが使われた

    ### 例
    - `AMD`: もっとも古いモジュールシステムの1つ。`require.js`で実装されていた
    - `CommonJS`:`Node.js`サーバ用のモジュールシステム
    - `UMD`: `AMD`や`CommonJS`と互換性あり


## Q125 言語レベルのモジュール構文について知っていますか?

??? success
    ### export
    - ファイルの外部からアクセス可能となる変数や関数にこのキーワードを付ける

    ### import
    - 他のモジュールから機能を使えるようにするためのキーワード

    ### 使用例

    ```js
    // 📁 sayHi.js

    export function sayHi(user){
      console.log(`Hello, ${user}`);
    }
    ```

    ```js
    // 📁 main.js

    import {sayHi} from "./sayHi.js";

    console.log(typeof sayHi); //function
    console.log("John"); //Hello, John
    ```

    ### ブラウザに取り入れる場合
    - モジュールは特別なキーワードと機能を提供するため、`<script type="module">`属性が必要となる

    ```html
    <!--📁 index.html--> 

    <script type="module">
      import {sayHi} from "./say.js";
      document.body.innerHTML = sayHi("John"); 
      //window.sayHi = sayHi;
    </script>
    ```

    ```js
    // 📁 say.js

    export function sayHi(user){
      return `Hello ${user}!`;
    }
    ```

    !!! warning
        - モジュールは`HTTP(S)`経由でのみ動作する
        - `file:///`では試せない
        - ローカルサーバ等を使って試すとよい



## Q126 通常のスクリプトとモジュールの違いが分かりますか?

??? success
    ### 常に`use strict`
    - 未定義変数への代入はエラーとなる

    ```html
    <script type="module">
      a = 5; //Uncaught ReferenceError: a is not defined
    </script>
    ```

    ### 最上位の`this`はundefinedになる

    ```html
    <script type="module">
      alert(this); //undefined
    </script>
    ```

    ### モジュールレベルのスコープ
    - モジュールは独自のスコープを持っており、モジュール内の最上位の変数や関数は、他のスクリプトからは見えない

    ```html
    <!--📁 index.html--> 

    <script type="module", src="user.js"></script>
    <script type="module", src="hello.js"></script>
    ```

    ```js
    // 📁 user.js
    
    //exportが必要
    let user = "Neko";
    ```

    ```js
    // 📁 hello.js

    //importが必要
    alert(user); //Error
    ```

    ### 例

    ```html
    <script type="module">
      let user = "John";
    </script>

    <script type="module">
      //違うmodule内であり、export/importしていないので使えない
      alert(user);
    </script>
    ```

    ### 開発者ツールでモジュールの変数にアクセスしたい
    - 上記の`script`タグ内で`window.sum = sum`などとするとよい(デバッグ目的に使う)

## Q127 モジュールコードは初回import時のみ評価される。この意味とその注意点が分かりますか?

??? success
    ### 複数のスクリプトでimport
    - モジュールの読み込みに副作用がある場合、初回のみその副作用は実行される

    ```js
    // 📁 index.js
    alert("Module is evaluated");
    ```

    ```js
    // 📁 hoge1.js
    import "./alert.js"; //Module is evaluated
    ```

    ```js
    // 📁hoge2.js

    //既にモジュールは1度読み込まれているため表示されない
    import "./alert.js"; 
    ```

    ```html
    <script type="module" src="./hoge1.js"></script>
    <script type="module" src="./hoge2.js"></script>
    ```

    ### 例

    - モジュールがオブジェクトを`export`

    ```js
    // 📁 admin.js
    export let admin = {
      name : "John"
    };
    ```

    ```js
    // 📁 hoge1.js

    import {admin} from "./admin.js";
    admin.name = "Pete";
    ```

    ```js
    // 📁 hoge2.js
    import {admin} from "./admin.js";
    console.log(admin.name); //Pete
    ```

    ### hoge2.jsで変更内容が閲覧できている理由
    - モジュールが1度だけ実行されるから
    - 初回の`import`でモジュールが`export`されると、その後`export`されたものはすべての`import`しているモジュールで利用される
    - 異なるコピーが作られるわけではない

    !!! warning
        - ローカルサーバで実行していると、以前のファイル読み込みが残っていることがある
        - 開発者サーバに不明なエラーが表示されるようなら、一度キャッシュをクリアする事(一敗)

    
## Q128. モジュールが1度だけ読み込まれることを生かして、オブジェクトのセットアップを行うことができますか?

??? success

    ### セットアップとモジュール
    1. モジュールがいくつかの構成手段を`export`
    2. 初回`import`時に初期化を行い、そのプロパティへ書き込み
    3. 以降の`import`では初期化されたモジュールを使用

    ### 例
    - `init.js`で`config`オブジェクトを初期化する

    ```js
    // 📁 admin.js

    export let config = {};
    export function sayHi(){
      console.log(`Ready to serve, ${config.user}`);
    }
    ```

    ```js
    // 📁init.js

    import {config} from "./admin.js";
    config.user = "Pete";
    ```

    ```js
    // 📁 hoge1.js

    import {sayHi} from "./admin.js";
    sayHi(); //Ready to serve, Pete!;
    ```

    ```html
      <!-- 📁 index.html -->
    <script type="module" src="./init.js"></script>
    <script type="module" src="./hoge1.js"></script>
    ```

## Q129 import.metaを使うと、現在のモジュールに関する情報が取得できることを知っていますか?

??? success
    ### import.meta
    - 現在のモジュールに関する情報を含む
    - ブラウザの場合、スクリプトの`url`
    - インライン上であれば、現在のウェブページが含まれる

    ```html
    <script type="module">
        console.log(import.meta); 
        //{url: 'http://localhost:8080/test/index.html', resolve: ƒ}
    </script>
    ```

    ```js
    // 📁init.js
    import {config} from "./admin.js";
    config.user = "Pete";
    console.log(import.meta)
    //{url: 'http://localhost:8080/test/init.js', resolve: ƒ}
    ```

## Q130 モジュールスクリプトが常に遅延されることを知っていますか?

??? success
    ### defer
    - `<script>`タグの属性として`defer`をつけると、`HTML`ドキュメントが完全に読み込まれるまで、読み込みを待つことになる
    - モジュールスクリプトの場合、明記しなくとも常に`defer`属性を指定したのと同じタイミングで読み込まれる

    ### 例
    - 後から読み込まれるため、後から書かれた`HTML`要素が見えている

    ```html
    <script type="module">
      alert(typeof button); //object(undefinedのalertの後で表示)
    </script>

    <script>
      alert(typeof button); //undefined
    </script>

    <!--id属性を用いてwindow.buttonという形でアクセス可能-->
    <button id="button">Button</button>
    ```

    ### 上記の特徴から見る注意点
    - モジュールを使うと、JSが読み込まれる前にHTMLドキュメントが表示される
    - そのためボタンをクリックしたが、JSが実装されておらず動かないということが起こりくる
    - 混乱を避けるため`JS`が読み込まれるまで、ローディングを行う、または透明なオーバレイを表示するなどの対策が考えられるられる

## Q131 モジュールスクリプトの場合、インラインスクリプトでAsyncを使えることを知っていますか?

??? success
    ### `<script async type="module">`
    - スクリプトの読み込みを非同期に行う
    - ページの解析はブロックしない
    - が、`HTML`の解析が終わるまで待つこともない。スクリプトが利用可能になったらすぐに実行する

    ### 使用箇所
    - `HTML`のレンダリングと関係のないJSスクリプト
    - たとえば、カウンタや広告、ドキュメントレベルのイベントリスナーなど

    ### 題意
    - 通常のスクリプトの場合、インラインスクリプトに対して、`async`属性を追加しても効果を持たない
    - モジュールスクリプトなら可能
    - 外部スクリプトならいずれの場合でも可能

    ### 使用例

    ```html
    <!-- 非同期に読み込まれる -->
    <script async type="module">
      import {counter} from "./hoge.js";

      coutner.count();
    </script>
    ```

## Q132 外部モジュールスクリプトと外部スクリプトの重大な2つの違いを知っていますか?

??? success
    ### 実行回数

    - 外部スクリプト

    ```js
    //2回実行される
    <script src="test.js"></script>
    <script src="test.js"></script>
    ```

    - 外部モジュールスクリプト

    ```js
    //1回だけ実行される
    <script type="module" src="test.js"></script>
    <script type="module" src="test.js"></script>
    ```

    ### CORSヘッダ
    - 外部スクリプト：必要ない
    - 外部モジュールスクリプト：必要
    - `Access-Control-Allow-Origin: <domain>`がない限り、ブラウザはスクリプトの実行をブロックする

## Q133 ブラウザによるimportではbareモジュールが許可されていないことを知っていますか?

??? success
    ### bareモジュール
    - 相対パスでも絶対パスでもない、モジュールパスのこと

    ### 例

    - 失敗例

    ```js
    import { config } from "admin.js";
    //Uncaught TypeError: Failed to resolve module specifier "test.js". Relative references must start with either "/", "./", or "../".
    ```

    - 成功例

    ```js
    import {config} from "./admin.js"
    ```

    ### 注意点
    !!! warning
        - `Node.js`やバンドルツールのような環境では、むき出しのモジュールを使うことができる
        - そのため今のところ(2024年1月)、これはブラウザ由来の問題である

## Q134 type="module"を理解しないような古いブラウザに対応したい場合の記法を知っていますか?

??? success
    ### nomodule
    - モジュールに対応しているブラウザではこの属性がついたスクリプトは実行されない
    - つまり、古いモジュールに対応していないブラウザでのみ動作する

    ### 古いブラウザはどのように解釈するか
    - 未知の`type`属性であった場合、処理をスキップする

    ### 例

    ```html
    <script type="module">
      alert("モダンブラウザで動作")
    </script>

    <script nomodule>
      alert("古いブラウザで動作")
    </script>

    ```

## Q135 モジュールバンドラを使う利点について知っていますか?

??? success
    ### 利点その1(ファイルまとめ)
    - 複数のJSファイルの依存関係(import/export)を解析して、1つのファイルとしてビルドする
    - 設定次第で複数のファイルにすることも可能

    ### 利点その2(最適化)
    - 到達不能コードの削除
    - 未使用の`export`の削除
    - `console`や`debugger`等の開発固有の文の削除
    - `minify`化(変数を短い名前に置換して、スペースも削除)

    ### 置き換え
    - 最先端のJS構文を`Babel`等を用いて古い構文に変換

    ### typeについて
    - ビルドされたスクリプトを用いる場合`type=module`は必要ない。通常のスクリプトの様に記載可能

    ### 使用例

    ```html
    <!--ビルドした結果bundle.jsを得たとする-->
    <script src="bundle.js"></script>
    ```

    ### モジュールバンドラの例(Webpack)
    - `Node.js`環境が必要
    - `src`フォルダ内にバンドル前のファイルを入れ、`dist`フォルダ内に出力
    - `dist`フォルダ内のスクリプトを使うという使い方が一般的
    - 後継として`Turbopack`や`Vite`などがある


## Q136 宣言とは別にexportを行える。この事実を知っていますか?

??? success
    ### 基本的なexport手法

    ```js
    //配列
    export let months = [1,2,3,4,5,6,7];
    //定数
    export const THIS_YEAR = 2024;
    //クラス
    export const User{
      constructor(name){
        this.name = name;
      }
    }
    //関数宣言
    export function sayHi(user){
      console.log(`Hello, ${user}`);
    }
    ```

    ### 宣言とは別にexportする
    - `export {}`の中に、`export`したい変数のリストを書く
    - 先頭に`export {}`を持ってくる場合は、下で関数式を使っていないか注意する事

    ```js
    function sayHi(user){
      console.log(`Hello, ${user}`);
    }

    function sayBye(user){
      console.log(`Bye-bye, ${user}`);
    }

    export {sayHi, sayBye};
    ```

## Q137. import as や export asを使うと、異なる名前でimport/exportが可能なことを知っていますか?

??? success

    ### export as

    ```js
    // 📁 say.js
    function sayHi(user) {
      console.log(`Hello, ${user}`);
    }

    function sayBye(user) {
      console.log(`Bye-bye, ${user}`);
    }

    //sayHiHi, sayByeByeという名前でexport
    export {sayHi as sayHiHi, sayBye as sayByeBye}
    ```

    ### import as

    ```js
    // 📁 main.js

    //hi, byeという名前でimport
    import {sayHiHi as hi, sayByeBye as bye} from "./say.js";

    hi("John"); //Hello, John!
    bye("John"); //Bye, John!
    ```

## Q138 `import * as <obj>`を利用して、すべてをimportできることを知っていますか?

??? success
    ### 全てをimport

    ```js
    // 📁 say.js

    function sayHi(user) {
      console.log(`Hello, ${user}`);
    }

    function sayBye(user) {
      console.log(`Bye-bye, ${user}`);
    }

    export { sayHi as hi, sayBye};
    ```

    ```js
    // 📁 main.js

    import * as say from "./say.js";

    say.hi("John"); //異なる名前でimportしているので
    say.sayBye("John");
    ```

    ### 問題点
    - `import {sayHi}`などと明示することで、より短い名前となる
    - 明示的に`import`すると、どこで用いられているか分かりやすい

    !!! info
        ### 全てを読み込むと読み込みは遅くなる?
        - ならない
        - `weppack`等のビルドツールは、未使用なものを削除した後で、最適化する。

## Q139 export defaultとexportの違いが分かりますか?

??? success
    ### export defaultの使い方

    ```js
    // 📁 user.js

    export default class User{
      constructor(name){
        this.name = name;
      }
    }
    ```

    ```js
    // 📁 main.js

    import User from "./user.js";
    new User("Neko");
    ```

    ### 説明
    - `import`するとき、波括弧はなし

    ### 別名でimportしたい場合
    - 単に`import`の後の名前を変更すればいい

    ```js
    // 📁 main.js
    import MyUser from "./user.js";
    new MyUser("Neko");
    ```

    ### 名前がないクラス、関数、値もexport可能

    - クラス

    ```js
    export default class{
      constructor();
    }
    ```

    - 関数

    ```js
    export default function (user){
      alert(`Hello, ${user}`);
    }
    ```

    - リスト等

    ```js
    // 📁 hoge.js
    export default [1,2,3,4,5];
    ```

    ```js
    import list from "./hoge.js";
    console.log(list[0]); //1
    ```

    ### 違い一覧

    |                | 名前付きexport               | default export              |
    | -------------- | ---------------------------- | --------------------------- |
    | export         | export class User{}          | export default class User{} |
    | import         | import {User as MyUser} from | import MyUser from ...      |
    | 複数export     | できる                       | できない                    |
    | 名前なしexport | 不可                         | 可                          |

    ### 名前付きとdefaultを混在させる
    - できる(推奨はされない)

    ```js
    // 📁 test.js

    export default [1, 2, 3, 4, 5];

    export let arr1 = [1, 2, 3, 4, 5];
    export let arr2 = Array.from({ length: 10 }, (_, i) => i);
    ```

    ```js
    // 📁 main.js

    import list, { arr1, arr2 } from "./say.js";

    console.log(list[0]) //1
    console.log(arr1); //[1,2,3,4,5]
    console.log(arr2); //[0,1,2,3,4,5,6,7,8,9]
    ```

    ### 注意点
    !!! warning
        - `import`時に独自の名前を付ける必要があるので、複数人で開発を行っていると誤解を生じる
        - ファイル名とimport時の名前を一緒にするべきという慣例もある
        - 煩わしく感じて、常に名前付きexportを用いるものもいる

## Q140 defaultというキーワードで、default exportが参照できることを知っていますか?

??? success

    ### *ですべてをimportしたいときに役立つ

    ```js
    // 📁 test.js

    export default [1, 2, 3, 4, 5];

    export let arr1 = [1, 2, 3, 4, 5];
    export let arr2 = Array.from({ length: 10 }, (_, i) => i);
    ```

    ```js
    import * as list from "./test.js";

    console.log(list.default); //[1,2,3,4,5];
    ```

    ### 名前付きexportと混在したい時の別解

    ```js
    import {default as list, arr1, arr2} from "./say.js";
    ```

## Q141 再エクスポートについて知っていますか

??? success
    ### export ... from ...
    - `import`した直後に`export`可能

    ### 使いどころ
    - パッケージの機能を単一のファイルから公開したい場合
    - たとえば`index.js`にすべての機能を集めたいとする
    - その場合、`index.js`ですべての機能を`import`して、`export`したい。こんな時、一文で書けるので役に立つ

    ### 使用例

    ```js
    // 📁 index.js

    export {login, logout} from "./helper.js";
    export {default as User} from "./user.js";
    ```

    ### export ... from ...　を使わなかった場合

    ```js
    // 📁 index.js
    import {login, logout} from "./helper.js";
    export {login, logout}

    import User from "./user.js";
    export {User};

    //二度手間で面倒くさい
    ```

## Q142 export User from "./hoge.js"が構文エラーになることを知っていますか?

??? success
    ### default exportの再エクスポート
    - 明示的に`{default as ...}`とする必要がある

    ### `export * from "./hoge.js`とした場合
    - `*`に`default`は含まれない
    - つまり、`*`は名前付きエクスポートのみを再エクスポートする

    ### 全てを再exportしたい場合

    ```js
    export * from "./hoge.js";
    export {default} from "./hoge.js"
    ```

    !!! info
        ### importの時は大丈夫
        - 以下は名前付き`export`、`default export`ともに`import`する
        - つまり、`import/export`は非対称

        ```js
        import * as obj from "./hoge.js";

        ```

## Q143 条件に応じて何かをimportしたい場合、Dynamic importsが使えることを知っていますか?

??? success
    ### 通常のexport/import
    - `static`
    - モジュールのパスは文字列でなければならない
    - 関数呼び出しもできない
    - 条件に応じた`import`や実行時の`export`は不可
    - `static`なのでコード構造を分析でき、未使用の`export`等を除去できる

    ### import(module)
    - モジュールを読み込み、モジュールが持つすべての`export`を含む(default exportも含む)`promise`(モジュールオブジェクト)を返す

    ### 使用例(then/catch)

    ```js
    // 📁 test.js
    export default [1, 2, 3, 4, 5];

    export let arr1 = [1, 2, 3, 4, 5];
    export let arr2 = Array.from({ length: 10 }, (_, i) => i);
    ```

    ```js
    let modulePath = prompt("Moudle path?"); // ./test.js

    import(modulePath)
      .then(obj => console.log(obj.arr1[0])) //1
      .catch(err => console.log(err));
    ```

    ### 使用例(async/await)

    ```js
    async function load(){
      let list = await import("./test.js");
      console.log(list.default); //[1,2,3,4,5];
      console.log(list.arr1[0]); //1
    }

    load();
    ```

    - 分割代入を行った場合

    ```js
    async function load() {
      let { arr1, arr2, default: list } = await import("./test.js");
      console.log(arr1); //[1,2,3,4,5];
      console.log(arr2);
      console.log(list);
    }

    load();
    ```

    !!! warning
        - `default`は`JS`のキーワードであるため、異なる名前で用いるようにしている

    !!! info
        ### script type="module"とdynamicインポート
        - `Dynamic`インポートは、通常のスクリプトで動作するため、`script type="module"`は必要ない