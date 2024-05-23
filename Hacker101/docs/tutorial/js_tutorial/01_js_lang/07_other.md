










## Q13. Setを知っていますか?

??? success

    - Setは重複した値を持たないJSのデータ構造です
    - 重複した値を追加した場合、何も起こりません
    - キーを持ちません
    - 複数の型を格納できます
    - `add()`で要素を追加できます
    - `delete()`で要素を削除できます。無い要素を削除しようとした場合、falseが返ります。エラーにはなりません
    - .sizeで要素の数を取得できます

    ```js
    const A = new Set();
    
    A.add("hoge");
    A.add("hoge");
    console.log(A.size); //1 
    A.add(2024);

    console.log(A.has("hoge")); //true
    A.delete(2024); //true
    A.delete(1014); //false

    console.log(A.size) //1;

    ```

## Q14. DOMについて理解していますか?
??? success
    - DOMは、Document Object Modelの略。
    - Webブラウザは、Webサーバから受け取ったHTMLドキュメントを解析する
    - そして、 __プログラムが文書の構造、スタイル、コンテンツを変更できるよう__ に、要素やテキスト、属性をオブジェクトに変換する。
    - この際、オブジェクトはいくつかのプロパティを持ち、互いを参照しあう
    - __オブジェクト同士のつながりを表現したもの__ をDOMと呼ぶ
    - つまりDOMはブラウザがWebページを解釈したものといえる

    ??? info
        - HTMLドキュメントはデータの取得にだけ使用される
        - 画面表示や表示の変更はDOMを介して行われる

    



## Q15. querySelector()とquerySelectorAll()を用いてDOM要素を選択する方法を知っていますか？

??? success
    ### querySelector()
    - 指定されたセレクタに一致する最初の要素を返す
    - 一致するものがない場合は、nullが返る

    ```html
    <div>
      <p id="hoge-title"></p>
      <ul>
        <li class="hogeClass">1</li> <!--document.querySelector(".hogeClass");の対象となる-->
        <li class="hogeClass">2</li>
        <li class="hogeClass">3</li>
      </ul>
    </div>
    ```
    ```js
    //最初のdivタグを返す
    const div = document.querySelector("div");
    //id属性がhoge-titleな最初の要素を返す
    const hogeTitle = document.querySelector("#hoge-title");
    //class属性がhogeClassな最初の要素を返す
    const hogeClass = document.querySelector(".hogeClass");
    ```

    ### querySelectorAll()
    - 指定されたセレクタに一致するすべての要素を返す
    - 一致するものが見つからない場合は空のNode-Listを返す

    ```html
    <div>
      <p id="hoge-title"></p>
      <ul>
        <li class="hogeClass">1</li> <!--document.querySelectorAll(".hogeClass");の対象となる-->
        <li class="hogeClass">2</li><!--document.querySelectorAll(".hogeClass");の対象となる-->
        <li class="hogeClass">3</li><!--document.querySelectorAll(".hogeClass");の対象となる-->
      </ul>
    </div>
    ```
    ```js
    const hogeClass = document.querySelectorAll(".hogeClass");
    ```
    ### 空のNodeList
    - 空の配列`[]`とは異なる
    - NodeListは`forEach`は使えるが、`map`等の反復処理メソッドは使えない
    - 添え字`hoge[0]`を使うことなどはできる

## Q16. DOMで新しい要素を作成する方法を知っていますか?

??? success
    - DOMの要素を作成するには、`document.createElement`が利用できる。引数にはタグ名を渡す。
    - 要素には属性が設定可能

    ```js
    const div = document.createElement("div");
    div.id = "hoge";
    div.className = "neko";
    div.setAttribute("data-id", "js"); //自作の要素を作成
    console.log(div);//<div id="hoge" class="neko" data-id="js"></div>
    ```
## Q17. DOM要素を削除する方法を知っていますか？

??? success
    - DOMの要素を削除するには`remove`または`removeChild`が使える
    - `remove`は指定のノードを削除する
    - `removeChild`は第1引数に指定した子ノードを削除する

    ```html
    <div class="hogeClass">
      <p id="hogeId"></p>
    </div>
    ```
    ```js
    const hogeClass = document.querySelector(".hogeClass");
    const hogeId = document.querySelector("#hogeId");

    hogeClass.removeChild(hogeId); //hogeIdを削除する
    hogeId.remove(); //hogeIdを削除する
    ```
## Q18. 要素の寸法を取得する方法を知っていますか?
??? success

    - 要素の寸法を取得するには、getBoundingClientRect()を使います
    ```html
    <div id="hogeId">hoge</div>
    ```

    ```js
    const hogeId = document.querySelector("#hogeId");
    const dimensions = hogeId.getBoundingClientRect();

    console.log(dimentions);
    //DOMRect{
    //  bottom: 62,
    //  height: 62,
    //  left: 25,
    //  right: 668.3333740234375,
    //  top: 0,
    //  width: 643.3333740234375,
    //  x: 25,
    //  y: 0,
    //}

    ```

## Q19. JSでウィンドウの寸法を取得する方法を知っていますか?
??? Success
    - `window.outerWidth`: ブラウザの幅
    - `window.outerHeight`: ブラウザの高さ
    - `window.innerWidth`: コンテンツ表示領域の幅
    - `window.innerHeight`: コンテンツ表示領域の高さ

## Q20. deferとasyncの違いが分かりますか?
??? success
    ### defer
    - deferスクリプトはHTMLページが完全に解析された後で実行される
    - deferスクリプトはドキュメント内での順序を維持する
    ```js
    <script defer src="script1.js"></script> <!--実行順序1-->
    <script defer src="script2.js"></script><!--実行順序2-->
    ```
    
    ###async
    - asyncスクリプトはHTMLのパーサを1次停止し、スクリプトの実行後に、HTML解析を再開する
    - ダウンロードされたものから実行されるので、実行順序は保証されない

    ```js
    <script async src="script1.js"></script>
    <!--下が先にダウンロードされたらこちらが先に実行される-->
    <script async src="script2.js"></script>
    ```

## Q21. appendChildと、insertBeforeの違いが分かりますか?
??? Success
    ### appendChild
    - `親要素.appendChild(新しい要素)`は指定された親要素の最後の子として新しい要素を追加
    ```html
    <div id="hogeWrapper">
      <p></p>
      <span></span>
      <!--ここにhogeが追加される-->
    </div>
    ```

    ```js
    const hogeWrapper = document.querySelector("#hogeWrapper");
    const hoge = document.createElement("div");
    hogeWrapper.appendChild(hoge);
    ```
    ### insertBefore
    - `親要素.insertBefore(追加要素、指定された子要素)`は指定された子要素の前に新しい要素を追加
    ```html
    <div id="hogeWrapper">
      <!--ここにhogeが追加される-->
      <div id="hogeChild"></div>
    </div>
    ```

    ```js
    const hogeWrapper = document.createElement("#hogeWrapper");
    const hogeChild = document.createElement("#hogeChild");
    const hoge = document.createElement("div");

    hogeWrapper.insertBefore(hoge, hogeChild);
    ```

## Q22. JSを用いて、ページの先頭までスクロールする方法を知っていますか?

??? success
    - `window.scrollTo(0,0);`を用いる。
    - 左上までスクロールが行われる
    

## Q23. イベントのキャプチャについて知っていますか?

??? success
    - イベントのキャプチャは、イベント伝播の最初のフェーズ
    - イベントは、
    ```mermaid
    graph LR;
    A[キャプチャリングフェーズ] --> B[ターゲットフェーズ]
    B --> C[バブリングフェーズ]
    ```
    - という風に進んでいく
    - 外側の要素がイベントをキャッチして、ターゲットまで下りていき(キャプチャリング)、
    - ターゲットに達するとそのイベントに対して登録されたすべてのイベントハンドラやイベントリスナーが呼び出され（ターゲットフェーズ）
    - また親まで遡っていく（バブリング）恰好
    - イベントは、もっとも外側の要素によってキャプチャされ、内側の要素に伝播される
    - キャプチャリング中に発生したイベントは、ほとんどの場合使用されない
    ### 例
    ```js
    <html>
      <body>
        <div>
          <button>a</button> <!--ここがクリックされた場合-->
        </div>
      </body>
    </html>
    ```
    1. windowオブジェクトがイベントをキャプチャする
    2. document -> html -> body -> div -> buttonと降りていく
    3. ターゲット要素に達した場合(e.target)でアクセスできる
## Q24. バブリングを知っていますか?

??? success

    - バブリングは、DOMの概念で、DOM内のネストされた要素から、親へとイベントが伝播する事を指す
    ??? warning
        ただし、`focus`イベントや`blur`イベントなどは、`Event.bubbles`プロパティが`false`なのでバブリングフェーズにおけるイベントの伝播を行わない
    - バブリング中に発生したイベントは通常、使用される（イベントハンドラやイベントリスナーが呼び出される）
    - 以下のコードにおいて、子要素(child)をクリックすると、コンソールには
    ```
    child click
    parent click
    ```
    と出力される

    ```html
    <div class="parent">
      <div class="child">
      </div>
    </div>

    <script>
      const parent = document.querySelector(".parent");
      const child = document.querySelector(".child");

      parent.addEventListener("click", ()=>{
        console.log("Parent click");
      } )

      child.addEventListener("click", ()=>{
        console.log("child click");
      } )
    </script>
    ```

## Q25. Mapを知っていますか

??? success

    - `Map`はJsのデータ構造の一つで、キーと値がペアで格納されます。
    - ハッシュテーブル、辞書とも呼ばれます
    - keyもvalueも任意のタイプにすることができます
    - keyは一意かつ不変です
    - valueについては変更することもできます

    ```js
    const myMap = new Map();
    const myObj = {name: "yamada"}
    myMap.set("key1", "hoge");
    myMap.set(12, "fuga");
    myMap.set(myObj, "hogehoge");

    console.log(myMap);//(1)

    //valueを取得
    console.log(myMap.get("key1")); //hoge

    //繰り返し
    for (const [key, val] of myMap){
      console.log(key, ":", val); //(2)
    }
    //削除
    myMap.delete("key1");

    //要素があるか否か
    console.log(myMap.has("key1")); //false

    //全消去
    myMap.clear();

    //サイズの確認
    console.log(myMap.size); //0
    ```

    1. `Map(3) {'key1' => 'hoge', 12 => 'fuga', {…} => 'hogehoge'}`
    2. 
    ```
    key1 : hoge
    12 ':' 'fuga'
    {name: 'yamada'} ':' 'hogehoge'
    ```



## Q27. 一定の間隔で繰り返しコードを実行したい場合、どうすればいいか分かりますか？

??? success
    - `setInterval(実行したい関数, ミリ秒)`を使うことで、第二引数に設定した間隔で繰り返すことが可能
    - `clearInterval()`で解除できる

    ```js
    let count = 0;
    const interval = setInterval(() =>{
      count++;
      console.log("Hello");

      if(count >= 5){
        clearInterval(interval); //5回呼ばれたら終了する
      }
    }, 1000); //1秒周期で繰り返す

    ```

## Q28. 一定時間後に、コードを1回だけ実行したい場合、どうすればいいかわかりますか？

??? success

    - `setTimeout()`というJavascriptの関数が使えます
    - `setTimeout(関数, ミリ秒)`という風に、実行したい関数を第1引数に、待ち時間を第2引数に書きます
    - タイムアウトの関数をクリアしたい場合は`clearTimeout()`が使えます

    ```js
    const timeout = setTimeout(()=>{
      console.log("Hello");
    }, 1000);

    clearTimeout(timeout); //1000ミリ秒経つ前に書くと解除される
    ```

## Q29. Strict-modeの落とし穴について知っていますか?

??? success

    ### Strict-modeについて(復習)
    - Strict-modeでは従来は受け入れていた一部のミスをエラーに変更します
    - JSは優しいので多くのミスを、受け入れてくれますが、これが後々大きな問題を引き起こすこともあるので、Strict-modeが存在します
  
    ### Strict-modeが検知するミスの例

    ```js
    //未定義の変数への代入
    "use strict";

    let typeVar;

    //strict-modeでない場合、スペルミスを通過してしまう
    typoVar = 17; //error 
    ```

    ### 使い方
    - ファイル、または関数の先頭に`"use strict"`と記載します
    - 注意点として、関数の先頭に記載する場合、デフォルト引数や、分割引数のある関数で使用すると構文エラーが吐かれます

    ### 落とし穴
    ```js
    // ok
    function sum(a, b){
      "use strict";
      return a+ b;
    }

    //ng
    function mul(a = 1,b = 2){
      "use strict"; //error
      return a*b;
    }
    ```

    ### モジュール内のコードについて
    - モジュールとは、特定の目的に対応するクラスや関数のライブラリを含んだものです
    - `export`キーワードを用いることで、他ファイルに対し、機能を提供します。`import`を使って受け取ることもあります。
    - モジュールの場合は、既に内容全体が自動的にStrict-modeになっているので、あえて`"use strict"`と明記する必要はありません

## Q30. デバッグの方法を知っていますか?

??? success

    ### コンソールロギング
    - `console.log()`：情報
    - `console.warn()`：警告
    - `console.error()` ：エラー
    - 上記のコンソール命令を使用して、値や変数等を開発者コンソールに出力できる

    ```js
    let hoge = 12;
    if(hoge >=10){
      console.warn("hogehoge!");
    }
    ```

    ### ブラウザ開発者ツール
    - F12または右クリックののち検証を押す
    - ソース：ロードされたスクリプトを確認し、ブレークポイントを設定することで、コードをステップ実行可能
    - コンソール：対話型JSの実行を可能にする
    - ネットワーク：ネットワーク関連の要求と応答を確認するのに役立つ

    ### コンソールタブでブレークポイントを挿入！
    - もちろん、ソースファイルにも適用できる
    ```js
    const hoge = () =>{
      const a = 12;
      debugger; //現在の変数の状況が確認できる
      console.log(a);
    }
    ```

    ### コールスタックとスコープ
    - `debugger;`ステートメントで一時停止したときに見れる情報
    - 関数が呼び出される流れを確認できる
    - `Scope`: ローカル変数とグローバル変数の値が表示される

## Q31. JSONを解析する方法を知っていますか?

??? success
    - JSONを解析するには`JSON.parse()`
    - これはJSON文字列を解析して、同等のJSを返す

    ```js
    const json = '{"name": "Javascript", "year": 1995}';
    const toJS = JSON.parse(json);

    console.log(toJS); //{name: 'Javascript', year: 1995}
    ```


## Q36. 非同期コードと同期コードの違いを知っていますか?
??? success
    - 同期コード：プログラムの実行をブロックする
    - 非同期コード：プログラムの実行をブロックしない

    ### 非同期コード
    - バックグラウンドで実行され、プログラムの実行をブロックしない
    - 通常、応答を待つ時間があったり、ネットワークに要求したりと、CPU使用率は低いが、完了までに時間がかかるタスクを実行するために使用する。

    ```js
    console.log("Before");
    setTimeout(()=>{
      console.log("Hello");
    },1000)
    console.log("After");

    //Before
    //After
    //Hello
    ```

    ### 同期コード
    - 同期コードは順番に実行され、完了するまでプログラムの実行をブロックする
    - タスクの完了に時間がかかるとほかのすべてのタスクは待機する羽目になる

    ```js
    console.log("Before");
    for(let i=0; i<1e9; i++){}
    console.log("After");
    ```
