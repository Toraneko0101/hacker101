# クイズ(上級5:generator)

## Q113 ジェネレータについて知っていますか?

??? success
    ### 通常の関数
    - 単一の値を返す(もしくは`undefined`)
    - 呼び出されると、内部が実行され、`return`が実行されるか、関数の末尾に達すると実行が終了する
    - 一時停止して、状態保持などはできない

    ### ジェネレータ
    - `yield`を用いて複数の値を生成可能
    - これらの値は反復処理できる
    - 呼び出された場合でも、一時停止が可能
    - 停止されている間、内部の状態は処理できるので再開可能
    - 無限に近い量の値を生成するとき、リストと異なり、中間の結果を格納するためのメモリが必要ないというメリットがある

    ### 構文
    - `generatorSequence()`が呼ばれてもコードは実行されない
    - 代わりに、ジェネレータと呼ばれるオブジェクトを返す
    - メインのメソッドは`next()`

    ```js
    function* generaterSequence(){
      yield 1;
      yield 2;
      return 3;
    }
    ```

    ### 手順
    ジェネレータは凍結された関数呼び出しと考えられる

    1. 作成時に、コードは先頭で一時停止される
    2. `next()`が呼ばれると、最も近い`yield <value>`まで実行
    3. `{value : <value>, done : false}`が返される
    4. 実行は停止し、再度`next()`が呼ばれると、次の`yield`まで実行
    5. 以下ループ
    6. `return`に到達すると`{value : <value>, done : true}`を返し、ジェネレータは終了する
    7. 以降も`next()`を呼び出すことはできるが、`{value: undefined, done: true}`しか返らない

    ### 使用例

    ```js
    function* generatorSequence(){
      yield 1;
      yield 2;
      return 3;
    }

    let generator = generatorSequence();

    let one = generator.next(); //{value: 1, done: false}
    let two = generator.next(); //{value: 2, done: false}
    let three = generator.next(); //{value:3, done: false}

    ```

    ### 注意点
    !!! warning
          - ジェネレータをロールバックすることはできない
          - つまり、一度`{value: 1, done: false}`を出力した後で、もう一度これを呼び出すことはできない
    
    ### 値を提供しているのは?
    - ジェネレータ関数(`function*`)はジェネレータを返すだけ
    - 上で言う、`generator.next()`はジェネレータ関数ではなく、返されたジェネレータに値を要求している
    
    ### function*について
    - `function *f()`と書くこともできるが、ジェネレータ関数であることが分かりやすいのは、`function*`

## Q114. ジェネレータ関数が反復できることを知っていますか?

??? success
    ### for...ofが使える

    ```js
    function* generatorSequence(){
      yield 1;
      yield 2;
      return 3;
    }

    let generator = generatorSequence();

    for(let value of generator){
      alert(value); //1,2
    }
    ```

    ### 問題点
    - `3`が表示されていない

    ### 原因
    - `for...of`は`{done : true}`の時、`value`を無視する

    ### 解決策
    - 全てを`yield`で返す
    - `return`がなくても、ジェネレータが最後まで実行されれば`done`は`true`になるのでOK

    ### 修正したコード

    ```js
    function* generatorSequence(){
      yield 1;
      yield 2;
      yield 3;
    }

    let generator = generatorSequence();

    for(let value of generator){
      alert(value); //1,2,3
    }

    generator.next(); //{value: undefined, done: true}
    ```

    ### ジェネレータは`iterable`なので
    - スプレッド演算子

    ```js
    function* generatorSequence(){
      yield 1;
      yield 2;
      yield 3;
    }

    let sequence = [...generatorSequence()];

    console.log(sequence); //[1,2,3]
    ```

    - Array.from

    ```js
    function* generatorSequence(){
      yield 1;
      yield 2;
      yield 3;
    }

    let arr = Array.from(generatorSequence()); //[1,2,3]

    ```

## Q115. Symbol.iteratorを用いて作った反復可能なオブジェクトが、ジェネレータに置き換えられることを知っていますか?

??? success
    ### 置き換え前(復習)

    ```js
    let range = {
      from: 1,
      to : 5,

      [Symbol.iterator](){
        this.current = this.from,
        return this;
      },

      next(){
        if(this.current <= this.to){
          return {done: false, value: this.current++};
        }
        return {done: true};
      }
    }

    console.log([...range])
    ```

    ### 置き換え後

    ```js
    function* generateSequence(start, end){
      for(let i=start; i<=end; i++){
        yield i;
      }
    }

    let sequence = [...generateSequence(1,5)];
    console.log(sequence); //[1, 2, 3, 4, 5]
    ```

    ### rangeオブジェクトを残したい場合

    ```js
    const range = {
      from : 1,
      to : 5,
      *[Symbol.iterator](){
        for(let value = this.from; value <= this.to; value++){
          yield value;
        }
      }
    }

    console.log([...range]); //[1,2,3,4,5]
    ```

    ### 上記が上手くいく理由
    - `*[Symbol.iterator]()`は`generator`である
    - `generator`は`next()`というメソッドを持っている
    - これは`{value : ..., done: true}`の形式で値を返す
    - このように`iterable`と互換性があるのは、そのようにgeneratorを設計したから

    ### 注意点
    ??? warning
        - 永遠に値を生成するジェネレータを作る場合は、`for...of`の中で`break`する

## Q116 yield*を用いて、別のジェネレータに実行を移譲し、その結果を転送できることを知っていますか?

??? success
    ### yield* expression
    - `expresson`:反復可能なオブジェクトを返す
    - `yield*`は`expression`によって返された各々の値を転送する
    - `yield*`自体の値は、`{done: true}`の時の`value`となる

    ### 転送の例

    ```js
    function* generateSequence(start, end){
      for(let i = start; i <= end; i++) yield i;
    }

    function* generatePasswordCodes(){
      yield* generateSequence(48,57); //0~9
      yield* generateSequence(65,90); //A~Z
      yield* generateSequence(97, 122);//a~z

      //以下と同等
      //for(let i=48; i<=57; i++) yield i;
    }

    let str = "";
    for(let code of generatePasswordCodes()){
      str += String.fromCodePoint(code);
    }

    console.log(str);
    //0123456789ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz
    ```

    ### まとめ
    - `yield*`を使うことで、とあるジェネレータを別のジェネレータのフローに挿入できる

## Q117 yieldが双方向であること、つまり、ジェネレータ内部に値を渡すこともできるということを理解していますか?

??? success
    ### generator.next(arg)
    - `next`なので次の`yield`まで進む
    - その際、現在、処理を中断している`yield`式が左辺に変数を持つのなら、`next()`メソッドに渡された引数が、その変数に代入され、ジェネレータ内で使われる
    - 渡されない場合は、変数には`undefined`が入る。`yield "2+2?"`だから`"2+2?"`が入るわけではない。この値は外部への受け渡し用にしか使われない

    ### 例

    ```js
    function* gen(){
      let result = yield "2+2?";
      alert(result);
      yield 2;
    }

    let generator = gen();
    let question = generator.next().value; //2+2?
    generator.next(4); //result = 4, なお、yield 2;が返る
    ```

    ### next(arg)はすぐに呼び出す必要はない

    ```js
    function* gen(){
      let result = yield "2+2?";
      alert(result);
      yield 11;
    }

    let generator = gen();
    generator.next(); //最初のyieldまで移動

    setTimeout(()=> {
        generator.next(4);
    }, 1000);
    ```

    ### 関数と呼び出しコードが値を渡しあう例

    ```js
    function* gen(){
      let ask1 = yield "2+2?";
      alert(ask1); //4

      let ask2 = yield "3*3?";
      alert(ask2); //9
    }

    let generator = gen();
    console.log(generator.next().value); //"2+2?"
    console.log(generator.next(4).value); //3*3?
    console.log(generator.next(9).done); //true
    ```

## Q118. エラーをyieldに渡すために、generator.throw(err)が使えることを知っていますか?

??? success
    ### generator.throw(err)
    - `err`は現在処理を中断している`yield`式に投げられる

    ### 例

    ```js
    function* gen(){
      let result = yield "2+2?"; //ここで例外となる
      alert("エラーの場合、ここにコードは達しない")
    }

    let generator = gen();
    let question = generator.next().value;

    try{
      //現在はlet result = yield "2+2?"の箇所で処理中断中
      generator.throw(new Error("The answer is not found"));
    }
    catch(e){
      alert(e); //Error: The answer is not found
    }

    ```

## Q119. 疑似乱数を生成し、seedを指定することで後から毎度同じ値を生成するジェネレータを作成することができますか?

??? succes
    ### 疑似乱数ジェネレータの例
    - 最初の値である`seed`を取る
    - 以降は公式を用いて、`next`を生成する
    - 最初の`previous == seed`
    - 疑似乱数なので`seed`が同じなら毎度同じ出力順番になる

    ```js
    next = previous * 16807 % 2147483647
    ```

    ### コード例

    ```js
    function* pseudoRandom(seed){
      let value = seed;
      while(true){
        value = value * 16807 % 2147483647;
        yield value;
      }
    }

    let generator = pseudoRandom(1);
    console.log(generator.next().value) //16807
    console.log(generator.next().value) //282475249
    console.log(generator.next().value) //1622650073
    console.log(generator.next().value) //984943658
    ```

    ### クロージャでもできそうだが
    - `for...of`を使ったイテレート
    - `yield*`を用いたジェネレータの入れ子
    - 以上のことをしたいならジェネレータにすること

## Q120 非同期のイテレータを作る方法を知っていますか?

??? success
    ### 使いどころ
    - `setTimeout`や別種の遅延処理の値に値が来るケース

    ### オブジェクトを非同期なiterableにするには?
    - `Symbol.asyncIterator`を使う
    - `next()`は`promise`を返す
    - `asyncキーワード`は`promise`を返すのでこれを使える

    ### 非同期オブジェクトをイテレートするには?
    - `for await (let item of iterable)`が使える

    ### 使用例

    ```js
    let range = {
      from: 1,
      to : 5,
      //promiseを返すnext()を持つオブジェクトを返せばいい。それが非同期イテレータ
      [Symbol.asyncIterator](){
        this.current = this.from;
        return this; //iteratorは自分自身
      },

      async next(){
        await new Promise(resolve => setTimeout(resolve, 1000));

        if(this.current <= this.to){
          return {done :false, value : this.current++};
        }
        return {done: true};
      },
    };

    (async () =>{
      for await (let value of range){
        alert(value);
      }
    })()
    ```

    ### 注意点
    !!! warning
        - スプレッド演算子は非同期には動作しない
        - スプレッド演算子は、`Symbol.iterator`を期待しているので当然といえば当然
        - `for...of`の場合も、`await`がないと`Symbol.iterator`が期待される

## Q121 非同期ジェネレータを用いて、非同期イテレータのコードを短く書く方法がわかりますか?

??? success
    ### async function*
    - `async`を付けるとジェネレータが非同期になる
    - `async`の中なので`await`も使える

    ### 使用例

    ```js
    async function* generateSequence(start, end){
      for(let i = start; i<=end; i++){
        await new Promise(resolve=>
          setTimeout(resolve, 1000)
        );

        yield i; //値はnext()を通じてアクセスされた時にPromiseとしてラップされる
      }
    };

    (async ()=>{
      let generator = generateSequence(1,5);
      for await (let value of generator){
        alert(value);
      }

      //for...ofループでない場合
      //await generator.next();
    })()
    ```

    ### rangeオブジェクトを残したい場合
    - `async *[Symbol.asyncIterator](){}`は非同期ジェネレータ

    ```js
    let range = {
      from : 1,
      to : 5,

      async *[Symbol.asyncIterator](){
        for(let value = this.from; value <= this.to; value++){
          await new Promise(resolve =>
            setTimeout(resolve, 1000)
          );

          yield value;
        }
      },
    };

    (async()=>{

      for await (let value of range){
        alert(value);
      }
    })()
    ```

    !!!info
        - `Symbol.iterator`と`Symbol.asyncIterator`を同じオブジェクトに追加することは可能
        - しかし使いどころがあるかは微妙

    !!!info
        - 以下のコードについて
        - モジュール未使用の場合は、トップレベルで`await`を使えない
        - 無名の`async`関数でラップしている

        ```js
        (async()=>{

          for await (let value of range){
            alert(value);
          }
        })()        
        ```

## Q122 非同期イテレータの技術を用いて、ページネーションを実装することができますか?

??? success
    ### オンラインAPIを考える
    - 事前に定められた`limit`分のデータを返し
    - 同時に、次のページへの`URL`を提供することが多い

    ### 例
    - `https://api.github.com/repos/<owner>/<repo>/commits`でURLをリクエスト可能
    - 30個分のコミットが`JSON`で返され、`LINK`ヘッダが次ページのリンクを提供する

    ### 非同期ジェネレータで実装する

    ```js
    async function* fetchCommits(repo){
      let url = `https://api.github.com/repos/${repo}/commits`;

      while(url){
        const response = await fetch(url);
        const body = await response.json(); //parse

        //次ページはレスポンスヘッダのLINKにある
        //matchは指定された正規表現に一致する部分文字列を抽出。(.*?)なので、任意の最小文字列
        //以下のような形を取り出す予定
        //Link: <https://api.github.com/Toraneko0101/My_Learning/commits?page=2>; rel="next"
        //から[1]でhttps://api.github.com/Toraneko0101/My_Learning/commits?page=2
        //他にもrel="prev"やrel="last", rel="first"などがある
        let nextPage = response.headers.get("Link")
          .match(/<(.*?)>; rel="next"/);
        
        //[0]:完全な一致
        //[1]:最初の丸括弧の中身
        nextPage = nextPage && nextPage[1];
        url = nextPage;

        for(let commit of body){
          yield commit;
        }
      }
    }

    (async()=>{
      let count = 0;
      for await (const commit of fetchCommits("Toraneko0101/My_learning")){
        console.log(commit.sha); //コミットのshaを出力
        if(++count == 100) break; //100を超えたら処理中でも終了
      }
    })()
    ```

    ### 補足
    - Web開発では、大きなファイルのダウンロードやアップロードを行う際、データをチャンクという単位で分けることがある
    - この時も、非同期ジェネレータが使える