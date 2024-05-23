# クイズ(中級3:配列)

## Q74. 配列の作成方法とアクセス方法について知っていますか?

??? success
    ### 宣言方法
    - 主に2番目の方法が用いられる

    ```js
    const array = new Array();
    const array1 = [];
    ```

    ### 初期値

    ```js
    const array = [
      1,
      2,
      3,
    ]
    ```

    ### 複数の型の要素を一度に格納可能

    ```js
    const array = [
      "Array",
      {name : "hoge"},
      12,
      true,
      function(){alert("Hoge")},
    ]
    ```

    ### アクセス
    - `[]`を用いる
    - 要素は0から始まる。(stringも一緒)

    ```js
    const array = [1,2,3];
    console.log(array[2]) //3
    console.log(array); //[1,2,3]

    ```

    ### 置き換え

    ```js
    const array = [1,2,3];
    array[2] = 4;
    console.log(array); //[1,2,4]
    ```

    ### at()
    - 末尾からアクセスすることも可能
    - マイナスをつけると末尾から何個目かをカウント可能

    ```js
    const array = [1,2,3,4,5];
    //array[-1]; //undefined

    console.log(array.at(-2)); //4 
    ```

## Q75. 先頭と末尾に対して操作を行う、配列のメソッド4種を知っていますか?

??? success
    ### 両端キュー
    - JSの配列はスタックとキューの機能を併せ持つ
    - これを両端キュー/(deque)と呼ぶ

    ### pop
    - 最後の要素を抽出して返す

    ### push
    - 末尾に要素を追加する

    ### shift
    - 先頭の要素を抽出して返す

    ### unshift
    - 先頭に要素を追加する

    ### 使用例
    - なお、複数の値を同時に挿入も可能
    - 削除する場合、削除した値が返り値となる

    ```js
    const array = [1,2,3,4,5];
    console.log(array.pop()); //5
    console.log(array.push(6,1,2));
    console.log(array.shift()); //1
    console.log(array.unshift(7)); 

    console.log(array); //[7, 2, 3, 4, 6, 1, 2]
    ```

## Q76. 配列もオブジェクトであることを知っていますか?

??? success
    ### 配列の内部
    - 配列は、順序的にデータを扱うために最適化されたオブジェクト

    ### 普通のオブジェクトのように扱ってはいけない
    以下のようにするとその利点が消える

    - 非数値プロパティを追加する
    - 穴を作る
    - 逆順でindexの高い順に配列を埋める

    !!! warning
        - 以下の様にすることも可能ではある
  
        ```js
        const array = [1,2,3];
        array[9999] = "hoge";
        array.test = "hoge";
        
        ```

## Q77. オーダー表記( ${\mathcal{O}}$ )とpop/pushが早い理由を知っていますか?

??? success
    ### ランダウのO表記
    - 計算量を表したもの
    - ${O(n^2)}$ であれば、データ数がnであるとき、n^2だけ計算量が必要となる
    - ${O(1)}$ であれば、計算量はデータ数に依存しない

    ### push/pop
    - 末尾に対する操作であるため早い
    - 全ての要素を移動させる必要がないから

    ### shift/unshift
    - 先頭に対する操作であるため遅い(O(n))
    - 削除/追加した後に、すべてのindexを変更する必要がある

## Q78. for ... ofを用いて配列にアクセスする方法を知っていますか?

??? success
    ### for ... inとの違い
    - `for ... in`:　すべてのプロパティを繰り返し処理する。汎用的なオブジェクトようなので速度も1/100
    - `for ... of`: インデックスプロパティのみを処理する

    ### 使用例

    ```js
    const array =[1,2,3];
    for(const val in array){
      console.log(val); //1,2,3
    }
    ``` 

## Q79. lengthを用いて配列をクリアする方法を知っていますか?

??? success
    ### 実際の値の数ではない
    - 最大の数値indexに1を加えたもの
    - というより、`array[1000]`とすると、0,1に値が入っていなくても、メモリが確保されるという方が正しい

    ### 面白い動作
    - `.length`に現在の要素数以下の値を指定すると、切り捨てが行われる
    - つまり、`array.length = 0`とすると、空配列に戻る

    ```js
    const array = [1,2,3,4,5];
    array.length = 2;
    console.log(array); //[1,2]
    ```

## Q80. new Arrayで配列を作成したときの注意点を知っていますか?

??? success
    ### 注意点
    - 数値一つの引数の場合、要素数を意味する
    - その他は普通

    ```js
    const array = new Array(10);// [空要素*10]
    const array1 = new Array(10,1); //[10,1]
    ```

## Q81. 多次元配列を作成できますか?

??? success
    ### 使用例

    ```js
    const matrix = [
      [1,2,3],
      [4,5,6],
      [7,8,9],
    ]
    ```

## Q82. toString()でカンマ区切りのリストを返せることを知っていますか?

??? success
    ### 文字列に変換する

    ```js
    const arr = [1,2,3];
    arr.toString(); //"1,2,3"

    const arr2 = [
      [1,2,3],
      [4,5,6],
    ];
    String(arr2) = '1,2,3,4,5,6';
    ```

    ### 理由
    - 配列は`toString()`のみを実装しているから
    - `Symbol.toPrimitive`や`valueOf`を持たない

## Q83. 配列の中に関数があったとして、その中のthisは通常何を指しますか?

??? success
    ### 答え
    - 配列自身

    ### 例

    ```js
    const array = [1,2];
    array.push(function(){
      console.log(this);
    })

    array.push(3,4,5);

    array[2](); //[1,2,f,3,4,5];
    ```

    ### 理由
    - `array[2]()`は、`object[method]()`を意味する
    - 従って、関数を呼び出した`object`は`array`なので配列自身。

## Q84. 配列から削除をする際に、deleteではなく、.spliceを使う理由が分かりますか?

??? success
    ### deleteを使った場合
    - 要素は縮小されない

    ```js
    const arr = [1,2,3];
    delete arr[1];
    console.log(arr); //[1,,3];
    ```

    ### splice(index[, deleteCount, elem1, ..., elemN])
    - `splice`は`index`から始め、`deleceCount`個の要素を削除した後、挿入を行う
    - 削除した要素が返り値となる

    ### 使用例

    ```js
    const arr = [1,2,3,4,5];
    let removed = arr.splice(1,2);
    console.log(removed); //[2,3]
    console.log(arr); //[1,4,5]
    ```

    ### 負のインデックスも使える
    - 削除しない場合、指定したindexの前に挿入される

    ```js
    const arr = [1,2,3,4,5];
    arr.splice(-1,0, 3,4);
    console.log(arr); //[1,2,3,4,5,3,4]
    ```

## Q85. slice()を使って部分配列をコピーする方法を知っていますか?
??? success

    ### result = arr.slice(start [, end])
    - 指定したindexの範囲のアイテムをコピーする
    - 元の配列には影響しない
    - 結果は`result`に返る

    ```js
    const arr = [1,2,3,4,5];
    const copy = arr.slice(1,3);
    const copy2 = arr.slice(1);

    console.log(copy); //[2,3]
    console.log(copy2); //[2,3,4,5]
    ```

## Q86. concat()を使って配列を結合できることを知っていますか?

??? success
    ### result = arr.concat(arg1, ... argN)
    - 結果は`result`に返る
    - `argN`が配列でなければ、引数自体がコピーされる

    ```js
    const arr = [1,2];
    const result = arr.concat([3,4], 5);
    console.log(result); //[1,2,3,4,5]
    ```

    ## Symbol.isConcatSpreadableプロパティについて
    - 通常、`argN`が配列でなければ、引数はそのまま1つの要素としてコピーされる

    ```js
    const arr = [1,2];
    const arrLike = {
      0 : "hoge",
      length : 1
    };

    console.log(arr.concat(arrLike)); //1,2, {object}
    ```

    - 上記のプロパティがある場合は、配列として扱う。

    ```js
    const arr = [1,2];
    const arrLike = {
      0: "hoge",
      1: "fuga",
      [Symbol.isConcatSpreadable]: true,
      length: 2
    };

    console.log(arr.concat(arrLike)); // [1, 2, 'hoge', 'fuga']
    ```

## Q87. 配列の全要素に対して関数を実行する際に使う、arr.forEach()について知っていますか?

??? success
    ### arr.forEach(function(item [, index, array]){})
    - `item`: 各要素
    - `index`: インデックス
    - `array`: 元配列
    - 返り値は`undefined`

    ```js
    const arr = [1,2,3];

    ["hoge", "fuga", "foo"].forEach((item, index, array)=>{
      console.log(`${item} is at index ${index} in ${array}`);
      //hoge is at index 0 in hoge,fuga,foo
      //fuga is at index 1 in hoge,fuga,foo
      //foo is at index 2 in hoge,fuga,foo
    })
    ```

## Q88. 配列内で要素を探すうえでも、includes/indexOf/lastIndexOfが使えることを知っていますか?

??? success
    ### arr.indexOf(item, from);
    - インデックス`from`から、`item`を探す
    - 見つかった場所の`index`を返す。無い場合は-1

    ```js
    const arr = [1,0,2];
    console.log(arr.indexOf(0)); //1
    console.log(arr.indexOf("hoge")); //-1
    ```

    ### arr.lastIndexOf(item, from)
    - 基本的に同じ。
    - 末尾から見ていく

    ### arr.includes(item, from)
    - 見つかった場合、true
    - 見つからないならfalse

    ```js
    const arr = [1,2,3];
    arr.includes(4); //false
    ```

    ### 注意点
    - `NaN`を正しく処理できるのは`includes`だけ

    ```js
    const arr = [NaN];
    console.log(arr.indexOf(NaN)); //-1
    console.log(arr.includes(NaN)); //true
    ```

## Q89. 特定の条件を持つ、要素(単体)を見つけるとき、arr.findとfindIndexが使えることを知っていますか

??? success
    ### arr.find(function(item, index, array){})
    - `true`なら`item`が返され`iterator`停止
    - `false`ならundefinedが返される

    ### 例1

    ```js
    const result = [1,2,9,4,5].find((item)=>{
      if(item % 3 == 0) return true;
      return false;
    })

    console.log(result); //9
    ```

    ### 例2

    ```js
    const users = [
      {id:1, name: "hoge"},
      {id:2, name: "fuga"},
      {id:3, name: "foo"},
    ];

    const user = users.find(item => item.id === 1);
    console.log(user.name); //hoge
    ```

    ### arr.findIndex(function(item, index, array){})
    - 基本的に一緒
    - 見つかった場合、indexを返す

    ```js
    const result = [10,20,3,4,5].findIndex((item)=>{
      if(item == 20){return true;}
      return false;
    })

    console.log(result); //1
    ```

    ### arr.findLast() と arr.findLastIndex()
    - 終わりから探索する

    ```js
    [1,2,3].findLast((item)=>{
      if(item % 2 == 1){return true;};
        return false;
    }); //3

    [1,2,3].findLastIndex((item)=>{
      if(item % 2 == 1){return true;};
        return false;
    }); //2
    ```


## Q90. 特定の要素を複数返したい場合、filter()が使えることを知っていますか?

??? success
    ### results = arr.filter(function(item, index, array){})
    - `true`なら`item`は`results`に`push`
    - `false`なら何もしない
    - いずれにせよ`iterator`は継続
    - 何も見つからない場合は空配列を返す

    ```js
    const array = [0,1,2,3,4,5,6];

    const results = array.filter((item)=>{
      if(item % 2 == 0) return true;
      return false;
    })

    console.log(results); //[0,1,2,3,4,5,6]
    ```

## Q91. 配列内の全要素に対して、関数を実行した結果を新しい配列で返したいときに、mapが使えることを知っていますか?

??? success
    ### forEachとの違い
    - `map`は元配列を変更しない

    ### result = arr.map(function(item, index, array){})

    ```js
    const arr = [1,2,3,4,5];
    const result = arr.map((item)=>{
      return item*2;
    })

    console.log(result); //[2,4,6,8,10];
    ```

## Q92. 配列内の要素をソートしたいときにsortが使えることを知っていますか?

??? success
    ### 注意点
    - デフォルトでは辞書順としてソートされる

    ```js
    const arr = [1,2,15];
    arr.sort();
    console.log(arr); //[1,15,2]
    ```

    ### arr.sort(fn);
    - `fn`として関数を適用する
    - `fn`には2つの引数を設定し、2つの要素(前の要素、後の要素)を比べたときの大小関係のルールを記載する
    - 返り値として負の数を設定すれば、「入れ替えない」
    - 0なら入れ替えない
    - 正の数なら「入れ替える」

    ### 例
    - 昇順

    ```js
    //下の場合、
    //前方の要素をa, 後方の要素をbとしたとき、
    //a < bなら、マイナスなので、入れ替えない
    //a > bならプラスなので入れかえ
    //つまり、昇順を意味する
    const arr = [1,7,8,6,5,3,2];
    arr.sort((a,b) => a - b)
    ```

    - 降順

    ```js
    const arr = [1,7,8,6,5,3,2];
    arr.sort((a,b) => b - a);
    // [8, 7, 6, 5, 3, 2, 1]
    ```

    - 分かりやすく書いた昇順の例

    ```js
    const arr = [1,7,8,6,5,3,2];
    arr.sort(function(a,b){
      if(a > b) return 1;
      if(a == b) return 0;
      else return -1;
    })
    ```

    ### 文字列の場合、localCompareを使う
    - ドイツ語の場合

    ```js
    const countries = ['Österreich', 'Andorra', 'Vietnam'];

    countries.sort((a,b) => a.localeCompare(b));
    console.log(countries); // ['Andorra', 'Österreich', 'Vietnam']
    ```

## Q93. 要素の順を逆転させたいときにreverse()が使えることを知っていますか?

??? success
    ### arr.reverse()

    ```js
    const arr = [1,2,3,4,5];
    arr.reverse();
    console.log(arr); //[5,4,3,2,1]
    ```

## Q94. 設定した区切り文字で文字列を配列に分解、もしくはその逆を行いたいとき、split/joinが使えることを知っていますか?

??? success
    ### result = arr.split(delim [, limit])
    - `delim`: 区切り文字
    - `limit`: 配列の長さ制限
    - 結果は`result`に返る

    ```js
    const names = "Hoge, Fuga, Foo";
    const arr = names.split(", ");
    console.log(arr); // ['Hoge', 'Fuga', 'Foo']

    const arr1 = names.split(", ", 2)
    console.log(arr1); // ['Hoge', 'Fuga']
    ```

    ### 1文字単位で分割
    - `delim`に空文字を設定すると、文字列を文字の配列に分割する

    ```js
    const str = "test";
    const charList = str.split("");
    consol.log(charList); // ['t', 'e', 's', 't']
    ```

    ### result = arr.join(str);
    - `arr`の各要素を`str`でつないだ文字列を生成する
    - 結果は`result`に返る

    ```js
    const arr = ["Hoge", "Fuga", "Foo"];
    const str = arr.join(", ");
    console.log(str); //Hoge, Fuga, Foo
    ```

## Q95. 配列内の総和や総積等を単一の値で返したいときに役立つ、reduce()について知っていますか?

??? success
    ### arr.reduce(function(accumulator, item, index, arr){}, initial)
    - `accumulator`: 前の関数呼び出しの結果。初回は`initial`
    - `item`: 現在の配列の要素
    - `index`: 位置
    - `arr`: 配列本体
    - `initial`: 初期値。設定なしの場合、配列の最初の要素が初期値

    ### reduce()の概要
    - 関数が適用されると、その返り値が、次の関数呼び出しの引数(accumulator)となる
    - 最終的な結果が、返り値として戻る

    ### 例(総和)

    ```js
    const arr = [1,2,3,4,5];
    const result = arr.reduce((sum, current) => {
      return sum + current;
    }, 0);

    console.log(result); //15
    ```

    ### 例2(総積)

    ```js
    const arr = [1,2,3,4,5];
    const result = arr.reduce((mul, current)=>{
      return mul * current;
    },1);

    console.log(result); //120
    ```

    ### 初期値設定をお勧めする理由
    - 初期値が無い場合、空配列でエラーになる

    ```js
    const arr = [];
    arr.reduce((sum, current) => sum + current); //Reduce of empty array with no initial value
    ```
    
    ### arr.reduceRight()について
    - 右から左へ実行する

    ```js
    const arr = [1,2,3,4,5];
    const result = arr.reduceRight((sum, current) => {
      console.log(current);//5,4,3,2,1
      return sum + current;
    }, 0);
    console.log(result); //15
    ```

## Q95. 配列と通常のオブジェクトとを区別する際に用いる、Array.isArrayについて知っていますか?

??? success
    ### Array.isArray(value)
    - `value`が配列の時に`true`を返す

    ### 例

    ```js
    Array.isArray({}); //false
    Array.isArray([]); //true
    Array.isArray([1,2,3]); //true
    ```

## Q96. sortを除く、関数を呼び出す配列メソッドが、thisArgというパラメータオプションを受け取ることを知っていますか?

??? success
    ### thisArg
    - `thisArg`は、`func`における`this`になる
    - `thisArg`はオプションの中でも最後の引数になる
    - `arr.find(func, thisArg)`

    ### 例

    ```js
    const army = {
      minAge: 18,
      maxAge: 27,
      canJoin(user){
        return user.age >= this.minAge && user.age < this.maxAge;
      }
    };

    let users = [
      {age : 16},
      {age : 20},
      {age : 23},
      {age : 30},
    ];

    //第1引数に関数自体を設定し、オブジェクト無しで関数を呼び出している形になるので、thisが必要になる
    //ここで、thisArg == army
    let soldiers = users.filter(army.canJoin, army);
    
    //此方の方が一般的。オブジェクトarmyとして、canJoinを呼び出している
    let soldiers_1 = users.filter(user => army.canJoin(user));

    console.log(soldiers.length); //2
    console.log(soldiers[0].age); //20
    ```

## Q96. 配列の各要素に関数を適用し、結果に応じて真偽値を返すevery()とsome()の仕様を知っていますか?

??? success

    ### arr.every(fn(item, index, array))
    - 全ての結果が`true`なら`true`
    - 1つでも`false`があるなら`false`

    ### arr.some(fn(item, index, array))
    - 1つでも`true`があるなら`true`
    - 全部`false`なら`false`

    ### like || &&
    - `fn`が真を返すなら`arr.some()`はiteratorを停止
    - `fn`が偽を返すなら`arr.every()`はiteratorを停止

    ### 使用例

    ```js
    const arr = [1,2,3,4,5];

    const resultEvery = arr.every((item)=>{
      if(item % 2 != 0) return false;
      return true;
    })

    const resultSome = arr.some((item)=>{
      if(item % 2 != 0) return false;
      return true;
    })

    console.log(resultEvery); //false
    console.log(resultSome); //true
    ```

    ### 使用例2(everyで配列比較)

    ```js
    const array1 = [1,4,3,2,4];
    const array2 = [1,4,3,2,4];

    const arrayEqual = (arr1, arr2) =>{
      return arr1.length === arr2.length &&
        arr1.every((item, index)=>{
          return item === arr2[index];
        })
    }

    console.log(arrayEqual(array1, array2)); //true
    ```

## Q97. 配列を同じ値で埋めたいときに使えるfillについて知っていますか?

??? success
    ### arr.fill(value, start, end);
    - `start ~ (end-1)`まで`value`で配列を埋める

    ```js
    const arr = [1,2,3,4,5];
    arr.fill(0, 1, 4);
    console.log(arr); // [1, 0, 0, 0, 5]
    ```

## Q98. 多次元配列を低次元にするflatと、map()を行った結果をフラット化する、flatMapについて知っていますか?


??? success
    ### result = arr.flat(depth);
    - `depth`: 何次元までの配列をフラット化するか(default:1)
    - フラット化した結果は、`result`に返る

    ```js
    const arr = [
      [
        [1,2,3],
        [4,5,6],
        [7,8,9],
      ],
      [
        [10,11,12],
        [13,14,15],
      ],
    ];

    const result = arr.flat(2); // [1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15]
    const result1 = arr.flat(1);
    //[[1,2,3],[4,5,6],[7,8,9],[10,11,12],[13,14,15]]
    ```

    ### result = arr.flatMap(fn(item, index, arr){});
    - `depth`は1段階で固定

    ```js
    const arr1 = [1,2,3,4];

    const result1 = arr1.map(x => [x*2]);
    //  [[2], [4], [6], [8]]

    const result2 = arr1.flatMap(x => [[x * 2]]);
    //  [[2], [4], [6], [8]]

    //繰り返しflatMapをして次元を下げることもできる
    const result3 = arr1.flatMap(x => [[x * 2]].flatMap(x => x))

    // [2,4,6,8]
    ```

## Q99. arr["2"]とarr[2]が同じ場所を指す理由を理解していますか?

??? success
    ### 理由
    - `arr[2]`の2はJSエンジンによって内部的に`toString`で`"2"`に変換されるから
    - これは`arr["2"]`と`arr["02"]`が違う場所を指すための仕組みでもある
    - なお、`arr["02"]`は、配列の反復処理の対象にならない
  
## Q100. 配列の一部を配列内の他の場所にコピーしたいときに用いる、copyWithin()について知っていますか?

??? success

    ### arr.copyWithin(target, start, end)
    - `target`:コピーしたい場所
    - `start ~ end`: 範囲

    ```js
    const array = [1,2,3,4,5];
    array.copyWithin(0,3,5);

    .console.log(array); //[4,5,3,4,5]
    ```

## Q101. 元の配列を変更するメソッドと、変更しないメソッドの区別ができていますか?

??? success
    ### 注意点
    - 変更しないメソッドに見覚えがない場合は後述

    | 役割         | 変更する       | 変更しない           |
    | ------------ | -------------- | -------------------- |
    | 末尾削除     | pop            | slice(0, -1)         |
    | 末尾追加複数 | push(v1, v2)   | concat([v1, v2])     |
    | 逆順         | reverse()      | toReversed()         |
    | 先頭削除     | shift()        | slice(1)             |
    | 先頭追加     | unshift(v1,v2) | toSpliced(0,0,v1,v2) |
    | ソート       | sort()         | toSorted()           |
    | スライス     | splice()       | toSpliced()          |
    | 穴埋め       | fill()         | 無し                 |
    | 関数適用     | forEach()      | map()                |
    | 総和など     | 無し           | reduce()             |
    | フィルター   | なし           | filter()             |

## Q102 元配列を変更しないメソッドについて理解していますか?(例: toSpliced)

??? success
    ### result = arr.toSorted(fn)
    - 元配列を変更せずソート。
    - 値を返す

    ```js
    const arr = [5,3,4,2,11];

    const result = arr.toSorted((a,b)=> a - b);
    console.log(result);  // [2, 3, 4, 5, 11]
    ```

    ### result = arr.toReversed()
    - 元配列を変更せずReverse

    ```js
    const arr = [5,3,4,2,11];
    const result = arr.toReversed()
    console.log(result);  // [11, 2, 4, 3, 5]
    ```

    ### result = toSpliced(start, deleteCount, item1, ..., itemN)
    - `start`: 起点
    - `deleteCount`: 削除個数
    - `itemN`: 追加するもの

    - 元の配列を編集せず、要素の削除、置換が可能

    ```js
    const arr = [1,2,3,4,5];

    //先頭追加
    const result1 = arr.toSpliced(0, 0, 6, 7); //[6,7,1,2,3,4,5]

    //末尾追加(lengthはlastIndex+1なのでこれを活用する)
    const result2 = arr.toSpliced(arr.length, 0, 6); //[1,2,3,4,5,6]

    //先頭削除
    const result3 = arr.toSpliced(0,1); //[2,3,4,5]
    
    //末尾削除
    const result4 = arr.toSpliced(-2,2); //[1,2,3]


    ```

    ### arr.with(index, value)
    - `index`の値を`value`に変更する

    ```js
    const arr = [1,2,3,4,5];
    const result = arr.with(2,6);
    console.log(result); //[1, 2, 6, 4, 5]

    const result2 = arr.with(2,6).map(x => x**2);
    console.log(result2); //[1, 4, 36, 16, 25]
    ```

## Q103. 疎配列と、その挙動について知っていますか?

??? success
    ### 疎配列
    - 以下の様に、要素がないindexがある配列
    - `const arr = [1, , 3,4,,6]`

    ### withは常に密配列を生成する
    - 疎である分は、`undefined`に変換される

    ```js
    const arr = [1, , 3,4,,6];
    console.log(arr.with(0,2)); // [2, undefined, 3, 4,  undefined, 6]
    ```

    ### 疎配列は一貫性のない動作をする
    - 一般的に古いメソッドは空をスキップする
    - 新しいメソッドは`undefined`として扱う
    - 参照元[mdn:配列メソッドと空のスロット](https://developer.mozilla.org/ja/docs/Web/JavaScript/Reference/Global_Objects/Array#%E9%85%8D%E5%88%97%E3%83%A1%E3%82%BD%E3%83%83%E3%83%89%E3%81%A8%E7%A9%BA%E3%81%AE%E3%82%B9%E3%83%AD%E3%83%83%E3%83%88)

    ### 古いメソッドと新しいメソッドの例
    - 新しいメソッドの場合、アクセスする前に`index in array`によるチェックを行う必要がある
    - チェックを行うことで、該当indexには、`undefined`を含む何らかの要素があるとわかり、空と`undefined`を混同しなくなる

    ```js
    const arr = [1, , 3,4,undefined,6];

    arr.forEach((item)=>{
      console.log(item);
    })
    ```

    ```js
    const arr = [1, , 3,4,undefined,6];
    arr.find((item,index) =>{
      if(!(index in arr)){
        console.log(`arr[${index}] is Empty`); //arr[1] is Empty
      }

      if(item == 6)return true;
      return false;
    })
    ```

## Q104. ダッシュ区切りのワードをキャメルケースに変更できますか?(活用)

??? success
    ### 答え

    ```js
    const camelize = str =>{
      return str
      .split("-")
      .map((item, index)=> index == 0 ? item : item[0].toUpperCase() + item.slice(1))
      .join("")
    }

    const result = camelize("test-dash-message");
    console.log(result); //testDashMessage
    ```
  
## Q105. 配列内の要素をシャッフルする手段が思いつきますか?

??? success

    ### ダメな例
    !!! failure

        ### 概要
        - 確かに`Math.random() -0.5`はマイナスとプラスを問う確率で生成する
        - しかし`sort`の内部は、おそらくクイックソートであろうが、等しくすべての要素同士を比較するわけではない
        - よって偏りが生まれる

        ```js
        [1,2,3,4,5].sort(()=> Math.random() -0.5);
        ```
    
    ### Fisher-Yates-shuffle
    - 逆順に配列を見ていき、各要素をその前のランダムな要素と入れ替える

    ```js
    const shuffle = arr =>{
      for(let i = arr.length -1 ; i > 0; i--){
        let j = Math.floor(Math.random() * (i+1)); //0~i
        [arr[i], arr[j]] = [arr[j], arr[i]]; //分割代入(後記)
      }
    }

    //0~99までの配列を生成している
    const array = Array.from({length : 100}, (_, i) => i);
    shuffle(array);
    console.log(array);
    //[24, 82, 17, 44, 51, 91, 98, 20, 30, 92, 38, 56, 55, 31, 90, 36, 4, 48, 16, 62, 0, 46, 3, 88, 81, 63, 9, 29, 37, 49, 99, 85, 78, 18, 68, 28, 60, 22, 58, 87, 97, 77, 6, 66, 40, 74, 41, 39, 43, 8, 5, 59, 1, 52, 15, 70, 7, 53, 34, 84, 32, 42, 61, 57, 19, 89, 64, 54, 25, 33, 47, 50, 35, 80, 10, 95, 2, 65, 96, 26, 45, 73, 94, 21, 76, 12, 72, 14, 79, 83, 67, 86, 27, 71, 75, 11, 23, 13, 93, 69]
    ```