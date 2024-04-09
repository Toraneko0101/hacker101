# クイズ(初級2:基本構文)

## Q13. JSのスクリプト(コード)をHTMLに挿入する方法を知っていますか?

??? success
    ### 概要
    - `<script>`タグを使用して、任意の場所に挿入する
    - `alert("表示したいメッセージ")`は、ブラウザ上にポップアップを表示する。

    ```html hl_lines="4-6"
    <html>
      <body>
        <h1>hogehoge</h1>
        <script>
          alert("Hello World");
        </script>
      </body>
    </html>
    ```

## Q14. `<script>`タグの属性が必須でなくなったことを知っていますか?

??? success
    ### type
    - HTML4までは以下のような書き方を必要とした
    - `<script type="text/javascript">`
    - 今は必須ではない
    - また意味も変わっている

    ### language
    - JSがデフォルトの言語になったので、JSを使う以上、以下のような書き方は必要ない
    - `<script language="javascript">`

    ### スクリプト前後のコメント`<!-- -->`
    - 古いブラウザは、スクリプトタグを解釈できなかった
    - そのためscript要素の内容に対してコメントアウトを行い、要素の内容がテキストとして表示されることを防いでいた
    - 古いというのは15年以上前を指す
    - これが書かれている場合、コードの年数が15年以上経っている、もしくは書き手の知識が15年以上前で止まっていることを意味する
    - 検索中にこれらのコードを見つけた場合、参考にしない方がいい
    
    ### 参考(2007年5月に、徳丸様によって書かれた記事)
    - 15年前時点ですでに、i-mode以外はScriptに対応していたことが分かる
    - [Javascriptのエスケープについて](https://ockeghem.hatenablog.jp/entry/20070519/1179592129)


## Q15. 外部ファイルからHTMLに対してJSのコードを埋め込む方法を知っていますか?
??? success

    ### 概要
    - `src`に指定するパスは、現在のフォルダからの相対パス
    - サイトのルートからの絶対パスでも可
    - URLを含めることもできる

    ```html
    <script src = "script1.js"></script> <!--HogeHogeと表示-->
    <script src = "script2.js"></script><!--FugaFugaと表示-->
    ```
    
    - script1.js
    
    ```js
    //htmlではないので、<script>タグは必要ない
    alert("HogeHoge");
    ```
    - script2.js

    ```js
    alert("FugaFuga");
    ```

    ### 注意点
    !!! warning
        - `<script>`タグの中に、コードを書き、かつ`src`を設定した場合、タグ内のコードは無視される

## Q16. 外部ファイルからスクリプトを読み込むメリットを知っていますか?

??? success
    ### 概要
    - ファイル分割は、複数ページで該当スクリプトを使いたいときに威力を発揮する

    ### 効果
    1. 一度ブラウザがスクリプトをダウンロードすると、それはキャッシュに保存される
    2. 他のページが該当スクリプトを必要としたとき、それはダウンロードされず、キャッシュから取得される
    3. そのためダウンロード分の時間が節約される

## Q17. 外部スクリプトを読み込んで、ブラウザ上のページに`alert`を表示できますか?

??? success
    ```html title="index.html"
    <!DOCTYPE html>
    <html>
      <body>
          <script src="alert.js"></script>
      </body>
    ```

    ```js title="alert.js"
    alert("Hello World");
    ```


## Q18. 文末にセミコロン(;)を書かなかった場合の挙動について、理解していますか?

??? success
    ### 基本
    - 文はセミコロンで区切ることができる

    ### ほとんどの場合、セミコロンは省略できる
    - ほとんどのケースで改行はセミコロンの代替になる

    ```js
    alert("Hello World")
    alert("Hello World")
    ```

    ### ほとんどのケース?

    - 不必要なケースでは挿入されない

    ```js
    alert(3 + 
    1
    + 2 
    );  //6
    ```

    ### セミコロンを付けなかったために起こるエラー
    - 以下を理解する必要はない

    ```js
    alert("Hogehoge");
    [1, 2].forEach(alert);
    ```

    - セミコロンがついていなかった場合、こう解釈される

    ```js
    //改行後が、[]で始まる場合、セミコロンは自動挿入されない
    alert("Hogehoge")[1,2].forEach(alert);
    ```

    ### つまり
    - セミコロンは除いてもいい
    - セミコロンを使った方が安全

## Q19. コメントの書き方について理解していますか?

??? success

    ### コメント概要
    - コメント内に書いた内容は無視される
    - コメントを入れ子で書くことはできない
    - VSCode等なら`Ctrl + /`などで一括コメント、コメントアウトが可能

    ### 1行のコメント
    ```js
    // hoge
    // fuga
    ```

    ### 複数行のコメント

    ```js
    /*
    * hoge
    * fuga
    * hogehoge 
    */
    ```



## Q20. コメントを多用した場合、JSの動作は遅くなりますか?

??? success
    ### コメントは動作を遅くする?
    - `No`
    - JSコードを圧縮化するライブラリが多数存在する
    - 圧縮すると、コメントや空白は除去される

    ### 圧縮化の効果について
    - 読み込みに関する時間が減少する(高速化)

    ### 圧縮化${\neq}$難読化
    - かつてはコードを理解しにくくする目的で、圧縮による難読化(コード内の変数などを1文字に変換)が為されていた
    - しかしAIの進化や解析ツールの進化に伴い、難読化されたコードは容易に解読できるようになっている
    - 実際に難読化済みのコードを、生成AIに投げたところ、完全に近い出来で復元が行われた

## Q21. use strictの意味とその必要性を理解していますか?

??? success

    ### 前提
    - `use strict`は正しい文法のみを許可する

    ### なぜ必要なのか
    - JSは優しいので多くのミスを受け入れてくれるが、このやさしさが後々大きな問題を引き起こすことがある

    ### 記述方法
    - `use strict`は先頭に書く
    - `use strict`より先にコメント以外を書いたとき、これは正しく動作しない
    - use strictはシングルクォート、またはダブルクォートで囲む

    ```js
    // hoge
    "use strict";
    alert("Hello");
    ```

    ### 注意点
    !!! warning
        - 開発者ツールでJSを使う場合も、先頭行に書く

## Q22. JSにおける変数宣言と代入の手法について理解していますか?

??? success
    ### 変数の宣言
    - 変数宣言の際には、`var`もしくは`const`というキーワードも使える
    - `let`との差異については後述

    ```js
    //キーワード 変数名
    let hoge;
    ```

    ### 代入

    ```js
    let hoge;
    // 変数名 = 値
    hoge = 12;
    ```

    ### 宣言と代入を同時に行う

    ```js
    let hoge = 12;
    ```

    ### 複数の変数を宣言する

    ```js
    let hoge = 12;
    let fuga = 18;
    ```

    ### letを使った場合、(同名の変数に対して)2回宣言はできない

    ```js
    let message = "hoge";
    //let message = "fuga"; //error
    ```

    ### コラム(関数型プログラミング)
    ??? info
        - 関数型プログラミング言語の場合、変数は変化しない
        - 関数型プログラミングは可読性に適している
        - 例として`Scala`や`Erlang`
        - これらの言語では、変数に異なる値を入れたくなった場合、新たな変数を宣言する。

    ### 変数の命名
    1. 使えるのは、文字と数字と`$`と `_`
    2. 先頭に数字を使ってはいけない

    ```js
    // let 1a; //error
    // let my-name; //error
    let hoge = 12;
    let hogeName = 13; 
    let $ = 1;
    let _ = 2;
    alert($ + _); //3
    ```
    !!! warning
        - 英語以外の文字も使えるが推奨されない

        ```js
        let あ = "neko";
        alert(あ); //neko
        ```

        - キャメルケースが推奨される

        ```js
        //単語が続く場合、各単語を大文字で始めるという意味
        let myVeryLongVariableName = "hogehoge";
        ```

        - 大文字小文字は区別される

        ```js
        let apple = 12;
        let Apple = 13;
        alert(apple); //12
        ```

        - 予約名は使えない
  
        ```js
        //let let = 5; //error
        //let function = 12; //error
        ```

## Q23. Javascriptで変数宣言時に用いる3つのキーワードについて覚えていますか?

??? success
    以下の3つのキーワードのうち、いずれかを用いる

    - var
    - let
    - const

## Q24. 定数を扱う方法と大文字の慣習について知っていますか?

??? success

    ### 定数

    ```js
    const hoge = 12;
    //hoge = 13; //error 
    ```

    ### 大文字の慣習
    - 実行前に判明している値についてはエイリアスとして、定数を使うことがある。

    ```js
    const COLOR_WHITE = "#FFF";
    
    let color = COLOR_WHITE;
    alert(color); //#FFF
    ```

    ### 実行中に決定する定数については通常通り

    ```js
    const pageLoadTime = /*何かしらの値*/;
    ```

## Q25. 変数に適切な名前を付ける重要性について理解していますか?

??? success
    ### ただし、書き捨て用のコードは別
    - コードに2回以上目を通す可能性があるなら、人間が見て分かりやすい名前を用いるべき
    - テスト用の使い捨てコードである場合は、構わない

    ### 悪い例1

    ```js
    let a;
    let data;
    let val;
    ```

    ### 悪い例2
    - 用語が統一されていない

    ```js
    let currentVisitor;
    let prevUesr;
    let NextCustomer;
    ```

    ### 悪い例3
    - 何がSuperなのかわからない

    ```js
    let superValue;
    let hyperAction;
    ```

## Q26. 変数を再利用して、実行時間を節約しようと考えたことはありますか？

??? success
    ### 概要
    - 恐らくやめた方がいい

    ### 理由
    - 現代のJSはコードを十分に最適化する
    - 変数が異なったものを投げ入れるだけの箱になると、デバッグや引継ぎの際に、恐ろしいことになる

## Q27. console.log()について知っていますか?

??? success
    ### 概要
    - `console.log()`は、メッセージをウェブ上のコンソールに出力するための機能

    ### 使用例
    - 開発者ツールに打ち込むと、変数の結果を見ることができる

    ```js
    let num = 12;
    console.log(num); //12
    ```

## Q28. 型について知っていますか?

??? success
    ### 基本
    - JSの値は常に特定の型に属する
    - JSの変数はどんなデータ型でも入れることができる
    - このような言語を動的型付けという
    - Pythonなどと同じ

    ### 型の種類(8つ)
    - 数値型
    - BigInt型
    - 文字列型
    - boolean型
    - null
    - undefined
    - object型(中級で解説)
    - symbol型(中級で解説)

## Q29. 数値型とその特徴について知っていますか?

??? success

    ### 概要
    - 主に算術演算などに使う
    - 数値や浮動小数点両方に使われる

    ### 特殊な数値型
    - `Infinity`: 無限
    - `-Infinity`:負の無限大
    - `NaN`: 計算上のエラー

    ### 例

    ```js
    let num = 123;
    num = 12.3;
    num = Infinity; //無限
    num = -Infinity; //負の無限大
    num = NaN; //未定義の動作を行った場合
    ```

## Q30. `1 / 0`はエラーになりますか?

??? success
    - `No`

    ### 理由
    - JSの場合、0除算を行うと極限のような扱いになる
    - 出力としては` 1 / 0 = Infinity`

    ### 算術演算でエラーを吐くことはない
    - 最悪の場合でもNaNになる
    - NaN以降はどのような算術演算を行っても、答えは`NaN`になる

    ```js
    num = ("Hoge" / 2 + 5); //NaN
    ```

## Q31. 大きな数字を扱いたいときに用いるBigInt型について知ってますか?

??? success
    ### 使いどころ
    - 大きな数${2^{53}-1}$以上の数を扱いたい場合
    - または${-(2^{53}-1)}$以下の数を扱いたい場合に使う


    ### 表現方法
    - 末尾に`n`を付ける

    ```js
    const bigInt = 123456787678n;
    ```

## Q32. 文字列型の表現方法を知っていますか?

??? success
    ### 基本
    - シングルクォート、ダブルクォートで囲む

    ```js
    let name = "Yamada";
    console.log(name); //Yamada
    ```

## Q33. 文字列に変数を埋め込む方法(テンプレート)について知っていますか?

??? success
    ### 変数や数値を埋め込みたい場合
    - バッククォートと、`${}`を用いる

    ### 例

    ```js
    let name = "hoge";
    console.log(`Hello ${name} !`); //Hello hoge !
    console.log(`私は ${1 + 3}　歳です`); //私は 4歳 です
    ```

## Q34. boolean型について知っていますか?

??? success
    - `true`または`false`という値を持つ

    ```js
    let isFirstNameChecked = false;
    let isLastNameChecked = true;
    ```

## Q35. null値について知っていますか?

??? success
    ### C言語などとの違い
    - JSにおけるnullは、値が無い、空であることを意味する

    ```js
    let age = null;
    ```

## Q36. undefinedについて知っていますか?

??? success
    ### 概要
    - `undefined`は値が代入されていないことを意味する

    ```js
    let x;
    console.log(x); //undefined

    x = 10;
    x = undefined; //明示的にundefinedにすることも可能。推奨はされないが
    ```

## Q37. 変数の型を確認できるtypeof演算子について知っていますか?

??? success
    - `typeof`は、引数の型を返す
    
    ### 書き方
    - `typeof x`
    - `typeof(x)`

    ### 使用例

    ```js
    console.log(typeof 0); //number
    console.log(typeof 10n) //bigInt
    console.log(typeof "hoge"); //string
    console.log(typeof true); //boolean
    console.log(typeof undefined); //undefined

    console.log(typeof Math); //object
    console.log(typeof null); //object
    console.log(typeof alert); //function
    ```

    ### typeof nullについて
    - 結果は`object`になるがこれは言語のエラー
    - 本来は`null`でなければならないが公式で修正されていない

    ### typeof Mathについて
    - `Math`は数学的な操作を提供する、組み込み`object`

    ### alert
    - 本来、`function`型は存在しないが、typeofは分かりやすさのために、関数を`function`と返してくれる

## Q38. 公式のドキュメント等を読む際に、よく登場する`[]`の意味を知っていますか?

??? success
    - パラメータが必須ではないことを意味する

    ### 例
    - `prompt(title [, default])`
    - 上記の場合、defaultパラメータはオプションになる

## Q39.alert(), prompt(), confirm()の違いが分かりますか?

??? success
    ### 共通点
    - この3種の中にメッセージを入れると、小さなウィンドウが表示される
    - これはモーダルウィンドウと呼ばれる
    - モーダルとは、ユーザと対話的に入力を受け付けるという意味を持つ
    - モーダルウィンドウが表示されている最中は、ユーザは画面上の他の箇所を変更できない

    ### alert(message)
    - 指定されたメッセージと[OK]ボタンを含むアラートのボックスを表示する

    ```js
    alert("Hello");
    ```

    ### prompt(title[, default])
    - ユーザに入力を求めるダイアログボックスを表示する
    - ページに入る前にユーザに値を入力してもらいたい場合（パスワードなど）に使われる
    - ユーザがOKを押すと、`prompt()`は入力値を返し、キャンセルをクリックすると`null`を返す。

    ```js
    const name = prompt("What is your name?")
    console.log(name);

    //入力欄に前もって値を表示しておきたい場合
    prompt("表示したいメッセージ", "入力欄の初期値");
    ```

    ### confirm(question)
    - 指定されたメッセージと[OK]ボタン及び、[キャンセル]ボタンを含むダイアログボックスを表示
    - OKを押すとtrue, キャンセルならfalseが返る
    - ユーザに対して、何かを確認してほしい場合に使う

    ```js
    const result = confirm("Are you sure?");
    console.log(result); //true or false;
    ```




## Q40. 型変換のやり方について理解していますか?

??? success
    ### 基本
    - 多くの場合、関数や演算子は値を正しい型に変換する(暗黙的な変換)
    - しかし明示的な変換が必要となる場合もある

    ### (暗黙的な)文字列への変換
    - `+`演算子の左辺または右辺が文字列であった場合、片方の値は文字列に自動変換される

    ```js
    const a = "num = " + 10; // "num = 10";
    ```

    ### (明示的な)文字列への変換
    - 明示変換したい場合、`String()`を使う

    ```js
    typeof String(10); //string
    ```

    ### (暗黙的な)数値への変換
    - [-]や[*]や[/]演算子などで数値と演算を行うと、対象の値は数値に自動変換される

    ```js
    console.log("6" / "2"); //3

    ```

    ### (明示的な)数値への変換
    - `Number()`を用いる

    ```js
    typeof Number("12"); //number
    ```

    ### (暗黙的な)Booleanへの変換
    - if文(後述)の中に値を入れる

    ```js
    if(3){

    }
    ```

    ### (明示的な)Booleanへの変換
    - Boolean()を用いる

    ```js
    typeof Boolean(4); //true
    ```

## Q41. 数字として無効な値をNumber()で型変換した時にどうなるか知っていますか?

??? success

    - `NaN`になる

    ### 例

    ```js
    let age = Number("12歳");
    console.log(age); //NaN
    ```

## Q42. nullやundefined,boolean型,空文字をNumber()で型変換した時にどうなるか知っていますか?

??? success

    ### null
    - `0`

    ### undefined
    - `NaN`

    ### true
    - 1

    ### false
    - 0

    ### 空文字("")
    - 0

## Q43. " 24  "のように左右にスペースがあるStringをNumberで型変換したときにどうなるか知っていますか?

??? success
    ### 答え
    - 最初と最後のスペースは取り除かれ、普通に型変換される

    ```js
    Number("   24"); //24

    //これはダメ
    Number("  2  3 "); //NaN
    ```

## Q44. 16進数や指数表記、BigIntなどの値を、型変換しようとしたときにどうなるか知っていますか?

??? success
    - 通常の10進数に直される

    ```js
    String(1e3); //"1000";
    String(0x12); //"18"

    Number("1e3"); //1000;
    Number("0x12"); //18

    String(10n); //"10";
    ```

    !!! info
        - その他の例

        ```js
        Number(["78"]) //78
        ```

## Q45. Booleanを用いた際の型変換の規則について知っていますか?

??? success

    ### falseになるもの
    - ""
    - 0
    - 0n
    - undefined
    - null
    - NaN

    ### 意外にもtrueになるもの
    - falseに上げたもの以外すべて
    - `[]`や`{}`。マイナスの値なども`true`になる
    !!! warning
        - "0": 0の文字列は`true`になる


## Q46 JSの算術演算子(べき乗含む)について知っていますか?

??? success
    ### JSには以下の演算子がある
    - 加算`+`
    - 減算`-`
    - 乗算`*`
    - 除算`/`
    - 剰余`%`
    - べき乗`**`

    ### 除算について
    - 割り切れなかった場合、小数点の値となる
    - 切り捨てはされない

    ### 剰余について
    - 余り

    ### べき乗について
    - 下の例はこうなる ${2^3}$

    ```js
    console.log(2 ** 3); //8
    ```

    - 非整数値も可能　${4^{\frac{1}{2}}}$

    ```js
    console.log(4 ** (1/2)) //2
    ```

## Q47. `+`演算子の特殊な使い方(型変換含む)を理解していますか?

??? success

    ### 文字列の連結

    ```js
    let str = "prev" + "Next";
    console.log(str); //prevNext

    let hoge = 2 + 2 + "1";
    console.log(hoge); //"41"
    ```

    ### 型変換
    - `+`演算子を単一の値に対して用いることもできる
    - 対象の値が数値型以外であれば、数値型に変換する

    ```js
    console.log(+true); //1
    console.log(+""); //0
    ```

    ### 型変換の例

    ```js
    let apples = "2";
    let oranges = "3";

    console.log(apples + oranges); //"23"
    console.log(+apples + +oranges); //5
    ```

## Q48. 演算子の優先順位について理解していますか?

??? success
    ### 基本ルール
    - 優先度が高い方が、優先される
    - 同じ優先度の場合は、左から先に評価される

    ### 覚えておいた方がいい点
    - `()`(16)は何より優先される
    - 単項演算子(14)は、二項演算子(11)より優先度が高い
    - べき乗(13) > 乗除剰(12) > 加減(11)
    - 代入演算子の優先度は低い(2)
    - 1番優先度が低いのは、カンマ演算子(1)

    ### 恐らく不要な早見表
    ??? info
        | 演算子         | 優先順位 |
        | -------------- | -------- |
        | ()             | 18       |
        | x(y)           | 17       |
        | x.y            | 17       |
        | x?.y           | 17       |
        | x[y]           | 17       |
        | new x(y)       | 17       |
        | import(x)      | 17       |
        | new x          | 16       |
        | x++            | 15       |
        | x--            | 15       |
        | ++x            | 14       |
        | --x            | 14       |
        | !x             | 14       |
        | ~x             | 14       |
        | +x             | 14       |
        | -x             | 14       |
        | typeof x       | 14       |
        | void x         | 14       |
        | delete x       | 14       |
        | await x        | 14       |
        | x ** y         | 13       |
        | x * y          | 12       |
        | x / y          | 12       |
        | x % y          | 12       |
        | x + y          | 11       |
        | x - y          | 11       |
        | x << y         | 10       |
        | x >> y         | 10       |
        | x >>> y        | 10       |
        | x < y          | 9        |
        | x <= y         | 9        |
        | x > y          | 9        |
        | x >= y         | 9        |
        | x in y         | 9        |
        | x instanceof y | 9        |
        | x == y         | 8        |
        | x != y         | 8        |
        | x === y        | 8        |
        | x !== y        | 8        |
        | x & y          | 7        |
        | x ^ y          | 6        |
        | x `            | ` y      | 5   |
        | x && y         | 4        |
        | x `            |          | ` y | 3 |
        | x ?? y         | 3        |
        | x = y          | 2        |
        | x += y         | 2        |
        | x -= y         | 2        |
        | x **= y        | 2        |
        | x *= y         | 2        |
        | x /= y         | 2        |
        | x %= y         | 2        |
        | x <<=y         | 2        |
        | x >>=y         | 2        |
        | x >>>=y        | 2        |
        | x &=y          | 2        |
        | x ^=y          | 2        |
        | x `            | `=y      | 2   |
        | x &&= y        | 2        |
        | x `            |          | `=y | 2 |
        | x ??=y         | 2        |
        | x ? y : z      | 2        |
        | x => t         | 2        |
        | yield x        | 2        |
        | yield* x       | 2        |
        | ... x          | 2        |
        | x,y            | 1        |


## Q49. 代入がチェーン出来ることを知っていますか?

??? success
    - 代入は、あくまでも演算子なので以下の様に書ける

    ```js
    let a, b, c;
    //演算子の優先順位を考える
    //=の演算子は優先度が2, +の演算子の優先度は11
    a = b = c = 2 + 2;

    console.log(a); //4
    console.log(b); //4
    console.log(c); //4
    ```

    ### 疑問点
    - 同じ優先度なら、左から右へ評価されるのではなかったのか?
    - その場合、`2+2`の後に、`a = b`が評価され、`a = 4`とはならない気がする

    ### 回答
    - チェーンされた代入は右から左へと評価される
    - これは代入演算子の右結合性という
    - 従って

    ```
    1. a = b = c = 2 + 2;
    2. 2 + 2が評価される
    3. c = 4が評価される
    4. b = cが評価される
    5. a = bが評価される
    ```

## Q50. in-place修正について知っていますか?

??? success
    - 変数に演算子を適用した後、同じ変数に結果を格納したい場合に使う

    ```js
    let n = 2;
    n = n + 5;
    n = n * 12;
    ```

    ### 短縮形
    - 優先順位は代入演算子(2)と同じなので、ほとんどの計算の後で実行される

    ```js
    let n = 2;
    n += 3 + 5; //10
    n += 5; //15
    n *= 12; //180
    n -= 4; //176
    n /= 2; //88
    n %= 3; //1
    ```

## Q51. インクリメントとデクリメントについて知っていますか?

??? success

    ### `++`
    - 変数を1増加させる

    ### `--`
    - 変数を1減少させる

    ### 注意点
    - インクリメント/デクリメントは変数にのみ適用可能

    ```js
    //5++ //error
    ```

## Q52. 前置インクリメントと、後置インクリメントの違いについて理解していますか?

??? success
    ### 基本
    - `++`と`--`は変数の前後療法に配置可能

    ### 前置インクリメント(pre-increment)
    - 前置インクリメント演算子は、変数の値を1だけ増加させてから、値を返す

    ```js
    let x = 1;
    console.log(++x); //2
    console.log(x); //2
    ```

    ### 後置インクリメント(post-increment)
    - 後置インクリメント演算子は、変数の値を返してから、その値を1だけ増やす

    ```js
    let x = 1;
    console.log(x++); //1
    console.log(x); //2
    ```

    ### 他の演算子の中でインクリメントする

    ```js
    let counter = 1;
    //優先順位は、後置(15) > 乗算(12)
    console.log(2 * counter++); //2 //値が返されてから、掛け算が行われる。しかし後置なので値は増えない
    ```

    ### 1行1アクションの方が可読性が高い

    ```js
    let counter = 1;
    console.log(2 * counter);
    counter++;
    ```

## Q53. ビット演算子について理解していますか?

??? success

    ### `&`
    - 論理積(AND)

    ### `|`
    - 論理和(OR)

    ### `^`
    - 排他的論理和(XOR)

    ### `~`
    - 否定(NOT)

    ### `<<`
    - 左シフト

    ### `>>`
    - 右シフト

    ### `>>>`
    - 符号なし右シフト

    ### 使いどころ
    - Web開発ではあまりないはず

    ### 使用例

    ```js
    console.log(4 & 3); //0   //100 & 011 = 000
    console.log(4 | 3); //7   //100 | 011 = 111
    console.log(4 ^ 3); //7   //100 ^ 011 = 111
    console.log(~4); //-5  //~0000100 =  ...11111011 
    console.log(4 >> 2); //1
    console.log(4 << 2); //16
    console.log(-9 >>> 2); //1073741821
    ```

## Q54. カンマ演算子について理解していますか?
??? success

    - カンマ演算子は、演算対象を左から右に評価し、最後の値を返す
    - 式自体は評価されるが、返却されるのは最後の値だけ
    - 複数の式を単一の式として用いたいときに使う

    ### 基本例
    ```js
    let x = 1;
    x = (x++, x);
    console.log(x); //2

    let y = 1;
    y = (y*=2, y*=3, y*=4, y+=1, y);
    console.log(y); //25
    ```

    ### forループの例(理解できない場合は飛ばす事)
    - 単一の式が求められる場所に、複数の式をねじ込むことができる

    ```js
    for (let i = 0, j = 5; j >=0; i++, j--){
      console.log(i+j);
    }

    //output
    //5
    //5
    //5
    //5
    //5
    //5
    ```

    ### カンマ演算子の別例
    - `()`でくくっているのは、カンマの優先度が低い(1)ため
    ```js
    console.log((1,2)); //2 (1)
    console.log((1+2,3+4)); //7
    1,2 //2
    ```

    1. 最後の値が評価されている

## Q55. JSにおける比較演算子の書き方を知っていますか?

??? success

    ### `a > b`
    - aはbより大きい

    ### `a < b`
    - aはbより小さい

    ### `a >= b`
    - aはb以上

    ### `a <= b`
    - aはb以下

    ### `a == b`
    - aはbと等しい

    ### `a != b`
    - aはbと等しくない

    ### 基本
    - 結果は`true`または`false`
    - つまりBoolean型で返される

    ### 使用例

    ```js
    console.log(12 > 9); //true
    let result = 12 != 12;
    console.log(result); //false   
    ```

## Q56. 文字列比較がUnicode順であることを知っていますか?

??? success
    ### そもそも文字列比較とは

    ```js
    console.log("ABC" < "DEF"); //true
    console.log("B" < "a"); //true
    ```

    ### なぜ B < aなのか。
    - BはUnicodeでいうと、61
    - aはUnicodeでいうと、97
    - よって、B < a

## Q57.異なる型を比較したときの挙動を知っていますか?

??? success
    ### 数値型に変換される

    ```js
    console.log("2" > 1); //2 > 1が為される。よってtrue
    console.log("01" == 1); //1 == 1になる。よってtrue 
    ```


## Q58. `==`と`===`の違いについて知っていますか?

??? success

    ### 概要
    - `==`: 同じ型でなくとも、値があっていれば`true`
    - `===`: 値があっていても型が違っていれば、`false`
    - `!=`: 型が違っていても、値があっていれば`false`
    - `!==`: 値があっていても型が違っていれば`true`

    ### なぜそうなるのか
    - `==`: 比較前に型変換される
    - `===`: 型変換しない(厳密比較と呼ばれる)

    ```js
    console.log(1 == "1"); //true
    console.log(1 === "1"); //false
    console.log(1 === 1); //true
    ```

    ### なぜ厳密等価が必要なのか
    - 以下の例が成り立つのは直感的に不適切であるため

    ```js
    console.log(0 == false) //true
    console.log(" " == false); //true
    ```

## Q59. nullとundefinedを、その他の値と比較した場合の結果を知っていますか?

??? success
    ### null === undefined
    - false
    - 別の型なので自明

    ### null == undefined
    - true
    - 特殊なルールが適用される

    ### null と 0の比較
    - 比較はnullを0として扱う
    - 等価はnullをnullとして扱うので、異なっているとみなされる。


    ```js
    console.log(null > 0); //false
    console.log(null == 0); //false
    console.log(null >= 0); //true
    ```

    ### undefinedと0の比較
    - undefinedは比較する際、`NaN`に変換される
    - `NaN`はすべての比較で`false`を返すので以下の結果となる

    ```js
    console.log(undefined > 0); //false
    console.log(undefined < 0); //false
    console.log(undefined == 0); //false
    console.log(NaN == 0); //false
    ```

    ### 落とし穴を回避するためには
    - `null/undefined`を取りうる値は、他の値と比較しない。
