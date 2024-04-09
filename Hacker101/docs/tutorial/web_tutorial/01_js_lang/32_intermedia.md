# クイズ(中級)

## Q1. forEach()は新しい配列を返しますか(y/n)?

??? success
    - `No`
    - `forEach()`は配列内の各要素に対して、第1引数の関数を実行する。返り値は破棄され、常に`undefined`が返る。ここがこQ1で述べる`map`と異なる。
    - `forEach()`の中で`break`などは使えない。またこれを使いたい場合、`forEach()`は適切な方法ではない
    - また、`forEach()`は同期的な関数を期待する。
    - `forEach()`は反復処理の前に配列のコピーを作成しないので、反復処理の中で要素の個数を変更してはならない。

    ### 例(forループとforEach比較)

    ```js
    const items = ["item1", "item2", "item3"];
    const copyItems = [];

    for(let i=0; i<items.length; i++){
      copyItems.push(items[i]);
    }

    const copyItems2 = [];
    items.forEach((item)=>{
      copyItems2.push(item);
    })
    ```

    ### 例2 不連続な場合飛ばされる

    ```js
    testArray = [1,3,5,/**/,7];
    testArray.forEach((element)=>{
      console.log(element);
    })
      //output
      //1
      //3
      //5
      //7
    ```

## Q2.map()は新しい配列を返しますか? (y/n);

??? success
    - `Yes`
    - `map()`は元の配列を変更せず、呼び出された配列内のすべての要素に対して、関数を呼び出した結果を含む、新しい配列を返す

    ```js
    const hoges = ["JS", "React", "Node.js"];
    const newHoges = hoges.map((hoge) =>{
      return `${hoge} Roadmap`;
    });

    console.log(newHoges); //['JS Roadmap', 'React Roadmap', 'Node.js Roadmap']
    ```

## Q3. filter()メソッドについて知っていますか?

??? success
    - `filter()`は条件に基づいて配列をフィルタリングする
    - 条件を満たした要素のみで、新しい配列が作成される

    ```js
    const numbers = [1,2,3,4,5,6];

    const evenNumbers = numbers.filter((number)=>{
      return number % 2 == 0;
    })

    console.log(evenNumbers); //[2,4,6];
    ```

## Q4. mapとreduceの違いは分かりますか?

??? success

    - `map`は、配列内のすべての要素に対して操作を行い、新しい配列を返します
    - `reduce`は配列内の各要素を1つずつ呼び出し、前の結果に対して操作を行い、結果として単一の値を返します

    ```js
    const A = [1,2,3,4,5];
    const B = A.map(a => a*2); //[2,4,6,8,10]

    //reduce(第二引数は初期値になります:defaultは0)
    const C = A.reduce((prev, current) => prev + current, 0); //15 
    const C = A.reduce((prev, current) => prev * current, 1); //120
    ```



## Q6. スプレッド演算子について知っていますか？

??? success
    - スプレッド演算子とは3つのドット`...`を指します
    - これを使うと、配列の要素やオブジェクトのプロパティ値等の __反復可能なオブジェクト__ を、展開することができます
    - 配列のコピーやマージなど、多くの場面で役立ちます

    ### copy

    ```js
    const A = ["Javascript", "React", "Node.js"];
    const B = [...A];
    console.log(B); //['Javascript', 'React', 'Node.js']
    ```
    ### merge

    ```js
    const A = ["Javascript", "React", "Node.js"];
    const otherArray = ["AWS", "Amplify"];

    const mergeArray = [...A, ...otherArray];
    console.log(mergeArray);  // ['Javascript', 'React', 'Node.js', 'AWS', 'Amplify']
    ```

    ### object-copy

    ```js
    const A = {
      name: "Javascript",
      type: "script",
    };

    const copyA = {...A};
    console.log(copyA); //{name: 'Javascript', type: 'script'}
    ```

## Q7. 複数の配列をマージする方法について知っていますか？

??? success

    ### concat()を使う
    - concatは2つ以上の配列を結合し、新しい配列を返します
    - 既存の配列は変更されません

    ```js
    const arr1 = [1,2,3];
    const arr2 = [4,5,6];
    const arr3 = arr1.concat(arr2);
    console.log(arr3); //[1,2,3,4,5,6]
    ```

    ### スプレッド演算子を使う
    - スプレッド演算子は、反復可能なオブジェクトを、リストの引数として展開します

    ```js
    const arr1 = [1,2,3];
    const arr2 = [4,5,6];
    const arr3 = [...arr1, ...arr2];
    console.log(arr3); //[1,2,3,4,5,6]
    ```

## Q8. 配列内で、1回しか登場しない値（ユニーク値）を列挙できますか？

??? success

    ### setを使った場合

    ```js
    const A = ["Javascript", "React", "React", "Node.js", "Javascript"];

    //スプレッド演算子で、Setオブジェクトとの要素を展開している (1)
    const uniqueA = [...new Set(A)];
    ```

    1. スプレッド演算子の説明
    ```js
    const arr1 = [1,2,3];
    const arr2 = [...arr1, 4,5,6];

    //〇[1,2,3,4,5,6]
    //✖[[1,2,3],4,5,6]
    ```

    ### filterを使った場合
    - 効率が悪いので非推奨

    ```js
    const A = ["Javascript", "React", "React", "Node.js", "Javascript"];
    const uniqueA = A.filter(
      (a, index) => A.indexOf(a) === index //(1)
    );

    console.log(uniqueA)
    ```

    1. 
    ・`A.indexOf(a)` => aが配列A内で最初に出現するindexを返す
    ・つまり、配列A内で最初に出現するaならtrue, 2回目以降(重複している)ならfalseを返す

    ### reduceを使った場合

    ```js
    const A = ["Javascript", "React", "React", "Node.js", "Javascript"];
    const uniqueA = A.reduce((unique, a) =>{
      return unique.includes(a) ? unique : [...unique, a];
    }, []); //(1)

    console.log(uniqueA);
    ```

    1. 初期値`[]`。リスト内を1つずつ探索し、重複していれば何もしない。重複していなければ末尾に追加。

    ### forEach()を使った場合

    ```js
    const A = ["Javascript", "React", "React", "Node.js", "Javascript"];

    const uniqueA = [];

    A.forEach(a => {
      if(!uniqueA.includes(a)){
        uniqueA.push(a);
      }
    })

    console.log(uniqueA);
    ```

    ### for ... ofを使った場合

    ```js
    const A = ["Javascript", "React", "React", "Node.js", "Javascript"];

    const uniqueA = [];

    for (const a of A){
      if(!uniqueA.includes(a)){
        uniqueA.push(a);
      }
    }
    console.log(uniqueA);
    ```

## Q9. preventDefault()の使い道が分かりますか？

??? success
    - `preventDefault()`はイベントのデフォルトのアクションを防止するために使用される

    ```html
    <a href="#" class="link"></a>
    <script>
      const link = document.querySelector(".link");

      link.addEventListener("click", (event)=>{
        event.preventDefault(); //(1)
        console.log("Clicked on Link!"); //(2)
      })
    </script>
    ```

    1. 本来はlinkをクリックすると、ページトップ(#)に飛ぶが、その動作をブロック
    2. 代わりに、コンソールにメッセージを表示している





## Q12. Hoisting(巻き上げ)について知っていますか?

??? success

    - JSでは、コードの実行前に変数と関数の宣言をスコープの先頭に移動する
    - 変数や関数の宣言は、varなら関数スコープ単位で、letやconstならブロックスコープ単位で巻き上げられる
    - 初期化については移動されない

    ### コードの例

    ```js
    console.log(x === undefined); //true
    var x = 3;
    console.log(x);
    ```

    ### 実際の実行順序

    ```js
    var x; //(1)
    console.log(x === undefined); //true
    x = 3;
    console.log(x);
    ```

    1. varは関数スコープの先頭まで巻き上げられるので、`x is not defined`と言われることはない

    ### 以下の例がダメな理由
    - constやletで宣言した変数がブロックスコープの先頭まで巻き上げられることを考えると、以下の例は問題なさそうに見える
    - しかし、`let`で宣言される関数が、宣言文の位置に到達する前にスコープ内で実際にアクセスされた場合は、参照エラーとなる。このようなエラーが発生する領域をTDZ(Temporal Dead Zone)と呼ぶ

    ```js
    {
      console.log(x === undefined); //true
      let x = 3;
      console.log(x);
    }
    ```

## Q13. Javascriptにおいて参照はコピーされますか?

??? success
    - `No`
    - オブジェクトを変数に割り当てると、変数にはオブジェクトへのコピーではなく、参照が含まれる
    - 変数を別の変数に割り当てると、2番目の変数にもオブジェクトへの参照が含まれる
    - 結果、いずれかの変数を使用してオブジェクトを変更すると、オブジェクト自体が変化し、その変更はもう一方の変数を使用して表示される

    ```js
    const obj = {name: "hoge"};
    a = obj;
    b = a;
    a.name = "fuga";
    console.log(b); //{name: "fuga"};
    console.log(obj); //{name: "fuga"};
    ```

## Q14.JSにおいて、可変長の引数を受け入れる方法を知っていますか?

??? success
    ### argumentsオブジェクトを使用する
    -  `arguments`は __渡された__ すべての引数を保持する配列のようなオブジェクトで、関数式内でのみ使用できる
    ```js
    function displayArgs(){
      for(let i = 0; i < arguments.length; i++){
        console.log(arguments[i]);
      }
    } 
    displayArgs(1,2,3,4);
    ```
    ??? warning
        - 以下の式は動かない
        - これは、アロー関数が関数式の完全な代替ではなく、`arguments`を持っていないから
        ```js
        const displayArgs =() =>{
          for(let i = 0; i < arguments.length; i++){
            console.log(arguments[i]);
          }
        } 
        displayArgs(1,2,3,4);
        ```

    ### 残余引数を使った場合
    - 此方の方が柔軟で現代的
    - argumentsオブジェクトと違い、実際の配列を提供する

    ```js
    const displayArgs = (a, b, ...args) =>{
      console.log(args); //[3.4];
      args.forEach((arg) => console.log(arg));
    }

    displayArgs(1,2,3,4);

    //output
    //3
    //4
    ```



## Q16.アロー関数と関数式におけるthisの挙動の違いについて理解していますか?

??? success

    ### 関数式の場合
    - 関数式の場合、`this`は関数が宣言された時点では決まっておらず、呼び出し元のオブジェクトを意味する

    ```js

    function setMessage(){
      console.log(this.message);
    }

    const obj = {
      message: "Hello Japan",
      func: setMessage
    }

    const obj2 = {
      message: "Hello USA",
      func : setMessage
    }

    obj.func(); //Hello Japan (1)
    obj2.func(); //Hello USA  (2)
    ```

    1. thisはobj
    2. thisはobj2

    ### アロー関数の場合
    - アロー関数の場合、自身ではthisを持たず、代わりに、その関数が定義された時点でのthis(つまり親スコープ = `function(){}`の親であるobj)を参照する
    - つまり、thisの値は宣言時に確定する。

    ```js
    let globalVar = 'global scope';

    const obj = {
      prop: 'object scope',
      method: function() {
        if(true){
          if(true){
            return () =>{
              console.log(this.prop);
            }
          }
        }
      }
    };

    const arrowFunc = obj.method();
    arrowFunc(); //Outputs: object scope

    ```
