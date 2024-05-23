# クイズ(初級3:条件分岐)

## Q60. if文の記法を知っていますか?
??? success
    ### 概要
    - `if`は結果がtrueであれば、`{}`内のコードを実行する

    ```js
    let num = 12;
    if(num >= 10){
      console.log("Yes");
    }
    ```

    ### 実行する分が1行であれば`{}`は省略できる

    ```js
    let num = 12;
    if(num >= 10)
        console.log("Yes");
    ```

    ### 型変換で触れたことを思い出す

    ```js
    if(1){
      console.log("常に実行される");
    }
    if(true){
      console.log("常に実行される");
    }
    if(false){
      console.log("常に実行されない");
    }
    if(""){
      console.log("常に実行されない");
    }
    if(NaN){
      console.log("常に実行されない");
    }
    ```

## Q61. `else`句について知っていますか?
??? success
    ### 概要
    - 条件が偽の場合に実行される

    ### 例

    ```js
    let num = 12;

    if(num >= 20){
      console.log("Yes");
    }else{
      console.log("No");
    }
    ```

## Q62. 条件のパターンをいくつか書きたいときに`else if`が使えることを知っていますか?

??? success
    ### 基本
    - `else`を書くか書かないかは任意

    ```js
    let num = 12;

    if(num >= 100){
      console.log("a");
    }else if(num >= 10){
      console.log("b");
    }else if(num >= 5){
      console.log("c");
    }else{
      console.log("d");
    }

    //b
    ```

## Q63. 三項演算子について理解していますか？

??? success
    - `条件 ? trueの場合 : falseの場合`と記述する

    ### 例

    ```js
    let num = 12;
    //括弧で囲む必要はないが、可読性を重視している
    let result = (num > 10) ? "Yes" : "No";
    console.log(result); //Yes
    ```

## Q64. 三項演算子を入れ子のように記述できることを知っていますか?

??? success
    ### 注意点
    - 入れ子が多くなる場合、`if`を用いた方がいい

    ### 例
    - falseであれば、次の条件をチェックしている

    ```js
    let age = 12;
    let result = (age < 3) ? "age less than 3" : 
    (age < 5) ? "age less than 5" :
    (age < 10) ? "age less than 10" :
    (age < 20) ? "age less than 20" :
    "others";

    console.log(result); //age less than 20
    ```

## Q65. 論理演算子について理解していますか?
??? success
    ### 基本
    - 論理演算子は1つの文の中に複数配置できる

    ### `||`：OR。
    - 最初のtrueを返す。trueがない場合は最後の値を返す

    ```js
    console.log(true || "hello"); //true
    console.log(false || "hello"); //hello
    console.log(false || null); //null
    console.log(3 || undefined): //3
    ```
    ### `&&`：AND
    - 最初のfalseを返す。falseがない場合は最後の値を返す

    ```js
    console.log(false && null); //false
    console.log(false && "hello"); //false
    console.log(true && null); //null
    console.log(true && "hello"); //hello
    ```
    
    ### `!`：否定。
    - ブール値(Boolean)を反転する

    ```js
    console.log(!true); //false
    console.log(!"hello"); //false
    console.log(!0); //true
    ```

    ### 複数つなげて書く

    ```js
    const value1 = "";
    const value2 = null;
    const value3 = "Hello";
    //全ての変数が偽であれば、Anonymousが出力される
    const result = value1 || value2 || value3 || "Anonymous";
    console.log(result); //"Hello"
    ```

## Q66. 短絡評価について理解していますか?

??? success

    ### 概要
    - `||`や`&&`は、返す値が決まった場合、その他の引数を評価しない
    - そのため以下のようなことが可能である

    ### 使用例
    - これを利用して、`||`の場合`false`。`&&`の場合`true`の時だけコマンドを実行するコードを書いたりする

    ```js
    true || console.log("Yes"); //何も出力されない
    false || console.log("No"); //No

    true && console.log("左辺の条件はtrueだった");
    ```

## Q67. `&&`が`||`より優先度が高いことを覚えていますか?

??? success
    ### 覚えていないと、以下の答えを間違える

    ```js
    let result = false || true || true && false;
    console.log(result); //true
    ```

## Q68. if文の中で論理演算子を使ったことがありますか?

??? success
    
    ### 以下のように書いて複数の条件を設定できる

    ```js
    let hour = 8;

    if(hour < 9 || hour > 18){
      console.log("The office is closed");
    }
    ```

    ### 否定演算子の場合

    ```js
    if(!0){
      console.log("Yes")
    }; //Yes
    ```

    ### `&&`の場合

    ```js
    let hour = 8;
    if(hour >= 9 && hour <= 18){
      console.log("in time");
    }
    ```


    ### 3つ以上の条件を設定することも可能

    ```js
    let isWorkingTime = false;
    let isHoliday = false;
    let isWeekend = true;

    if(isWorkingTime || isHoliday || isWeekend){
      console.log("Overtime");
    }
    ```

## Q69. Nullish Coalescing Operator(??)の意味を知っていますか?
??? success

    ### 概要
    - `??`は左側の値が`null`または`undefined`の場合、右側の値を返し、それ以外の場合は、左側の値を返す
    - 初期値を設定したい場合に便利

    ```js
    console.log(null ?? "hello"); //hello
    console.log(undefined ?? "hello"); //hello
    console.log("" ?? "hello"); //""
    console.log(0 ?? "hello"); //0
    ```

    ### 注意点
    - 括弧で優先順位を明示的に示さない限り、`??`を`&&`や`||`とともに使うことはできない

    - 以下はできない(2024年1月現在)

    ```js
    let x = 1 && 2 ?? 3;
    ```

    - 以下は可能

    ```js
    let x = (1 && 2) ?? 3;
    console.log(x); //2
    ```
