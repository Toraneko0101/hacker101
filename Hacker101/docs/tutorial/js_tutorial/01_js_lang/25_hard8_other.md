# クイズ(上級8:その他)

## Q159 evalを用いるとコード文字列を実行できることを知っていますか? また忌み嫌われていることも。

??? success
    ### 注意点
    !!! warning
        - 開発者ツール等の場合、`This document requires 'TrustedScript' assignment.`等のエラーが出る(サーバーを立てればいい)
        - `eval`を利用する理由はほとんどない
        - `minifier`は`eval`から見える可能性のあるすべてのローカル変数を置き換えないので、コードの圧縮率に悪影響が出る
        - `eval`内部で外部のローカル変数を使うと、コードのメンテナンスが難しくなる

    ### 注意点の対策
    - `window.eval()`とすると、コードはグローバルスコープで実行されるので、外部のローカル変数を使用する可能性がなくなる
    - ローカル変数がどうしても必要なら、`eval`を`new Function`に変更して、変数は引数で渡す

    ### 上記の注意点を踏まえた使用例

    - 基本

    ```js
    let code = `alert("Hello")`;
    eval(code); //Hello
    ```

    - `eval`の結果は最後の文の結果となる

    ```js
    let value = eval(`let i = 0; ++i`);
    console.log(value); //1
    ```

    - `window.eval`を用いるようにする

    ```js
    let x = 1;
    {
      let x = 5;
      window.eval(`alert(x)`); //1
    }
    ```

    - ローカル変数が必要な場合(new Functionの`[[Environment]]`はグローバルのレキシカル環境を参照するので)

    ```js
    {
      let f = new Function("a", "alert(a)");
      let a = 20;
      f(a); //20
    }
    ```

    ### strict-modeの場合
    - `stric-mode`の場合`eval`は独自のレキシカル環境を持つので、`eval`内で宣言された関数/変数は外部から見れなくなる
    - 非`strict-mode`なら外部から見られる

    ```js
    let x = 5;
    eval("x = 10; y = 5");
    console.log(y); //5
    ```

    ```js
    "use strict";
    let x = 5;
    eval("x = 10; y = 5");
    console.log(y); //5    
    //Uncaught ReferenceError: y is not defined
    ```

## Q160 カリー化の意味を知っていますか?

??? success
    ### カリー化
    - 関数を変形する事
    - 例) `f(a,b,c)`を`f(a)(b)(c)`
    - カリー化は関数を呼び出さず、変形する

    ### 手動での作成例
    - クロージャの性質も利用して、ラッパーを作成する

    ```js
    function curry(f){
      return function(a){
        return function(b){
          return f(a,b);
        }
      }
    }
    
    function sum(a,b){
      return a + b;
    }

    let curriedSum = curry(sum);
    console.log(curriedSum(1)(2));
    ```

    ### lodashライブラリの例
    - `loadsh`の`_.curry`を使うと、関数を通常通り呼んだり、部分的に読んだりできる

    ```html
    <script src="https://cdnjs.cloudflare.com/ajax/libs/lodash.js/4.17.21/lodash.min.js">
    </script>

    <script>
      function sum(a,b){
        return a+b;
      }

      let curriedSum = _.curry(sum);
      console.log(curriedSum(1,2)); //3
      console.log(curriedSum(1)(2)); //3
    </script>
    ```

## Q151 カリー化の利点を知っていますか?

??? success
    ###　部分適用
    - 現在の関数はそのままに、引数の一部だけを固定した関数を作ることができる

    ### 拡張性
    - 関数を細かな部品に分割できるので、部品を組み合わせて新しい関数を作成しやすくなる

    ### 可読性
    - カリー化された関数のチェーンは、関数の流れをより明確に表現可能

    ### 再利用
    - 同じ引数を何度も適用したいときに、時短になる

    ### 使用例(`lodash`使用)

    ```html
    <script src="https://cdnjs.cloudflare.com/ajax/libs/lodash.js/4.17.21/lodash.min.js">
    </script>

    <script>
      function log(date, importance, message){
        console.log(`[${date.getHours()}:${date.getMinutes()}] [${importance}] [${message}]`);
      }

      log = _.curry(log); //curry化

      //Dateだけを固定
      let logNow = log(new Date());

      //他の引数を使用
      logNow("INFO", "test-message"); //[19:22] [INFO] [test-message]
      logNow("WARNING", "hoge"); //[19:22] [WARNING] [hoge]
      
      //更に、重要度レベルも固定する
      let debugNow = logNow("DEBUG");
      let errorNow = logNow("ERROR");

      debugNow("test2"); //[19:22] [DEBUG] [test2]
      errorNow("test3"); //[19:22] [ERROR] [test3]
    </script>
    ```

## Q152 部分適用&通常の使用の両方が可能な、高度なカリー化を実装できますか(like lodash)

??? success
    ### 例

    ```js
    function curried(func){
      return function curried(...args){
        if(args.length >= func.length){
          return func.apply(this, args);
        }
        else{
          return function(...args2){
            return curried.apply(this, args.concat(args2));
            //第二引数が次の...argsとなる
          }
        }
      }
    }

    /*
    1. 渡されたargsの数が元関数で定義されている引数以上なら
       単にfunc.applyを使用する
    2. 引数に満たないなら部分適用して、ラッパを返す(返り値は引数が固定された関数)
    3. ラッパは新しい引数とともに、以前与えられた引数をcurriedに再適用していく
    4. 最終的には`if`の分岐を通り結果が得られる
    */

    function sum(a,b,c){
      return a+b+c;
    }

    let curriedSum = curried(sum);
    console.log(curriedSum(1,2,3));
    console.log(curriedSum(1)(2,3));
    console.log(curriedSum(1)(2)(3));
    ```

    ### 注意点
    - 上記の例からも明白だが、元関数の引数が`...args`である場合、`func.length`は`0`となる
    - よって、カリー化を行う際は、元関数の引数の数は固定されている必要がある

    ### 他の言語の場合
    - カリー化とは以下のものを指す
    - `sum(a,b,c)`が`sum(a)(b)(c)`
    - JSは、以下もできるという点でより柔軟である
    - `sum(a,b)(c)`

## Q153 複雑なメソッド呼び出しを行ったときに、thisを失う可能性がある理由を参照型という言葉を用いて説明できますか?

??? success
    ### 例えば以下のような呼び出しを考える

    ```js
    let user = {
      name : "John",
      hi(){console.log(`Hello ${this.name}`)},
      bye(){console.log(`Bye ${this.name}`)}
    };

    (user.name == "John" ? user.hi : user.bye)(); //Error
    //三項演算子を使って、2つのメソッドをすぐに呼び出さず選択している。
    (user.hi)(); //Hello, John
    //単純にメソッド名だけを括弧で囲んでも、thisのコンテキストには影響しない
    ```

    ### obj.method()の挙動
    - `obj.method()`を考える
    - 1つ目の動作として、`.`演算子がプロパティ`obj.method`を抽出している(`[[Get]]`)
    - 2つ目の動作として、`()`演算子でそれを実行している`[[Call]]`
    - ここで、最初の処理から2つ目の処理へ、`this`がどのように受け渡されるのか考える

    ### `.`演算子や角括弧
    - ドットは関数ではなく、参照型を返す
    - 参照型の値は`(base, name, strict)`
    - ここで、`base`はオブジェクト。`name`はプロパティ。`strict`は`true/false`で`strict`モードか否か

    ### つまり
    - 厳密モードで、`user.hi`へアクセスを行った場合、得られる結果は`(user, "hi", true)`という形の、プロパティと取得元オブジェクトの情報を両方保持する参照型
    - __参照型に対して`()`で呼び出すと、__ オブジェクトとメソッドについての正しい情報を受け取ることができる
    - つまり、参照型は`.`から`()`へ情報を渡す特別な内部型といえる。

    ### ここで`hi = user.hi`を考える
    - 参照型に対して、関数呼び出し以外の操作を行った場合、(たとえば代入や三項演算子や`||`や`&&`)、参照型は破棄され、単に関数の値が渡る(thisには何も設定できなくなる)
    - そしてその後の操作で参照型の情報は取得できなくなる
    - `(user.hi)()`が許されるのは、関数呼び出し以外の動作を行っていないから
    - 一方、`obj.method()`や`obj[method]()`の場合も、参照型を介して情報を渡すことができているので、呼び出しの中で正常に`this`を用いることができる

## Q154 BigIntをnumberに変換する際の注意点を知っていますか?(加えてその他の注意点も)

??? success
    ### 単項+で整数にはできない

    ```js
    let bigint = 1n;
    console.log(+bigint); 
    ```

    ### 整数だということを忘れないようにする

    ```js
    console.log(5n/2n); //2n
    ```

    ### 型変換は明示的に

    ```js
    let number = 2;
    let bigint = 2n;
    console.log(BigInt(number)); //2n
    console.log(Number(bigint)); //2
    ```