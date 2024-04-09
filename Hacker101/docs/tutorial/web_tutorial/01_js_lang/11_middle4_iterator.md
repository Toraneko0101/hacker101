# クイズ(中級4:イテレータ)

## Q106 配列以外も反復可能であることを知っていますか?

??? success
    ### 概要
    - 反復可能(`iterable`)なオブジェクトは`for ...of`ループに使用可能
    - また文字列なども反復可能

## Q107. Symbol.iteratorを用いると、任意のオブジェクトをiterableにできることを知っていますか?

??? success
    ### Symbol.iterator
    - 組み込みのシンボル
    - 同名のメソッドをオブジェクトに追加すると、`for ...of`時に、これが呼び出される

    ### Symbol.iteratorの制約
    1. `iterator`を返す必要がある
    2. 返される`iterator`は`next()`を持つ。持たないとエラー
    (`for ... of`は次の値が必要になると、`next()`を呼ぶため)
    3. `next()`の結果は、`{done: Boolean, value: any}`
    (`done = true`なら繰り返し終了)
    4. `true`でない場合、`value`は新しい値である必要がある。
    (同じ値の場合、無限ループするため)

    ### Symbol.iteratorの実装例
    - Symbolをkeyに設定するときは、`[]`で囲む

    === "コード例"

        ```js
        const range = {
          from: 1,
          to : 5,
        };

        //1. for...ofは最初にこれを呼び出す
        range[Symbol.iterator] = function(){

          //2. iteratorオブジェクトを返す
          //以降、for...ofはこのイテレータでのみ機能し、次の値を要求
          return{
            current: this.from,
            last : this.to,

            //3. loopにより、繰り返しnext()が呼ばれる
            next(){
              if(this.current <= this.last){
                //4. {done : boolean, value : value}が返る
                //ここでは値を返した後で、プロパティを加算している
                //仮に、anotherValueというプロパティを追加して返したとしても、for...ofでは使われない
                return {done: false, value: this.current++};
              }
              else{
                return {done: true};
              }
            }
          }
        }

        for(const num of range){
          console.log(num);
        }
        ```

    === "注意点"

        ### 概要
        - `range`自身は、`next()`を持たない
        - 持っているのは、呼び出される`iterator`である
        - 反復するのは`iterator`であり、rangeのメソッドは、反復オブジェクトを呼び出した後は用済みになっている

    ### range自身をiteratorとした場合
    - 返り値で`this`を選択して、自身を`iterator`とする

    ```js
    const range = {
      from: 1,
      to : 5,

      [Symbol.iterator](){
          this.current = this.from;
          return this;
      },

      next(){
        if (this.current <= this.to){
          return {done: false, value: this.current++};
        }
        else{
          return {done: true};
        }
      }
    };

    for (const num of range){
      console.log(num); //1,2,3,4,5
    }
    ```

    ### iteratorの欠点
    - `iterator`は1つしかないので。1つのオブジェクトに対して、同時に2つの`for ... of`を実行することはできない

    ### 無限のイテレータを指定して、breakで止める

    ```js
    const range = {
      from : 1,
      to : Infinity,

      [Symbol.iterator](){
        this.current = this.from;
        return this;
      },

      next(){
        if(this.current <= this.to){
          return {done : false, value: this.current++};
        }
        else{
          return {done: true};
        }
      }
    }

    for(const num of range){
      //rangeイテレータを強制的に停止する
      if(num >= 10) break;
      console.log(num);
    }
    ```

## Q108. サロゲートペアを含む文字列が正しく反復処理されることを知っていますか?

??? success
    ### 通常文字

    ```js
    for (const char of "hoge"){
      console.log(char); 
      //h
      //o
      //g
      //e
    }
    ```

    ### サロゲートペア
    - 分解されることはない

    ```js
    const str = "😈😞";
    for(const char of str){
      console.log(char);
      //😈
      //😞
    }
    ```

## Q109. 文字列のイテレータを明示的に呼び出す方法を知っていますか？

??? success
    ### for ... ofの内部処理を手動でやる

    ```js
    const str = "Hoge";

    //文字列ラッパーの返り値としてiteratorを取得
    const iterator = str[Symbol.iterator]();

    while(true){
      //iteratorを手動呼び出し
      //1回目{done: false, value: "H"}
      //最後{done: true, value: undefined}
      const result = iterator.next();

      if(result.done) break;
      console.log(result.value); //1文字ずつ出力
      //H
      //o
      //g
      //e
    }
    ```

    ### for ... ofと比べたときの利点
    - 繰り返し処理を途中で中断可能
    - `for ... of`なら最後までループしてしまう

## Q110. 反復可能(iterable)と、配列ライク(array-like)の違いが分かりますか？

??? success
    ### iterable
    - `Symbol.iterable`メソッドを実装したオブジェクト

    ### array-like
    - `index`と`length`を持ったオブジェクト

    ### 概要
    - 片方を満たしたからといって、もう片方も真だとは限らない

    ### 例
    - `array-like`だが`、iterable`ではない

    ```js
    //array-like
    const arrayLike ={
      0 : "hoge",
      1 : "neko",
      length : 2
    };

    //error (Symbol.iteratorがないから)
    //for (const item of arrayLike){};
    ```

    ### 共通点
    - `array-like`も`iterable`も、`Array(配列)`ではないので、`pop`や`filter`等のメソッドは持たない

## Q111. Array.fromを用いて、iterableやarray-likeな値を本物の配列に変換する方法を知っていますか?

??? success
    ### Array.from
    - `iterable`や`array-like`を`Array`に変換する
    - 変換すると、配列のメソッドを呼べるようになる

    ### array-likeからの変換

    ```js
    const arrayLike = {
      0 : "hoge",
      1 : "fuga",
      length : 2,
    };

    const arr = Array.from(arrayLike);
    arr.pop();
    console.log(arr); //["hoge"]
    ```

    ### iterableからの変換
    - なお、`for ... of`を明示的に呼び出す必要はない
    - これは`Array.from`が内部で`range`オブジェクトの`Symbol.iterator`メソッドを呼び出しているため
    - イテレータが`to`まで進む中で、取得された値は、`Array.from`によって新しい配列に追加される

    ```js
    const range = {
      from : 1,
      to : 5,
      [Symbol.iterator](){
        this.current = this.from;
        return this;
      },

      next(){
        if(this.current <= this.to){
          return {done: false, value: this.current++};
        }
        else{
          return {done: true};
        }
      }
    };

    const arr = Array.from(range);
    console.log(arr); //[1,2,3,4,5]
    ```

    ### Array.from(obj [, mapFn(elm, idx), thisArg])
    - 完全な構文が上記
    - `mapFn`は配列に追加する前に各要素に適用する関数
    - `elm`：現在処理中の要素
    - `idx`:処理中要素のindex
    - `thisArg`は関数における`this`

    ### 使用例

    ```js
    const arr = Array.from({length: 3}, (_, i)=> i);
    console.log(arr); //[0,1,2]
    ```

    ### 文字列を文字配列に変換
    - `Array.from`はサロゲートペア文字を正しく扱う

    ```js
    const str = "𝒳😂";
    const chars = Array.from(str);

    console.log(chars[0]); //𝒳
    ```

    - 内部的にはこれと同じ(内部でfor...ofを読んでいることが明白)

    ```js
    const str = "𝒳😂";
    const chars = [];
    for(const char of str){
      chars.push(char);
    }
    console.log(chars[0]);//𝒳
    ```

    ### サロゲートペアを意識したslice関数を実装

    ```js
    const slice = (str, start, end) =>{
      return Array.from(str).slice(start, end).join("")
    };

    const str = "𝒳😂";
    const result = slice(str,0,1);
    console.log(result); //𝒳
    ```

## Q112. 100~199までの要素を持った配列をArray.fromを使って作ることができますか? ただし10で割り切れるものは中身に持たない

??? success
    ### 答え

    ```js
    Array.from({length: 100}, (_, i) => i+100)
    .filter(i => i % 10 ? true : false)
    ```