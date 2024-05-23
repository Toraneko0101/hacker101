# クイズ(1:DOM概要)

## 方針
??? info
    ### 問われる内容
    - DOMについて
    - レンダリングのフローについて


## Q1. DOMがHTMLドキュメントを変換したオブジェクト同士のつながりを表現したものであることを理解していますか?
??? success

    ### DOM
    - Document Object Modelの略
    
    ### 前提知識
    - `Web`ブラウザは、`Web`サーバから受け取った`HTML`ドキュメントを解析する
    - 解析の際には、ドキュメント(要素、テキスト、属性など)を、オブジェクトに変換する
    
    ### 変換すると何がいいのか
    - プログラムが文書の構造、スタイル、コンテンツを変更可能になる
    
    ### 改めてDOMとは
    - オブジェクト同士のつながりを表現したもの(`HTML`ドキュメントを変換したことで作成された)

## Q2. DOMを使う理由として、JSがHTML文書を作成/変更する機能を持っていないことがあげられることを理解していますか?

??? success
    ### DOMとHTMLドキュメントの違い
    - HTMLドキュメントはデータの取得にだけ使用される
    - 画面表示や表示の変更をするならDOMを介す
    

    ### JSとの違い
    - `Javascript`は単体では、`HTML`文書を作成・変更するための機能を持っていない
    - そのため、`DOM`という`WEB API`を介して、ウェブページとのその要素にアクセスする
    
    ### API?
    - `DOM`は、文章の構造表現を、単一の`API`から利用できるように設計されている
    - つまり、`HTML`文書を変更したい場合は、`JS`から`API`を用いて`DOM`を操作することになる。

    ### WEB APIという言葉について
    - `Application Programming Interface`
    - `WEB`上で提供される`API`
  

## Q3. DOMが使われるようになった経緯と、各ブラウザで同名の機能が使える理由を理解していますか?
??? success
    ### 端的に言うと
    - `DOM`はドキュメント操作に関する取り決め
    - 機能名と結果を統一するため
    
    ### 90年代のWebブラウザ
    - 各々が独自の方法でHTMLを操作しており、互換性がなかった
    - これは、ウェブ開発者は、ブラウザごとにプログラムを作成する必要があることを意味し、非効率であった
    
    ### 98年の勧告
    - W3C(HTML,URL,XML等WWWで使われる技術の標準化を行う団体)は98年にDOMを勧告した
    - これは、 __機能名と結果__ を統一するもので、実装の方法については個々のブラウザに委ねられている
    - 統一以降のブラウザでは、Aという機能を使用した場合、どのブラウザでも同じ結果を得ることができるようになった

    ### 実装の方法が委ねられている理由
    - ブラウザは、`ECMAScript`という仕様に則り、スクリプトを搭載している
    - が、`ECMAScript`のどのバージョンに対応しているかは、ブラウザ毎によって異なる。
    - そのため実装の方法も異なってくる
    
    ### なぜDOMでブラウザ操作の取り決めをするのか
    - `ECMAScript`は、JSの言語仕様を取り決める
    - ドキュメント操作は、JSの範囲外なため取り決めない
    - 代わりに、DOMという別の仕様で取り決めを行い、得られる結果を統一。その上で、JSはDOMを介してドキュメントを操作する。

## Q4. DOMの定義は、WHATWGによって規定されていることを知っていますか?
??? success

    ### DOMの規定先について
    - 現在のDOMは、`WHATWG`によって策定されている

    ### DOMを定義する言語
    - `WEB IDL(WEB interface description language)`
    - `IDL`は、特定の言語に依存しない汎用的なインターフェース定義を提供する
    - そして、それは特定のプログラミング言語に変換可能である

    ### WHATWGの定義を覗いてみた

    ??? example

        ### querySelectorの説明を見る
        - `node . querySelector(selectors)`
        - `selectors`に一致するノードの子孫である、最初の要素を返す。要素がない場合は`null`を返す
        - [参照元](https://dom.spec.whatwg.org/)

        ### 子孫
        - AがBの子であるか、AがBの子孫であるオブジェクトCの子である場合、オブジェクトAはオブジェクトBの子孫と呼ばれる

        ### つまり?
        - 重要なのは、結果が上記の説明に沿っていれば、実装の仕方は問わないということ

## Q5. なぜJSという言語で、ウェブブラウザを操作するのか理解していますか?
??? success
    ### 他の言語からHTMLドキュメントを操作できるか?
    - PythonやPHP等にもライブラリが存在し、DOMは使用可能
    - ただしこれらの言語は、ブラウザ側で実装されておらず、サーバ側で動作（スクレイピングなどに利用）する
    - ウェブブラウザに実装されているJSのように、クライアントサイドのHTMLをリアルタイムで変更することはできない

    ### なぜJSで操作するのか?
    - 各ブラウザがJSを実装しており、またJSで操作ができるようにDOMを実装しているから
    - 将来、他の言語を実装するブラウザを開発されたとしても、余程のシェアがない限り、標準化は為されないため、この傾向は変わらないと思われる。


## Q6. ブラウザからサーバへリクエストを送ったとして、それからDOMツリーが構築されるまでの大まかな流れを理解していますか？
??? success
    ### そもそもDOMツリーとは
    - HTMLドキュメントは木構造
    - そのためドキュメントを解析して作成されるDOMも、木構造モデル
    - この木構造モデルを一般的にDOMツリーと呼ぶ


    ### DOMツリー作成までの手順

    ```mermaid
    graph LR;

    バイトデータ取得　--> A[HTMLドキュメント変換]
    A --> B[トークン化]
    B --> C[オブジェクト化]
    C --> D[DOMツリー]

    ```

## Q7. バイトデータをHTMLドキュメントに変換する際、Content-Typeヘッダが参照されることを知っていますか?

??? success

    ### HTMLドキュメント変換まで
    1. ウェブブラウザがウェブサーバにリクエスト
    2. 通信が確立
    3. データをダウンロード
    4. 未加工のバイトデータを受け取る
    5. ブラウザは、HTTPレスポンスの`Content-Type`ヘッダーに指定されているエンコード情報に基づいて、HTMLドキュメントに変換（デコード）

    ### デコードの例
    - 受け取るバイトデータ(0Aは改行文字)

    ```
    3C 68 74 6D 6C 3E 0A
    ```

    - Content-Typeヘッダー

    ```
    Content-Type: text/html; charset=utf-8
    ```

    - 変換後

    ```
    <html>

    ```

## Q8. HTMLドキュメントをトークン化していく際、ドキュメントが先頭から1文字ずつ読まれていくことを知っていますか?

??? success

    ### トークン化
    - データを最小の構成単位（トークン）に分解すること
    
    ### 手法について
    - HTMLのトークンを行う場合、ドキュメントを1文字ずつ読んでいく
    - この手法は、W3CのHTML標準に基づいている

    ### トークン化の際の注意点
    - HTMLに誤った要素があった場合、この段階で修正を行う(たとえば閉じタグがない時に、自動挿入するなど)
    - `<head>`前のスペースは無視される
    - `<body/>`の後ろに記述があった場合、`<body>`の中に移動させるという決まりごとがある

    ### トークン化の例
    - HTMLドキュメント

    ```html
    <div>
      <p class="intro">Hello,world!</p>
    </div>
    ```

    - トークン化

    ```
    StartTag: <div>
    Character:
      StartTag: <p
      AttributeName: class
      AttributeValue: intro
      Endtag: >
      Character: Hello,world!
      EndTag: </p>
    Character:
    EndTag: </div>
    ```

## Q9. コメントや空白文字などの構成要素もすべてオブジェクト化されることを知っていますか?

??? success

    ### オブジェクト化
    - コメントや空白文字を含む、すべての構成要素をオブジェクトに変換
    - コメントの場合`#comment`という形になる

    ### オブジェクト化の例

    ```
    DOCTYPE: html, HTML, HEAD, #text:, TITLE, #text: hoge, ...
    ```

## Q10. オブジェクトからどのようにDOMツリーが構築されるのか知っていますか?

??? success

    ### オブジェクトのプロパティ
    - プロパティに設定された情報から、その親子関係通りに木構造を生成していく

    ### 注意点
    - コメントはDOMツリーには含まれない傾向がある
    
    ### DOMツリーの例

    ```
    ├DOCTYPE: html
    └HTML
        ├HEAD
        └BODY
        ├H1
        | └#text: 見出しです
        ├DIV
        | └p
        |   └#text: Hello,World!
        └#comment: コメント
    ``` 

    ### 補足（用語）
    - ノード: `DOM`ツリーにおけるすべてのオブジェクト
    - ルート: `DOCTYPE: html`等
    - 子ノード：直接の配下にあるノード
    - 子孫ノード：さらにネストされたノード
    - 親ノード：子ノードから見た上のノード
    - 先祖ノード：親ノードを含むさらに上のノード
    - 兄弟ノード：同じ親ノードを持つノード
    
    ### トラバーサル
    - 木構造モデルをもれなく辿ること



## Q11. DOMツリーの構築と、CSSOMの関係性を知っていますか?
??? success
    
    ### CSSOM(CSS Object Model)
    - `CSS`のルールやプロパティがオブジェクトとして表現されたもの
    - スタイル情報にアクセスするための`API`を提供する
    
    ### CSSOMの作成タイミング
    - `DOM`ツリーの構築中に`CSS`の記述、あるいは`CSS`ファイルへの参照があると作成される
    

    ### CSSOM構築の流れ

    ```mermaid
    graph LR;

    A[バイトデータ取得] --> B[CSSドキュメント変換]
    B --> C[トークン化]
    C --> D[オブジェクト化]
    D --> E[CSSOMツリー]
    ```

    ### CSSOMとレンダーブロッキング
    - `CSSOM`の構築中も`DOM`の構築は進行される
    - しかし、`CSSOM`が構築中の場合、次のステップ（レンダーツリーの構築）に進めない
    - レンダーツリーの構築ができないと、レンダリングの処理（画面表示）が遅れてしまう
    - レンダリングの処理が遅れることを、レンダーブロッキングという

    ### レンダーブロッキングの時間
    - CSSファイルが大きいほど、CSSOMの構築時間は長くなる。
    - 結果レンダーツリーの構築が遅くなるので表示が遅れる

    ### 遅延読み込みでブロッキング対策
    - ファーストビュー（最初に表示される部分）にかかわるCSSだけを通常読み込みし、それ以外のCSSはレンダーブロッキングが行われない遅延読み込みを行う

    ### 遅延読み込みの例(条件付加)
    - メディア属性で条件を付加
    - 条件が満たされたときにはじめて、レンダーブロッキングが起きるようにする

    ```html
    <!-- `desktop.css`ファイルは、画面の幅が768px以上の場合にのみダウンロードされる -->
    <!-- 画面幅が小さい時は、不要な読み込み遅延を避けることができる -->
    <link rel="stylesheet" href="desktop.css" media="screen and (min-width: 768px)">
    ```

## Q12. CSSOMの構造について知識はありますか?
??? success
    ### 構造について
    - 継承に似ている。
    - 親ノードの設定を子ノードが継承し、子ノードで異なる設定があると、設定が上書き

    ### 親要素からスタイルを継承する例

    ```html
    <body style="font-size: 14px; color: black;">
      <p>
      <span>a</span>
      </p>
      <div>
      <p style="font-size: 12px;">
        <span style="color: blue;">b</span>
      </p>
      </div>
      <div style="color: yellow;">
      <div>c</div>
      </div>
    </body>

    <!-- a: font-size: 14px; color: black; -->
    <!-- b: font-size: 12px; color: blue; -->
    <!--c: font-size: 14px; color: green; -->
    ```

    ### 重要度について
    - 同一ノードに対して複数の設定があった場合、より具体的なセレクタが優先される
    - 例えば下記の場合、`div p span{}`が適用される

    ```css
    div p span{

    }

    p span{

    }

    span{

    }
    ```

    ### 重要度が同じ場合、
    - 後から書かれたスタイルが優先される
    - 下記の場合、色は青になる

    ```css
    span {
      color: "black";
    }

    span{
      color: "blue";
    }
    ```

    ### !important
    - !importantが付いたスタイルは、重要度や、後から書かれているかにかかわらず、優先される

    ```css
    !important span{
      color: "black";
    }

    span{
      color: "blue";
    }
    ```

    ### CSSとデフォルト値
    - ウェブブラウザには`user agent stylesheet`というデフォルトの設定がある
    - CSSの設定はオーバーライド

    ### デフォルト値の無効化
    - リセットCSS(reset.css)はデフォルト値を無効化する
    - 例として`destyle.css`という`reset.css`を適用すると、タグの大きさが全て同じに設定される

## Q13. DOMツリーの構築とJavascriptの関係性について知っていますか?
??? success
    ### JS読み込みについて
    - JSは記述された場所で実行される

    ### DOM構築の中断
    - HTMLパーサがJSのコードに到達すると、DOMの構築は一時中断され、処理の制御はJSエンジンに移る

    ### DOM構築の再開
    - JSがその時点でのDOMやCSSOMの変更処理を完了すると、DOMの構築は再開される

    ### パーサブロッキングリソース
    - パーサの処理を中断させるリソース

    ### 中断時間について
    - JSの処理が大きくなると、中断時間も長くなる
    - 外部jsファイルの場合、読み込み時間も中断時間に含まれる

    ### JSを読み込む場所について
    - その時点で処理を行いたいノードが作成されていない場合、当然処理は行えない
    - 従ってJSは、`</body>`の真上に書かれたり、`defer`属性を付けたり、非同期処理で適用されたりする

    ??? warning
        - レンダリングは段階的に行われるため、JSより1つ以上上の行に書いたHTMLとCSSが解析されている保証はない

## Q14. DOMツリーとCSSOMツリーが構成されると生成されるレンダーツリーの作成ステップについて理解していますか?

??? success
    ### レンダーツリーとは
    - DOMとCSSOMの2つをもとに作成されるもの

    ### レンダーツリーに含まれる要素とその意義
    - 表示される要素とそのスタイル情報
    - ウェブブラウザはレンダーツリーをもとに、画面表示を行う
    
    ### レンダーツリーの作成ステップ
    1. DOMツリーのルートから順に処理を行う
    2. 表示に関係のないノードはスキップする(`meta`タグや`display: none;`等)
    3. 各ノードでは、CSSOMの設定を適用する
    4. 以上を繰り返す
  
## Q15 レンダーツリーの作成後に行われるレイアウトフェーズについて知っていますか?

??? success
    ### レイアウトフェーズ
    - レンダーツリーに含まれる情報(要素、スタイル等)をもとに、各ノードに対して、サイズや位置などの計算を行う
    - 計算後、最終的な配置(`x, y, width, height`)が確定する
    - これを __レイアウト__ という

    !!! tips
        計算にはノードに対するスタイルの他、たとえば以下のものが使われる

        - ブラウザのwindowサイズ
        - viewportのサイズ
        - パーセンテージベースのサイズ指定
        - 相対的な配置
        - 画像の実際の解像度
        - テキストの行の高さ
        - ボックスモデルの影響
        - floating要素の影響

## Q16. レイアウトフェーズの後で行われる、ペインティングフェーズについて知っていますか?

??? success
    ### ペインティングフェーズ
    1. 配置場所が確定したら、実際のデバイスのピクセル密度(DPI)や画面解像度を参照する
    2. `x,y`等の情報を物理的なピクセルに変換
    3. 各要素に対して描画命令を生成する（たとえば線を描く、塗りつぶすなど）
    4. 実際に描画が行われる
    5. この過程を、 __ペインティング__ という 
   
    ### ペインティングにかかる時間について
    - レンダーツリーの容量に依存
    - 複雑なグラデーションなども負荷がかかる
    - 2回目以降は、変更箇所のみが再描画（リペイント）されるため、処理は軽い

## Q17 レンダリング(画面表示)のフローについて理解していますか?(Q16までのまとめ)

??? success
   
    ### 主に以下の順序
    1. DOMツリーが構築
    2. CSSOMツリーが構築
    3. DOMツリーとCSSOMツリーを組み合わせてレンダーツリーを構築
    4. レンダーツリーをもとにレイアウト
    5. レイアウトをもとにペインティング


    ### 注意点
    - 上記の順序は、(JSの処理を除いて)オーバーラップして処理される
    - 4番の処理がすべて完了しないと、5番の処理に移らないという理解は誤り

    ### クリティカルレンダリングパス
    - ブラウザが、サーバからデータをダウンロードしてから、最初のペイントが行われるまでの流れのこと

## Q18. クリティカルレンダリングパスを求める際、イベントログが参考になることを知っていますか？

??? success

    ### 確認方法
    - 開発者ツールの「パフォーマンス --> イベントログ」を確認
    
    ### 例
    - 大幅に省略しています

    | 開始時間    | セルフ時間 | 合計時間   | アクティビティ                   |
    | ----------- | ---------- | ---------- | -------------------------------- |
    | 227.8ミリ秒 | 0.0ミリ秒  | 0.0ミリ秒  | リクエストの送信                 |
    | 227.9ミリ秒 | 0.0ミリ秒  | 0.0ミリ秒  | レスポンスの受信                 |
    | 229.5ミリ秒 | 0.0ミリ秒  | 0.0ミリ秒  | データの受信                     |
    | 233.0ミリ秒 | 49.2ミリ秒 | 49.2ミリ秒 | プロファイリングのオーバーヘッド |
    | 290.7ミリ秒 | 0.1ミリ秒  | 1.7ミリ秒  | スクリプトの評価                 |
    | 331.8ミリ秒 | 4.3ミリ秒  | 33.7ミリ秒 | HTMLの解析                       |
    | 382.9ミリ秒 | 5.6ミリ秒  | 4.6ミリ秒  | スタイルの再計算                 |
    | 388.5ミリ秒 | 4.8ミリ秒  | 4.8ミリ秒  | レイアウト                       |
    | 395.3ミリ秒 | 0.8ミリ秒  | 0.8ミリ秒  | ペイント                         |

    - なお、上記の例の場合、実際の処理は900ミリ秒あたりまで続いている
    - 実際に見ると、処理がオーバーラップしていることが確認できる

    ### 統計的に見たい場合
    - `パフォーマンス --> 概要`を見る
    - `パフォーマンス --> 呼び出しツリー`では、アクティビティ別等で所要時間を閲覧可能

    ### 所要時間が長いタスクを調べたい場合
    - 上部の、赤色の項目をクリック
    - アクティビティをクリックすると、警告の要因が表示
    - さらなる最適化を求める場合については後述
    - 記述するにあたって参照予定のURL : [時間のかかるタスクを最適化する](https://web.dev/articles/optimize-long-tasks?utm_source=devtools&hl=ja)

## これ以降は旧version。あとで消す

## Q16. Nodeの種類を大別できますか?

??? info
    - DOMとして作成された各オブジェクト(Node)は、大きく12種類に分類できる
    - これらはすべて独自のプロパティやメソッドを持つ
    - また、これらはすべてNodeインターフェースを親に持つ
    - NodeインターフェースはEventTargetインターフェースを親に持つ

    ### Node一覧
    - Document: HTMLツリーの頂点であるエントリーポイント
    - Element: HTMLエレメント
    - CharacterData: 文字を包含する中小インターフェース
    - Text: テキスト
    - Comment: ソース内のコメント
    - CDATASection: `<`や`&`をエスケープなしでそのまま記述する
    - Attr: Elementオブジェクトの1つの属性
    - DocumentFragment: Documentの軽量版。
    - DocumentType: !DOCTYPE

    ### 非推奨ノード一覧
    - Notation: 特定の種類のコンテンツの定義方法を表現する
    - Entity: 特定の文字列を代替する
    - EntityReference: 特定のエンティティを参照する

    ### 継承関係早見（主なものだけ記載）
    ```
    EventTarget
      └ Node
         ├ [Document]
         ├ [Element]
         |    ├ HTMLElement
         |    |   ├ HTMLAnchorElement
         |    |   └ HTMLInputElement
         |    └ SVGElement
         ├ [Attr]
         ├ [DocumentFragment]
         ├ [DocumentType]
         └ [CharacterData]
              ├ [Text]
              ├ [Comment]
              └ [CDATASection]
    [Notation]
    [Entity]
    [EntityReference]
    
    ```

    ### 例
    - HTMLAnchorElementインターフェース(`<a>`)や、HTMLInputElementインターフェース(`<input>`)は、HTMLElementインターフェースが元になっている
    - またHTMLElementインターフェースは、Elementインターフェースが元になっている

## Q17. Nodeがどのオブジェクトから生成されているかを調べる事ができますか?

??? info
    - `要素.constructor.name`を用いる

    ### 例

    ```js
    //p要素を選択した場合
    console.log($0.constructor.name); //HTMLParagraphElement

    //h2要素を選択した場合
    console.log($0.constructor.name); //HTMLHeadingElement

    //画像を選択した場合
    console.log($0.constructor.name); //HTMLImageElement
    ```

## Q18. console.dir()の意味合いを知っていますか?

??? info
    - `console.dir()`はオブジェクトに対し、利用可能なすべてのプロパティとメソッドを表示する

    ### 例

    ```js
    //とあるimg要素を選択
    console.dir($0); 
    
    //outputs:
    //accessKey: ""
    //align: ""
    //alt: "Final Screenshot"
    // ...
    ```

## Q19. オブジェクトの継承関係を確認することはできますか?

??? info
    - `object instanceof constructor`を使う

    ### 例
    - JSはprototypeチェーンで継承を実現しているため、基底の機能も使うことが可能

    ```js
    //適当なpタグを選択
    console.log($0 instanceof HTMLElement); //true
    console.log($0 instanceof Element); //true
    console.log($0 instanceof Document); //false
    console.log($0 instanceof Node); //true
    console.log($0 instanceof EventTargetc); //true

    //HTMLElementはConstructorなので以下の様にしてはいけない
    console.log(HTMLElement instanceof Element);

    const d = new Document();
    console.log(d instanceof Node); //true: あくまでobjectと比較する事
    ```

## Q20. nodeTypeを使って、条件分岐の式を書くことができますか?

??? info
    - nodeTypeはNodeインターフェースの読み取り専用プロパティ
    - 整数値でノードが何であるかを識別する

    ### 一覧
    - Element: (1)
    - Attribute: (2)
    - Text: (3)
    - CDATASection: (4)
    - ProcessingInstruction: (7)
    - Comment: (8)
    - Document: (9)
    - DocumentType: (10)
    - DecumentFragment: (11)

    ### 使用例

    ```js
    document.nodeType === Node.DOCUMENT_NODE; //true
    document.nodeType === 9; //true

    document.createDocumentFragment().nodeType === Node.DOCUMENT_FRAGMENT_NODE; //true

    const p = document.createElement("p");
    p.textContent = "テスト";
    p.nodeType === Node.ELEMENT_NODE; //true
    p.firstChild.nodeType === Node.TEXT_NODE;
    ```

## Q21. DOMで扱われる文字について理解していますか?

??? info
    - DOMで扱う文字を __DOMString型__　という
    - 符号なし16bit整数
    - JSのStringに対応しているので、JSではStringとして使用可能
    - つまり、JSから用いる以上、通常の文字列として扱える

    ### 例
    - `Element.innerHTML`プロパティは、NodeのHTMLドキュメントをDOMString型で保持する

    ```js
    const hoge = document.createElement("h1");
    hoge.innerHTML = "hogehoge";
    console.log(hoge.innerHTML); //hogehoge
    ```

## Q22. Nodeのクラス属性が、どのような形で保持されているか知っていますか?

??? success
    - スペースで区切られた属性値は、リストオブジェクトとして保持される
    - この時、 __DOMTokenList__　インターフェースが用いられる

    ### 例
    - `className`は、`Element.classList`で保持される

    ```js
     const hoge = document.createElement("h1");
    hoge.innerHTML = "hogehoge";
    hoge.classList = "hoge fuga";
    console.log(classList); //DOMTokenList(2) ["hoge", "fuga", value: "hoge fuga"];
    ```

## Q23. Nodeのスタイル属性がどのように保持されているか分かりますか?
??? info

    - CSSに関する情報は.styleプロパティで閲覧可能
    - styleプロパティは`CSSStyleDeclaration`オブジェクトが生成元になっている

    ### 例

    ```js
    const div = document.createElement("div");
    div.style = "color: #f22; background-color: #fcc; width: 400px; height: 300px;";
    console.log(div.style.constructor.name); //CSSStyleDeclaration
    console.log(div.style);

    //outputs: (プロパティを見ると、反映されていることが確認できる)
    // CSSStyleDeclaration {
    //0: 'color', 
    //1: 'background-color', 
    //2: 'width', 
    //3: 'height', 
    // accentColor: '', 
    // additiveSymbols: '', 
    // alignContent: '', 
    // alignItems: '', 
    // alignSelf: '', 
    // …
    // }
    ```

## Q24. Nodeをリストで扱うオブジェクトについて知っていますか?

??? info

    ### HTMLCollection
    - たとえば`getElementsByClasssName()`は引数に指定したclassNameを持つ要素の、`HTMLCollection`を返す

    ### 例1

    ```js
    const div1 = document.createElement("div");
    const div2 = document.createElement("div");
    div1.id = "aa";
    div2.id = "bb";
    div1.className = "foo";
    div2.className = "foo";
    document.body.appendChild(div1);
    document.body.appendChild(div2);

    /*
    <div class="foo" id="aa"></div>
    <div class="foo" id="bb"></div>
    */

    const divList = document.getElementsByClassName("foo");
    console.log(divList);

    /*
    * outputs:
    * HTMLCollection(2) [div#aa.foo, div#bb.foo, aa: div#aa.foo, bb: div#bb.foo]
    * 0: div#aa.foo
    * 1: div#bb.foo
    * aa: div#aa.foo
    * bb: div#bb.foo
    * length: 2
    * [[Prototype]]: HTMLCollection
    *   item: ƒ item()
    *   length: (...)
    *   namedItem: ƒ namedItem()
    *   ...
    */
    ```

    ### HTMLCollectionが持っているプロパティ等

    - `lenght`: アイテム数を所持
    - `item()`: 指定された位置にあるノードを返す
    - `namedItem()`; 指定されたid(またはname)属性が一致するノードを返す
    - `for ... of`: 可能

    ```js
    //1番目のノードにアクセス
    console.log(divList[0]);
    console.log(divList.item(0))

    //idがaaであるノードを取得
    console.log(divList.namedItem("aa"));

    //lengthメソッド
    console.log(divList.length);

    //for ... ofを用いる
    for(const node of divList){
      console.log(node);
    }

    ```

    ### HTMLCollectionの注意点
    !!! warning
        - 配列ではないので、配列特有のメソッドは使えない
        - その場合は、Array.fromやスプレッド構文で変換可能
        - for ... inを使うと、列挙可能なすべてのプロパティを反復するので使うべきではない

    - 例

    ```js
    const list = [...divList];
    list.toSorted(); //listに変換したので可能

    for (const node in divList){
      console.log(node); //0,1,2,length,item ...
    }
    ```

    ### NodeList
    - HTMLCollectionと比べて扱えるメソッドが多い
    - たとえば`querySelectorAll()`は、引数の`selector`に一致する要素をNodeListで返す

    ### NodeListのメソッド例

    | メソッド名 | 機能 | 内容                         |
    | ---------- | ---- | ---------------------------- |
    | item()     | 取得 | 指定位置のノードを返す       |
    | forEach()  | 反復 | 各要素に指定された関数を実行 |
    | entries()  | 反復 | キーと値の順次処理           |
    | keys()     | 反復 | キーを順次処理               |
    | values()   | 反復 | 値を順次処理                 |

    ```js
    const nodes = document.querySelectorAll(".foo");
    console.log(nodes);

    /*
    * NodeList(2) [div#aa.foo, div#bb.foo]
    * 0: div#aa.foo
    * 1: div#bb.foo
    * length: 2
    * [[Prototype]]: NodeList
    */

    //divElementだけを表示する
    //.nodeNameでタグ名を確認できる
    nodes.forEach((node)=> {
      if(node.nodeName == "DIV") console.log(node);
    });

    //entries
    for ([key, value] of nodes.entries()){
      console.log(key, value);
    };
    /*
    * 0 <div id="aa" class="foo"></div>
    * 1 <div id="aa" class="foo"></div>
    */

    ```

## Q25. HTMLCollectionとNodeListの違いを理解していますか?

??? info
    ### HTMLCollectionはDOMの変更に対して動的
    - `getElementsByClassName()`で取得した後にノードを追加
    - その後に、再度取得すると反映される

    ### NodeListはDOMの変更に対して静的?
    - `querySelectorAll()`で取得した後でノードを追加
    - 再度取得しても反映されない

    ### 動的なNodeListも存在する
    - 例として`getElementsdByName()`がある

    ### 例

    ```js
    const htmlCollection = document.getElementsByClassName("foo");
    const nodeList = document.querySelectorAll(".foo");

    console.log(htmlCollection.length); //2
    console.log(nodeList.length); //2

    const li = document.createElement("li");
    li.classList = "foo";

    //htmlCollectionには追加していないが反映されている
    console.log(htmlCollection.length); //3
    console.log(nodeList.length); //2
    ```

## Q26. Documentインターフェースについて知っていますか？

???info
    ### 概要
    - DocumentインターフェースはDOMの全体像を管理するインターフェース
    - DOM内に存在する各ノードのエントリーポイントになる
    - そのため __ノードを取得する__ 様々なメソッドを所持している
    - documentオブジェクトは、Documentインターフェースを継承している

    ### documentオブジェクトとデフォルトプロパティ
    - Nodeはデフォルトでプロパティを持っている。
    - 開発者はその値を上書きしていく。

    ### デフォルトプロパティの例
    - `<meta charset="UTF-8">`を記述した場合

    ```js
    console.log(document.characterSet); //UTF-8
    ```

    - 記述しない場合

    ```js
    console.log(document.characterSet); //windows-1252
    ```

    ### インターフェースの継承関係を確認する
    ```mermaid
    graph LR;

    A[Object] --> B[EventTarget]
    B --> C[Node]
    C --> D[Document]
    ```

    ```js
    console.log(document)
    /*
    * [[Prototype]]: HTMLDocument
    *   [[Prototype]]: Document
    *     [[Prototype]]: Node
    *       [[Prototype]]: EventTarget
    *         [[Prototype]]: Object
    */
    ```

    ### HTMLDocumentについて
    ??? warning
        - もともとHTMLDocumentインターフェースはDocumentインターフェースを拡張するために実装された
        - しかし現在では多くの機能がDocumentインターフェースに統合されており、エイリアスのような扱いになっている
        - 今後、このインターフェースは削除される可能性がある

## Q27. documentオブジェクト(node)はJSのオブジェクトでしょうか？(y/n)

??? info
    - `Yes`

    ### 確認方法
    - JSのオブジェクトなので、以下の様にプロパティを追加可能

    ```js
    const foo = document;
    foo.bar = "Hello";
    //引数に設定したプロパティを持っているか確認する
    console.log(foo.hasOwnProperty("bar")); //true
    ```

## Q28. インターフェースにプロトタイプを設定した場合、それを実装するオブジェクトで使用できますか?(y/n)

??? info
    - `Yes`
    - インターフェースにプロトタイプを設定すると、それを実装するオブジェクト全てで使用可能

    ```js
    Node.prototype.Hello = () => {
      return "Hello";
    }

    const foo = document.createElement("div");
    console.log(foo.Hello()); //Hello
    ```

## Q29. Nodeのプロパティを変更することで、なぜ画面表示に影響するのか理解していますか？

??? tips
    - 例えば以下のコードで、画面上にHelloが表示されるのはなぜだろうか？

    ```js
    const foo = document.getElementById("foo");
    foo.innerHTML = "Hello";
    ```

??? info
    ### 参照
    - クイズ(発展)のQ17でも説明されている
    - JSは、数値やブール型などはスタックで管理している
    - 一方、Objectや配列の場合、実際のデータはヒープで管理し、スタックからそのデータを参照する
    - つまり、上記の例でいう、fooは、ヒープ内で管理されているデータを、参照しているに過ぎない
    - そのためfooを変更すると、参照している値も変化する
    - 参照している値が変わったということは、画面表示も当然変化する。（同じ値を参照しているため）
    - 分からない場合は、そもそもDOMツリーが、互いのオブジェクトを参照することで、構成されていることを再確認しておきたい

## Q30. Document.documentElementが何を意味するか知っていますか?

??? info
    - `documentElement`プロパティは、ルート要素である`<html>`のノードの参照を所持する

    ### 要素を全表示

    ```js
    const html = document.documentElement;
    console.log(html);
    ```

    ### `<html>`ノードの子要素を確認する(補足:childrenとchildNodeの違い)
    - Nodeオブジェクトのプロパティである`children`が使える
    - `.children`はHTMLドキュメントのルート要素の子ノードのうち、要素ノードのみ`HTMLCollection`で返す
    - `.childNodes`はテキストノードやコメントノードを含む、すべての子ノードを動的な`NodeList`で返す

    ```js
    const html = document.documentElement;
    console.log(html.children); // HTMLCollection(2) [head, body];

    conso.log(html.childNodes); // NodeList(3) [head, text, body];
    ```

## Q31. Document.headが何を意味するのか知っていますか?

??? info
    - `<head>`ノードの参照を保持する

    ### 例

    ```js
    const head = document.head;
    console.log(head.children); //HTMLCollection(11) [meta, title,style,custome-style,...]
    ```

## Q32. Document.bodyが何を意味するのか知っていますか?

??? info
    - `<body>`ノードの参照を保持する
    - `<body>`がない場合は`null`となる
    - `Document.head`と異なり代入可能だが、代入すると子ノードはすべて消滅する

    ### 例

    ```js
    const body = document.body;
    console.log(body.children); //HTMLCollection(7) [script, link, ...]
    ```

    ### 使用箇所に関する注意点
    !!! warning
        - `head`内で使用すると、まだ`<body>`が読み込まれていないので、`null`となる

## Q33. Document.getElementById()の意味合いを理解していますか?

??? info
    ### 概要
    - 引数に指定したid属性が付与されている要素のノードを返す
    - 存在しない場合は`null`
    !!! warning

        - id属性はHTMLの中で一意であるというルールがある(そのため任意の要素に対しては呼び出させない。というより呼び出す必要がない)
        - しかし一意でなくてもエラーは起きない
        - その場合、`getElementById()`は最初のものを取得する
    !!! example

        ```js
        const foo = documetn.getElementById("foo");
        foo.firstChild; //最初の子要素を表示
        ```

## Q34. Document.getElementsByClassName()で複数のクラス名を指定する方法を知っていますか?

??? info
    ### 基本
    - `.getElementsByClassName()`は、指定されたclass属性を持つノードを`HTMLCollection`で返す
    - 特定のノードを取得したい場合は、indexで指定する
    !!! warning
        - 複数形の`s`がついている。対象が複数なので

    !!! example

        ```js
        const fooList = document.getElementsByClassName("foo");

        console.log(fooList[0]);
        ```

    ### 複数のクラス名を指定する

    ```js
    //`hoge`クラスおよび`fuga`クラスを両方持つ要素を取得
    document.getElementsByClassName("hoge fuga");
    ```

    ### 更に限定する

    ```js
    //`main`というIDを持った要素内の`text`クラスを持つ要素を取得
    document.getElementById("main").getElementsByClassName("test");
    ```

## Q35. Document.getElementsByTagName()で全ノードを取得する方法を知っていますか?

??? info
    ### 基本
    - `getElementsByTagName()`は、指定されたタグ名を持つノードを`HTMLCollection`で返す

    ### コード例

    ```js
    const fooList = document.getElementsByTagName("div");
    //全てのタグを対象としてノードを取得したい場合
    const fooList = document.getElementsByTagName("*");
    ```

    ### 注意点
    !!! warning
        - `getElementByTagName()`は引数を小文字に変換する。そのため、大文字小文字のタグが混在している際には不向き
        - webkitの場合、`NodeList`が返されるバグがある。そのため、ブラウザによっては、要素が動的に変更されない恐れがある。

## Q36. Document.getElementsByName()を用いる際の注意点について知っていますか？

??? info

    ### 注意点
    - `.getElementsByName()`は指定されたname属性を所持するノードを __動的__ な`NodeList`で返す
    - つまり、`NodeList`であるため`forEach`は使えるが、動的であるため、要素数が変更されている可能性がある
    - またEdgeでは`HTMLCollection`を返すという厄介な特徴もある
    - 使いたくない

## Q37. Document.querySelectorAll()で疑似クラスを取得できることを理解していますか?

??? info
    ### 概要
    - `querySelectorAll()`は指定したCSSセレクタを所持するノードを静的な`NodeList`で返す
    - CSSセレクタとして疑似クラスも取得可能

    ### CSSセレクタについて
    - 詳しくはCSSで触れる
    - 先頭`.`でクラス指定
    - 先頭`#`でid指定

    ### 疑似クラス
    - セレクタに付加するキーワード
    - 例えば`:hover`なら、ユーザのポインタがボタンの上に載ったときに、適用される。

    ### 例

    ```js
    //fooクラスを指定
    console.log(document.querySelectorAll(".foo"));

    //fooとbarのclassを保持するdiv
    console.log(document.querySelectorAll("div.foo, div.bar"));

    //fooのidを保持するdiv
    console.log(document.querySelector("div#foo"));
    
    //ulタグ内のliタグのうち、最後の要素
    console.log(document.querySelectorAll("ul > li:last-child"))

    //data-active属性が1であるliタグ
    console.log(document.querySelectorAll("li[data-active='1']"))
    ```

    ### 注意点
    !!! warning
        - ここで、`:visited`を試してみると、`length = 0`という結果が得られる
        - これはユーザのプライバシーを保護するためであり、従って一部の疑似クラスには対応していない。

    ### querySelector()の場合
    - 引数に一致する最初のElementを返す
    - querySelectorAll()より速い

    ### 特殊文字のエスケープについて
    - そもそも不正なCSSセレクタを指定しなければいい話ではある
    - CSSの構文に従っていないセレクタに一致させるためには、バックスラッシュでエスケープする必要がある
    - しかし、バックスラッシュはJSのエスケープ文字でもあるので、JSのエスケープ文字であることを、先にエスケープ文字でエスケープしておく(`\\`)

    ```html
    <div id="foo:bar"></div>
    <script>
        document.querySelector("#foo\\:bar");
    </script>
    ```

## Q38. 以下のDocumentインターフェースにおけるプロパティについて、大体の役割が分かりますか？

??? tips
    - よく使うものを列挙した
    - `timeline`、`location`、`readyState`は使い道が多そう

    ### Document.activeElement
    ??? info
        - 現在、focusのある要素を返す

    ### Document.characterSet
    ??? info
        - 文書に適用されている文字セットを返す(例: UTF-8)

    ### Document.childElementCount
    ??? info
        - 現在の文書の子要素の数を返す(通常は`<html>`なので1)
        - 特定の要素の子要素の数を取得したい場合は`Element.childElementCount`の方を用いる

    ### Document.contentType
    ??? info
        - 現在の文書のMIMEヘッダからContent-typeを返す
        - 例) `text/html`

    ### Document.documentURI
    ??? info
        - ドキュメントに関連付けられたURIを返す
        - 変更は不可

    ### Document.forms
    ??? info
        - 現在の文書中の`<form>`要素を`HTMLCollection`で返す

    ### Document.images
    ??? info
        - 現在の文書中の`<image>`要素を`HTMLCollection`で返す

    ### Document.links
    ??? info
        - 現在の文書中のすべてのハイパーリンクのリスト`HTMLCollection`でを返す

    ### Document.scripts
    ??? info
        - 現在の文書中のすべての`<scripts>`要素を、HTMLCollection`で返す

    ### Document.styleSheets
    ??? info
        - 文書に埋め込まれているスタイルシートオブジェクトやリンクされているスタイルシートのリストを返す

    ### Document.timeline
    ??? info
        - 新しく2022年に利用可能になったプロパティ
        - Performance.timeOriginからの経過時間をミリ秒単位で表す
        - Performance.timeOrigin以前は、currntTimeはnullになる
        - timelineはdocumentの存続期間中維持される
        ### 使用例

        ```js
        if (document.timeline.currentTime > 100000) {
          console.log("too long time");
        };
        ```
    
    ### Document.defaultView
    ??? info
        - windowオブジェクトを返す

        ### 使用例

        ```js
        console.log(document.defaultView.width) // 150;
        ```
    
    ### Document.lastModified
    ??? info
        - 文書の最終更新日を返す

    ### Document.location
    ??? info
        - 現在の文書のURIを返す
        - URLを文字列として受け取るだけならdocument.URLも有用
        - `document.location`に他の文字列を代入すると、ブラウザは代入されたウェブサイトを読み込む
        - ホスト名や、プロトコルなどの情報も含まれる

        ### 使用例

        ```js
        //ページ遷移
        document.location = "https://www.example.com"
        ```

    ### Document.readyState
    ??? info
        - documentの読み込み状態を記述する
        - このプロパティの値が変化すると、`readystatechange`イベントが発行される

        ### 状態について
        - `loading`: 読み込み中
        - `interactive`: 文書は読み込んだが、スクリプトや画像、スタイルシートは読み込み中。`DOMContentLoaded`が発行されている状態
        - complete: 文書とすべてのサブリソースが読み込み済み。`load`イベントが発行されている

        ### 使用例
        - `readyStateChange`イベントを`load`イベントの代替とする
        ```js
        document.onreadyStatechange = () =>{
          if(document.readyState === "complete"){
            console.log("document is loaded.");
          }
        }
        ```
    
    ### Document.referrer
    ??? info
        - 直前に閲覧していたURIを返す

        !!! warning
            - ユーザがリンクではなく、直接ページに移動した場合は空文字になる
            - rel="noreferrer"で無効化されたりするので、大抵使えない

    ### Document.title
    ??? info
        - 現在の文書の`title`を取得する
        - 設定も可能

## Q39. 非推奨のプロパティが存在することを理解していますか?

??? info
    - たとえば以下のプロパティは、昔は使われていた
    - 時折公式ドキュメントを参照し、頭の中の情報を更新すべき
    ### 例
    - `Document.linkColor`: 文書中のハイパーリンクの色を取得/設定する

    ### 代替案の例
    - `:link`の疑似クラスで設定する

    ```css
    a:link{
      color: "red";
    }
    ```

## Q40. getElementById()がwindow.プロパティと等価でない理由を知っていますか?

??? info
    ### 概要
    - たとえば以下でも取得は可能

    ```js
    const div = document.createElement("div");
    document.body.appendChild(div);
    div.id = "foo";
    console.log(window.foo);
    console.log(foo); //windowは省略できるのでこう書ける
    ```

    ### id名が変数名として成立しない場合は、以下の様に書く

    ```js
    console.log(window.["foo-a"])
    ```

    ### 問題点
    - 同名の変数がある場合は、そちらが優先される

    ```js
    const foo = "H1";
    const div = document.createElement("div");
    document.body.appendChild(div);
    div.id = "foo";
    console.log(window.foo); //<div id="foo"></div>
    console.log(foo); //H1
    ```

## Q41. Nodeインターフェースについて知っていますか?
??? info
    - NodeインターフェースはすべてのDOMインターフェースの親となる。
    - 参照(Q16. 継承関係早見)
    - そのため`Document`や`Element`を含むすべてのノードは、`Node`インターフェースの機能を使用可能
    - `Node`は`EventTarget`インターフェースを継承している
    - `Node`のプロパティは基本的に、テキストノードやコメントノードを含むすべてのノードが対象。

## Q42. Node.nodeNameの使いどころが分かりますか?

??? info
    - 指定したノードの名前を大文字で所持する

    ### 例

    ```js
    console.log(document.body.nodeName); //BODY
    //aタグを選択
    console.log($0.nodeName); //A
    ```
## Q43. Node.childNodesの使いどころが分かりますか?

??? info
    - 子ノードのリストをNodeListで保持する
    - `Element.children`と異なり、すべての子ノードが含まれる(参照&使用例：Q30)

## Q44. 先頭の子ノードに対して、childNodesより高速にアクセスしたい場合、どうすればいいですか?
??? info
    - `Node.firstChild`を使う
    - `Node.lastChild`の場合、最後の子ノードにアクセス可能
    - 使用例はQ44

## Q45. firstElementChildとfirstChildの違いが分かりますか?
??? info
    - `Element.firstElementChild`はテキストやコメントなどを除く子ノードから選択される

    ### 使用例

    ```js
    console.log(document.firstChild); //<!DOCTYPE html>
    console.log(document.firstElementChild); //<html>...</html>
    ```

## Q46. 子ノードを持っているかどうかを真偽値で表す方法を知っていますか?

??? info
    - `Node.hasChildNodes()`
    - これが真であれば、さらに再帰を行うなどの使い道

## Q47. 親ノードを確認する方法を知っていますか?

??? info
    - `Node.parentNode`
    - `removeChild`に使用するなどの使い道

## Q48. 兄弟ノードを取得する方法を知っていますか?

??? info
    ### Node.nextSibling
    - 次の兄弟ノードを求める

    ### Node.previousSibling
    - 前の兄弟ノードを求める

    ### 注意点
    !!! warning
        - 改行などのテキストノードを含む
        - 例によって、Elementだけの兄弟ノードを求めたいなら、`Element.nextElementSibling`等を用いる

## Q49. Node.textContentとElement.innerHTMLの違いが分かりますか?

??? info
    ### Node.textContent
    - `textContent`はテキストを代入可能
    - HTMLタグの場合は、エンコードされて代入されるのでHTMLが解釈されない

    ### Element.innerHTML
    - HTMLタグが解釈される

    ### 解釈しないメリット
    - XSS攻撃の対策

## Q50. Node.nodeValueとdataプロパティの違いはありますか?(y/n)

??? info
    - `No`
    - 完全に好みの問題

    ### nodeValue概要
    - ノードの値をDOMStringで保持する
    - 主に以下のものに対して使用する

    ### 使い道
    - Attrノード：属性値
    - Textノード:テキストの値
    - Commentノード:コメントの値

    ### 例

    ```html
    <body>
    <!--test-->
    ```

    - コメントを書き換える

    ```js
    //改行を含むので、index = 1がコメントとなる
    document.body.childNodes[1].nodeValue = "Nekodayo"
    ```

    - 書き換え後

    ```html
    <body>
    <!--Nekodayo-->
    </body>
    ```

## Q51. Elementインターフェースの特徴について理解していますか?

??? info
    ### Elementインターフェース
    - HTMLドキュメントで言うノード。要するに、タグで囲まれた範囲のノードが実装するインターフェース。
    - Nodeインターフェースを実装しているので、そちらのプロパティ、メソッドももちろん使える

    ### 例

    ```js
    document.body instanceof Element; //true
    document instanceof Element; //false
    ```

## Q52. Element.innerHTMLで、`<script>`要素を実行する方法を知っていますか?

??? info
    ### そもそも.innerHTMLとは
    - 該当ノードのHTMLドキュメントをDOMStringで保持するもの
    - HTMLドキュメントで置き換え可能
    - `+=`を行うと、既存のものに追加も可能

    ### 例
    - 内部的には古いコンテンツを削除した後、新しいコンテンツと古いコンテンツを連結したものを代入しているので、効率は悪い
    ```js
    document.body.innerHTML += "<pp>AA</pp>";
    ```

    ### 本題
    - `innerHTML`は本来XSS対策で`script`要素を実行しない

    ```js
    document.body.innerHTML = "<script>alert()</script>";
    console.log(document.body.innerHTML); //実行されない
    ```

    ### 抜け道
    - `onerror`(下の例だと、正しくないURLなので即座にイベントが発動する) イベント等を使用する
    - 将来的に外部からのデータを動的に挿入するコードが書かれた時、セキュリティリスクになりうるので、極力`Node.textContent`を使うべき
    ```js
    document.body.innerHTML = "<img src='' onerror='alert(10)'>"; //実行される
    ```

## Q53. Element.outerHTMLとinnerHTMLの違いを説明してください

??? info
    - `outerHTML`は自分自身を含むHTMLを返す
    - そのため空白で置き換えると、要素自体を削除可能

    ### 注意点
    ??? warning
        - `innerHTML`と`outerHTML`は要素にしか適用できない
        - コメントなどその他のノードを変更するには`nodeValue/data`を用いる

## Q54. Element.matches()の使いどころが分かりますか?

??? info
    ### 概要
    - `matches()`は、指定したセレクタに一致するかをチェックして、真偽値を返す

    ### 使用例
    - `li`タグの中から、`class`属性が`foo`である物を抽出している

    ```js
    const elm = document.querySelectorAll("li");
    elm.forEach(e => {
      if(e.matches(".foo")){
        console.log(e.innerHTML + ": Hello");
      }
    });
    ```
## Q55. Element.closest()を使って、条件に一致する祖先を取得できますか？

??? info
    - `.closest()`は、指定する要素自身とその祖先を対象に、指定したセレクタに一致するものを取得する
    - 複数候補がある場合は一番自分に近いものを取得する

    ### 使用例

    ```html
    <body>
      <div class="aa">
        <ul class="bb">
          <li class="cc">aa</li>
          <li>bb</li>
        </ul>
      </div>
    </body>
    ```

    ```js
    const elm = document.querySelector('.cc');    
    console.log(elm.closest(".bb")); //<ul class="bb">...</ul>
    console.log(elm.closest("div")); //<div class="aa">...</div>
    //div要素ではないもの
    console.log(elm.closest(":not(div)")); //<li class="cc">aa</li>

    ```

## Q56. 以下の場合、レンダリングは何度行われますか?

??? tips
    ### 例
    1. Aのノードを削除
    2. Bのノードを削除
    3. Cのノードの上にノードを1つ追加
    ```html
    <ol id = "foo">
      <li>A</li>
      <li>B</li>
      <li>C</li>
    </ol>
    ```

??? info
    - `3回`
    - DOMの操作を直接行う場合、操作の度にレンダリングは発生する

    ### 理由
    - 現在`ol`の`children`を考えたとき、`[0]`はAである。ここで、Aを削除すると、Bが`[0]`になる。
    - この時、DOMツリーの更新が行われていないと、リストのindexと画面表示でつじつまが合わなくなる。
    - そのためDOMが更新されたら、そのたびに画面も更新される

    ### 対策
    - Reactのような仮想DOMを用いる
    - これは、DOMの変更処理をまとめて、表示に関する一連の処理を1度で行うことが可能。

## Q57. 次のHTMLドキュメントのうちHogehogeのみを書き換えてください(ノードの更新)

??? tips
    - `innerHTML`を使うのは禁止
    ```html
    <p id="foo">I'm using
      <strong>Hogehoge</strong>
      now.
    </p>
    ```

??? info

    ```js
    const foo = document.querySelector("#foo");
    //strong要素があるのは2つ目。その中のうち1つ目がtext。
    //よって、該当要素をnodeValueで変更する
    foo.childNodes[1].childNodes[0].nodeValue = "HTML";
    ```

## Q58. 特定のタグ名を持つノードと、指定したテキストを持つノードを作成してください(ノードの作成)

??? info
    ### Document.createElement()
    - 指定したタグ名でノードを作成

    ```js
    const div = docuemnt.createElement("div");
    ```

    ### Document.createTextNode();
    - 指定したテキストで新しいノードを作成

    ```js
    const text = document.createTextNode("Hello");
    ```

## Q59. appendChildが近年使われていないことを知っていますか?(ノードの追加)

??? info
    ### Node.appendChild()
    - 指定したノードの子ノードリストの末尾にノードを追加

    ### Node.insertBefore(追加ノード, 子ノード)
    - 指定したノードの子ノードとして指定した場所の前にノードを追加

    ### Node.replaceChild(追加ノード, 置き換えたい場所)
    - 置き換えたい場所にあったノードと置き換える

    ### 使用例

    ```js
    const div1 = document.createElement("div");
    const div2 = document.createElement("div");
    const div3 = document.createElement("div");
    div1.id = "aa";
    div2.id = "bb";
    div3.id = "cc";

    document.body.appendChild(div1);
    document.body.insertBefore(div2, document.body.children[0]);
    document.body.replaceChild(div3, document.body.children[0]);
    ```

    - 最終結果

    ```html
    <body>
      <div id="cc"></div>
      <div id="aa"></div>
    </body>
    ```

    ### 使われていない理由
    - Textノードが追加できないなど、柔軟ではないため

## Q60. appendやprepend等を使ったことがありますか?

??? info
    ### Element.append()
    - 末尾にノードを追加

    ### Element.prepend()
    - 先頭にノードを追加

    ### Element.before()
    - 指定したノードの前にノード追加

    ### Element.after()
    - 指定したノードの後にノードを追加

    ### Element.replaceWith()
    - ノードを置き換え

    ### 特徴
    - DOMStringも追加可能(Textノードとして追加される)
    - 追加すると、`undefined`を返す
    - 複数のノードを一度に追加可能
    - 追加内容はText

    ### 使用例

    ```html
    <ol id="foo">
      <li>A</li>
      <li>B</li>
    </ol>
    ```

    ```js

    //append
    const foo = document.querySelector("#foo");
    const li_1 = document.createElement("li");
    const text_1 = document.createTextNode("C");
    li_1.append(text_1);
    foo.append(li_1);

    /*
    * A
    * B
    * C
    */

    //prepend
    const li_2 = document.createElement("li");
    const text_2 = document.createTextNode("X");
    li_2.append(text_2);
    foo.prepend(li_2);

    /*
    * X
    * A
    * B
    * C
    */

    //before
    const li_3 = document.createElement("li");
    //DOMString追加
    li_3.append("Y");
    foo.children[1].before(li_3);

    /*
    * X
    * Y
    * A
    * B
    * C
    */

    //after
    const li_4 = document.createElement("li");
    const text_4 = document.createTextNode("Z");
    li_4.append(text_4);
    foo.children[1].after(li_4);

    /*
    * X
    * Y
    * Z
    * A
    * B
    * C
    */

    //replaceWith
    const li_5 = document.createElement("li");
    const text_5 = document.createTextNode("A+");
    li_5.append(text_5);
    foo.children[3].replaceWith(li_5);

    /*
    * X
    * Y
    * Z
    * A+
    * B
    * C
    */

    //HTML追加(textとして追加するのでHTMLは解釈されない)
    //つまり、(< は、&lt)として解釈される
    const li_6 = document.createElement("li");
    li_6.prepend("<p>Hello</p>");
    li_6
    <li>​<p>Hello</p>​</li>​
    foo.append(li_6);

    /*
    * X
    * Y
    * Z
    * A
    * B
    * C
    * <p>Hello</p>
    */

    //複数同時挿入
    const li_7 = document.createElement("li");
    const text_7 = document.createTextNode("D");
    const li_8 = document.createElement("li");
    const text_8 = document.createTextNode("E");
    li_7.append(text_7);
    li_8.append(text_8);
    foo.children[6].before(li_7, li_8);

    /*
    * X
    * Y
    * Z
    * A
    * B
    * C
    * D
    * E
    * <p>Hello</p>
    */
    ```

## Q61. Element.insertAdjacentHTML()を使ったことがありますか?

??? info
    ### 概要
    - `insertAdjacentHTML()`は、テキストをHTML(またはXML)にパースして挿入する

    ### innerHTMLより速い?
    - 所持しているDOMStringを削除して置き換える必要がないので早い。

    ### 使い方
    | 文字        | 追加場所                 |
    | ----------- | ------------------------ |
    | beforebegin | 指定ノードの前           |
    | afterend    | 指定したノードの後ろ     |
    | afterbegin  | 指定ノードの子要素の先頭 |
    | beforeend   | 指定ノードの子要素の末尾 |

    ### 使用例

    ```html
    <ol id="foo">
      <li>A</li>
      <li>B</li>
    </ol>
    ```

    ```js
    const foo = document.querySelector("#foo");
    foo.insertAdjacentHTML("beforebegin", "<p>TEST-A</p>");

    /*
    * TEST-A
    *    1. A
    *    2. B
    */
    foo.insertAdjacentHTML("afterend", "<p>TEST-B</p>");

    /*
    * TEST-A
    *    1. A
    *    2. B
    * TEST-B
    */
    foo.insertAdjacentHTML("afterbegin", "<li>X</li>");

    /*
    * TEST-A
    *    1. X
    *    2. A
    *    3. B
    * TEST-B
    */
    foo.insertAdjacentHTML("beforeend", "<li>Y</li>");

    /*
    * TEST-A
    *    1. X
    *    2. A
    *    3. B
    *    4. Y
    * TEST-B
    */
    ```

    ### insertAdjacentText()
    - これはテキストノードを挿入するもの
    - 使い方は`insertAdjacentHTML()`と同じ
    - しかし、`prepend`の方が短く書けるので使う機会がない

    ### Document.write()
    !!! failure
        - 直接HTMLを挿入する
        - write()はページがロードされている間だけ動作し、そのあとに呼び出した場合は、既存のコンテンツを消す
        - つまり時間差で実行すると、既存のコンテンツが消える
        - HTML5では、使用禁止。

## Q62. Node.cloneNode()でノードをコピーする方法を知っていますか?

??? info
    ### 使い方
    - 引数に`true`を設定した場合：すべての子ノードを含むdeepcopy
    - 引数に`false`を設定した場合、子ノードを持たないコピーを作成

    ### 使用例

    ```html
    <ol id="foo">
      <li>A</li>
      <li>B</li>
    </ol>
    ```

    ```js
    const foo = document.querySelector("#foo");

    //子ノードを含むコピー
    const copy = foo.cloneNode(true);
    copy.insertAdjacentHTML("afterbegin", "<li>X</li>");
    foo.after(copy);
    //子ノードを含まないコピー
    const copy1 = foo.cloneNode(false);
    copy1.insertAdjacentHTML("afterbegin", "<li>Y</li>");
    copy.after(copy1);

    /*
        1. A
        2. B

        3. X
        4. A
        5. B

        6. Y
    */
    ```

## Q63. removeChildとremoveの違いについて知っていますか?

??? info
    - DOMの要素を削除するには`remove`または`removeChild`が使える

    ### remove
    - は指定のノードを削除する

    ### removeChild
    - 第1引数に指定した子ノードを削除する

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

## Q64. DocumentFragmentの特徴と利点について理解していますか?

??? info
    ### 概要
    - 独立した1つのノードツリーの頂点にあるノード
    - 親を持たない

    ### 使い道
    - `createElement()`で作成したノードに子ノードを追加したい場合、`innerHTML()`や`insertAdjacentHTML()`が使えるが、追加するたびにDOMを形成し直すので、時間がかかる
    - 一方、DocumentFragmentは、メモリ内に存在し、メインのDOMツリーの一部ではない
    - そのため子要素を追加しても再レンダリングは行われない
    - よって、複数要素を追加する場合は、これを使うことで性能の改善が見込める

    ### 使用例

    ```js
    var element = document.body;
    var fragment = document.createDocumentFragment();
    var browsers = ["Firefox", "Chrome", "Opera", "Safari"];

    browsers.forEach((browser)=>{
      var li = document.createElement("li");
      li.textContent = browser;
      fragment.append(li);
    });

    //レンダリングはこの1回で済む
    element.append(fragment);

    /*
    * ・Fragment
    * ・Chrome
    * ・Opera
    * ・Safari
    */
    ```

## Q65. HTMLタグの属性を操作する方法を知っていますか?

??? info
    ### プロパティへの代入
    ```html
    <input id="aa" type="text" value="bb">
    <p><a id="bb" href="test" type="text">TEST</a></p>
    ```

    - HTMLタグが標準的に実装していない属性でも設定可能
    - たとえば本来、`HTMLParagraphElement`は`type`属性を持っていないが取得はできている

    ```js
    const aa = document.querySelector("#aa");
    const bb = document.querySelector("#bb");

    //取得
    console.log(aa.type); //test
    console.log(bb.href); //..../test

    //代入
    aa.value = "cc";
    console.log(aa.value); //cc
    ```

    ### メソッドで属性を操作

    | メソッド名                      | 内容                 |
    | ------------------------------- | -------------------- |
    | Element.hasAttribute(属性名)    | 属性が存在すればtrue |
    | Element.getAttribute(属性名,値) | 値を取得             |
    | Element.setAttribute(属性名,値) | 値を設定             |
    | Element.removeAttribute(属性名) | 属性を削除           |

    ### 使用例

    ```html
    <div id="foo" type="text"></div>
    ```

    ```js
    const foo = document.querySelector("#foo");
    console.log(foo.hasAttribute("type")); //true
    console.log(foo.getAttribute("type")); //text
    foo.setAttribute("type", "aa"); 
    console.log(foo.getAttribute("type")); //aa
    foo.removeAttribute("type"); 
    console.log(foo.hasAttribute("type")); //false
    ```

    ### プロパティの代入と上記メソッドの違い

    - メソッドは属性を直接変更する。プロパティはプロパティを変更する。
    - おおよそのものは同期しているが、異なるものも存在する
    - 具体的な例を挙げると、`.value`。これはユーザの入力を反映したい場合は適しているが、フォーム要素以外の場合は、意思と異なる結果になる可能性がある

    ```js
    foo.value = "fuga"; //変化しない
    foo.setAttribute("value", "fuga"); //変化する
    ```
    ### 注意点
    !!! warning
        - 注意点として、属性名は大文字小文字を区別しない
        - また、値を設定しても文字列となる


## Q66. 属性とプロパティの値が異なる場合があることを知っていますか?

??? info
    ### checked
    ```html
    <input id="foo" type="checkbox" checked>
    ```

    ```js
    console.log(foo.getAttribute("checked"));- //空文字
    console.log(foo.checked); //true
    foo.checked = false;
    console.log(foo.checked); //false
    ```

    ### style

    ```html
    <div id="foo" style="font-size:12px; color:red;">Hello</div>
    ```

    ```js
    console.log(foo.getAttribute("style")); //font-size; 12px; color:red;
    console.log(foo.style); //{0: "font-size", 1:"color"};
    foo.style.color = "blue";

    console.log(foo.style.color);
    ```

    ### href

    ```html
    <a id="foo" href="#test">link</a>
    ```

    ```js
    console.log(foo.getAttribute("href")); //#test
    console.log(foo.href); //....../hoge.html#test
    ```

## Q67. 属性の一覧を取得する方法が分かりますか?

??? info
    ### 概要
    - `Element.attributes`を使う
    - これはノードに設定している属性のリストを保持している
    - 値はNamedNodeMapインターフェースを継承しており、NamedNodeMapはObjectを実装している
    - `attributes.name`と`attributes.value`を主に使う

    ### 使用例

    ```js
    const div = document.querySelector("div");

    for(const attr of div.attributes){
      console.log(attr.name, attr.value);
    };

    /*
    * id: foo
    * type: text
    */
    ```

## Q68. カスタム属性とdata-*の安全性について知っていますか?

??? info
    ### そもそもカスタム属性とは
    - 標準で実装されている属性とは別に、自由な属性名で使用できるもの
    - JSで特定の値を管理したい場合などに使える

    ### 使用例

    ```html
    <div foo="id"></div>
    <div foo="name"></div>
    ```

    ```js
    const user = {
      id : "abc",
      name: "Jhon"
    };

    //特定の属性をセレクタで取得したいときは[属性]
    for(let elm of document.querySelectorAll("[foo]")){
      let attr = elm.getAttribute("foo");
      elm.innerHTML = user[attr];
    }
    ```
    - 結果

    ```html
    <div foo="id">abc</div>
    <div foo="name">Jhon</div>
    ```

    ### 問題点
    - 一見便利そうだが、遠い未来で`foo`という属性名が標準化される可能性がある
    - 衝突を避けるために、`data-*`という属性名がある
    - これは`カスタムデータ属性`と呼ばれ、標準化される可能性はない

    ### HTMLElement.dataset
    - カスタムデータ属性の値を所持している
    - `data-***`という属性名は、`dataset.***`として指定する
    - `data-aa-bb`なら、`dataset.aaBb`の様にキャメルケースにする
    - なお、`getAttribute`で取ることもできる

    ```html
    <div data-foo="id"></div>
    <div data-foo="name"></div>
    <script>
      const user = {
        id : "abc",
        name: "Jhon"
      };

      for(let elm of document.querySelectorAll("[data-foo]")){
        //let attr = elm.getAttribute("data-foo");
        let attr = elm.dataset.foo;
        elm.innerHTML = user[attr];
      }      
    </script>
    ```