# クイズ(新機能)

## 対象内容/読者
??? info
    - 昔JSについて触れていたが、数年程度現場を離れている
    - ここでは、2019年以降に追加された機能について取り扱う
    - ES6, ES7, ES8, ES9までの機能についてはクイズ(上級)までに記載
    - 目安としてES8の目玉機能は、async/awaitなので、この機能を知らなければ上級の方を先に閲覧すること

    ??? info
        下記のものは便宜上、クイズ(初級)などに記載した
        - Promise.allSettled(): ES2020
        - ?? : ES2020

## Q0 Node.jsが新機能に対応しているか調べたい時

??? info
    - [node.green](https://node.green/);

## Q1 ES2023で配列の非破壊操作が可能になったことを知っていますか?

??? info
    ### toSorted()
    - 元の配列を編集せずに新たな配列を作成しソート
    ```js
    const list1 = [3,1,2];
    const list2 = list1.toSorted();
    console.log(list1, list2); //[3,1,2], [1,2,3];
    ```

    ### toReversed()
    - 元の配列を編集せずに新たな配列を作成し反転

    ```js
    const list1 = [3,1,2];
    const list2 = list1.toReversed();
    console.log(list1, list2); //[3,1,2], [2,1,3];
    ```

    ### toSpliced(start, deleteCount, item1, item2, ...)
    - 元の配列を編集せず、要素の削除、置換が可能
    - startだけでも可
    - start + deleteCountだけでも可

    ```js
    const list1 = ["a", "b", "c"];
    const list2 = list1.toSpliced(1, 1, "d"); //1番目の要素を1つ削り、そこにdを追加(0の場合、単に追加される)
    console.log(list1, list2); //["a", "b", "c"], ["a", "d", "c"];

    //マイナスの場合は、末尾からカウント
    const list3 = list1.toSpliced(-1, 1, "e");
    console.log(list3); //["a", "b", "e"];

    //要素数を超えている場合は、単なる追加関数となる
    const list4 = list1.toSpliced(100, 40, "f", "g"); // ['a', 'b', 'c', 'f', 'g']
    ```

    ### with(index, value)
    - 第1引数にインデックス番号、第2引数に置換する値を指定
    - 置換に役立つ

    ```js
    const list1 = [1,2,3,4,5];
    const list2 = list1.with(1, 100);
    console.log(list1, list2); //[1, 2, 3, 4, 5], [1, 100, 3, 4, 5]
    ```

## Q2 ES2023で配列の要素を末尾から探索できるようになったことを知っていますか?

??? info

    ```js
    const list = [500, 20, 200, 300, 50, 150];

    //ES2022以前
    const item1 = list.find(item => item < 100);
    console.log(item1); //20

    //ES2023以降
    //条件に合う要素のインデックス番号を前から探索
    const item2 = list.findIndex(item => item < 100);

    //条件に合う要素のインデックス番号を後1から探索
    const item3 = list.findLastIndex(item => item < 100);
    
    console.log(list[item2], list[item3]); //20, 50
    ```

## Q3. ES2023で、ファイルの先頭行にある`#!`がコメントとして扱えるようになったことを知っていますか?

??? info
    - `Node`の場合、`shebang`は元々使えた
    - しかし、`js`にはこれが今まで存在せず、コードの使いまわしができなかった
    - `ES2023`以降は、先頭行の`#!`のみコメントとみなされる

## Q4. ES2022で.at()が追加され、リストの末尾を簡単に参照できるようになったことを知っていますか？

??? info
    ### リストの場合

    ```js
    const array = [1,2,3];
    //console.log(array[-1]); //undefined
    console.log(array.at(-1)); //3
    ```

    ### 実は文字列でも可能

    ```js
    const myString = "あいうえお";
    console.log(myString.at(-2)); //え
    ```

## Q5. ES2022でトップレベルのawaitがasync無しで利用できるようになったことを知っていますか?

??? info
    - 通常

    ```js
    const start = async = () =>{
      const data = await fetch("https://pokeapi.co/api/v2/pokemon/ditto");
      const obj = await data.json();
      console.log(obj);
    }

    start();
    ```
    - 開発者ツールなどで以下の様にテスト可能

    ```js 
    //書き捨てのコードを試すのに便利
    const data = await fetch("https://pokeapi.co/api/v2/pokemon/ditto");
    const obj = await data.json();
    console.log(obj);
    ```

    !!! warning
        - トップレベル以外(つまり、async無しの関数式)の中では利用できない
        - ESModuleとしてしか利用できないので、scriptタグで利用する場合は、`<script type="module">`とする必要がある 

    ### 使用例1
    - 外部ファイルに宣言したモジュールを非同期で読み込む

    ```js
    //Node.jsの場合、拡張子が.mjsだとESModule扱いになる

    //動的モジュール読み込み
    const {someModule} = await import("./someModule.mjs");
    console.log(someModule);

    //実行環境に応じて読み込む
    const {workingList} = await import(`./i18/${navigator.languagte},js`);
    ```

## Q6. ES2022でプライベートのインスタンスフィールド、メソッドが追加されたことを知っていますか?

??? info
    - 接頭辞に`#`を付けると、フィールドはプライベートになる
    - 命名規則の`_`を使う必要はない
    !!! warning
        - 開発者ツールで試している場合は、プライベートが効かないことがあるので、ソースで試す事
        - `#`はTypescript3.8以降でも使える

    ```js
    class MyCounter{
      #count;

      constructor(count){
        this.#count = count;
      }

      #calc(){
        return this.#count * 10;
      }

      say(){
        console.log(this.#calc());
      }

      hasCount(){
        return #count in this;
      }
    }

    const obj = new MyCounter(10);
    obj.say(); //100
    //console.log(obj.#count()); //error
    //console.log(obj.#calc()); //error

    //inを使って外部から存在を確かめることもできない
    console.log(obj.hasCount()); //true
    //console.log("#count" in obj); 
    ```

## Q7. クラスでstaticフィールドが使えるようになったことを知っていますか?

??? info
    ### 従来

    ```js
    class OldClass{
      //...
    }

    OldClass.message = "a";
    console.log(OldClass.message);
    ```
    ### ES2022以降

    ```js
    class MyClass {
      //静的フィールド
      static message = "i";

      //静的メソッド
      static sayHello(){
        console.log(MyClass.message);
      }

      //静的なプライベートフィールド
      static #counter = 1;

      //静的なプライベート関数
      static #getNextId(){
        return MyClass.#counter++;
      }

      //ただのプライベート変数
      #id;

      constructor(){
        this.#id = MyClass.#getNextId();
      }

      getId(){
        return this.#id;
      }
    }

    console.log(MyClass.message);
    const list = [new MyClass(), new MyClass()];
    console.log(list[1].getId()); //2
    ```

    ### 解説
    - 静的なプライベート関数や変数を作成すると、外部からの呼び出しでstaticな値が変えるのを避けることができる。
    - 上記の場合は、内部でIdの値を管理し、初期化以外で変更できないようにしている

## Q8. ES2022でクラスの静的イニシャライザーブロックが利用可能になったことを知っていますか?

??? info
    ### 概要
    - `static{}`のようにキーワード後の、ブロック内にコードを記述する

    ### 使い道
    - クラス宣言のタイミングで、静的変数に値を格納して使いたいときに用いる
    - 静的イニシャライザーブロックは、コンストラクタよりも先に実行されるので都合がいい

    ### 例

    ```js
    class MyClass{
      static #myProperty;

      //静的イニシャライザーブロック
      static{
          //本来はここに複雑な処理が書かれる
          const json = JSON.parse(`{"someField" : "hoge"}`);
          this.#myProperty = json.someField;
      }

      constructor(){
        console.log(MyClass.#myProperty);
      }
    }

    new MyClass(); //hoge
    ```

## Q9. ES2022でObject.hasOwn()が使えるようになったことを知っていますか?

??? info
    - オブジェクトにプロパティが存在するかの確認が容易になる

    ### 従来

    ```js
    const example = {
      property: "hoge"
    }

    console.log(Object.prototype.hasOwnProperty.call(example, "property"));

    //以下だと書き換えられるので非推奨だった
    //またexampleオブジェがhasOwnPropertyというメソッドを持っているとオーバーライドする
    console.log(example.hasOwnProperty("property"));
    ```

    ### ES2022以降

    ```js
    const example = {
      property: "hoge",
    }

    console.log(Object.hasOwn(example, "property")); //true
    ```

## Q10. ES2022でErrorオブジェクトにcauseというフィールドが追加されたことを知っていますか?

??? info
    ### 概要
    - エラーをcatchして、再度エラーを投げる場合に便利
    - 既存の場合、`try-catch`を重ねると、内部のエラー情報が失われてしまっていたので、連結する必要などがあり、不便だった
    - `throw new Error("Error", {cause: error}) //errorは元となるエラーオブジェクト`

    ### 例1
    - エラーBの`cause`にエラーAが入っている
    - `stack`も確認可能

    ```js
    try{
      try{
        throw new Error("エラーA");
      }catch(error){
        throw new Error("エラーB", {cause: error});
      }
    }catch (error){
      console.dir(error);
    }
    ```

    ### 例2
    - コード

    ```js
    async function start(){
      try{
        await load();
      }catch(error){
        console.log(error);
        console.log(error.cause); //内部のエラー情報を取得
      }
    }

    async function load(){
      try{
        const result = await loadJson();
        console.log(result);
      }catch(error){
        //causeに設定したものも取得できる
        throw new Error("読み込みに失敗!", {cause : error});
      }
    }

    async function loadJson(){
      let data;
      try{
        data = await fetch("https://pokeapi.co/api/v2/pokemon-species/aegislash");
      }catch(error){
        //オフラインの場合
        throw new Error("fetchに失敗しました", {cause : error});
      }

      if(data.ok === false){
        //404
        throw new Error("ファイルの読み込みに失敗しました");
      }

      let json;
      try{
        json = await data.json();
        return json;
      }catch (error){
        throw new Error("JSONデータの展開に失敗しました", {cause: error});
      }
    }
    ```

    - オフラインにした場合
    ```
    GET https://pokeapi.co/api/v2/pokemon-species/aegislash net::ERR_NAME_NOT_RESOLVED

    hoge.js:5 Error: 読み込みに失敗！
        at load (hoge.js:15:15)
        at async start (hoge.js:3:9)
    hoge.js:6 Error: fetchに失敗しました。
        at loadJson (hoge.js:25:15)
        at async load (hoge.js:12:24)
        at async start (hoge.js:3:9)
    ```
    - URLを存在しないものにした場合
    ```
    hoge.js:22 GET https://pokeapi.co/api/v2/pokemon-species/aegislashs 404 (Not Found)
    Error: 読み込みに失敗！
    at load (hoge.js:15:15)
    at async start (hoge.js:3:9)

    hoge.js:6 Error: ファイルの読み込みに失敗しました。
    at loadJson (hoge.js:30:15)
    at async load (hoge.js:12:24)
    at async start (hoge.js:3:9)
    ```

## Q11. ES2022で正規表現にマッチインデックス機能(`/d`フラグ)が追加されたことを知っていますか?

??? info
    ### 概要
    - `/d`を使うと、正規表現にマッチした部分文字列のうち、先頭と末尾のインデックスについて、追加情報を確認可能

    ```js
    const text = "今日の夕食代:500円";
    //dフラグを追加
    const regexp = /今日の夕食代:(?<digit>\d{3})円/dg;

    for(const match of text.matchAll(regexp)){
      console.log(match);
    }
    ```
    - 結果

    ```js
    [
      '今日の夕食代：500円',
      '500',
      index: 0,
      input: "今日の夕食代：500円",
      groups: { digit: '500' },
      indices: {
        [ 0, 11 ],
        [ 7, 10 ],
        groups: { 
          digit: [7, 10]
        }
      }
    ]
    ```

## Q12. ES2021で論理代入演算子(`||=`など)が追加されたことを知っていますか?

??? info
    ### `||=`
    - `x || x = 5`等と同じ
    - よって、`x`が偽の場合のみ、右辺が評価される

    ### `&&=`
    - `x && x = 5`等と同じ
    - よって、`x`が真の場合のみ、右辺が評価される

    ### `??=`
    - `x ?? x = 5`等とな時
    - よって、`x`がnullかundefinedの場合のみ、右辺が評価される
    - ??について知らない場合は、クイズ(中級)を参照のこと。これもES2020で追加された

    ```js
    var x;
    x ||= 12; //xが偽の場合のみ、値を代入
    console.log(x); //12
    x ||= 9;
    console.log(x); //12

    x &&= 9; //xが真の場合のみ、値を代入
    console.log(x); //9

    x ??= 5; //xがnull or undefinedの時、値を代入
    console.log(x); //9
    ```

## Q13. ES2021で、数値の桁区切りとして`_`を使えるようになったことを知っていますか?

??? info

    ```js
    console.log(1_234 * 12); //14808
    ```

## Q14. ES2021で、見つかったものをすべて置換するreplaceAll()が追加されたことを知っていますか?

??? info
    - `replace`より強力な分、正規表現は使えない

    ### 復習(replace)
    - /.../gを指定すると、見つかったものをすべて置換する

    ```js
    "AAA".replace(/A/, "X"); //XAA
    "AAA".replace(/A/g, "X"); //XXX
    ```

    ### replaceAll
    - 正規表現が使えないので、`//`のような記法はできない

    ```js
    "AAA".replaceAll("A", "X");
    ```

## Q14. ES2021で追加された、いずれかのタスクが完了したら終了されるPromise.any()について知っていますか?

??? info
    ### 概要
    - プロミスのうち、いずれかが`fulfilled`になった場合、最初の値でresolveされる。すべて拒否された場合、AggregateErrorが返る

    ```js
    const promise1 = Promise.reject(0);
    //setTimeoutの第三引数は第一引数にわたる
    const promise2 = new Promise((resolve) => setTimeout(resolve, 100, "quick"));

    const promise3 = new Promise((resolve) => setTimeout(resolve, 500, "slow"))

    const promises = [promise1, promise2, promise3];
    //最初にresolveで返るのは、quick
    Promise.any(promises).then((value) => console.log(value));


    ```

## Q15. ES2021で追加されたオブジェクトの弱参照を生成するWeakRef()について知っていますか?

??? info
    - 通常、すべての参照が切れないとガベージコレクションは行われない
    - WeakRef()を用いて弱参照を作った場合、普通の参照（強参照）の数が0になるとガベージコレクションが行われる

    ### 使い道?
    - DOMが削除されたら、変数も`undefined`になるようにする

    ??? failure
        - 以下の例はうまく作用しない
        - 公式を見ても、使用はできるだけ避けるべきと書かれている
        - 少なくとも、製品のような環境での使用は行わない方がいい（ガベージコレクションのタイミングも複雑なので）

        ```html
        <div id="myElement"></div>
        <script>
            const dom = document.querySelector("#myElement");
            let elementRef = new WeakRef(dom);
            let i = 0;
            const intervalId = setInterval(() => {
                const element = elementRef.deref();
                console.log(element);
                if (!element) {
                    console.log("The element has been removed from the DOM.");
                    elementRef = null;
                    clearInterval(intervalId);
                } else {
                    console.log("The element is still in the DOM.");
                    i++;
                }

                if (i >= 5) {
                    dom.remove();
                }
            }, 1000);    
        </script>
        ```

## Q16. ES2020で追加された長整数型(BigInt)について知っていますか?

??? info
    ### 概要
    - `BigInt`は任意の精度を持てる。`2^53`以上の値が欲しいならこれを使う
    - 長整数型の場合、末尾にnがつく

    ```js
    console.log(23456789765432123456787545678656);
    console.log(BigInt(12345)); //12345n;
    ```

## Q17. ES2020で、for ... inでオブジェクトを回したときに順序が保証されるようになったことを知っていますか?

??? info
    - 下記の通り

    ```js
    const obj = {name : "hoge", age: 12};
    for(const o in obj){
      console.log(obj[o]);
      //hoge
      //12
    }
    ```

## Q18. ES2020でオプショナル連結(?.)と呼ばれる記法が可能になったのを知っていますか?

??? info

    ### 従来
    ```js
    //事前チェックが必要だった
    if(response.body){
      console.log(response.body.length);
    }
    ```

    ### ES2020以降
    ```js
    console.log(response.body?.length); //存在しなくともTypeErrorにはならず、undefinedが返る
    ```

## Q19. ES2020でグローバルオブジェクトを意味する、globalThisが追加されたのを知っていますか?

??? info
    ### 概要
    - globalThisはグローバルオブジェクトを示す
    - ブラウザ上のJSでは`window`オブジェクト
    - Node.jsでは`global`オブジェクトと等価

    ### 確認コード

    ```js
    console.log(globalThis === document.defaultView); //true
    ```

## Q20. ES2020で追加されたstr.matchAll()という正規表現の機能について知っていますか？

??? info
    - 正規表現について詳しくない場合は、[工事中]を参照
    - 文字列strの中からregexpにマッチするものをイテレータで返す

    ```js
    for (const elm of "2020-12-31".matchAll(/[0-9]+/g)){
      console.log(elm);
    }
    //['2020', index: 0, input: '2020-12-31', groups: undefined]
    //['12', index: 5, input: '2020-12-31', groups: undefined]
    //['31', index: 8, input: '2020-12-31', groups: undefined]
    ```

## Q21. ES2020で追加されたダイナミックインポートについて知っていますか?

??? info
    ### importが完了した際に、関数を実行可能

    ```js
    import("./module.js").then(mod =>{
      console.log(mod.a1);
    })
    ```

## Q22. ES2020で追加されたexport * as ns from module構文について知っていますか?

??? info
    ### 概要
    - importしたものを直接exportできるようになった

    ### 従来

    ```js
    import * as mod from "./module.js";
    export {mod};
    ```

    ### ES2020以降

    ```js
    export * as mod from "./module.js";
    ```

## Q23. ES2020で追加されたimport.metaについて知っていますか?

??? info
    ### 概要
    - モジュールのメタ情報にアクセス可能

    ### 使用例
    - クエリパラメータやハッシュも含まれることに注意
  
    ```html
    <script type="module", src="my-module.js"></script>
    ```

    ```js
    console.log(import.meta); //{url : "file:///hoge/user/my-module.js" }
    ```


## Q24. ES2019で、try-catchのcatchにおける引数が省略できるようになったことを知っていますか？

??? info
    ### 使い道
    - Errorオブジェクトを使う必要がないが、エラーは検知したい場合

    ```js
    try{
      throw "hoge";
    }catch{
      console.log("Some error");
    }
    ```

## Q25. Symbol.descriptionについて

!!! info
    - 工事中

## Q26. JSONのサロゲートペア文字について

!!! info
    - 工事中

## Q27. ES2019で、toString()で関数定義を文字列に変換する際、内部のコメントや空白も文字列化するようになったことを知っていますか?

??? info
    - 以下の通り

    ```js
    function add(x,y){
      /*加算*/
      return x + y;
    };

    console.log(add.toString());
    ```

    - 出力結果
    
    ```
    function add(x,y){
      /*加算*/
      return x + y;
    }
    ```

## Q28. ES2019でObject.fromEntries()を使って、配列をオブジェクト化できるようになったことを知っていますか?

??? info
    ### 使用例

    ```js
    const obj = Object.fromEntries([
      ["x", 100],
      ["y", 200],
    ])

    console.log(obj); //{x: 100, y: 200}
    ```

## Q29. ES2019でstring.trimStart()/trimEnd()を使って、文字列の前方、後方のみのホワイトスペースを削除できるようになったことを知っていますか?

??? info

    ### 従来
    - 全ての空白が取れてしまっていた

    ```js
    "   ABC   ".trim(); //ABC
    ```

    ### ES2019以降

    ```js
    "   ABC   ".trimStart();  // "ABC   "
    "   ABC   ".trimEnd();    // "   ABC"
    ```

## Q30. ES2019でarray.flat()を使い、多次元配列を低次元にできるようになったことを知っていますか?

??? info
    ### flat(使用例)
    - 引数(depth)で、何次元までの配列をフラット化するか指定

    ```js
    const arr = [
      [
        [1,2],
        [3,4],
      ],
      [5,6],
    ]

    console.log(arr.flat(2)); //[1,2,3,4,5,6]
    console.log(arr.flat(1)); //[[1,2], [3,4], 5,6]
    ```

    ### flatMap(callback)
    - こちらもES2019で追加された機能
    - 配列に対してmap()を行い、結果として得られた配列をフラット化する

    ### 使用例

    ```js
    const arr = ["Blue Green", "Red Yellow"];
    console.log(arr.map(x => x.split(" ")));//[['Blue', 'Green'], ['Red', 'Yellow']]
    console.log(arr.flatMap(x => x.split(" ")));//['Blue', 'Green', 'Red', 'Yellow']
    ```
