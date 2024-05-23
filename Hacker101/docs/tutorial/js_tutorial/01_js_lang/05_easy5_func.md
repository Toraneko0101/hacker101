# クイズ(初級5：関数)

## Q78. 関数の意味と、functionを用いた関数定義について知っていますか?

??? success
    ### 関数
    - スクリプトの様々な場所で、同じアクションをしたくなった時に使用する（コードを複数回書く必要がないように）

    ### 組み込み関数
    下記も、JSによって前もって定義されている関数の1つ

    - `alert`
    - `prompt`
    - `confirm`

    ### 関数定義
    - 関数を作るためには、関数定義が必要

    ### 呼び出し
    - 関数を呼び出すには、`関数名()`と書く

    ### 構文

    ```js
    //関数定義
    function 関数名(引数){
      処理内容
    }

    //呼び出し
    関数名()
    ```

    ### コード例

    ```js
    function showMessage(){
      console.log("Hello");
    }

    showMessage();
    showMessage();
    showMessage();
    ```

## Q79. 関数に引数を渡す方法を知っていますか?

??? success
    ### コード例
    - 仮引数：関数内に仮で設定される引数
    - 実引数：呼び出す際に、実際に渡される引数

    ```js
    function showMessage(from, text){
      console.log(from + ":" + text);
    }

    showMessage("Ann", "Hello"); // Ann : Hello
    //実引数の数が足りていないと、仮引数は定義されず、undefinedとなる。
    showMessage("Taro"); // Taro : undefined
    ```

## Q80. 引数のデフォルト値について知っていますか?

??? success
    ### デフォルト値
    - 呼び出し時に、値が渡されなかった時に使用される値

    ### 使用例

    ```js
    function showMessage(from, text = "hoge"){
      console.log(from + ":" + text);
    }

    showMessage("Taro"); //Taro : hoge
    ```

## Q81. 引数のデフォルト値に、関数呼び出し等を設定できることを知っていますか?

??? success
    ### コード例

    ```js
    function showMessage(from, text = createDefaultMessage()){
      console.log(from + ":" + text);
    }

    function createDefaultMessage(){
      return "hogehogehoge"; //returnについては後程説明
    }

    showMessage("Ann", "Hello"); // Ann : Hello
    showMessage("Taro"); //Taro:hogehogehoge
    ```

## Q82. デフォルトパラメータを関数中で設定したい場合、`??`や`undefined`が使えることを理解していますか?

??? success
    ### undefinedを使う場合

    ```js
    function showMessage(text){
      if(text === undefined){
        text = "empty message";
      }

      console.log(text);
    }

    showMessage(); //empty message;
    ```

    ### ??を使う場合

    ```js
    function showMessage(text){
      console.log(text ?? "empty message");
    }

    showMessage(); //empty Message;
    ```

## Q83. 関数から値を返却したい場合、returnを用いることを理解していますか?

??? success
    ### returnの例
    - `return`を行った場合、関数内の処理はそこで終了する

    ```js
    function sum(a, b){
      return a + b;
    }

    let result = sum(1, 2);
    console.log(result); //3
    ```

    ### returnは関数内に複数かける

    ```js
    function checkAge(age){
      if(age > 18){
        return true;
      }else{
        return false;
      }
    }
    ```
## Q84. 早期リターンの有用性について理解していますか?

??? success

    ### 入れ子が生じにくくなる
    - 下記では`if`の場合、`return`を行っているので、`else`句を書く必要がない。

    ```js
    function checkAge(age){
      if (age > 18){
        return "university";
      }
      return "others";
    }
    ```


## Q85. returnがない関数。またはreturn;では、undefinedが返されることを理解していますか?

??? success
    ### 答え
    - `undefined`が返される

    ### 例

    ```js
    function returnVoid(){
    };
    let value = returnVoid();
    console.log(value === undefined); //true
    ```

## Q86. returnと値との間に改行を入れてはいけない理由を理解していますか?

??? success

    ### 答え
    - JSは`return`の後にセミコロンを想定するから

    ### 下記のコードは`undefined`を返す

    ```js
    return
      (hoge + fuga);
    ```

## Q87. 複数行にわたってreturnを返したい場合、()を使うことを理解していますか?

??? success
    ### これは可能
    - `()`の中にセミコロンを書かないよう注意

    ```js
    function sum(a, b){
      return(
        a + b
      )
    }

    sum(12, 21); //33
    ```

## Q88. 関数の正しい命名記法について理解していますか?

??? success
    ### あくまでも一例です
    - `show...`: 何かを表示する
    - `get...`: 値を取得する
    - `calc...`: 何かを計算する
    - `create...`: 何かを生成する
    - `check...`: 何かをチェックして、真偽値を返す

    ### 関数例

    ```js
    showMessage(); //メッセージを表示する
    getAge(); //年齢を取得する
    calcSum(); //合計を計算して返す
    createForm(); //フォームを生成して返却する
    checkPermission(); //権限をチェックして、true/falseを返す
    ```

## Q89. 関数の命名記法を守らなかった場合の危険を理解していますか?

??? success

    ### 一例
    1. たとえば`checkPermission()`という関数があったとする
    2. この関数を見た人は、通常、これは権限をチェックするためのもので、この関数を何度実行しても、副作用は生じないと考える
    3. しかし、実は`checkPermission()`には、変数の値を変更する処理が含まれていたとする
    4. 結果、読み手と書き手の間で、齟齬が発生し、コードレビュワーや、後任の人間は不具合の原因に奔走することになる

## Q90. 1つの関数で1つのアクションを行うことが望ましい理由が分かりますか?

??? success
    ### 命名上の都合
    - 1つの関数で2つ以上の処理を行うと、関数名を付けるのに苦労する

    ### 例
    1. たとえば、`isDrikingAlcohol()`内で、お酒が飲めるか判定するとともに、警告を表示したくなった場合
    2. 警告を同じ関数内に書くと、関数名と処理が一致しなくなる
    3. 結果、関数の命名規則が守られなくなり、デバッグやテスト時に苦しむことになる(もしくは不要であったはずのコメントが大量に生まれる)

    ### 解決策
    1. 関数を分割する
    2. 2つの処理を呼ぶ、3つ目の関数を作る

    ### 改善例
    1. `isDrikingAlcohol()`は変えない
    2. `showAgeLimitWarning()`を作る
    3. `isDrikingAlcohol()`内で、`showAgeLimitWarning()`を呼び出す

    ```js
    function isDrikingAlcohol(age){
      if(age < 20){
        showAgeLimitWarning();
        return false;
      }
      return true;
    }

    function showAgeLimitWarning(){
      console.log("You're prohibited from drinking until you are 20");
    }

    isDrikingAlcohol(17);


    ```

## Q91. 時に関数がコメントのような役割を持つことを理解していますか?

??? success
    ### 再利用しない場合でも、関数は有用

    ```js
    function showPrimes(n){
      for(let i = 2; i < n; i++){
          if(!isPrime(i)) continue;

          console.log(i); //a prime
      }
    }

    function isPrime(n){
      for(let i = 2; i <= Math.sqrt(n); i++){
        if (n % i == 0) return false;
      }
      return true;
    }

    showPrimes(32); //2~31までの中で、素数を表示
    ```

## Q92. 3種のスコープについて知っていますか?
??? success
    - スコープとは、変数や関数が利用できる領域のこと。
    - JSには3種のスコープがある
    ### グローバルスコープ
    - ここで宣言された変数や関数はどこでも利用可能

    ```js
    const hoge = 10;
    {
      console.log(hoge); //10
    }
    ```
    ### 関数スコープ
    - 関数内で宣言された変数・関数は関数内で利用可能

    ```js
    function fuga(){
      const hoge = 10;
      {
        console.log(hoge); //10
      }
      console.log(hoge); //10
    }

    //console.log(hoge); //error
    ```
    ### ブロックスコープ
    - `{}`の中で宣言された変数・関数は`{}`の中でだけ利用可能

    ```js
    {
      const hoge = 10;
      if(true){
        const fuga = 20;
      }
      console.log(hoge); //10
      //console.log(fuga); //error
    }
    //console.log(hoge); //error
    ```

## Q93. varとlet, constの違いは分かりますか？

??? success
    | -     | 再代入 | 再定義 | スコープ         |
    | ----- | ------ | ------ | ---------------- |
    | var   | 〇     | 〇     | 関数スコープ     |
    | let   | 〇     | ✖      | ブロックスコープ |
    | const | ✖      | ✖      | ブロックスコープ |

    ### 再代入

    ```js
    var hoge = 1;
    hoge = 2;

    let fuga = 2;
    fuga = 3;

    const hogehoge = 3;
    //hogehoge = 4; //error
    ```

    ### 再定義

    ```js
    var hoge = 1;
    var hoge = 2;

    let fuga = 1;
    //let fuga = 2; //error

    const hogehoge = 3;
    //const hogehoge = 4; //error
    ```
    ### スコープ

    ```js
    var hoge = 1;
    if(hoge === 1){
      var num1 = 1;
      let num2 = 2;
      console.log(num1); //1
      console.log(num2); //2
    }

    console.log(num1); //(1)
    //console.log(num2); //error
    ```
    
    1. varはブロックスコープではないので、if文の外で`console.log()`を行っても、値が取得できる

## Q94. 関数式と関数宣言の違いについて理解していますか?

??? success
    ### 関数式

    ```js
    let sayHello = function(){
      console.log("Hello");
    };
    ```

    ### 関数宣言との些細な違い
    - 関数が変数に代入されている
    - 末尾にセミコロンがついている

    ### 関数宣言との重大な違い
    - 関数式の場合、 __記述前は呼び出し不可__
    - 関数宣言は、 __スコープ外からは呼び出し不可__

    ### 関数宣言が好ましい場合

    - 関数宣言

    ```js
    sum(12, 21); //33
    function sum(a,b){
      console.log(a + b);
    }
    ```

    - 関数式

    ```js
    multiple(12, 21); //error
    let multiple = funciton(x, y){
      console.log(x * y);
    }
    ```

    ### 関数式が好ましい場合
    - 関数宣言

    ```js
    let age = 20;

    if(age < 18){
      function welcome(){
        alert("Hello");
      }
    }else{
      function welcome(){
        alert("Greetings");
      }
    }

    welcome(); //error
    ```

    - 関数式

    ```js
    let age = 16;
    let welcome;

    if(age < 18){
        welcome = function(){
          alert("Hello");
        };
    }else{
        welcome = function(){
          alert("Greeting");
        }
    }

    welcome(); //Hello
    ```

    - 関数式(三項演算子)

    ```js
    let age = 16;

    let welcome = (age < 18 ) ?
      function(){alert("Hello"); } :
      function(){alert("Greetings!"); };
    
    //実際のところ let welcome = function(){alert("Hello"); } 
    
    welcome(); //Hello
    ```

    ### 関数宣言と関数式の使いどころ
    - 通常は関数宣言の方が自由度がある
    - スコープ外に書く必要がある場合は、関数式

    ### 関数宣言を後から変数に代入することもできる

    ```js
    function sayHello(){
      console.log("Hello");
    }

    let func = sayHello;

    func(); //Hello
    sayHello(); //Hello (これが動く理由は参照を学ぶとわかる)
    ```

## Q95. 関数式の末尾にセミコロンがついている理由が分かりますか?

??? success
    ### 答え
    - 関数式はコードブロックではないから
    - 関数式の後ろの`;`は、単に文の終わりを意味する

    ### セミコロンが要らないもの
    これらは末尾にコードブロック`{}`がある

    - `if{}`
    - `for{}`
    - `function f{}`


## Q96. コールバック関数について知っていますか?

??? success
    ### コールバック関数
    - とある関数の呼び出し時に、引数として渡される別の関数

    ### 例
    - 下記のコードにおける、`showOK`と`showCancel`がコールバック関数

    ```js
    function ask(question, yes, no){
      if(confirm(question)) yes()
      else no();
    }

    function showOk(){
      alert("You agreed.");
    }

    function showCancel(){
      alert("You canceled the execution.");
    }

    ask("Are you sure?", showOk, showCancel);
    ```

## Q97. コールバック関数の中で無名関数を使う方法について知っていますか?

??? success
    ### 無名関数
    - 名前を持たない関数
    - 関数式の右辺だけを書くとこうなる
    - 変数に割り当てられていないので再利用はできない


    ### コード例

    ```js
    function ask(question, yes, no){
      if(confirm(question)) yes() 
      else no();
    }

    ask(
      "Are you sure?",
      function() {alert("You agreed");}, //無名関数
      function() {alert("You canceled the exection.");} //無名関数
    );
    ```

## Q98. アロー関数式の記法について知っていますか?

??? success

    ### 構文
    - 引数`arg1...argN`を取り、`expression`を評価し、その結果を返す関数`func`を作る。

    ```js
    let func = (arg1, arg2, ...argN) => expression
    ```

    ### 具体例

    ```js
    let sum = (a,b) => a + b;

    console.log(sum(1,2)); //3
    ```

    ### 引数が1つの場合、括弧は省略可能

    ```js
    let double = a => a * 2;
    console.log(double(12)); //24
    ```

    ### 引数がない場合は、空の括弧が必要

    ```js
    let showMessage = () => console.log("Message");

    showMessage();
    ```

    ### 複数行の場合は`{}`と(undefined以外の値を返したい場合は)`return`が必要

    ```js
    let sum = (a, b) => {
      let result = a + b;
      return result;
    }

    console.log(sum(12, 21)); //33
    ```

