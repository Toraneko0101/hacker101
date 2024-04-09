# クイズ(初級4:ループ)

## Q70. whileループについて知っていますか?

??? info
    ### 構文

    ```js
    while(condition){

    };
    ```

    - `condition`が`true`である間、`{}`内の処理を繰り返す

    ### 使い道
    - ループ回数が自明でない、ループ等

    ### 使用例

    ```js
    let i = 0;
    while(i < 3){
      console.log(i); //0, 1, 2
      i++;
    }
    ```

## Q71. do...while文について知っていますか?
??? success
    ### 構文
    
    ```js
    do{

    }while(condition);
    ```
    ### 使い道
    - `do...while`は、条件にかかわらず、必ず1回はコード内のブロックを評価したいときに使用する
    - 条件(condition)がtrueであればループを繰り返す

    ### 使用例

    ```js
    let i = 0;
    do{
      console.log(i);
      i++;
    }while(i < 3);

    //0
    //1
    //2
    ```
  
## Q72. forループについて知っていますか?

??? success
    ### 構文

    ```js
    for(begin; condition; step){
      //body
    }
    ```

    ### 手順

    1. beginが評価される
    2. 1回目のループとしてconditionが評価される
    3. conditionが偽なら、ループ停止
    4. bodyの処理が実行
    5. stepの処理が実行
    6. 2 ~ 5を繰り返す

    ### 使用例

    ```js
    for (let i = 0; i < 5 ; i++){
      console.log(i); //0,1,2,3,4
    }
    ```

    ### 使用例2
    - 初期化(begin)に使う変数をループの外で宣言した場合、ループ外でも変数は使用可能

    ```js


    let i = 0;

    for(i = 0; i < 3; i++){
      console.log(i); //0,1,2
    }

    console.log(i); //3
    ```

## Q73. forループで、各パートを省略できることを知っていますか?

??? success
    ### 例
    - 初期化(begin)と、各ステップ(step)を省略した例

    ```js

    let i = 0;
    for(; i < 3;){
      console.log(i++); //0,1,2
    }
    ```

## Q74. Javascriptで無限ループを作る方法について知っていますか?

??? success
    ### whileループ
    - while(条件)
    - 条件をtrueにすることで、無限ループが可能

    ```js
    while(true){

    }
    ```
    ### forループ
    - 条件をtrueにすることで、無限ループが可能

    ```js
    for(let i=0; true; i++){

    }
    ```

    ### 全てを省略したforループ

    ```js
    for(;;){

    }
    ```


## Q75. breakとcontinueについて理解していますか?

??? success

    ### break
    - breakはループの続行を強制的に停止する

    ### continue
    - continueは現在のループをスキップして、次のループに入る


    ### コード例

    ```js
    for (let i =0; i<5; i++){
      if(i == 1){
        continue;
      }
      if(i == 3){
        break;
      }
      console.log(i);
    }

    //output
    //0
    //2
    ```

## Q76. ラベルステートメントを知っていますか?

??? success

    ### 概要

    - `label:`は識別子の先頭にラベルを付けるために使用される
    - ※`break`や`continue`の際に、付け加えることで、ラベルをつけた場所に移動できる
    - 通常の場合、`break`等は内側のループを抜けるのみだが、labelを書くことで、幾つ入れ子になっていても、目的の位置まで抜け出ることが可能

    ### 使用例

    ```js
    loop1: for(let i=0; i<3; i++){
      loop2: for(let j=0; j<3; j++){
        if(j == 1){
          continue loop1;
        }
        console.log(`i:${i}, j:${j}`)
      }
    }

    //output (1)
    //i:0, j:0
    //i:1, j:0
    //i:2, j:0
    ```

    1. 通常の場合は、`continue`や`break`を用いても、スコープを一つ抜けるのみである。
    ```js
    for(let i=0; i<3; i++){
      for(let j=0; j<3; j++){
        if(j == 1){
          continue;
        }
        console.log(`i:${i}, j:${j}`)
      }
    }
    //output
    //i:0, j:0
    //i:0, j:2
    //i:1, j:0
    //i:1, j:2
    //i:2, j:0
    //i:2, j:2
    ```

## Q77. switch, case文について知っていますか?
??? success

    ### 概要
    - switchは式を評価
    - 式の値を`case`句と照合し、一致していれば実行する

    ### 注意点
    - `break`しない場合、次の`case`句も実行されてしまう
    - これを生かして、`case`を続けて書くことで、複数値に対して、1種の処理を実行可能
    - `case`の条件には、変数などが混じった式も使える
    - 全ての`case`に引っかからなかった場合(というより`break`されていない場合)、`default`句が実行される

    ### コード例
    - なお、型は同じである必要がある

    ```js
    let num = 3;
    switch (num){
      case 0:
      case 1:
        console.log("num equal 0 or 1");
        break;
      case num - 1:
        console.log("num equal 2");
        break;
      case "3":
        console.log("型が違うので引っかからない");
        break;
      default:
        console.log(`num = ${num}`);
      case "4":
        console.log("breakしなかったので実行された");

    }

    //output
    //num = 3;
    //breakしなかったので実行された
    ```

