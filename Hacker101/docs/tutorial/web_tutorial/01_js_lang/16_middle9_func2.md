# クイズ(中級9:関数発展)

## Q147 再帰関数とその書き方を知っていますか?

??? success
    ### 再帰
    - 条件分岐の結果、自身を呼び出す可能性を持つ関数
    
    ### 例

    ```js
    const pow = (x, n) =>{
      if(n == 1) return x;
      return x * pow(x, n - 1);

      //三項演算子を使った場合
      //return (n == 1) ? x : x * pow(x, n - 1)
    }

    pow(3, 12); //531441
    ```

    ### 再帰の注意点
    - 入れ子呼び出しの最大数は再帰の深さと呼ばれる
    - 上記例の場合、再帰の深さ = n
    - これが`100,000`を超えると、デフォルトの場合、エンジンによって制限される可能性がある。

    ```js
    pow(3,1e7); //Uncaught RangeError: Maximum call stack size exceeded
    ```

## Q148. 再帰呼び出しの仕組みと、実行コンテキストについて知っていますか?

??? success
    ### 再帰呼び出しの仕組み
    1. 関数が自身を呼び出す（ネスト呼び出し）
    2. 現在の関数が一時停止する
    3. ネスト呼び出しが実行される
    4. 一時停止した時点から、関数が再開される

    ### 再帰が可能なわけ(スタック)

    - 下記のコードを対象に考える

    ```js
    function pow(x,n){
      if (n == 1) return x;
      return pow(x, n - 1) * x;
    }

    console.log(pow(2,3));
    ```

    1. `pow(2,3)`が呼び出される
    2. 実行コンテキストに変数が格納される。`Context : {x : 2, n : 3}`
    3. `n != 1`なので、3行目まで移動する
    4. `pow(x, n - 1) = pow(2,2)`が呼ばれる
    5. 実行コンテキストに変数が格納される。`Context : {x : 2, n : 2}`
    6. `n != 1`なので3行目まで移動する
    7. `pow(x, n - 1) = pow(2,1)`が呼ばれる
    8. 実行コンテキストに変数が格納される。`Context : {x : 2, n : 1}`
    9. `n == 1`なので`2`が返る
    10. `pow(2,2)`の実行が再開される
    11. `2*2 = 4`が返る
    12. `pow(2,3)`の実行が再開される
    13. `2*3 = 8`が返る
    14. `console.log()`で出力される

    !!! success
        - 一時停止した関数が再開されるとき、その再開地点は、再帰呼び出し＝サブコールの直後からになる。
        - つまり、今回の場合、結果が返ってきたタイミングで、`*`が評価される。

    ###　実行コンテキスト
    15. JSはコードを実行コンテキストと呼ばれる実行環境に分けて実行する
    16. コンテキストは、コード実行中に作成され、コールスタックに積まれる
    17. 終了したら、取り除かれる
    18. つまり、関数内で自身を呼ぶと、後から呼ばれた方が上に積み重なり、先に実行されることになる
    19. 実行が終われば、呼んだ側のコードが実行される

    __※　コールスタック：実行コンテキストが積み重なる領域__

    ### 実行コンテキストの種類
    - グローバルコンテキスト：コードを実行する前に生成される
    - 関数コンテキスト：関数が実行される直前に生成される

    ### コード実行の手順
    1. 最初に、グローバルコンテキストが生成される
    2. 関数があると、関数コンテキストが生成
    3. 関数の中に、さらに関数があれば、そのコンテキストが生成
    4. この間、グローバルコンテキストは中断されている
    5. 関数の実行が終われば、またグローバルコンテキストが実行
    6. 最終行まで行くと終了

    ※　生成されたコンテキストは、コールスタックにpushされる
    ※　コンテキストは、コールスタックからpopされ、実行される
    ※　常に一番上のスタックがpopされて、実行される。

    ### コールスタックの確認
    - 開発者ツールを開く

    === "コード例"

        ```html
        <script>
          function a() {
            debugger;
            console.log("A");
          }

          function b() {
            a();
            console.log("B");
          }

          function c() {
            b();
            console.log("C");
          }

          c();
        </script>
        ```
    
    === "説明"
        - `anonymous`はグローバルスタックを指す

        ```title="コールスタック"
        -> a
           b
           c
           anonymous
        ```

## Q149. 再帰の弱点を知っていますか?

??? success
    ### ループはメモリを節約する
    - 実行コンテキストは、メモリを必要とする

    ### 再帰をループで書き直した例

    ```js

    const pow = (x, n) =>{
      let result = 1;
      for(let i = 0; i < n; i++){
        result *= x;
      }
      return result;
    }

    pow(2, 3);
    ```

    ### 再帰の利点
    - 理解や保守をしやすくする

## Q150. シンプルでないオブジェクトに対して、再帰を使って柔軟にアクセスする手法を知っていますか?

??? success
    ###　オブジェクトの例
    - 将来的に部署の構造が変わる可能性を考える必要がある

    ??? example

        ```js
        let company = {
          sales: [{
            name: 'John',
            salary: 1000
          }, {
            name: 'Alice',
            salary: 600
          }],

          development: {
            sites: [{
              name: 'Peter',
              salary: 2000
            }, {
              name: 'Alex',
              salary: 1800
            }],

            internals: {
              america: [{
                name: 'Jack',
                salary: 1300
              },{
                name : 'Bob',
                salary : 1500
              }],
              japan : [{
                name : 'Yamada',
                salary: 780
              }]
            }
          }
        };
        ```
    
    ### forループが不適切な例
    - 所によって、再帰でいう深さが異なる
    - また、将来的に現在、葉であるオブジェクトに子ができるかもしれない
    - 全てを考えてループを書くと、コードは醜くなる。

    ### 再帰の例

    ```js
    let company = {
      sales: [{name: 'John',salary: 1000}, {name: 'Alice',salary:600}],
      development: {sites: [{name: 'Peter',salary: 2000}, {name: 'Alex',salary: 1800}],internals: {america: [{name: 'Jack',salary: 1300},{name : 'Bob',salary : 1500}],japan : [{name : 'Yamada',salary: 780}]}}
    };

    const sumSalaries = (department) =>{
      //配列は、再帰ツリーの葉であるため、即座に結果を返す
      if (Array.isArray(department)){
        return department.reduce((prev, current)=>{
          return prev + current.salary
        }, 0)
      }

      //objectの場合、サブコール(再帰呼び出し)を作る。
      let sum = 0;
      for(let subdep of Object.values(department)){
        sum += sumSalaries(subdep);
      }

      return sum;
    }

    console.log(sumSalaries(company)); //8980
    ```

## Q151. HTMLドキュメントに対して再帰が使えることを理解していますか?

??? success
    ### HTMLタグは再帰的
    - タグの中にタグがある場合、再帰が使える

## Q152. 連結リストと呼ばれる再帰構造を理解していますか?

??? success
    ### 連結リストの使い道
    - 要素の削除と、挿入を考える
    - 例えば`unshift`の場合、要素番号を1つずつずらす必要があり、`O(n)`の時間を要する
    - たとえば配列の要素が`1e9`あるとき、これは大きなボトルネックになる
  
    ### 連結リスト(linked list)
    - 早い挿入/削除を可能とする
    - データの探索には時間がかかる

    ### 連結リストの定義
    - `value`: 値
    - `next`: 次の連結リスト要素。末尾の場合は、nullを参照

    ### 手作業で連結リストを作る

    ```js
    const list = {
      value : 1,
      next : {
        value : 2,
        next : {
          value : 3,
          next : {
            value : 4,
            nesxt : null,
          },
        },
      },
    };


    ```

    ```mermaid
    graph LR;
    A[1] -->|next| B[2]
    B --> |next| C[3]
    C --> |next| D[4]
    D --> |next| E[null]
    ```

    ### 連結リストを分割する

    ```js
    let list = { value: 1 };
    list.next = { value: 2 };
    list.next.next = { value: 3 };
    list.next.next.next = { value: 4 };
    list.next.next.next.next = null;

    let secondList = list.next.next;
    list.next.next = null;
    ```

    ```mermaid
    graph LR;
    A[1] -->|next| B[2]
    B --> |next| C[null]
    D[3] --> |next| E[4]
    E --> |next| F[null]
    ```

    - 戻す

    ```js
    list.next.next = secondList;
    ```

    ```mermaid
    graph LR;
    A[1] -->|next| B[2]
    B --> |next| C[3]
    C --> |next| D[4]
    D --> |next| E[null]
    ```

    ### 先頭に新しい値を追加する

    ```js
    let list = { value: 1 };
    list.next = { value: 2 };
    list.next.next = { value: 3 };
    list.next.next.next = { value: 4 };
    list.next.next.next.next = null;

    list = {value : "new-item", list};
    ```

    ```mermaid
    graph LR;
    F[new item] --> A[1]
    A[1] -->|next| B[2]
    B --> |next| C[3]
    C --> |next| D[4]
    D --> |next| E[null]
    ```

    ### 途中の値を取り除く

    ```js
    let list = { value: 1 };
    list.next = { value: 2 };
    list.next.next = { value: 3 };
    list.next.next.next = { value: 4 };
    list.next.next.next.next = null;

    //次を変更することで、3が消える
    list.next.next = list.next.next.next;
    ```

    ```mermaid
    graph LR;
    A[1] -->|next| B[2]
    B --> |next| D[4]
    D --> |next| E[null]
    ```

    ### 連結リストのメリット
    - 要素を簡単に組み替え可能

    ### 連結リストのデメリット
    - 要素のアクセスが困難
    - 配列と異なり、N番目の要素ならN回nextを行う必要あり

    ### 末尾にアクセスするには?
    - リストの最後の要素を参照する`tail`等を追加
    - 前の要素を参照するために、`prev`プロパティを付け加える

## Q153. フィボナッチ数列のように、同じ値で関数を何度も呼び出すとき、単純な再帰が不適切であることを理解していますか?

??? success
    ### 単純な再帰
    - 計算量は ${O(2^n)}$ で、指数的に増えていく

    ```js
    const fibonacci = (n) =>{
      if(n == 1 || n == 2) return 1;
      return fibonacci(n-1) + fibonacci(n-2);
    }

    //50以上にすると、おそらく10分以上ブラウザの動作が停止する
    const start = Date.now();
    console.log(fibonacci(42)); //267914296
    console.log(Date.now()- start); //1833
    ```
    ### メモ化再帰
    - 登場した値をメモする

    ```js
    const memo = new Map();

    const fibonacci = (n) =>{
      let result;
      if (memo.has(n))
        return memo.get(n);
      if (n <= 1)
        result = n
      else
        result = fibonacci(n-1) + fibonacci(n-2);
      memo.set(n, result);

      return result;
    }

    const start = Date.now()
    fibonacci(100); //354224848179262000000
    console.log(Date.now()- start); //0
    ```

    ### 動的計画法
    - 小さい値から考えていく

    ```js
    const fibonacci = (n) =>{
      let a = 1;
      let b = 1;
      for(let i = 3; i <= n ; i++){
        let c = a + b;
        a = b;
        b = c;
      }

      return b;
    }

    fibonacci(100); //354224848179262000000
    ```

## Q154 連結リストの結果を再帰的に出力する方法が分かりますか?

??? success
    ### 答え

    ```js
    let list = { value: 1 };
    list.next = { value: 2 };
    list.next.next = { value: 3 };
    list.next.next.next = { value: 4 };
    list.next.next.next.next = null;

    const printList = (list) =>{
      console.log(list.value);
      if(list.next){
        printList(list.next);
      }
    }

    printList(list);
    ```

    ### 逆順

    ```js
    let list = { value: 1 };
    list.next = { value: 2 };
    list.next.next = { value: 3 };
    list.next.next.next = { value: 4 };
    list.next.next.next.next = null;

    const printReversedList = list =>{
      if(list.next){
        printReversedList(list.next);
      }
      console.log(list.value);
    }

    printReversedList(list);
    ```

## Q155. 可変長引数をサポートしたいときに使える、残余引数(Rest Parameters)について知っていますか?

??? success
    ### JSにおいて、関数は任意の数の引数で呼べる
    - 使わなかった引数は無視される

    ```js
    const sum = (a, b) =>{
      return a + b;
    }

    sum(1,2,3,4,5,6); //3
    ```

    ### 残余引数(...)
    - 残りのパラメータは配列にまとめられる。

    ```js
    const sumAll = (a, b, ...args) =>{
      let sum = a + b;
      console.log(args); // [3, 4, 5, 6]
      for (let arg of args) sum += arg;
      return sum;
    }

    sumAll(1,2,3,4,5,6);  //21
    ```

    !!! warning
        - 残余引数は最後に書く必要がある

        ```js
        //Rest parameter must be last formal parameter
        const sumAll = (a, ...b, c) =>{

        }
        ```

## Q156. 残余引数が存在する前に用いられていた、argumentsについて知っていますか?

??? success
    ### arguments
    - 全ての引数を含む、`array-like`

    ### 使用例

    ```js
    function test(a, b, c){
      console.log(arguments.length); //3
      console.log(arguments[0]); //1
      console.log(arguments[1]); //2
    };

    test(1,2,3);
    ```

    ### 注意点
    !!! warning
        - アロー関数では動作しない(外部関数がある場合は、その`arguments`を利用する)
        - 配列メソッドを利用できない(`array-like`)なので

## Q157 スプレッド演算子について知っていますか?

??? success
    ### スプレッド演算子(...)
    - 残余引数(複数のパラメータ -> 配列)のちょうど逆
    - `...arr`の場合、`iterable`なオブジェクト`arr`を複数のパラメータに展開する

    ### 使用例

    ```js
    const arr = [3,5,1];
    //配列ではなく、3,5,1を渡していることになる
    console.log(Math.max(...arr)); 
    ```

    ### 通常の値と組み合わせることも可能

    ```js
    const arr1 = [1,-2,3,4];
    const arr2 = [8,3,-8,1];

    Math.max(...arr1, 1, 2, ...arr2, 35); //35
    ```

## Q158 スプレッド演算子を使って、配列のマージを行うことができますか?

??? success
    ### 使用例

    ```js
    const arr1 = [1,-2,3,4];
    const arr2 = [8,3,-8,1];

    //展開した値を、listで囲っている
    const arr3 = [...arr1, ...arr2];  

    console.log(arr3);
    //[1, -2, 3, 4, 8, 3, -8, 1]  
    ```

## Q159. スプレッド演算子とArray.fromの違いを理解していますか?

??? success
    ### iterableならスプレッド演算子が使える

    ```js
    const str = "Hello";
    console.log([...str]); //['H', 'e', 'l', 'l', 'o']
    ```

    ### array-likeを配列に変換するときは、Array.from
    - `Array.from`は`array-like`でも動作する
    - スプレッド演算子は`iterable`のみ

    ```js
    const obj = {
      0 : "name",
      1 : "age",
      length : 2
    };

    console.log(Array.from(obj));
    // ['name', 'age']

    console.log([...obj]); //Uncaught TypeError: obj is not iterable
    ```

    ### iterableはどちらも可能

    ```js
    const obj = {
      from : 1,
      to : 5,
      [Symbol.iterator](){
        this.current = this.from;
        return this;
      },
      next(){
        if(this.current <= this.to){
          return {done: false, value : this.current++};
        }
        return {done: true};
      }
    };

    console.log(Array.from(obj)); //[1, 2, 3, 4, 5]
    console.log([...obj]); //[1,2,3,4,5]
    ```

## Q160. スプレッド構文を使って、配列/オブジェクトを複製できることを知っていますか?

??? success
    ### 配列の複製

    ```js
    const arr = [1,2,3];
    cosnt arrCopy = [...arr];

    arr.push(4);
    console.log(arr); //[1,2,3,4]
    console.log(arrCopy); //[1,2,3]
    ```

    ###　オブジェクトの複製

    ```js
    const obj = {a:1, b:2, c:3};
    const objCopy = {...obj};

    obj["d"] = 4;
    console.log(objCopy); //{a: 1, b: 2, c: 3}
    ```

    ### Object.assign([], arr)等との差異
    - `setter`(後述)

    ### 深い複製はこちら(復習)

    ```js
    const arr = [
      [1,2,3],
      [4,5,6],
    ]

    const deepCopy = structuredClone(arr);
    deepCopy[0][1] = 2;
    console.log(arr); //[[1,2,3],[4,5,6]]
    
    /*
    const arrCopy = [...arr];
    arrCopy[0][1] = 3;
    console.log(arr); //[[1,3,3],[4,5,6]]
    */
    ```

## Q161. ネストされた関数が作成可能であることを知っていますか?

??? success
    ### ブロックスコープについて(復習)
    - `let`や`const`がコードブロック`{}`の中で宣言された場合、その変数を使えるのはブロックの中だけ

    ### ブロックスコープを用いて処理を隠す
    - 異なるスコープなので、再宣言ではない

    ```js
    {
      let message = "Hello";
      console.log(message); //Hello
    }

    {
      let message = "Goodbye";
      console.log(message); //Goodbye
    }
    ```

    ### ifやforやwhile内の変数も同じ

    ```js
    if(true){
      let hoge = "neko";
    }

    for(let i=0; i<10; i++){console.log(i)};

    let i = 1;
    while(i < 10){
      let j = 10;
      console.log(i + j);
      i++;
    }
    ```

    ### ネストされた関数
    - `fullName`を返す

    ```js
    function main(firstName, lastName){
      function getFullName(){
        return firstName + " " + lastName;
      }

      console.log("Hello, " + getFullName());
      //Hello, John Smith
      console.log("Bye-bye, " + getFullName());
      //Bye-bye, John Smith
    }

    main("John", "Smith");
    ```

## Q162 レキシカル環境について知っていますか?

??? success
    ### レキシカル環境
    - JSのグローバルスコープ、ブロックスコープ、関数は`Lexical Environment`(レキシカル環境)と呼ばれるオブジェクトを保持している

    ### レキシカル環境オブジェクトの構成
    1. 環境レコード：プロパティとしてすべてのローカル変数を持つ、それから`this`の値を持つ
    2. 外部のレキシカル環境への参照。通常、現在の波括弧のすぐ外側に関連付けられている

    ### 変数について
    - __環境レコードのプロパティ__

    ### 変数を取得するとは?
    - __環境レコードオブジェクトのプロパティを取得または変更__ すること

    ### レキシカル環境の例

    ```js
    let phrase = "Hello";
    ```

    ```
    Lexical Environment
        環境レコード
            phrase : "Hello"
        外部参照
            null
    ```

    ### レキシカル環境の例(コードが実行されていった場合)

    ??? example

        ```js
        /*
        Lexical Environment
            環境レコード
                phrase: <uninitialized>
            外部参照
                null
        */

        let phrase;

        /*
        Lexical Environment
            環境レコード
                phrase: undefined
            外部参照
                null
        */
        phrase = "Hello";
        /*
        Lexical Environment
            環境レコード
                phrase: "Hello"
            外部参照
                null
        */
        phrase = "Bye";

        /*
        Lexical Environment
            環境レコード
                phrase: "Bye"
            外部参照
                null
        */
        ```
    
    ### 説明
    1. スクリプトが開始されると、レキシカル環境には、 __宣言されたすべての変数があらかじめ用意__ されている
    2. 最初は未初期化`<uninitialized>`の状態で、JSエンジンは存在を知っているが、`let`で宣言されるまでは参照不可
    3. `let phrase`定義
    4. 定義されると、変数を使用可能になる
    5. `phrase`に値が割り当てられる
    6. `phrase`の値を変更する

    !!! warning
        - レキシカル環境とは、動作の仕組みを説明するために理論的に存在しているもの
        - そのため、環境レコードオブジェクトをコード上で取得したり、変更することはできない
  
    ### レキシカル環境の例(関数宣言)
    - レキシカル環境が作られた時、関数宣言があれば、`let`とは異なり、すぐに使用できる

    ```js
    /*
    Lexical Environment
        環境レコード
            phrase: <uninitialized>
            say: function
        外部参照
            null
    */
    let phrase = "Hello";


    function test(name){
      console.log(name);
    }
    ```

    !!! warning
        - `let say = function(name)`のような関数式は当てはまらない。

    ### レキシカル環境の例(関数実行時)
    1. 呼び出しの先頭で新しいレキシカル環境が作られる
    2. レキシカル環境には、ローカル変数と呼び出しパラメータが格納される
    3. 関数呼び出し中は、2つのレキシカル環境があり、関数呼び出し用とグローバルがある。
    4. 内部のレキシカル環境は、外部のものに対する外部参照を持っている

    ```js
    let phrase = "Hello";

    function say(name){
      /*
      Lexical Environment
          環境スコープ
              name : "Hoge"
          外部参照
              --->  
                  Lexical Environment
                      say : function
                      phrase : "Hello"
                  外部参照
                      null
      */
      console.log(name);
    }

    say("Hoge");
    ```

    ```mermaid
    graph LR;
    A[name : Hoge] --> |外部参照| B[say: function \n  phrase : Hello] 
    B --> |外部参照| C[null]
    ```

    ### コードが変数にアクセスするときの挙動
    5. 内部のレキシカル環境を探す
    6. 無ければ外側を探す
    7. グローバルに至るまで繰り返す

    !!! warning
        - `strict`モードの場合、変数がどこにもなければエラー
        - `strict`モードでないなら、新しいグローバル変数を作成する

    !!! info
        - 上記コードの場合、`name`にアクセスする場合、関数のレキシカル環境の中から見つける
        - `phrase`にアクセスしたい場合、関数内のレキシカル環境を探し、無ければ外部参照を行い、グローバルでそれを見つける。

    ### レキシカル環境(returnで関数を返す場合)

    ```js
    function makeCounter(){
      let count = 0;

      return function(){
        return count++;
      };
    }

    let counter = makeCounter();
    //function(){return count++}
    counter()
    ```

    ### 説明

    - makeCouter()が呼び出される
    
    ```mermaid
    graph LR;
    A[count : 0] --> |外部参照| B[makeCounter: function \n counter : undefined]
    B --> |外部参照| C[null]
    ```

    - 呼び出しと同じタイミングで、入れ子の関数が作られる
    !!! info
        - 全ての関数は、作成元のレキシカル環境を記憶している
        - (どのレキシカル環境から生成されたかを記憶する)
        - その情報は、`[[Environment]]`という隠しプロパティに格納されている

    ```mermaid
    graph LR;
    D["counter.[[Environment]]"] --> A[count : 0]
    A[count : 0] --> |外部参照| B[makeCounter: function \n counter : undefined]
    B --> |外部参照| C[null]
    ```

    - 上記から分かる通り、`counter.[[Environment]]`には`{count : 0}`というレキシカル環境への参照がある
    - この参照は、関数生成時に一度だけ設定され、永遠に存在し続ける
    - `counter()`が呼ばれ、呼び出しに対するレキシカル環境が作成される。

    ```mermaid
    graph LR;
    A["empty"] -->|outer| B["count : 0"]
    B --> |outer| C["makeCounter: function \ncounter : function"]
    C --> |outer| D[null]
    ```

    - `count`を探す場合、最初に自身のレキシカル環境を検索する。
    - ローカル変数はないので、関数生成時に設定しておいた`[[Environment]]`をもとに外部参照を探しに行く
    - `count`変数を見つけ更新する

    !!! info
        - __変数は、その変数が存在するレキシカル環境で更新される__

    - つまり、一度`counter()`を実行すると以下の様になる

    ```mermaid
    graph LR;
    A["empty"] -->|outer| B["count : 1"]
    B --> |outer| C["makeCounter: function \ncounter : function"]
    C --> |outer| D[null]
    ```


## Q163. クロージャについて理解していますか?

??? success
    ### クロージャ
    - 外部変数を記憶し、アクセスできる関数
    - JSにおいては、すべての関数はクロージャ(例外あり:new Function)

    ### まとめ
    - JSの関数は`[[Environment]]`プロパティを使って、生成元のレキシカル環境を記憶している
    - そのため内部に変数がない場合、`[[Environment]]`を使って外部変数にアクセスできる

## Q165. レキシカル環境がメモリから削除するタイミングを理解していますか?

??? success
    ### 到達可能な間だけ
    - 通常は、関数呼び出しが終わると、メモリから削除される
    - 呼び出し終了後も、依然として到達可能である場合、存在し続ける

    ```js
    function f(){
      let value = 123;

      return function(){
        console.log(value++);
      }
    }

    //g.[[Environment]]は、f()呼び出し時のレキシカル環境{value : 123}への参照を保持し続ける
    let g = f();

    //呼び出し時のレキシカル環境に到達不能になるので、この時点でメモリからクリアされる。
    g = null;

    //何度も呼ばれる場合
    //対応するレキシカル環境オブジェクトはそれぞれ残る
    const arr = [f(), f(), f()];
    arr[0](); //123
    arr[1](); //123
    arr[0](); //124
    ```

## Q166 ブレイクポイントで停止中、console.log()をコンソールに打ち込むと、ローカル変数が取得できることを知っていますか?

??? success
    ### 使用例

    ```js
    const exampleFunc = () =>{
      const variable = "I'm in the function";
      debugger;
    }

    const varibale = "I'm global";
    ```

    - 停止した状態で、コンソールに以下を記載

    ```js
    console.log(variable); //I'm in the function
    ```

## Q167 外部変数にクロージャを用いてアクセスする際の注意点（デバッグ時）について知っていますか?

??? success
    ### 具体例

    ```js
    function f() {
      let value = 123;

      function g() {
        debugger;
      }

      return g;
    }

    let g = f();
    g();
    ```

    ### 概要
    - 上記において、コンソールに`console.log(value)`とした場合、`123`が想定される。
    - しかし実際には、`value is not defined`という答えが返ってくる
    - これはJSエンジンによる最適化が原因である

    ### 最適化?
    - Firefoxの場合、開発者ツールを見ると、以下の様になっていることが分かる

    ```
    value : optimized away
    ```

    - つまり、このような変数の値をデバッグ中に検査することができないのは、JSエンジンの最適化のためである
    - 実際、本来`value`は取得できるはずなので、`debugger`の上の行に`console.log()`を書くと正常に出力される

    ### ひとまずの対策?
    - `debugger`ステートメントの位置より前で`value`が使われていない場合、エンジンはその変数を最適化の対象としてみなす。
    - 最適化の対象とされると、実際のコードから削除されるので、デバッガで`value`を見れなくなる
    - よって、こうする

    ```js
    function f() {
      let value = Math.random();

      function g() {
        console.log(value);
        debugger; 
      }

      return g;
    }

    let g = f();
    g();
    ```

    - この状態でコンソールに打ち込む

    ```js
    alret(value); //何らかの小数点が正常に出力される
    ```

    ### デバッグ時にコードを最適化せずに実行するオプションについて
    - 調査中

## Q168. クロージャの性質を生かして、簡単な関数フィルタを作ることができますか？

??? success
    ### 例

    ```js
    const arr = [1,2,3,4,5,6,7];

    const inBetween = (a,b) =>{
      //呼び出し時のレキシカル環境は、a,bの情報を保持している
      return (x) =>{
        return a <= x && x <= b;
      }
    }

    const inArray = arr =>{
      return (x) =>{
        return arr.includes(x);
      }
    }
    console.log(arr.filter(inBetween(3,6))); //3,4,5,6
    console.log(arr.filter(inArray([1,2,10]))); //1,2
    /*
    inBetween(3,6);
    //a,bの情報は固定され、arrの各要素がxに入る
    (x) =>{
        return a <= x && x <= b;
      }
    */

    ```

## Q169. クロージャを使って、簡易的なソート関数を作ることができますか?

??? success
    ### 使用例

    ```js
    let users = [
      { name: "John", age: 20, surname: "Johnson" },
      { name: "Pete", age: 18, surname: "Peterson" },
      { name: "Ann", age: 19, surname: "Hathaway" }
    ];

    const byField = (field) =>{
      return (a, b) => a[field] > b[field] ? 1 : -1
    }

    //sortが2つの値を使うことを考え、fieldを参照できるようにする
    users.sort(byField("name"));
    const sortByAge = byField("age");
    users.sort(sortByAge);

    console.log(users); // Pete, Ann, John
    ```

## Q170. sum(1)(2)の様に動作する関数を書けますか?(クロージャ)

??? success
    ### 答え

    ```js
    const sum = (a) =>{
      return (b) =>{
        return a + b;
      }
    }

    console.log(sum(1)(2)); //3
    //必ず、5と何かを足したい場合
    const five = sum(5);
    console.log(five(2)); //7
    console.log(five(6)); //11
    ```

## Q171. 以下の変数が参照できるか分かりますか?

??? tips

    ```js
    let x = 1;

    function func() {
      console.log(x); // ?

      let x = 2;
    }

    func();    
    ```

??? success
    ### できない
    - 内側の関数のレキシカルスコープ

    ```mermaid
    graph LR;
    A["x: uninitialized"] --> B["x"]
    ```

    - 変数`x`はV8から見ると、初期化されていない状態で存在している。
    - 初期化されていないため使えないが、存在するため、外部のレキシカル環境を参照することはなく、グローバル変数を利用することはできない。
    - このような、変数が一時的に利用できないゾーンは __デッドゾーン__と呼ばれる。

## Q172. varがコードブロックを貫通する理由を知っていますか?

??? success
    ### 答え
    - JSでは長い間、コードブロックが独自のレキシカル環境を持っていなかったため

    ### 復習(巻き上げ:宣言)
    - `var`にデッドゾーンはない

    ```js
    function sayHi(){
      phrase = "Hello";
      alert(phrase);

      //巻き上げられるので、この条件分岐は意味を持たない
      if(false){
        var phrase;
      }
    } 
    ```

    ### 復習(代入は巻き上げられない)

    ```js
    function sayHi(){
      alert(phrase); //undefined

      var phrase = "Hello";
    }
    ```

## Q173. 古いスクリプトで用いられていたIIFE(即時実行関数)について知っていますか?

??? success
    ### IIFE(immediately-invoked function expressions)
    - 作成された後にすぐ呼ばれる
    - 従ってコードはすぐに実行され、自身のプライベート変数を持つ

    ```js
    (function(){
      let message = "Hello";
      alert(message);
    })();
    ```

    ### なぜ括弧で囲むのか
    - JSは`function`を見つけると関数宣言の開始と判断する(式の最中に登場する場合は別)
    - しかし、関数宣言は名前が必須なのでエラーとなる
    - これを関数式とみなすには、()で関数全体を囲む必要がある

    ### 関数宣言をすぐに呼び出してはいけないのか?
    - 関数宣言は、即時呼び出しできない

    ```js
    function go(){

    }(); //Error
    ```

    ### 関数式を意味したい場合、()以外も使える
    - 関数 __式__ であることをJSエンジンに理解してもらいたいので、`function`より前に、`!`や`+`を置き、以降を式として解釈させる
    - 通常の関数式も、式として解釈されている

    ```js
    //IIFE
    (function(){
      alert("関数全体を括弧で囲む");
    })();

    (function(){
      alert("呼び出しも含めて括弧で囲む");
    }());

    !function(){
      alert("NOT演算子で式を開始することもできる");
    }();

    +function(){
      alert("単項プラスで式を開始することもできる");
    }();

    -function(){
      alert("単項マイナスも可能");
    }

    ~function(){
      alert("これも可能");
    }
    ```

## Q174. グローバルオブジェクトの標準的な名前として、globalThisが利用できることを知っていますか?

??? success
    ### グローバルオブジェクト
    - どこでも利用可能な変数と関数を提供する

    ### window
    - 環境がブラウザの場合の、グローバルオブジェクト

    ### global
    - 環境がNode.jsの場合の、グローバルオブジェクト

    ### globalThis
    - 全ての環境でサポートされているグローバルオブジェクトの名前
    - 他の環境でコードを実行させる可能性があるならこれを使うべき。

    ### 使用例

    ```js
    //ブラウザの場合
    console.log(window === globalThis); //true

    window.alert("Neko");
    globalThis.alert("Neko");
    alert("Neko");
    ```

    ### グローバルオブジェクトの省略
    - グローバルオブジェクトのすべてのプロパティへは直接アクセス可能

## Q175. varで宣言されたグローバル関数や変数が、グローバルオブジェクトのプロパティになることを知っていますか?

??? success
    ### 使用例

    ```js
    var globalVar = 5;
    let hoge = 4;
    const fuga = 3;

    var sayHi = function(){alert("Hi")};

    console.log(globalThis.globalVar); //5
    // console.log(globalThis.hoge); //Error
    // console.log(globalThis.fuga); //Error
    console.log(window.sayHi()); //Hi

    ```

    ### これは意図せず上書きされることを意味する
    - var

    ```js
    window.hoge = 10;
    var hoge = 20;
    console.log(window.hoge); //20
    ```

    - let

    ```js
    window.hoge = 10;
    let hoge = 20;
    console.log(window.hoge); //10
    ```
    ### 関数宣言も実は可能

    ```js
    function sayHi(){
      alert("Hi");
    }

    window.sayHi(); //Hi
    ```

    ### 注意点
    !!! warning
        - 上記の機能は互換性のために存在している
        - 使ってはいけない
        - __モジュールを使用している場合、`var`で宣言されたグローバル変数はグローバルオブジェクトのプロパティにはならない__

    ### プロパティに設定する === varで宣言

    ```js
    window.currentUser = {
      name : "John",
      age : 30,
      isAdmin: false,
    }

    alert(window.currentUser.name)
    ```

    !!! warning
        - これも、推奨されない
        - グローバル変数の使用は減らすべき

## Q176. グローバルオブジェクトがpolyfillのために使えることを知っていますか?

??? success
    ### 組み込みのオブジェクトが存在するかテストが可能

    ```js
    if(!window.Promise){
      //存在しない場合は記述する
      //window.Promise = ...
    }
    ```

## Q177. 関数オブジェクトがnameプロパティを持っていることを知っていますか?

??? success
    ### 関数はオブジェクト
    - よって、プロパティの追加/削除も可能

    ### nameプロパティ
    -  関数名を取得

    ```js
    function sayHi(){
      alert("hi");
    }

    console.log(sayHi.name); //sayHi
    ```

    - 関数式で合っても可能

    ```js
    const sayHi = function(){
      alert("Hi");
    }

    console.log(sayHi.name); //sayHi
    ```

    - デフォルト値を通じた代入の場合も動作する

    ```js
    function f(sayHi = function(){}){
      alert(sayHi.name);
    }

    f(function(a,b){return a+ b}); //
    f(); //sayHi
    ```

    ### contextual name(文脈上の名前)
    - 関数がそれを提供しない場合、つまり、代入の場合では、文脈から名前を見つけ出す

    ### オブジェクトメソッドでも可能

    ```js
    const user = {
      sayHi(){};
      sayBye : function(){},
    }

    console.log(user.sayHi.name); //sayHi
    console.log(user.sayBye.name); //sayBye
    ```

    ### 正しい名前を把握するすべがない時
    - 空になる

    ```js
    const arr = [function(){}, ()=>{}];
    console.log(arr[0].name); //
    console.log(arr[1].name); //
    ```

## Q178. 関数パラメータ(仮引数)の数を返す、lengthプロパティについて知っていますか?

??? success
    ### 概要
    - 残余引数はカウントされない

    ```js
    function f1(a){};
    function f2(a,b){}
    function many(a,b, ...rest){};
    const arrow = (a,b) => {};

    console.log(f1.length); //1
    console.log(f2.length); //2
    console.log(many.length); //2
    console.log(arrow.length); //2
    ```

    ### 使用例
    - 汎用的なハンドラを提供する
    - 引数が0の場合、もしconfirmでOKを押したなら、メッセージを表示
    - 引数が0でない場合、confirmの結果を表示

    ```js
    const ask = (question, ...handlers) =>{
      const isYes = confirm(question);

      for(let handler of handlers){
        if(handler.length == 0){
          if (isYes) handler();
        }
        else{
          handler(isYes);
        }
      }
    }

    ask(
      "Question?", 
      ()=> alert("You said yes"), 
      result => alert(result)
    )
    //そもそもなぜ関数定義の後に()を付けると内部のコードが実行されるのかについては後述
    //arrowの即時実行: (()=> alert("You said yes"))()
    ```

## Q179. 関数に独自のプロパティを追加できることを知っていますか?

??? success
    ### カスタムプロパティ

    ```js
    const sayHi = () =>{
      console.log("Hi");
      sayHi.counter++;
    }

    sayHi.counter = 0;
    sayHi();
    sayHi();
    console.log(sayHi.counter); //2
    ```

    !!! warning
        ### プロパティは変数ではない

        ```js
        const sayHi = () =>{
          console.log("Hi");
          let counter = 0; //これはsayHi.counterを意味しない
          sayHi.counter++;
        }

        sayHi.counter = 0;
        sayHi();
        sayHi();
        console.log(sayHi.counter); //2
        ```

    ### クロージャとの違い
    - 外部からもアクセスできてしまう

    ### クロージャの例

    ```js
    function makeCounter(){
      let count = 0;
      return function counter(){
        return count++;
      }
    }

    let counter = makeCounter();
    console.log(counter()); //0
    console.log(counter()); //1
    ```

    ### カスタムプロパティの例

    ```js
    function makeCounter(){
      function counter(){
        return counter.count++;
      }

      counter.count = 0;
      return counter;
    }

    let counter = makeCounter();
    console.log(counter()); //0
    console.log(counter()); //1
    counter.count = 10;
    console.log(counter()); //10
    ```

## Q180. 名前付き関数式とその利点について知っていますか?

??? success
    ### 一般的な関数式

    ```js
    const sayHi = function(name){
      alert(`Hello, ${name}`);
    };
    ```

    ### 名前付き関数式(Named Function Expression: NFE)

    ```js
    const sayHi = function func(name){
      alert(`Hello, ${name}`);
    }
    ```

    ### 関数宣言ではない
    - 代入式の一部として作成されているから

    ### NFEの利点
    1. 関数の内側から関数を参照可能
    2. 関数の外側からは見えない

    ### 1は関数式でもできそうだけど......
    - 自身を呼び出してはいけないのか？

    ```js
    let sayHi = function(name){
      if(name){
        alert(`Hello, ${name}`):
      }
      else{
        sayHi("Guest");
      }
    }
    ```

    ### 問題点
    - `sayHi`の値は変わる可能性がある
    - 実行時に、別の変数になっており、エラーを吐く可能性

    ```js
    let sayHi = function(name){
      if(name){
        alert(`Hello, ${name}`);
      }
      else{
        sayHi("Guest");
      }
    }

    let welcome = sayHi;
    sayHi = null;
    welcome(); //sayHi("Guest")はsayHiがnullなのでError    
    ```

    ### 原因
    - 関数内部のレキシカル環境を考えたとき、通常の関数式の場合、`sayHi`は存在しない。
    - そのため、外部参照から、`sayHi`を取得する
    - しかし、`sayHi`は`null`である
    - そのため、`null("Guest")`となり、失敗する。

    ```mermaid
    graph LR;
    A["name"] --> |外部参照| B[sayHi]
    ```

    ### NFEを使う

    ```js
    let sayHi = function func(name){
      if(name){
        alert(`Hello, ${name}`);
      }
      else{
        func("Guest");
      }
    };

    let welcome = sayHi;
    sayHi = null;
    welcome(); //Hello, Guest
    ```

    ### 関数の外側からは見えないという利点について
    - 関数内のレキシカル環境における環境レコードオブジェクトのプロパティとしてNFEが作られるため

    ```js
    const sayHi = function func(name){
      if(name){
        alert(`Hello, ${name}`);
      }
      else{
        func("Guest");
      }
    }

    sayHi(); //Hello, Guest
    //func(); //Error
    ```

    !!! warning
        ### 名前付き関数宣言はない
        - 関数宣言に対して、もう1つの内部の名前を追加することはできない

## Q181. 実行コンテキストとレキシカル環境の差異について理解していますか?(復習)

??? success
    ### 実行コンテキスト
    - JSが実行される環境を意味する(グローバルor関数)
    - 実行上の入れ子構造をコールスタックとして保持している
    - 変数、関数、引数の値などの情報が含まれる
    - 実行における状態の管理

    ### レキシカル環境
    - 実行中のコードから見える範囲を指す
    - 環境レコード(Environment Record)と外部参照(outer)から構成される。
    - スコープ内の変数や関数へのアクセスを管理するためのデータ構造
    - 各実行コンテキストは、スコープに関連付けられたレキシカル環境を持っている
    - 内部のレキシカル環境は、外部のレキシカル環境を参照する

## Q182. Jqueryやlodash等のライブラリがメインの関数を1つ作成し、そこに多数のヘルパー関数を付与していることを知っていますか?

??? success
    ### jquery
    - メインの関数は`$`

    ### lodash
    - メインの関数は`_`

    ### 例
    - lodashはメインの関数として`_`を作り、そこに`keyBy`などのプロパティを追加している。

    ### `$`や`_`を用いるわけ
    - グローバル空間での衝突を防ぐため
    - 大抵、1つのライブラリに対して1つのグローバル変数のみが与えられる。

## Q183. sum(1)(2)(3)(4)や、sum(1)(2)(3)のように任意の量の括弧で呼び出し、数値を返す関数を書くことができますか?

??? success
    ### 答え

    ```js
    const sum = (a) =>{
      let currentSum = a;

      //再帰ではなく、関数を返しているだけ
      //currentSumは、関数作成時に参照済み
      const f = (b) =>{
        currentSum += b;
        return f;
      }

      f.toString = function(){
        return currentSum;
      }

      return f;
    }

    console.log(sum(1)(2)(3)(3) == 9); //true
    let sum1 = sum(1)(2)(3);
    sum1 = sum1(3);
    console.log(sum1 == 9); //true
    ```

## Q184 文字列から関数を生成することができる、new Functionについて知っていますか?

??? success
    ### 注意点
    !!! warning
        - 以下のようなエラーに直面するかもしれない
  
        ```
        Uncaught EvalError: Refused to evaluate a string as JavaScript because 'unsafe-eval' is not an allowed source of script in the following Content Security Policy directive:
        ```

        ### unsafe eval
        - これはCSPに違反したコードを書いたため、JSが文字列を評価しなかったことを意味する
        - `new Function`は文字列をJSコードとして評価する機能を提供する。
        - しかし、それにはセキュリティ上のリスクが伴うため、ブラウザではしばしば、動的に生成されたコードや`eval`の仕様が禁止される。
        - ブラウザでコードを実行している場合、そのセキュリティポリシーから逃れることはできない

        ### 解決法
        - `Node.js`で試す


    ### 例

    ```js
    const sum = new Function("a", "b", "return a + b");
    console.log(sum(1,2))
    ```

    ### new Function([arg1, arg2, ... argN], functionBody)
    - 関数は文字列で作られ、実行時に渡され、動的に生成される
    - 引数が欲しい場合は、関数本体の前に書く

    ### 使いどころ
    - サーバから新しい関数を受け取り、それを実行する
    - テンプレートから動的に関数をコンパイルする

## Q185. new Functionで作られた関数の[[Environment]]がグローバルのレキシカル環境を参照することを知っていますか?

??? success
    ### 通常
    - `[[Environment]]`は作成元のレキシカル環境を参照する

    ### new Functionを使用して作られた関数の場合
    - グローバルのレキシカル環境を参照する

    ### new Functionの例

    ```js
    const value = "neko";
    const getFunc = () =>{
      let value = "test";

      let func = new Function("console.log(value)");

      return func;
    }

    getFunc()(); //neko
    ```

    ### 通常

    ```js
    const value = "neko";
    const getFunc = () =>{
      let value = "test";

      let func = function(){console.log(value)};

      return func;
    }

    getFunc()(); //test
    ```

    ### new Functionを用いる際は、外部変数を参照しない
    - `minified`(短縮化)が行われた時、通常の関数内で外部変数を参照していれば、うまく短縮される
    - しかし、`new Function`の場合、関数は文字列である
    - そのため文字列内で参照している外部変数名を正しく認識でない可能性がある
    - 従って、関数内で特定の外部変数を参照していると、参照先を得られないという不具合が起こりうる
    - よって、そもそも`[[Environment]]`が生成元を参照していたとしてもいいことはない。
    - 明示的にパラメータを渡すようにする。

    ### 結論
    - `new Function`として作成された関数に何かを渡したい場合、引数を使用するようにする。

## Q186. 指定時間経過後に、コードを1度だけ実行したい場合、setTimeoutが使えることを知っていますか?

??? success
    ### timerId = setTimeout(func|code, delay[, arg1, ...])
    - `timerId`: タイマーの識別子。ブラウザでは数値。Nodeの場合、タイマーオブジェクトを返す。
    - `func|code`: 通常は実行したい関数を指定。コードを指定した場合、ブラウザではCSPに引っかかる
    - `delay`: 実行前の遅延時間(ms)
    - `arg`: 関数の引数

    ### 実行例
    - 1秒後に`sayHi()`を呼ぶ

    ```js
    const sayHi = () =>{
      alert("Hello");
    }

    //sayHi()としないこと。関数を実行した結果が、setTimeoutに渡されることになる。
    setTimeout(sayHi, 1000);
    ```

## Q187 clearTimeoutを使うと、スケジューリングがキャンセルできることを知っていますか?

??? success
    ### clerTimeout(timerId)
    - `timerId`:　タイマー識別子
    - setTimeoutの呼び出しはタイマー識別子を返す溜め、それを利用

    ```js
    const timerId = setTimeout(()=>{alert("hoge")}, 10000);
    //実行される前にキャンセル
    clearTimeout(timerId);
    ```

## Q188. 関数を定期的に呼び出したい場合、setIntervalが使えることを知っていますか?

??? success
    ### setInterval(func|code, delay[, arg1, arg2...])
    - 引数の意味は`setTimeout`と同一

    ### 止めたい場合: clearInterval(timerId)

    ```js
    const timerId = setInterval(()=> alert("tick"), 2000);

    //5秒後にstop
    setTimeout(()=>{
      clearInterval(timerId); 
      alert("stop");
    }, 5000);
    ```

## Q189. 関数を定期的に呼び出したい場合、再帰的なsetTimeoutが使えることを知っていますか?

??? success
    ### setIntervalを使った例

    ```js
    const timerId = setInterval(()=> alert("tick"), 1000);
    ```

    ### 再帰的なsetTimeout

    ```js
    let timerId = setTimeout(function tick(){
      //この位置にtimerId = setTimeout(tick, 1000); を書けばsetIntervalと同じ

      alert("tick");
      timerId = setTimeout(tick, 1000); //実行終了次第、スケジュール
    },1000);
    ```

## Q190 定期的なスケジュールを行いたい場合、再帰的なsetTimeoutの方が、setIntervalより柔軟であると知っていますか?

??? success
    ### サーバの負荷に合わせて間隔を調整したい場合

    ```js
    let delay = 5000;

    let timerId = setTimeout(function request(){
      //request処理

      if(/*遅延が大きかったら*/){
        delay *= 2;
      }

      timerId = setTimeout(request, delay);
    }, delay);
    ```

    ### setIntervalとの違い
    - 再帰的な`setTimeout`は実行の間の遅延を保証する
    - `setInterval`は保証しない

    ### setIntervalの例
    - 1秒以上、`alert`を表示していれば、呼び出しは全く停止することがない

    ```js
    //クリックしていくと、表示が1000とかけ離れていることが確認できる。
    //つまり、setIntervalはalertを表示している間も、タイマーを進める。
    let start = Date.now();
    setInterval(()=>{
      alert(Date.now() - start);
      start = Date.now();
    },1000);
    ```

    ### 再帰的なsetTimeout

    ```js

    //1000付近であることが確認できる。
    //setTimeoutの場合、コード実行後に再帰的にタイマーを設定すれば、実行時の遅延は起きない
    let start = Date.now();
    setTimeout(function run(){
      alert(Date.now() - start);
      start = Date.now();

      setTimeout(run, 1000);
    }, 1000);
    ```

## Q191. スケジューリングされているのが小さな関数であっても、clearInterval等でクリアした方がいい理由を知っていますか？

??? success
    ### ガベージコレクション
    - 関数が`setInterval/setTimeout`に渡された時、内部参照が作られ、スケジューラがそれを保存する
    - そのためクリアしない限り、常に1つ以上の参照が存在している状態になり、関数はガベージコレクションの対象にならない

    ### setTimeoutの場合
    - スケジューラが呼び出すとメモリから解放される

    ### setInterval
    - 関数は`cancelInterval`が呼び出されるまで、メモリ上に存在し続ける

    ### レキシカル環境について
    - 関数内のレキシカル環境はそれぞれ外部参照を持っている
    - そのため関数が生きている限り、`[[Environment]]`を通じて参照している外部の変数も生き続ける。
    - 外部の変数が全て生きているため、関数自身よりはるかにメモリを食う場合がある

    ※ __[[Environment]]を通じてレキシカル環境が参照されていたとしても、実際に該当オブジェクトがどこからも参照されていない場合は(到達不可能である場合は)、ガベージコレクションの対象になる__

    ### 結論
    - 要らなくなったらすぐに、`clearTimeout/clearInterval`で解放する

## Q192. ブラウザにおいて遅延ゼロが、0ミリ秒後を意味しない事を知っていますか?

??? success
    ### 実際のところ
    - タイマーが5つ以上ネストすると、間隔は少なくとも4ミリ秒後になる。

    ### 参考(HTML 8.6 Timers)

    ```
    Timers can be nested; 
    after five such nested timers, however, 
    the interval is forced to be at least four milliseconds.
    ```

    ### 1000ミリ秒後は、1000ミリ秒後を意味しない
    - CPU負荷や他のタスクなどによる遅延が考えられるため
    - ブラウザタブがバックエンドモードになっていたり
    - PCの充電が少ない場合も遅延する

    ```
    This API does not guarantee that timers will run exactly on schedule. 
    Delays due to CPU load, other tasks, etc, are to be expected.
    ```

    ### 具体例
    - 5回目の呼び出し以降は4ミリ秒以上遅延していることが確認できる

    ```js
    let start = Date.now();
    let times = [];

    setTimeout(function run(){
      times.push(Date.now() - start);
      //100ms以上経ったら、遅延を表示
      if(start + 100 < Date.now()) alert(times);
      else setTimeout(run, 0);
    },0);

    /*実行例1*/
    //0,0,0,0,5,10,15,20,25,30,35,40,44,50,55,60,66,71,76,82,87,91,96,102

    /*実行例2*/
    //1,1,1,1,5,9,14,18,24,28,33,38,42,47,51,56,61,66,70,75,79,83,88,92,97,101
    ```

    ### 注意点
    - `Node.js`のようなサーバサイドでは話は別

## Q193. setTimeout/setIntervalがJSの仕様に含まれていないことを知っていますか?

??? success
    ### 概要
    - `setTimeout`のような非同期操作やブラウザ環境に依存する機能は`ECMAScript`仕様に含まれない

    ### 属している仕様
    - `Web API`の一部である`Timer API`の機能

## Q194. 重い処理ではあるものの、同じ引数を用いた場合、結果が不変。そんな処理を効率的に行いたい場合、デコレータが使えることを知っていますか?

??? success
    ### 結果をキャッシュして、再計算を避けるには?
    1. メインの処理を行う関数を書く
    2. キャッシュ機能を担当するラッパー(デコレータ)を作成
    3. 関数にデコレータを適用する
   
    ※ __デコレータは別の関数を取り、その振る舞いを変更する__

    ### コード例

    ```js
    function slow(x){
      //CPUを大量に消費するジョブのダミー
      let sum = 0;
      for(let i = 0; i <= 1e9; i++){};
      return x;
    }

    function cachingDecorator(func){
      let cache = new Map();

      return function(x){
        //結果がMapにあれば、再計算無しでそれを返す
        if(cache.has(x)){
          return cache.get(x);
        }
        //無ければ、計算して、結果をcacheに格納
        //funcに関しては、[[Environment]]を通じてアクセス可能
        let result = func(x);
        cache.set(x, result);  
        
        return result;
      };
    }

    slow = cachingDecorator(slow);

    let start = Date.now();
    console.log(slow(1)); //760ms
    console.log(Date.now() - start);

    start = Date.now();
    console.log(slow(1)); //0ms
    console.log(Date.now() - start);

    start = Date.now();
    console.log(slow(2)); //765ms
    console.log(Date.now() - start);
    ```

    ### 時間以外の利点
    - `cachingDecorator`は再利用可能
    - 別の処理の重い関数に適用することもできる
    - キャッシュのロジックは分離されているので`slow`関数自身の複雑性は増加しない

## Q195. オブジェクトのメソッドに対してキャッシュデコレータを設定したい場合、`.call()`が使えることを知っていますか?


??? success
    ### 動作しないコード

    ??? warning

        ```js
        let workwer = {
          someMethod(){
            return 1;
          },

          slow(x){
            alert("Called with " + x);
            return x * this.someMethod(); //ここ!
          }
        };

        function cachingDecorator(func){
          let cache = new Map();
          return function(x){
            if (cache.has(x)){
              return cache.get(x);
            }

            let result = func(x); //ここ!
            cache.set(x, result);
            return result;
          }
        }

        worker.slow = cachingDecorator(worker.slow);

        console.log(worker.slow(2)); //Error
        ```
    
    ### 理由
    - ラッパーがオリジナルの関数を`func(x)`として呼び出しているため
    - 関数を呼び出したオブジェクトが存在しないので、`this`は`undefined`となり、`return x * this.someMethod();`の箇所で、エラーとなる

    ### func.call(context, arg1, arg2, ...)
    - 明示的に設定した`this`で関数を呼び出す
    - `context`: `this`とみなす
    - `arg`: 引数
    - `func`:実行される関数

    ### 簡単な例

    ```js
    function sayHi(){
      console.log(this.name);
    }

    const user = {name : "John"};
    const admin = {name : "Admin"};

    //this = user
    sayHi.call(user); //John
    //this = admin 
    sayHi.call(admin); //Admin
    ```
    
    ### 注意点
    !!! warning
        - アロー関数では`call`は使えない

    ### 書き直した例

    ```js
    let worker = {
      someMethod(){
        return 1;
      },

      slow(x){
        alert("Called with" + x);
        return x * this.someMethod(); //ここ！
      },
    };

    function cachingDecorator(func){
      let cache = new Map();
      return function(x){
        if(cache.has(x)){
          return cache.get(x);
        }
        let result = func.call(this, x); //ここ!
        cache.set(x, result);
        return result;
      }
    }

    worker.slow = cachingDecorator(worker.slow);

    console.log(worker.slow(2));
    console.log(worker.slow(2)); //cache作動
    ```

    ### 説明
    1. デコレータ適用後の`worker.slow`はラッパーのreturn
    2. ここで`worker.slow(2)`を実行すると、関数を呼び出したオブジェクトは`worker`と認識される
    3. そのうえで、`func.call(this,x)`とすると、func、つまり、slow(x)は、`worker`を`this`に設定した上で呼び出されることになる。
    4. `worker.someMethod()`なのでうまく動作する

## Q196. 2つの引数(入力結果)を対象としたメソッドをキャッシュする方法が分かりますか?

??? success
    ### アイデア
    - 複数の値を1つに結合する。
    - たとえば数値を結合して、文字列にしたものをキーとする

    ### 使用例(2つの引数)

    ```js
    let worker = {
      slow(min, max){
        console.log(`Called with ${min}, ${max}`);
        return min + max;
      }
    };

    function cachingDecorator(func, hash){
      let cache = new Map();
      return function(){
        let key = hash(arguments); //arguments:渡された引数をすべて取得
        if(cache.has(key)){
          return cache.get(key);
        }

        let result = func.call(this, ...arguments);
        cache.set(key, result);
        return result;
      }
    }

    //argumentsが渡ってくる: Array-likeであることに注意
    function hash(args){
      return args[0] + "," + args[1]
    }

    worker.slow = cachingDecorator(worker.slow, hash);
    //worker.slow : function(){let key = hash(arguments)..
    console.log(worker.slow(3,5)); //Called with 3,5 : 8
    console.log(worker.slow(3,5)); //8
    console.log(worker.slow(3,6)); //Called with 3,6: 9

    ```

## Q197. func.call()とfunc.apply()の違いが分かりますか?

??? success
    ### func.apply(context, args)
    - `.call`は引数のリストを期待する
    - `apply`は配列ライクなオブジェクトを期待する

    ### 注意点
    - `call`はスプレッド演算子を使用して、`iterable`なargsを渡すことができる
    - `apply`は`array-like`以外許可しない


    ### 配列を渡す場合は?
    - `array-like`かつ`iterable`ならいずれかを使える

    ### 高速なのは?
    - `apply`の方が早い

    ### Q196のコードを書き換えるなら?

    - func.call

    ```js
    let result = func.call(this, ...arguments);
    ```

    - func.apply

    ```js
    let result = func.apply(this, arguments);
    ```

## Q198. 任意の数の引数を対象とした、メソッドをキャッシュする方法が分かりますか？

??? success
    ### 概要
    - Q196のハッシュ関数の改善を考える

    ```js
    function hash(args){
      return args[0] + "," + args[1]
    }
    ```

    ### 失敗例
    !!! warning
        - `arguments`は本物の配列ではないので、`join`メソッドは使えない

        ```js
        function hash(){
          return arguments.join();
        }

        hash(1,2,3);
        ```

    ### 成功例

    ```js
    function hash(){
      return [].join.call(arguments);
    }

    hash(1,2,3); //1,2,3
    ```

    ### メソッドの借用
    - 上記のトリックは、メソッドの借用と呼ばれる
    - 通常の配列から結合メソッドを借りている
    
    ### メソッドの借用が上手くいくわけ
    1. まず前提として、joinメソッドは本物の配列に対してしか使えない(後記: Array.prototype.joinであるため)
    2. そのため空配列を用意し、そのメソッドとしてjoinを書く。joinを呼び出したのは空配列というオブジェクトなので、通常、`this`はこの空配列を指す
    3. ここで、`func.call() == join.call()`を利用しているので、`join`における`this`が`arguments`とみなされる
    4. `join()`メソッドは`this`の`length`プロパティを読み込み、次にキーが`length`より小さい非負の整数である各プロパティにアクセスするという挙動を持つ。
    5. そのため、`this`である`arguments`の`length`プロパティを読み込み、各プロパティにアクセスして結合する

    ### 最終的な解法

    ```js
    let worker = {
      slow(){
        console.log("Calc");
        let sum = 0;
        for(const arg of arguments){
          sum += arg;
        }

        return sum;
      }
    };

    function cachingDecorator(func, hash){
      let cache = new Map();
      return function(){
        let key = hash(...arguments); //arguments:渡された引数をすべて取得。展開して渡す
        if(cache.has(key)){
          return cache.get(key);
        }

        let result = func.call(this, ...arguments);
        cache.set(key, result);
        return result;
      }
    }

    //argumentsが渡ってくる: Array-likeであることに注意
    function hash(args){
      return [].join.call(arguments);
    }

    worker.slow = cachingDecorator(worker.slow, hash);
    console.log(worker.slow(3,5,9,0)); //Calc 17
    console.log(worker.slow(3,5,9,0)); //17
    console.log(worker.slow(3,6)); //Calc 9
    ```

## Q199. デコレートされた関数にプロパティが含まれていた場合、それはコピーされないことを理解していますか?

??? success
    ### 使用例
    - 例えば、`Q198`で`worker.slow.name`を試してみる。
    - デコレータを通す前では、それは`slow`となっている
    - 通した後は、`""`になっていることが確認できる。

    ### 補足
    - 関数のプロパティへのアクセスを維持するデコレータを作成したい場合、`Proxy`オブジェクト(後述)を使用する必要がある

## Q200. ラッパーを用いてすべての関数呼び出しを保存することができますか?

??? success
    ### 使用例

    ```js
    let work = (a,b) =>{
      console.log(a + b);
    }

    function spy(func){
      //この内部にエラーがあっても、エラーが出るのは呼び出し時
      //return wrapperの時点では関数が返されるだけ
      function wrapper(...args){
        wrapper.calls.push(args);
        return func(...arguments); //スプレッド演算子で展開
        //return func.apply(null, arguments); //applyの場合、array-likeを展開する
        //func = (a,b) =>{console.log(a+b)}
      }

      wrapper.calls = [];
      return wrapper;
    }

    work = spy(work);
    //work.calls = [];

    work(1,2);
    work(4,5);

    for(const args of work.calls){
      console.log("calls:" + args.join())
      // calls: 1,2
      // calls: 4,5
    }
    ```

## Q201. 呼び出し時に設定分の遅延を行う、デコレータを生成できますか?

??? success
    ### 使用例

    ```js
    const f = (...args) =>{
      //Array-likeを本物の配列として使う
      alert(args.join())
    }

    function delay(f, ms){
      //引数は可変長を可能とする
      return function(){
        setTimeout(()=> f.apply(this, arguments), ms)
      }
    }

    let f1000 = delay(f, 1000);
    let f50 = delay(f, 50);
    f1000("neko", "inu", "nezumi")
    f50("lion", "tiger");
    ```

## Q202. デバウンスの意味と、それをデコレータで実践するやり方を知っていますか?

??? success
    ### デバウンス(debounce)
    - 連続して大量に繰り返される処理が、指定時間以内に複数回発生する場合、最後の1回だけ実行する

    ### 使いどころ
    - `input`に対して`onChange`を付けた場合のことを考える
    - 1文字入力するたびにイベントが発生すると、無駄なリソースを食う
    - そのためデバウンスを用い、入力が終わってからイベントを実行するようにする

    ### デバウンスの挙動
    1. デバウンスする必要があるイベントが発生するとタイマーを開始させる
    2. タイマーが期限切れになる前に新しいイベントが圧制した場合、タイマーをリセットする
    3. タイマーがカウントダウンの終わりに達した場合、デバウンス関数を実行

    ### 使用例
    - `[[Environment]]`から`isCooldown`を参照できるようにしておく。

    ```js
    const target = (...args) =>{
      console.log(args[0])
    }
    let f = debounce(target, 1000);

    function debounce(func, ms){
      let timer; //[[Environment]]から参照
      return function(...args){
        clearTimeout(timer);
        timer = setTimeout(() => {func.apply(this, args);}, ms);
      }
    }

    //1000ms以内に再度呼び出すと実行されない
    f(1);
    f(2);
    setTimeout(()=> f(3), 100); //実行される
    setTimeout(()=> f(4), 1110);
    setTimeout(()=> f(5), 1500); //実行される

    ```

## Q203. スロットリングの意味と、それをデコレータで実現する方法を知っていますか?

??? success
    ### スロットリング
    - 連続して大量に繰り返される処理を一定間隔で間引く
    - 指定された時間ごとに最大1回だけ処理を実行する
    - 具体的には最初の1回と、一定時間経過後の呼び出しの場合は実行
    - 時間が一定経過していない場合は、実行しない
    - 最後の1回は必ず実行する

    ### コード例

    ```js
    const target = (...args) =>{
      console.log(args[0]);
    }

    let f = throttle(target, 1000);

    //最初の1回
    //経過時間を上回った場合も実行
    //最後の1回も実行
    function throttle(func, delay){
      let timerId = null;
      let lastExecTime = 0;

      return function(...args){
        let elapsedTime 
            = performance.now() - lastExecTime;

        const execute = () =>{
          func.apply(this, args);
          lastExecTime = performance.now();
        }

        //もしtimerがなければ実行,あれば削除
        if(!timerId) execute();
        else clearTimeout(timerId);

        //経過時間を上回っていれば実行
        if(elapsedTime >= delay)
            execute();
        //既存のタイマーを上書きする形
        else timerId = setTimeout(execute, delay);
      }
    }

    f(0); //timerがないので実行
    f(1); //経過時間を下回っているので、タイマーセット
    f(2); //経過時間を下回っているので、前のタイマーをリセットしてからタイマーセット。
    //1000ms経った時点で、execute()
    //lastExecTimeの更新
    setTimeout(()=> f(3), 1500); //1000msから500msしかたっていないので、経過時間を下回っている。timerセット。
    setTimeout(()=> f(4), 2100); //経過時間を上回っているので実行。
    setTimeout(()=> f(5), 2700); //経過時間を下回っているのでタイマーセット。3700msの時点で実行

    //outputs:
    //0, 2, 4, 5
    ```

## Q204. ブラウザの場合、setTimeoutで関数を呼び出すと、`this`が`window`に設定されることを理解していますか?

??? success
    ### 使用例

    ```js
    const user = {
      firstName: "John",
      sayHi(){
        console.log(`Hello, ${this.firstName}`);
      }
    };

    setTimeout(user.sayHi, 1000);
    //Hello, undefined
    //window.firstNameなので
    ```

    ### 理由
    - 下記の動作を思い出す
    - `f`に代入した時点で、ただの関数として扱われている
    - メソッドとして呼び出されていないので、`this`は`window`オブジェクトである。
    - 補足として`setTimeout`は第1引数に関数を取る

    ```js
    let f = user.sayHi;
    f(); //Hello undefined
    ```

## Q205. setTimeoutに設定した関数内で、`this`を使う方法が分かりますか?

??? success
    ### 関数内でメソッドを呼ぶ(方法1)
    - 外部のレキシカル環境から`user`を受け取ってメソッドを呼び出している。

    ```js
    let user = {
      name : "John",
      sayHi(){
        console.log(`Hello, ${this.name}`);
      }
    };

    setTimeout(function(){
      user.sayHi();
    }, 1000);
    ```

    ### アロー関数ver

    ```js
    let user = {
      name : "John",
      sayHi(){
        console.log(`Hello, ${this.name}`);
      }
    };

    setTimeout(()=> user.sayHi(), 1000);
    ```

    ### 方法1の脆弱性
    - 1s経過前に、`user`が値を変更する可能性がある

    ```js
    let user = {
      name : "John",
      sayHi(){
        console.log(`Hello, ${this.name}`);
      }
    };

    setTimeout(()=> user.sayHi(), 1000);

    user = {
      sayHi(){console.log("お粗末!!!")},
    };

    //output:
    //お粗末!!!
    ```

## Q206. bindを使うと、`this`を固定できることを知っていますか?

??? success
    ### 概要
    - `bind`は`this`を固定する

    ### boundFunc = func.bind(context)
    - `func`に`this=context`を渡して、`this`の値を固定する
    - `boundFunc`: `this`が固定された`func`
    - `boundFunc()`で呼び出す際、`this`は固定時の __古いオブジェクトを参照__ する。

    ### 使用例1(1s経過前にuserが変更されても大丈夫)

    ```js
    let user = {
      name : "John",
    }

    function func(phrase){
      console.log(phrase + "," + this.name);
    }

    const funcUser = func.bind(user);

    //オブジェクト無しでもthisは固定されているので実行可能
    funcUser("Hello"); //Hello, John

    //プロパティの変更の場合、オブジェクトはそのままなので意味はない
    user.name = "Neko";
    setTimeout(funcUser, 1000, "Goodbye"); //Goodbye, Neko

    //オブジェクトを変更
    user = {
      name : "Hoge",
    }

    //オブジェクトが変更されても、古いオブジェクトを参照するので、結果は変わらない
    //古いオブジェクトは、bindによって参照されているので、ガベージコレクションの対象にはならない
    setTimeout(funcUser, 1000, "Goodbye"); //Goodbye, Neko
    ```

    ### 使用例2
    - 渡されるときに、関数になってしまうので、`bind`している

    ```js
    "use strict";
    function askPassword(ok, fail) {
      let password = prompt("Password?", '');
      if (password == "rockstar") ok();
      else fail();
    }

    let user = {
      name: 'John',

      loginOk() {
        alert(`${this.name} logged in`);
      },

      loginFail() {
        alert(`${this.name} failed to log in`);
      },

    };
    askPassword(user.loginOk.bind(user), user.loginFail.bind(user));
    ```

## Q207. bindを使うと、this以外にも引数もバインドできることを知っていますか?

??? success
    ### bound = func.bind(context [, arg1, arg2...])
    - 引数をバインドして、部分的に値を固定する

    ### 使用例

    ```js
    function exponentiation(a,b){
      return b ** a;
    }

    let squared = exponentiation.bind(null, 2);
    let cubed = exponentiation.bind(null, 3);

    console.log(squared(3)); //9
    console.log(squared(9)); //81
    console.log(squared(14)); //196
    console.log(cubed(16)); //4096
    ```

    ### 部分関数
    - 上のような例は部分関数アプリケーションと呼ばれる
    - 基礎的な関数のうち、幾つかを固定することで、新しい関数を生成する
    - `this`は使用しないので`null`を置いている

    ### 部分関数を作るわけ
    - 毎回最初の引数を設定する手間を省くため
    - 基礎的な関数から特定用途の関数を生み出すため

    ### 例2

    ```js
    function send(from, to, text){
      return `
      from: ${from}
      to:   ${to}

      text: ${text}
      `
    };

    let sendTo = send.bind(null, "Toraneko");

    console.log(sendTo("Sironeko", "Nya-nya"));
      /*
      from: Toraneko
      to:   Sironeko

      text: Nya-nya
      */
    console.log(sendTo("Kuroneko", "Meow-meow"));
      /*
      from: Toraneko
      to:   Kuroneko

      text: Meow-meow
      */
    ```

## Q208. 多くのメソッドを持っているオブジェクトを考えたとき、それらすべてをバインドすることができますか?

??? success

    ### JSライブラリを頼らない場合

    ```js
    //サンプルなのでよくないコードです
    const user = {

      name : "John",
      getName(){
        console.log(this.name);
      },

      address : "Tokyo",
      getAddress(){
        console.log(this.address);
      },

      tel : "000-0000",
      getTel(){
        console.log(this.tel);
      },
    }

    for(let key in user){
      if(typeof user[key] == "function"){
        user[key] = user[key].bind(user);
      }
    }

    let f = user.getName;
    user = null;
    f(); //John
    ```

    ### 頼った場合
    - `lodash`の`_.bindAll`が使える

## Q209.　引数をいくつか固定したいが、thisは固定したくない場合、どのようにできますか?

??? success
    ### 固定するものを考える
    - 呼び出す関数
    - 任意の引数

    ### 固定したくないものを考える
    - `this`
    - 残りの引数

    ### 使用例

    ```js
    //クロージャで、呼び出す関数と指定した引数のみ固定する
    //thisについてはcallを用いて呼び出す対象にする
    function partial(func, ...argsBound){
      return function(...args){
        return func.call(this, ...argsBound, ...args);
      }
    }

    let user = {
      name : "John",
      say(time, phrase){
        console.log(`${time}: ${this.name}: ${phrase}`);
      }
    };

    let anotherUser = {
      name : "Ann",
    }

    //funcと、argsBoundが固定
    user.sayNow = partial(user.say, new Date().getHours() + ":" + new Date().getMinutes());

    user.sayNow("Hello");; //15:34: John: Hello

    let f = partial(user.say, new Date().getHours() + ":" + new Date().getMinutes());

    //thisは呼び出す際に決まる
    anotherUser.sayNow = f;
    anotherUser2.sayNow = f;
    anotherUser.sayNow("Goodbye"); //15:34: Ann: Goodbye
    anotherUser.sayNow("bye-bye");//15:34: Hoge: bye-bye
    
    ```

## Q210 bindで関数を再バインドできないことを知っていますか?

??? success
    ### 使用例

    ```js
    const user = {
      name : "Hoge",
    };

    function func(phrase){
      console.log(phrase + "," + this.name);
    }

    const funcUser = func.bind(user);
    funcUser("Hello"); //Hello, Hoge
    
    const mistakeBindUser = funcUser.bind({name : "Fuga"});
    mistakeBindUser("Hello"); //Hello, Hoge
    
    ```

## Q211. アロー関数は自前のthisを持ちません。これを生かしてメソッドの内側を反復することができますか？

??? success
    ### 失敗例
    - functionはオブジェクト無しで呼び出されるので、`this`は当然`undefined`

    ```js
    const group = {
      title : "Conference",
      participate: ["John", "Ann", "Bob"],

      showList(){
        this.participate.forEach(
          function(participate){
            console.log(this.title + ":" + participate)
          }
        )
      }
    }

    group.showList(); //Error  
    ```


    ### 成功例
    - アロー関数は`this`を持たず、自分を囲んでいる実行コンテキストの`this`を参照する
    - アロー関数は実行コンテキストを持たない。現在の実行コンテキスト内で動く、小さな部品として作用する

    ```js
    const group = {
      title : "Conference",
      participate: ["John", "Ann", "Bob"],

      showList(){
        this.participate.forEach(
          participate => console.log(
            this.title + ": " + participate
          )
        )
      }
    }

    group.showList();
    //Conference: John
    //Conference: Ann
    //Conference: Bob
    ```

## Q212. アロー関数のデメリットと特徴を理解していますか?(復習)

??? success
    ### アロー関数はコンストラクタとして使えない
    - `new`としては呼び出せない

    ### アロー関数はメソッドとして使えない
    - 自分を囲んでいる実行コンテキストの`this`を参照するため

    ### アロー関数にbind,apply,callを適用しても意味がない
    - 前述のように、アロー関数の`this`は常に、生成時に自分を囲んでいた実行コンテキストの`this`を参照するため無効化される

    ```js
    const hoge = ()=>{return this.name};
    const fuga = function(){return this.name};

    const user = {
      name : "John"
    }

    let h = hoge.bind(user);
    let f = fuga.bind(user);

    console.log(h()); //""
    console.log(f()); //John
    ```

    ### bindとの違い
    - `bind(this)`は関数のバインドされたバージョンを作成
    - アローは単純に、外部のレキシカル環境を探しに行く

    ### argumentsを持たない
    - 単純にデメリットというわけでもない

    ### 遅延デコレータ(arrow)
  
    ```js

    function defer(f, ms){
      return function(){
        //独自のthisやargumentsを持たず、外から参照するので
        setTimeout(()=> f.apply(null, arguments), ms);
      };
    }

    function sayHi(who){
      alert("Hello, " + who);
    }

    let sayHiDeferred = defer(sayHi, 1000);
    sayHiDeferred("John");
    ```

    ### 遅延デコレータ(func)

    ```js
    //function内に、argumentを持ち越せないので、余計な変数を定義する必要がある
    //thisが欲しい場合、これもsetTimeoutの外で変数を作っておく必要がある
    function defer(f, ms){
      return function(...args){
        setTimeout(function(){
          return f.apply(null, args);
        }, ms)
      }
    }

    function sayHi(who){
      alert("Hello, " + who);
    }

    let sayHiDeferred = defer(sayHi, 1000);
    sayHiDeferred("John");
    ```

    ### superを持っていない
    - 後述