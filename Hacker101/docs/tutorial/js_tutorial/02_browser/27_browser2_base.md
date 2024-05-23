# クイズ(2:基本)

## 方針
??? success
    - DOM以外のブラウザ概要
    - 開発者ツール等

## Q19 ホスト環境という言葉の意味が分かりますか?

??? success
    ### ホスト環境
    - 言語のコア(JS)に加えて、プラットフォーム固有の機能を提供する環境のこと

    ### 例
    - ブラウザなら`JS` + `Web`ページを制御する手段
    - `Node.js`なら`JS` + サーバサイドの機能

## Q20 JSがWebブラウザで実行されているとき、windowがグローバルオブジェクト以外にブラウザウィンドウを指すことを理解していますか?

??? success
    ### グローバルオブジェクトとしての役割
    - グローバル関数は`window`のプロパティとしてアクセス可能

    ```js
    function sayHi(){
      console.log("Hello");
    }

    window.sayHi(); //Hello
    ```

    ### ブラウザウィンドウとしての機能

    ```js
    console.log(window.innerHeight); //553
    ```

## Q21 BOMについて知っていますか?

??? success
    ### BOM(Browser Object Model)
    - ブラウザによって提供される`document`以外の追加オブジェクト

    ### 例1(navigatorオブジェクト)
    - ユーザのブラウザ情報やPCの情報を取得する

    ```js
    console.log(navigator.userAgent);
    //Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/121.0.0.0 Safari/537.36
    console.log(navigator.platform);
    //Win32
    ```

    ### 例2(locationオブジェクト)
    - 現在のURLを読んだり、ページ遷移に使う

    ```js
    console.log(location.href);

    if(confirm("Go to wikipedia?")){
      //ページ遷移
      location.href = "https://wikipedia.org";
    }
    ```

    ###　例3(alert/confirm/prompt)
    - これもDOMの一部

## Q22 windowオブジェクトがJS,DOM,BOMによって形作られていることを理解していますか?

??? success
    ### JSとwindowオブジェクト
    - `window`オブジェクトは`JS`のグローバルオブジェクトであり、ウェブページを動的に操作するためのプロパティを提供する

    ### JSとDOM
    - `window.document`プロパティを通じて、DOMツリーにアクセスし、HTMLやXMLドキュメントの内容を把握/変更する

    ### JSとBOM
    - `window`オブジェクトには、ブラウザのウィンドウや画面、`Navigator`関連のオブジェクトが含まれている

## Q23 HTMLドキュメントを見て、対応するDOMがどのような形になるか推測できますか?

??? success
    ### 例

    ```html
    <!DOCTYPE HTML>
    <html>
    <head>
      <title>About elks</title>
    </head>
    <body>
      The truth about elks.
    </body>
    </html>
    ```

    ### 対応するDOM

    ```
    <!DOCTYPE html>
    HTML
      HEAD
        #text "\n "
        TITLE
        #text "\n"
      BODY
        #text "\n  The truth about elks.\n\n\n"
    ```

    ### `<head>`前のスペースと改行は無視

    ```html
    <!DOCTYPE html>
    <html>


      <head>
      </head>
      <body>
      <!--hoge-->
      </body>
    </html>
    ```

    ```
    <!DOCTYPE html>
    HTML
      HEAD
        #text "\n"
      BODY
        #text "\n"
        comment "hoge"
        #text "\n\n\n"
      
    ```

    ### `</body>`の後ろに何かを置くと、bodyの中に移動

    ```html
    <!DOCTYPE html>
    <html>


      <head>
      </head>
      <body>
      </body>
      <div></div>
    </html>
    ```

    ```
    <!DOCTYPE html>
    HTML
      HEAD
        #text "\n"
      BODY
        #text "\n"
        DIV
        #text "\n\n"
    ```

    ### `<tbody>`タグについて
    - 省略してもDOMの中に自動的に生成される

    ```html
    <table id="table">
      <tr>
        <td>1</td>
      </tr>
    </table>
    ```

    ```
    TABLE
      TBODY
        TR
          TD
            #text 1
    ```

    ### Live DOM Viewer
    - ドキュメントを入力すると、それに対応したDOMを表示してくれる

## Q24 $0等を用いて、直前に選択したDOM要素を開発者ツール上で表示できることを知っていますか?

??? success
    ### $0
    - 直前に選択した要素を利用する

    ```js
    //<li>要素を選択したとする
    console.log($0); //<li></li>
    ```

    ### $1など
    - 1つ前に選択した要素を利用する
    - 数字が増える度にさかのぼることになる

    ### $0を用いてプロパティを変更

    ```js
    //直前に<li>を選択したとする
    $0.style.background = "red"; //<li>の背景色を赤色に変更
    ```

## Q25 ElementsタブのサブタブであるStylesやComputed,Event Listenersで行えることを理解していますか?

??? success
    ### Styles
    - `CSS`のルールを組み込みの`CSS`を含めて表示する
    - 動的に変更も可能

    ### Computed
    - 最終的に適用されたCSSを表示する
    - CSSの継承の流れを見ることもできる

    ### Event Listeners
    - 各DOM要素にかんれんづけられたイベントリスナを表示可能

    ### Property
    - 各DOM要素の`Property`を閲覧可能

    ### その他
    - `Accecibility`
    - `Layout`
    - `DOM Breakpoint`

## Q26 DevToolsのその他の機能について

??? success
    - 工事中


