# クイズ(3:DOMノード)

## Q27. DOMの各要素に対して移動したいとき、documentオブジェクトが起点となることを知っていますか?

??? success

    ### `<body>`内に`<div>`があったとすると
    - 以下の様にノードを移動可能

    ```mermaid
    graph TD;

    A["document"]
    B["document.documentElement"]
    C["document.body"]
    D["DIV"]
    E["?"]
    F["?"]
    G["?"]
    H["?"]


    A --> B
    B --> C
    C --> |childNodes| D
    D --> |parentNode| C
    D --> |lastChild| E
    D --> |firstChild| F
    D --> |nextSibling| G
    D --> |nextSibling| H

    ```

    ### document.documentElement
    - `<html>`タグの`DOM`ノード

    ### document.head
    - `<head>`タグ

    ### document.body
    - `<body>`タグ

    ### Node.parentNode
    - 親要素

    ### Node.childNodes
    - 全ての子要素の`Array-like`
    
    ### Node.firstChild
    - 最初の子要素

    ### Node.lastChild
    - 最後の子要素

    ### Node.previousSibling
    - 前の兄弟要素

    ### Node.nextSibling
    - 次の兄弟要素



    ### 読み込まれたか確認する

    !!! warning
        - たとえば`<head>`タグ内で`document.body`とするとそれは`null`である
        - また、`<body>`の`<script>`タグ内で、`document.body.childNodes`を行った場合、`<script>`以下のタグはまだ読み込まれていないので表示されない

    ### 使用例

    ```js
    console.log(document.body); //<body></body>
    console.log(document.head); //<head></head>
    console.log(document); //#document

    console.log(document.body.parentNode); //<html></html>
    console.log(document.childNodes); //NodeList(2) [<!DOCTYPE html>, html]

    console.log(document.firstChild); //<!DOCTYPE html>
    console.log(document.lastChild); //<html></html>

    console.log(document.head.nextSibling); //#text
    console.log(document.body.previousSibling.previousSibling); //<head></head>


    //bodyタグの先頭にdivタグがあり、その先頭にtextがある場合
    console.log(document.body.childNodes[0].firstChild); //#text
    ```

## Q28 Elementから継承した機能と、Nodeから継承した機能を使って、要素を移動する際の違いが分かりますか?

??? success
    ### Elementから継承している場合
    - 要素ノードのみが対象

    ### Nodeから継承している場合
    - テキストノードやコメントノードも対象

    ### Elementの例
    - `Element.children`: 要素ノードの子を`Array-like`で取得
    - `firstElementChild, lastElementChild`
    - `previousElementSibling, nextElementSibling`
    - `parentElement`

    ### 違いの例

    ```js
    console.log(document.body.previousSibling.previousSibling); //<head></head>

    console.log(document.body.previousElementSibling);//<head></head>
    ```

    ### parentElementについて
    - 親が要素ではないケースが存在する

    ```js
    console.log(
      document.documentElement.parentNode
    ); //#document

    console.log(
      document.documentElement.parentElement
    ); //null
    ```

    ### parentElementの使い道
    - `<html>`まで移動する

    ```js
    //nullになったらloopを抜ける
    while(elem = elem.parentElement){
      console.log(elem);
    }
    ```

## Q29 childNodesやchildrenで取得したArray-likeがライブであり、読み取り専用であることを知っていますか?

??? success
    ### 読み取り専用
    - `childNodes[i] = ...`などで子ノードを置き換えることはできない
    - これは`nextSibling`などのQ27における他のプロパティも同様

    ### ライブ
    - `elem.childNodes`への参照が維持されている状態で、`DOM`にノードを追加/削除すると、自動で反映される

    ```js
    const hoge = document.body.childNodes;
    console.log(hoge); //NodeList [text]

    const div = document.createElement("div");
    document.body.appendChild(div);
    console.log(hoge); //NodeList(2) [text, div]
    ```

    ### Array-fromをArrayに変更する

    ```js
    Array.from(hoge).map(elm => console.log(elm));
    // " "
    // <div></div>
    ```

## Q30 `<table>`というDOM要素が固有の追加プロパティを多く持っていることを知っていますか?

??? success
    ### 以下のテーブルについて考える

    ```html
    <table id="table">
      <tr>
        <th>col1</th>
        <th>col2</th>
      </tr>
      <tr>
        <td>elem1-1</td>
        <td>elem2-1</td>
      </tr>
      <tr>
        <td>elem1-2</td>
        <td>elem2-2</td>
      </tr>
    </table>
    ```

    ### table.row
    - テーブルの`<tr>`要素のコレクション

    ```js
    console.log(table.row); //HTMLCollection(3) [tr, tr, tr]
    ```

    ### table.caption/tHead/tFoot
    - それぞれ`<caption>`、`<thead>`、`<tfoot>`への参照

    ### table.tBodies
    - `<tbody>`要素のコレクション

    ```js
    console.log(table.tBodies); //HTMLCollection [tbody]
    console.log(table.tBodies[0].outerHTML);

    /*
    <tbody>
        <tr>
          <th>col1</th>
          <th>col2</th>
        </tr>
        <tr>
          <td>elem1-1</td>
          <td>elem2-1</td>
        </tr>
        <tr>
          <td>elem1-2</td>
          <td>elem2-2</td>
        </tr>
      </tbody>
    */
    ```

    ### thead/tfoot/tbody.rows
    - 以上三種の要素は`rows`プロパティを提供する

    ```js
    console.log(table.tBodies[0].rows); //HTMLCollection(3) [tr, tr, tr]
    ```

    ### tr.cells
    - `<tr>`内の`<th>`と`<td>`の集合

    ```js
    console.log(
      table.tBodies[0].rows[0].cells
    );
    //HTMLCollection(2) [th, th]
    ```

    ### tr.sectionRowIndex
    - `<tr>`を囲んでいる`<thead>/<tbody>`に与えられた`<tr>`の番号

    ```js
    console.log(
      table.tBodies[0].rows[1].sectionRowIndex
    );

    //1
    ```

    ### tr.rowIndex
    - テーブル内の`<tr>`の番号

    ### td/th.cellIndex
    - `<tr>`で囲まれている内側でのセルの番号

    ```js
    console.log(
      table.tBodies[0].rows[1].cells[0].cellIndex
    );
    //0
    ```

## Q31 `getElement*`や`querySelector*`を用いてページ内の任意の要素を取得できることを知っていますか?

??? success
    ### document.getElementById("ID名")
    - `document`オブジェクトに対して呼び出し可能
    - `text`や`comment`は対象ではない

    ```html
    <div id="elem">
      <div id="elem-content">Element</div>
    </div>

    <script>
      let elemContent = document.getElementById("elem-content");
      elemContent.style.background = "red";
    </script>
    ```

    ### window.idを使った場合
    - 要素にアクセスする場合、`window.id名`とできる
    - 上の例のようなハイフンが入った要素の場合、`window["elem-content"]`のように角括弧でアクセス
    - `document.getElementById`の方を用いるべき

    ### idが重複していた場合
    - `document.getElementById`は最初にヒットしたものを返す
    - `id`は重複するべきではない


    ### elem.querySelectorAll(css)
    - 引数に指定した`CSS`セレクタに一致する`elem`内のすべての要素を返す

    ```html
    <ul>
      <li>The</li>
      <li>test</li>
      <span>neko</span>
    </ul>
    <ul>
      <li>has</li>
      <li>passed</li>
    </ul>

    <script>
    //ulの最後の子要素がliタグであれば
      let elements = document.querySelectorAll("ul > li:last-child");

      for(let elem of elements){
        console.log(elem.innerHTML);
        //test, passed
      }
    </script>
    ```

    ### 補足(for ... of or for ... in)
    - コレクションは`iterable`なので`for ... of`を使う
    - `for ... in`だと`enumerable : true`がすべて引っかかる

    ### 疑似クラスも使えるが、プライバシー上の制限がある
    - `:visited`などの一部の疑似クラスは、ユーザのプライバシーを保護するため`JS`からアクセスすることはできない

    ```js
    //訪れたaタグを取得しようとしているが......
    document.querySelectorAll("a:visited");
    // NodeList [];
    ```

    ### elem.querySelector(css)
    - `elem.querySelectorAll(css)[0]`と同じ
    - 1つを返す分、処理が速い

    ### getElementsBy*
    - `elem.getElementsByTagName(tag)`: `tag`に`*`を指定すると、任意のタグを意味する。
    - `elem.getElementsByClassName(className)`
    - `document.getElementsByName(name)`
    - `querySelector*`等で殆どの場合代用が効くのであまり使われない

    ### 代用の方法

    ```html
    <div id="div" name="hoge" class="div">
      <p>
        <span></span>
      </p>
    </div>

    <script>
      const name = "hoge";
      //属性を記載
      const nameElements = document.querySelectorAll(
        `[name="${name}"]`
      ); //NodeList [div#div.div]

      const tagElements = document.querySelectorAll(
        "span"
      ); //NodeList [span]

      const classElements = document.querySelectorAll(
        ".div"
      ); //NodeList [div#div.div]

      const idElements = document.querySelectorAll(
        "#div"
      ); //NodeList [div#div.div]
    </script>
    ```

## Q32 `getElements*`と`querySelector*`の違いについて、ライブ(動的)か否かがあげられることを理解していますか?

??? success

    ### 比較表

    | メソッド                 | 検索方法       | 要素上で呼べるか | 動的 |
    | ------------------------ | -------------- | ---------------- | ---- |
    | `getElementById`         | `id`           | `-`              | `-`  |
    | `getElementsByName`      | `name`         | `-`              | `✓`  |
    | `getElementsByTagName`   | `tag`          | `✓`              | `✓`  |
    | `getElementsByClassName` | `class`        | `✓`              | `✓`  |
    | `querySelector`          | `css-selector` | `✓`              | `-`  |
    | `querySelectorAll`       | `css-selector` | `✓`              | `-`  |

    ### getElements*は動的
    - `DOM`にノードを追加すると反映

    ```html
    <div>First div</div>
    <script>
      let divs = document.getElementsByTagName("div");
      console.log(divs.length); //1
    </script>

    <div>Second div</div>

    <script>
      console.log(divs.length); //2
    </script>
    ```

    ### querySelector*は静的

    ```html
    <div>First div</div>

    <script>
      let divs = document.querySelectorAll("div")
      console.log(divs.length); //1
    </script>

    <div>Second div</div>

    <script>
      console.log(divs.length); //1
    </script>
    ```

## Q33 elem.matches(css)やelem.closest(css)を使って、要素やその親とCSSセレクタが一致するか確かめられることを知っていますか?

??? success
    ### elem.matches(css)
    - `elem`が与えられた`CSS`セレクタに一致するかチェック

    ```html
    <div class="hoge1"></div>
    <div class="hoge2"></div>
    <div class="hoge3"></div>
    <div class="neko1"></div>

    <script>
      for(let elem of document.body.children){
        if(elem.matches("div[class^='hoge']")){
          console.log(elem.className);
          //hoge1,hoge2,hoge3
        }
      }
    </script>
    ```

    ### elem.closest(css)
    - 自身を含む祖先ノードが、`CSS`セレクタに一致するかチェック

    ```html
    <div class="hoge">
      <ul class="fuga">
        <li id="target" class="nuko">
        <li class="hoge">
      </ul>
    </div>

    <script>
      let target = document.getElementById("target");
      console.log(
        target.closest(".hoge")
        //<div class="hoge"></div>
      )
    </script>
    ```

    ### elemA.contains(elemb)
    - `elemB`が`elemA`の中にある場合
    - もしくは`elemA === elemB`の時、`true`

    ```js
    let ul = document.createElement("ul");
    let li = document.createElement("li");
    ul.append(li);

    console.log(ul.contains(li)); //true
    ```

## Q34 DOMノードが継承している組み込みクラスについて知っていますか?

??? success
    
    ### 継承図一覧

    ```mermaid
    graph BT;

    S[Object]
    A[EventTarget]
    B[Node]
    C[Text]
    D[Element]
    E[Comment]
    F[HTMLElement]
    G[SVGElement]
    H[HTMLInputElement]
    I[HTMLBodyElement]
    J[HTMLAnchorElement]
    K[HTMLDocument]
    L[Document]

    H --> F
    I --> F
    J --> F
    F --> D
    G --> D
    E --> B
    D --> B
    C --> B
    B --> A
    A --> S
    K --> L
    L --> B

    ```

    ### EventTarget
    - 階層のルート。
    - 抽象クラスであり、このクラスからオブジェクトは生成されない。
    - 全ての`DOM`ノードがイベントをサポートしている所以

    ### Node
    - 抽象クラス
    - `DOM`ノードの基底

    ### Element
    - `DOM`要素の基底
    - 要素レベルの機能を提供

    ### HTMLElement
    - `HTML`要素の基底
    - 多くの`HTML`要素がこれを継承する

    ### HTML(Input/Body/Anchor)Element
    - それぞれ`<input>`、`<body>`、`<a>`要素のためのクラス

    ### Text/Comment
    - それぞれテキストノード、コメントノードのためのクラス

    ### HTMLDocument
    - documentオブジェクトのためのクラス

    ### HTML要素のクラスについて
    - タグは、各々のクラスからインスタンスとして生成される。
    - `<span>`など、独自のクラス(`HTMLSpanElement`)から生成されるが、固有のプロパティやメソッドを提供しないタグもある

    ### 継承関係を確認する例([[Prototype]])
    - `<input>`タグが使える機能について考える

    ```js
    //inputタグ
    const input = document.createElement("input");

    //inputタグはHTMLInputElementクラスから作られる
    console.log(
      input.constructor.name
    ); //'HTMLInputElement'

    //HTMLInputElementの機能が使える
    console.log(
      input.__proto__ === HTMLInputElement.prototype
    ); //true

    //HTMLElementの機能が使える
    console.log(
      HTMLInputElement.prototype.__proto__ === HTMLElement.prototype
    ); //true

    //Elementの機能が使える
    console.log(
      HTMLElement.prototype.__proto__ === Element.prototype
    ); //true

    //Nodeの機能が使える
    console.log(
       Element.prototype.__proto__ === Node.prototype
    );//true

    //EventTargetの機能が使える
    console.log(
      Node.prototype.__proto__ === EventTarget.prototype
    );//true

    //Objectの機能が使える
    console.log(
      EventTarget.prototype.__proto__ === Object.prototype
    ); //true

    //extendsで継承しておらず、組み込みクラス同士なので以下は成立しないことに注意する
    console.log(
      Node.__proto__ === EventTarget.__proto__
    ); //false

    ```

    ### コンストラクタを確認する方法その2

    ```js
    console.log(
      document.body.toString();
    )//'[object HTMLBodyElement]'
    ```

    ### 継承関係を確認する方法その2

    ```js
    console.log(
      document.body instanceof HTMLBodyElement,
      document.body instanceof EventTarget
    ); //true true
    ```

## Q35 console.dirとconsole.logの違いを知っていますか?

??? success
    ### JSオブジェクトに対して
    - 同じ動作をする

    ### DOM要素に対して
    - `console.log`は要素のDOMツリーを表示
    - `console.dir`はDOMオブジェクトとして要素を表示

    ```js
    //console.dirはプロパティを調べるのに役立つ
    console.log(document.body); //<body>...</body>

    console.dir(document.body);
    //body
    //  aLink: ""
    //  accessKey: ""
    //  ariaAtomic: null
    // ...
    // [[Prototype]]: HTMLBodyElement
    ```

## Q36. HTMLInputElementなどのDOM要素のクラスの情報はwhatwgで閲覧できますが、その仕様の見方が分かりますか?

??? success
    ### 例

    ```idl
    [Exposed=Window]
    interface HTMLInputElement : HTMLElement {
      [HTMLConstructor] constructor();

      [CEReactions] attribute DOMString accept;
      [CEReactions] attribute DOMString alt;
      [CEReactions] attribute DOMString autocomplete;
      [CEReactions] attribute boolean defaultChecked;
      ...
      [CEReactions] attribute unsigned long size;
      [CEReactions] attribute USVString src;
      ...
      undefined showPicker();
    };

    HTMLInputElement includes PopoverInvokerElement;
    ```

    - 補足説明

    ```
    [CEReactions]
      ・属性が変更された時に、その変更が正しく処理されることを保証
      ・適切な方法でエラーハンドリングされる
      ・属性の値が無効であるときや、変更が失敗した時、ブラウザは適切に対処する。
      ・（たとえば範囲外や条件を満たさない場合など）
      ・この属性がない場合は、上記の条件は保証されない

    attribute
      フィールドである
    
    DOMString
      DOMで扱われる文字
      JSのStringに対応。通常の文字列

    USVString
      サロゲートペア文字を考慮する必要がない
    
    includes
      ・継承関係にある
      ・上の文で言うと、`PopoverInvokerElement`はUI要素をトリガするための機能やメソッドを定義している。
      ・そのため`HTMLInputElement`はそれを用いることができる

    [Exposed=Window]
      ・インターフェースやメンバーが、ウィンドウオブジェクトに公開される
      ・上の例でいうと`HTMLInputElement`は`JS`からウィンドウオブジェクト経由でアクセス可能

    [HTMLConstructor]
      このコンストラクタが特定のHTML要素のコンストラクタであることを表す。
    ```

## Q37 DOMノードのタイプを数値として取得したいとき、elem.nodeTypeが使えることを知っていますか?

??? success
    ### 使用例

    ```html
    <script>
      let elem = document.body;
      console.log(elem.nodeType); //1
      console.log(elem.firstChild.nodeType); //3
      console.log(document.nodeType); //9

      console.log(
        document.nodeType === Node.DOCUMENT_NODE;
      ); //true
    </script>
    ```

    ### 説明

    ```
    Element:          (1)
    Attribute:        (2)
    Text:             (3)
    CDATASection:     (4)
    ProcessingInstruction: (7)
    Comment:          (8)
    Document:         (9)
    DocumentType:     (10)
    DecumentFragment: (11)

    5~7は非推奨ノードであるため記載しない
    ```

    ### instanceofとの違い
    - `nodeType`の方が簡潔

## Q38 nodeNameやtagNameプロパティを使うとDOMノードのタグ名を見ることができるが、前者と後者の違いが分かりますか?

??? success

    ### tagName
    - `Element`ノードに対してのみ存在する

    ### nodeName
    - 任意の`Node`で定義される
    - つまり、`text`や`comment`ノード、それからルートの`document`に対しても役立つ

    ### 使用例

    ```html
    <script>
      //textノードに対して
      console.log(
        document.body.firstChild.tagName
      ); //undefined

      console.log(
        document.body.firstChild.nodeName
      ); //#text

      //documentに対して
      console.log(document.tagName); //undefined
      console.log(document.nodeName); //#document

      console.log(document.body); //BODY
    </script>
    ```

    ### HTMLのタグ名について
    - `console.log(document.body)`の結果から分かる通り、HTMLドキュメントを受け取ったとき、タグ名は常に大文字
    - `XML`ドキュメントなら、タグ名の大小は区別される


## Q39 要素に対して書き込みを行いたい場合、`innerHTML`,`outerHTML`,`data/nodeValue`,`textContent`が使われますがその違いを知っていますか?

??? success
    ### innerHTML

    - 要素内の`HTML`を文字列として取得

    ```html
    <body>
      <p>test</p>
      <script>
        console.log(document.body.innerHTML);
        document.body.innerHTML = "<div>test</div>";
        //<div>test</div>に変更された
      </script>
    </body>
    ```

    ### 注意点
    - `<div>test`のように無効なHTMLを挿入しようとした場合、ブラウザ側が自動で修正する
    - `innerHTML`で`<script>`タグを挿入した場合、HTMLの一部にはなるが実行はされない

    ### +=は追加ではなく、置き換え
    - `innerHTML`に画像などが含まれる場合、`+=`を用いてもそれらはすべてリロードされる

    ```js
    document.body.innerHTML += "<p>test2</p>"

    //1 古いコンテンツが削除される
    //2 古いものと新しいものが連結したinnerHTMLが書かれる
    ```

    ### outerHTML
    - `innerHTML`に要素自身を加えたようなもの

    ```html
    <div id="elem">
      Hello <b>World</b>
    </div>
    <script>
      console.log(
        document.querySelector("#elem").outerHTML
      )

      /*
      <div id="elem">
        Hello <b>World</b>
      </div>
      */
    </script>
    ```

    ### outerHTMLに書き込んだ時の注意点
    - `elem.outerHTML`を変更しても`elem`は変わらない
    - 変更したい場合は、再度DOMに問い合わせる必要がある

    ```html
    <div id="elem">Hello</div>

    <script>
      let elem = document.querySelector("#elem");
      elem.outerHTML = "<div id='elem'>A new Elem</div>";

      console.log(elem.outerHTML);
      //<div id="elem">Hello</div>
      //ただし開発者ツールで見ると、変更はされている

      elem = document.querySelector("#elem");
      console.log(elem.outerHTML);
      //<div id="elem">A new Elem</div>
    </script>
    ```

    ### nodeValue/data
    - `innerHTML`と異なり、要素ノード以外に対しても有効
    - たとえば`#text`や`#comment`
    - `nodeValue`と`data`は同一

    ```html
    <body><!--Neko-->
      <script>
        let text = document.body.firstChild;
        console.log(text); //<!--Neko-->
        console.log(text.nodeValue); //Neko
        text.data = "Inu";

        console.log(text); //<!--Inu-->
      </script>
    </body>
    ```

    ### textContent
    - テキストを安全な方法で書き込む(HTMLタグをエスケープする)ことができる
    - 通常ユーザからの入力を`HTML`タグとして受け取ると、セキュリティ上の問題が発生するのでこちらを使う

    ```html
    <div id="elem1"></div>
    <div id="elem2"></div>

    <script>
      let elem1 = document.getElementById("elem1");
      let elem2 = document.getElementById("elem2");

      elem1.innerHTML = "<b>Hello</b>";
      elem2.textContent = "<b>Hello</b>";

      console.log(
        elem1.textContent;
      ); //Hello アクセスした場合は<></>は無視される

      /*
      Hello
      <b>Hello</b>
      */
    </script>


    ```

## Q40 Element要素の基本的なプロパティについて知っていますか?

??? success
    ### 詳細を知りたい場合
    - `whatwg`の各クラスを見るのが早い

    ### 特定のクラスについて知りたい場合
    - `console.dir`を使う

    ```js
    console.dir(
      document.body
    );
    ```

    ### 著名なプロパティについて
    - `id`: 文書全体で一意の識別子
    - `class`:主にCSSやJSで要素にアクセスするために使う
    - `style`:CSSスタイルをインラインで定義
    - `value`: `<input>`,`<select>`,`<textarea>`のための値
    - `hidden`:要素が見えるか否か

    ```html
    <div id="elem1"></div>
    <script>
      elem1.hidden = true;
      //elem1は表示されなくなる
    </script>
    ```

## Q41 HTMLの属性とプロパティが必ずしも1対1で対応していないということを理解していますか?

??? success

    ### 通常の場合
    - HTMLの属性は、自動的にDOMオブジェクトのプロパティとなる

    ```html
    <body id="page"> <!--id属性を定義-->
    
      <script>
        console.log(document.body.id); //page
      </script>
    </body>
    ```

    ### 独自のプロパティについて
    - 組み込み以外のプロパティも定義可能

    ```js
    document.body.myData = {
      name : "Hoge",
      title : "Nekomoti"
    };

    document.body.sayHi = function(){
      console.log(`Hello, ${this.tagName}`);
    };

    document.body.sayHi(); //Hello, BODY
    ```

    ### コンストラクタに追加することも可能
    - インスタンスから参照可能

    ```js
    Element.prototype.sayHi = function(){
        console.log(`Hello, ${this.tagName}`);
    };

    document.documentElement.sayHi(); //Hello, HTML
    document.head.sayHi(); //Hello, HEAD
    document.body.sayHi(); //Hello, BODY
    ```

    ### 改めてDOMオブジェクトのプロパティについて
    - 通常のJSオブジェクトと同じように振る舞う

    ### HTML属性について
    - タグが標準の属性を持っている場合、それはDOMプロパティとなる
    - 非標準の場合、DOMプロパティは生成されない

    ```html
    <body id="test" hoge="non-starndard">
    <script>
      console.log(document.body.id); //test
      console.log(document.body.hoge); //undefined
    </script>
    </body>
    ```

    ### 非標準の属性にアクセスしたい場合
    - `elem.hasAttribute(name)`: 存在チェック
    - `elem.getAttribute(name)`: 値取得
    - `elem.setAttribute(name,val)`: 値設定
    - `elem.removeAttribute(name)`: 属性削除
    - `elem.attributes`: 全ての属性を読み込む

    ### 上記メソッドで属性名にアクセスするときの注意点
    - `HTML`属性は大文字と小文字を区別しない
    - 属性に代入したものは文字列になる
    - 設定した属性については`outerHTML`で全て確認可能
    - `attributes`で全属性をイテレートする時`name`でプロパティ名。`value`でプロパティ値が参照可能

    ```html
    <body id="test" hoge="non-starndard">
    <script>

      //DOMプロパティからでは取得できない
      console.log(document.body.hoge); //undefined

      //has
      console.log(
        document.body.hasAttribute("hoge")
      ); //true
      
      //set
      document.body.setAttribute("hoge", 123);
      document.body.setAttribute("fuga", "neko");

      //get
      console.log(
        document.body.getAttribute("hoge")
      ); //"123"

      //attributes
      for(let attr of document.body.attributes){
        console.log(
          attr.name + "=" + attr.value
          //hoge=123, fuga=neko
        );
      }

      console.log(document.body);
      //<body id="test" hoge="123" fuga="neko"></body>

      //remove
      document.body.removeAttribute("HoGe")

      console.log(
        document.body.hasAttribute("hoge")
      ); //false
    </script>
    </body>
    ```

    ### 属性とプロパティの値が異なる場合
    - `href`
    - `style`

    ```html
    <!--属性は基本的にHTML属性を返す。-->
    <!--プロパティの場合、使用しやすい形に変更されている場合がある-->
    <a id="link" href="#"></a>
    <script>
      link.getAttribute("href");
      // "#"

      console.log(link.href); //相対URLやハッシュを指定しても常に完全なURLを返す
      //'http://localhost:8080/test/index.html#'

      link.getAttribute("style")
      //null

      console.log(link.style);
      //CSSStyleDeclaration {accentColor: '', additiveSymbols: '', alignContent: '', alignItems: '', alignSelf: '', …}

    </script>
    ```

    ### 変更が同期されない場合
    - `value`のプロパティを変更しても、属性には反映されない
    - 逆は更新される

    ```html
    <input>

    <script>
      let input = document.querySelector("input");
      input.setAttribute("value", "text");
      console.log(input.value); //text

      input.value = "neko";
      console.log(input.getAttribute("value")); //text
    </script>      
    ```

    !!! info
        - この特徴は逆に役立つ
        - ユーザが`value`を変更しても、プロパティは変更さえれていないので、元値が見られるという点で

    ### DOMオブジェクトのプロパティは文字列とは限らない

    ```html
    <input id="input" type="checkbox" checked>

    <script>
      console.log(input.getAttribute("checked")); //空文字
      console.log(input.checked); //true
    </script>
    ```

    ### どちらを使えばいいのか
    - 基本的に`DOM`オブジェクトのプロパティ
    - 正確な属性や非標準の属性が必要な場合に、属性を操作する

## Q42 HTML属性に非標準のものを用いたときの注意点と、data-*を用いる利点について知っていますか?

??? success

    ### 非標準の属性の使いどころ
    - `class`の指定の代替

    ```html
    <div class="order">
    Hello
    </div>

    <style>
      .order[order-state="canceled"]{
        color: red;
      }

      .order[order-state="pending"]{
        color: yellow;
      }
    </style>

    <script>
      const div = document.querySelector("div");
      //赤色になる
      div.setAttribute("order-state", "canceled");
    </script>
    ```

    ### 注意点
    - 将来、現在は非標準の属性が標準になる可能性がある
    - その場合、予期せぬ影響が生じる

    ### data-*
    - 将来的に追加されることはない
    - `elem`が`data-about`という属性を持つ場合、`elem.dataset.about`でアクセス可能
    - `data-about-neko`のように複数のハイフンを持つ場合、`dataset.aboutNeko`の様にキャメルケースになる

    ```html
    <div class="order">
      Hello
    </div>

    <style>
      .order[data-order-state="canceled"]{
        color: red;
      }

      .order[data-order-state="pending"]{
        color: yellow;
      }
    </style>

    <script>
      const div = document.querySelector("div");
      //プロパティから変更することも可能
      div.setAttribute("data-order-state", "pending");

      setTimeout(()=>{
        div.dataset.orderState = "canceled"
      }, 1000);
    </script>
    ```

## Q43 新しい要素を作成し、挿入する方法を知っていますか?

??? success
    ### document.createElement(tag)
    - 指定されたタグの新しい要素ノードを作成

    ### document.createTextNode(text)
    - 指定されたテキストの新しいテキストノードを作成

    ```js
    let div = document.createElement("div");
    let text = document.createTextNode("Nekoneko");
    ```

    ### node.append(insertNode or strings)
    - `node`の子要素の末尾にノードまたは文字列を追加

    ### node.prepend(insertNode or strings)
    - `node`の子要素の先頭にノードまたは文字列を追加

    ### node.before(insertNode or strings)
    - `node`の前にノードまたは文字列を追加

    ### node.after(insertNode or strings)
    - `node`の後にノードまたは文字列を追加

    ### node.replaceWith(insertNode or strings)
    - `node`をノードまたは文字列で入れ替える

    ### 注意点
    - テキストは安全な方法で挿入されるので、`HTML`タグを上記のメソッドで挿入することはできない
    - 1回の呼び出しで複数のノードやテキストのリストを挿入することも可能

    ### 使用例

    ```html
    <ol id="ol">
      <li>0</li>
      <li>1</li>
      <li>2</li>
    </ol>

    <script>
      //htmlタグは認識されない
      ol.before("<b>before</b>");
      ol.after("after");

      let liFirst = document.createElement("li");
      let liLast = document.createElement("li");
      let liLast2 = document.createElement("li");
      liFirst.innerHTML = "prepend";
      liLast.innerHTML = "append";
      liLast2.innerHTML = "append2";
      ol.prepend(liFirst);
      //複数挿入
      ol.append(liLast, liLast2);

      let rep = document.createElement("li");
      rep.innerHTML = "replaceWith"
      ol.children[1].replaceWith(rep);

      /*
      <b>before</b>
        1. prepend
        2. replaceWith
        3. 1
        4. 2
        5. append
        6. append2
      after
      */
    </script>
    ```

    ### elem.insertAdjacentHTML(where, html)
    - `beforebegin`:`where`に指定。`elem`の直前に挿入
    - `afterbegin`:`where`に指定。`elem`の子要素の先頭
    - `beforeend`:`where`に指定。`elem`子要素の末尾
    - `afterend`:`where`に指定。`elem`の直後

    ```html
    <ul></ul>
    <script>
      const ul = document.querySelector("ul");

      ul.insertAdjacentHTML(
        "beforebegin", "<p>Hello</p>"
      );
      ul.insertAdjacentHTML(
        "afterend", "<p>Bye</p>"
      );

      ul.insertAdjacentHTML(
        "afterbegin", "<li>1</li>" 
      );

      ul.insertAdjacentHTML(
        "beforeend", "<li>2</li>"
      );

      /*
      Hello
        * 1
        * 2
      Bye
      */
    </script>
    ```

    ### 兄弟メソッドもある
    - `elem.insertAdjacentText(where, text)`
    - `elem.insertAdjacentElement(where, elem)`
    - これらはHTMLではなく、テキストや要素を挿入する
    - しかし、`append`等を使えばいいので、使用されない

## Q44 ノードを削除するときに、removeを、複製したいときにcloneNodeが使えることを知っていますか?(またノードを入れ替えたいときに挿入メソッドで十分な事も)

??? success
    ### node.remove()
    - ノードを削除する

    ```html
    <div id="div1">neko</div>
    <div id="div2">inu</div>

    <script>
      setTimeout(()=> div1.remove(), 1000);
    </script>
    ```

    ### すべて削除

    ```js
    //indexが変動するのでfor...of等は使わないこと
    function clear(elem){
      while (elem.firstChild){
        elem.firstChild.remove();
      }
    }

    //もちろん以下でもいい
    elem.innerHTML = "";
    ```

    ### Nodeを入れ替えたい場合
    - 挿入メソッドで十分。
    - 存在するノードを挿入した場合、前の場所からは削除される

    ```html    
    <div id="div1">neko</div>
    <div id="div2">inu</div>

    <script>
      setTimeout(()=> div2.after(div1), 1000);
    </script>
    ```

    ### elem.cloneNode(true)
    - 子要素ありの深いコピーを行う

    ### elem.cloneNode(false)
    - 子要素なしの浅いコピーを行う

    ```html
    <div id="div">
      <strong>Hi, </strong>hoge!
    </div>

    <script>
      let div2 = div.cloneNode(true);
      let div3 = div.cloneNode(false);

      div.after(div2);
      div2.after(div3);

      /*
        <div id="div">
          <strong>Hi, </strong>hoge!
        </div>

        <div id="div">
          <strong>Hi, </strong>hoge!
        </div>

        <div id="div"></div>
      */
    </script>
    ```

## Q45 DocumentFragmentというノードのリストを返す際のラッパー用のDOMノードが存在することを知っていますか?

??? success
    ### 挿入操作を繰り返したくないときに使う

    ```html
    <ul></ul>

    <script>
      let ul = document.querySelector("ul");
      function getListContent(){
        let fragment = new DocumentFragment();

        for(let i=1; i<=3; i++){
          let li = document.createElement("li");
          li.append(i); //textを挿入
          fragment.append(li);
        }

        return fragment;
      }

      ul.append(getListContent());

      /*
        * 1
        * 2
        * 3
      */
    </script>
    ```

    ### 実際はこちらで十分
    - `prepend/append/before/after`は複数追加可能なので
    - 複数追加した場合でも、DOMへの追加は1回で済む
    - 実際に、`1e7`回ループしてみたが、速度は同等だった

    ```html
    <ul></ul>

    <script>
      let ul = document.querySelector("ul");
      function getListContent() {
        let result = [];

        for (let i = 1; i <= 3; i++) {
          let li = document.createElement("li");
          li.append(i); //textを挿入
          result.push(li);
        }

        return result;
      }

      //pushしたものを展開するだけでいい
      //append等は複数要素を追加可能なので
      ul.append(...getListContent());

      /*
        * 1
        * 2
        * 3
      */
    </script>
    ```

## Q46 古典的な挿入/削除メソッド(たとえば、appendChild)を目にしたことはありますか? 

??? success
    ### 学ぶ意味
    - 古いスクリプトで見かけるため
    - 新たに書く必要性は皆無

    ### parentElem.appendChild(node)
    - `parentElem`の末尾に`node`を追加

    ### parentElem.insertBefore(node, nextSibling)
    - `parentElem`の`nodeSibling`の前に挿入

    ### parentElem.replaceChild(node, oldChild)
    - `parentElem`内の`oldChild`を`node`に置き換える

    ### parentElem.removeChild(node)
    - `parentElem`から`node`を削除

    ### 役立つかもしれない点
    - 古いメソッドの方は挿入/削除したノードを返す
    - 新しいメソッドは返さない

    ### document.write(html)
    - 即座に`html`を書き込む
    - これはDOMがない時に作られたもの
    - ページがロードされている間にだけ動作して、その後に呼び出した場合、既存のドキュメントがすべて削除されるという特徴がある。

    ### writeの利点?
    - 書き込みが早い
    - ロードしている最中に、多くのテキストを追加する必要があり、かつ速度の問題を感じているなら候補となる
    - ただし、`document.write`はページのレンダリングをブロックするので、非同期的にロードした方がいいかも

## Q47 classNameやclassListを使って、クラスの追加/削除を行う方法を知っていますか?

??? success
    ### className
    - クラスの文字列全体を書き換える

    ### classList
    - 此方の方が柔軟
    - `elem.classList.add/remove("class")`: クラスの追加/削除
    - `elem.classList.toggle("class")`:クラスが存在する場合は削除。存在しないなら追加
    - `elem.classList.contains("class")`:クラスの存在をチェック
    - 反復可能

    ### 使用例

    ```html
    <div></div>
    <script>
      let div = document.querySelector("div");

      div.className = "hoge fuga foo";
      div.classList.add("bar");
      div.classList.remove("foo");
      div.classList.toggle("foo");
      
      div.classList.contains("foo"); //true

      for(const elem of div.classList){
        console.log(elem); //hoge, fuga, bar, foo
      }
    </script>
    ```


## Q48 styleを使って要素のスタイルを設定する方法とその注意点を知っていますか?

??? success
    ### 注意点
    - 複数語はキャメルケース
    - 通常は`class`を追加して、そのクラスに`CSS`を割り当てるべき
    - クラスで処理しきれない場合に、`style`で設定する
    - プレフィックス付きのプロパティの場合、先頭から大文字になる(例: `-moz-border-radius`なら`MozBorderRadius`)
    - 単位に注意する必要がある(例:`margin`)
  
    ### 使用例


    ```js
    document.body.style.backgroundColor = "red"
    document.body.style.margin = "20px"; //20の場合、動作しない
    ```

    ### elem.style.removeProperty("style")
    - 特定のスタイルプロパティを削除

    ```js
    document.body.style.background = "red";
    setTimeout(()=>
      document.body.style.removeProperty("background");
    , 1000)
    ```

    ### elem.style.cssText
    - スタイルを置換
    - 既存のスタイルを削除してしまうので、新規に要素を作成する際に使用される
    - なお、`setAttribute`で代用可能

    ```js
    //.styleは読み取り専用なので、設定は不可
    //代わりに、.style.cssTextを用いる

    let div = document.createElement("div");
    div.innerHTML = "Neko";
    div.style.cssText = `
      color: red !important;
      background-color: yellow;
      height: 100px;
      width: 100px;
      text-align: center;
    `

    document.body.append(div);

    //代用
    div.setAttribute("style", `
      color: red !important;
      background-color: blue;
      height; 50px;
      width: 50px;
      text-align: center;
    `)
    ```

## Q49 styleプロパティがstyle属性の値のみを操作可能ということと、計算後の値をgetComputedStyleで参照できることを知っていますか?

??? success
    ### 間違えやすい例
    - styleプロパティは、CSSクラスで定義されたスタイルについては関与できない

    ```html
    <!--CSSクラスの値は参照できない-->

    <style> div {color: red; margin: 5px} </style>
    <div id="div">Neko</div>
    
    <script>
      console.log(div.style.color); //空文字
    </script>
    ```

    ### getComputedStyle(element [,pseudo])
    - `element`:要素
    - `pseudo`:疑似要素
    - CSSクラスや継承されたスタイルが全て計算された後の値を取得する。

    ```html
    <style> div {color: red; margin: 5px} </style>
    <div id="div">Neko</div>

    <script>
      let computedStyle = getComputedStyle(div);
      console.log(computedStyle.marginTop); //5px
      console.log(computedStyle.color); //rgb(255,0,0)
    </script>
    ```

    ### 算出スタイル値と解決スタイル値について

    ```
    算出スタイル値
      ・すべてのCSSルールとCSS継承が適用された後の値
      ・`height: 1em`のように相対的な数値が残されている
    
    解決スタイル値
      ・ブラウザが要素を計算した後の絶対的な値で、`height: 18px`のように単位を固定されている。
      ・また、解決後の値は、浮動小数点を持つ(例: `width: 15.3px`)

    `getComputedStyle`
      ・プロパティの解決された値を返す。
      ・サイズ関連のデータなら通常単位は`px`
    ```

    ### getComputedStyleの注意点
    - 完全なプロパティ名が必要
    - たとえば`margin`は`marginTop/Left/Bottom/Right`の4要素をまとめて指定するが、`getComputedStyle`で用いた場合、その挙動はブラウザに任せられている

    ```html
    <div id="div"></div>
    <style>
      div {
        margin: 10px;
        padding: 10px;
      }
    </style>

    <script>
      let divComputed = getComputedStyle(div);

      console.log(divComputed.margin); //ブラウザ依存
      console.log(divComputed.marginTop); //10px
    </script>
    ```

## Q49 要素サイズを取得/変更する際に見かけるプロパティについて知識がありますか?

??? success
    ### 基本要素

    ```html
    <div id="example">hoge</div>
    <style>
      #example {
        width: 300px;
        height: 200px;
        border: 25px solid;
        padding: 20px;
        overflow: auto; /*内容が収まらない場合scroll-bar*/
      }
    ```

    ### 基本的な注意点
    !!! warning
        ### スクロールバーを考慮する
        - スクロールバーが横に表示され、幅が`16px`の場合、`width`は全体で`284px`になる

        ### padding-bottomとオーバーフロー
        - 要素に多くのテキストがある場合、ブラウザは表示しきれなかったテキストを`padding-bottom`に表示する
        - そのため`padding`がないように見えるが、依然として`padding`は存在している

    ### offsetParent, offsetLeft/Top

    ```
    offsetParent
      以下の要素のうち1つ以上を満たす、最も近い祖先
      1. positionが、absolute, relative, fixedのいずれか
      2. <td>, <th>, <table>タグのうちいずれか
      3. <body>タグ

    offsetLeft/Top
      offsetParentの左上隅からの相対的なx/y座標

    offsetParentがnullになる場合
      1. 要素が表示されていない場合
      2. <body>または<html>の場合
      3. position:fixedを持つ要素の場合
      ※この時、offsetLeft/Topは0になる
    ```

    ```html
    <main style="position: relative" id="main">
      <article>
        <div id="example" style="position: absolute; left: 180px; top: 180px">hoge</div>
      </article>
      <script>
        console.log(example.offsetParent.id); //main
        //articleは上の3つの条件を満たさないので
        console.log(example.offsetLeft); //180
        console.log(example.offsetTop); //180
      </script>
    </main>
    ```

    ### offsetWidth/height

    ```
    offsetWidth
      border/paddingを含む、要素の完全な幅
    offsetHeight
      border/paddingを含む、要素の完全な高さ

    0になる場合
      ・表示されていない場合
      ・先祖にdisplay: noneがある場合
      ・DOMツリーに追加されていない場合
      ※ジオメトリプロパティは表示要素でない場合計算されない
    ```

    ```html
    <style>
      #div{
        width: 300px;
        height: 200px;
        border: 20px solid red;
        padding: 30px;
      }
    </style>
    <div id="div">hoge</div>

    <script>
      //(width + padding * 2 + border * 2)
      console.log(div.offsetWidth); //400
      //200 + 20*2 + 30*2
      console.log(div.offsetHeight); //300
    </script>
    ```

    ### 要素が隠されているかをチェックする

    ```js
    function isHidden(elem){
      return !elem.offsetWidth && !elem.offsetHeight;
    }; //空要素や先祖がdisplay: noneならtrue
    ```

    ### clientTop/Left

    ```
    clientTop/Left
      パディングの外側の座標 - ボーダーの外側の座標
    ```

    !!! warning
        - 実際にはボーダーの幅であることが多い
        - ただし、ドキュメントが右から左に書かれるとき、スクロールバーの幅を含むため、定義は上記

    ### clientWidth/Height

    ```
    clientWidth/Height
      パディング + コンテンツの幅/高さ
    ```

    ```html
    <style>
      #div{
        width: 300px;
        height: 200px;
        border: 20px solid red;
        padding: 30px;
      }
    </style>

    <div id="div">hoge</div>

    <script>
      //width + padding * 2 = 360
      //※スクロールバーが存在する場合
      //width + padding * 2 = 344
      console.log(div.clientWidth)
      //height + padding * 2 = 260
      console.log(div.clientHeight)
    </script>   
    ```

    ### scrollWidth/Height

    ```
    clientWidth/Heightとの違い
      ・スクロールアウトされた部分も含める
      ・client*は、可視領域のみ    
    ```

    ```html
    <style>
      #div {
        width: 300px;
        height: 100px;
        overflow: auto;
      }
    </style>

    <div id="div"></div>
    <script>
      div.innerHTML = `。
      　赤とんぼの休んでいる竹には、朝顔のつるがまきついています。昨年の夏、この別荘の主人が植えていった朝顔の結んだ実が、また生えたんだろう――と赤とんぼは思いました。
      　今はこの家には誰もいないので、雨戸が淋しくしまっています。
      　赤とんぼは、ツイと竹の先からからだを離して、高い空に舞い上がりました。
      `

      console.log(div.clientWidth) //283
      console.log(div.clientHeight) //100
      console.log(div.scrollWidth) //283
      console.log(div.scrollHeight) //234

      //要素を一杯まで広げたいときに有用(px忘れに注意)
      div.style.height = `${div.scrollHeight}px`;
      
    </script>
    ```

    ### scrollTop/Left

    ```
    scrollTop/Left
      隠された部分の上/左部分
      つまり、どれだけスクロールされたか
    ```

    ```html
    <style>
      #div {
        width: 300px;
        height: 100px;
        overflow: auto;
      }
    </style>

    <div id="div"></div>
    <script>
      div.innerHTML = `。
      　赤とんぼの休んでいる竹には、朝顔のつるがまきついています。昨年の夏、この別荘の主人が植えていった朝顔の結んだ実が、また生えたんだろう――と赤とんぼは思いました。
      　今はこの家には誰もいないので、雨戸が淋しくしまっています。
      　赤とんぼは、ツイと竹の先からからだを離して、高い空に舞い上がりました。
      `
      //ちょっとスクロール
      setTimeout(()=>{
        console.log(div.scrollTop); //100
      }, 1000)

    </script>
    ```

    !!!warning
        - `scrollTop/Left`は書き込み可能

        ```js
        elem.scrollTop += 10; //10px分下にスクロール
        elem.scrollTop = 0; //トップまで戻る
        elem.scrollTop = Infinity; //ブラウザ依存
        ```
    
    ### まとめ

    | プロパティ         | 効果                                    |
    | ------------------ | --------------------------------------- |
    | offsetParent       | 最も近いposition指定された祖先          |
    | offsetLeft/Top     | offsetParentの左端からの距離            |
    | offsetWidth/Height | ボーダーを含む要素の幅/高さ             |
    | clientLeft/Top     | 通常、左/上のボーダー幅                 |
    | clientWidth/Height | パディングを含むコンテンツ幅/高さ       |
    | scrollLeft/Top     | 要素のスクロールアウトされた距離        |
    | scrollWidth/Height | スクロールアウトされた領域を含む幅/高さ |
    |                    |                                         |

## Q50 要素のサイズや位置に関する情報を取得したい場合、ジオメトリプロパティがgetComputedStyleより有用な理由が分かりますか?

??? success
    ### autoを返す可能性
    - インライン要素の場合起こりうる

    ```html
    <span id="elem">Hello!</span>
    
    <script>
      console.log(getComputedStyle(elem).width); //auto
      console.log(elem.offsetWidth); //要素が実際に占める幅
    </script>
    ```

    ### スクロールバーを考慮するか否か
    - これはブラウザ依存になる
    - `clientWidth/Height`なら確実に含まない

    ### 欠点ではないが
    - `getComputedStyle`は`padding`を含まない
    - `getComputedStyle`は数値ではなく、`px`付を返す

## Q51 ブラウザウィンドウの幅/高さを取得する方法とその注意点を知っていますか?

??? success
    ### document.documentElement.clientHeight/Width
    - スクロールバーやボーダーを含まない`<html>`

    !!! warning
        ### window.innerWidth/Heightは?
        - スクロールバーを含んでしまう

        ### DOCTYPEの必要性
        - `<!DOCTYPE HTML>`がないと、トップレベルのジオメトリプロパティが異なる可能性がある
        - そのため常に記載する必要がある
    
    ### documentのスクロールアウトした部分を含めた幅/高さ

    !!! warning
        ### ページ全体だとscrollHeight/Widthがいいのでは?
        - `clientHeight/Width`より小さくなる可能性あり
        - ブラウザ依存やスクロールバーの有無で変わる
        - そのため以下の様にする

    ```js
    //プロパティのうち最大のものをウィンドウサイズとする
    let scrollHeight = Math.max(
      document.body.scrollHeight,
      document.documentElement.scrollHeight,
      document.body.offsetHeight,
      document.documentElement.offsetHeight,
      document.body.clientHeight,
      document.documentElement.clientHeight
    );

    console.log(
      scrollHeight
    ); //553
    ```

## Q52 ページのスクロール状態について、window.pageXOffset/pageYOffset,scrollTo, scrollBy, scrollIntoViewというプロパティが役立つことを知っていますか?

??? success
    ### どれだけスクロールしたかなら、scrollTopやscrollLeftを使いたいところだが
    - `Chrome/Safari/Opera`の場合、`document.body.scrollTop/Left`
    - それ以外の場合は、`document.documentElement.scrollTop/Left`を使う必要がある
    - クロスブラウザの非互換性を鑑みて、以下を使う

    ### window.pageXOffset/pageYOffset
    - 現在のスクロール状態を取得する

    ```js
    console.log(window.pageYOffset); //1900
    ```

    ### window.scrollBy(x,y)
    - 現在の位置を基準として、ページをスクロール

    ```js
    window.scrollBy(0,2000); //Y方向に2000pxだけスクロール
    ```

    ### window.scrollTo(x,y)
    - ドキュメントの左上隅を基準に、ページをスクロール

    ```js
    window.scrollTo(0,0); //先頭に戻る
    ```

    ### elem.scrollIntoView(top)
    - `elem`:これが見えるようにページをスクロール
    - `top`: `true`なら要素の上端が上部に、`false`なら要素の下端が下部に

    ```js
    let div = document.createElement("div");
    document.body.append(div);
    div.scrollIntoView(false); //見える位置までスクロール
    ```

    ### スクロール不可にしたい場合

    - `document.body.style.overflow = 'hidden'`:不可
    - `document.body.style.overflow = ''`: 再開

    ```js
    document.body.style.overflow = "hidden";
    
    setTimeout(()=>{
      document.body.style.overflow = ""
    }, 1000);
    ```

    !!! warning
        ### スクロールバーが消えることを理解する
        - スクロールバーの領域の分、`padding`を増やしてやれば、画面が乱れることはなくなる

## Q53 要素のウィンドウ座標について操作を行う対場合、getBoundingClientRect/elementFromPointが役に立つことを知っていますか?

??? success
    ### elem.getBoundingClientRect()
    - 要素の`top, left, right, bottom`を表示する
    - `x,y,width,height`という情報も含んでいる
    - `x == left, y == top`
    - `x + width = right, y + height = bottom`

    ### ウィンドウ座標とは?
    - スクロールアウトを考慮せず、ウィンドウの左上隅から計算したもの
    - ページをスクロールすると、ウィンドウ座標は変わる。
    - よって、`elem.getBoundingClientRect()`のように特定の要素にアクセスした場合、マイナス等の値も表示される

    ```js
    //適当にスクロールした後で
    document.body.getBoundingClientRect();
    /*
    DOMRect {
      x: 0, 
      y: -14710, 
      width: 272.66668701171875, 
      height: 536.6666870117188, 
      top: -14710, 
      right: 272.66668701171875
      left: 0
      bottom: -14173.333312988281
      }
    */
    ```
    
    !!! warning
        - `CSS`と異なり、`right`や`bottom`でも左上隅を基準として座標が計算されることに注意

    
    ### document.elementFromPoint(x,y)
    - 指定したウィンドウ座標で最もネストされた要素を返す

    ```js
    // 現在の(20,30)という座標に HTML -> BODY -> SPAN要素があるとする

    let elem = document.elementFromPoint(20,30);
    elem.style.background = "red";
    console.log(elem.tagName); //SPAN
    ```

    !!! warning
        - `(x,y)`に負の値やウィンドウ幅以上の値が指定された場合、`null`が返る
        - 画面外のことまでは考慮してくれない。

## Q54 position:fixedを用いて、スクロールを無視するような位置に要素を配置することができますか?

??? success

    ### `position:fixed`はウィンドウ座標に依存する
    - よって、スクロールしても同じ位置に存在し続ける

    ```js
    //getBoundingClientRect + position:fixed

    let elem = document.createElement("div");
    elem.style.cssText = "width: 1000px; height: 10000px; background-color: gray";
    document.body.append(elem);

    //第一引数の上部にmessageを表示し続ける
    function createMessageUpper(elem, html){

      let message = document.createElement("div");
      message.style.cssText = "position:fixed; color: red";

      let coords = elem.getBoundingClientRect();
      message.style.left = coords.left + "px";
      message.style.top = coords.top + "px";

      message.innerHTML = html;
      return message;
    }

    //スクロールしても同じ位置に表示されることが確認できる
    let message = createMessageUpper(elem, "Hello");
    document.body.append(message);
    setTimeout(()=> message.remove(), 5000);
    ```

## Q55 top/leftとposition:absoluteを用いて、スクロール時に位置が変動するようなコンテンツをページ内に追加できますか?

??? success
    ### 要素のドキュメント座標を取得するコード

    ```js
    function getCoords(elem){
      let box = elem.getBoundingClientRect();

      //ウィンドウ座標 + スクロール分 = ドキュメント全体の座標
      return {
        top : box.top + pageYOffset,
        left : box.left + pageXOffset,
      }
    }
    ```

    ### position:absoluteで固定する
    - ドキュメント座標に依存しているので
    - 親要素が`position:absolute/relative`でない場合、ドキュメント全体に対する座標
    - `position`に関する指定がない場合、親要素から相対的に位置が決まる

    ```js
    //座標を求める
    const getCoords = elem =>{
      let box = elem.getBoundingClientRect();

      return {
        top : box.top + pageYOffset,
        left : box.left + pageXOffset
      };
    }

    //挿入する要素の位置を指定
    const positionAt = (anchor, position, elem) =>{
      let anchorCoords = getCoords(anchor);

      switch(position){
        case "top":
          elem.style.left = anchorCoords.left + "px";
          elem.style.top = anchorCoords.top - elem.offsetHeight + "px";
          break;
        case "bottom":
          elem.style.left = anchorCoords.left + "px";
          elem.style.top = anchorCoords.top + anchor.offsetHeight + "px";
          break;
      }
    }

    const showNote = (anchor, position, html) =>{
      let note = document.createElement("div");
      note.style.cssText = "position: absolute";
      note.innerHTML = html;
      document.body.append(note);
      positionAt(anchor, position, note);
    }

    //(基準となる要素,位置,挿入メッセージ)
    showNote($0, "top", "hoge");
    showNote($0, "bottom", "fuga");
    ```
