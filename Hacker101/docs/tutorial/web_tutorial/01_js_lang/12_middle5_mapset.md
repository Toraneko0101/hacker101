# クイズ(中級5:map&set)

## Q113. Map(辞書)の作り方と主なメソッドを知っていますか?

??? success
    ### Map概要
    - Objectと異なり、任意の型のキーを許可する
    - つまり、文字列以外もキーに持てる

    ### Map作成: new Map()

    ```js
    const map = new Map();
    ```

    ### 値の格納/上書き: map(key, value)
    - キーを文字列に変換しないので、以下は別物となる

    ```js
    map.set("1", "str1");
    map.set(1, "num1");
    map.set(true, "bool1");
    map.set(true, "bool2");
    map.set("hoge", undefined);
    ```

    ### 値の取得: map.get(key)
    - keyが存在しない場合、undefinedとなる

    ```js
    map.get(true); //bool2
    map.get(1); //num1
    ```

    ### キーの存在判別 map.has(key)
    -　値がundefinedであるか、keyがないかの区別等

    ```js
    map.has("hoge"); //true
    ```

    ### キーの削除 map.delete(key);

    ```js
    map.delete("hoge");
    map.has("hoge"); //false
    ```

    ### 中身を全消去する map.clear();

    ```js
    map.clear();
    ```

    ### 現在の要素数を取得する

    ```js
    console.log(map.size); //0
    ```

    ### keyとしてオブジェクトを使用する
    - `object`ではできないこと
    - (objectの場合、オブジェクトをキーとすると、デフォルトではすべて"[object Object]"に変換され、上書きされる)

    ```js
    const hoge = {name : "Hoge"};
    const fuga = {name : "Fuga"}

    const visitsCountMap = new Map();
    visitsCountMap.set(hoge, 123);
    visitsCountMap.set(fuga, 98);
    console.log(visitsCountMap.get(hoge)); //123
    ```

    ### 初期値を設定する
    - 二次元`Array`の様に書く

    ```js
    const map = new Map([
      ["apple", 120],
      ["orange", 83],
      ["grape", 210],
    ])

    map.get("grape"); //210
    ```

## Q114. mapの等価性と、map.set時にチェーンした際の挙動について知っていますか?

??? success
    ### mapの等価性
    - NaN同士を比較可能


    ```js
    const map = new Map();
    map.set(NaN, "neko");
    map.get(NaN)
    ```

    ### チェーンについて
    - `map.set`呼び出しの際、返り値は`map`自身になるので、チェーン可能

    ```js
    const obj = {};
    const func = ()=>{};

    const map = new Map();
    map.set("1", "str")
    .set(1, "num")
    .set(true, "bool")
    .set(obj, "obj")
    .set(func, "func")

    map.get(obj); //"obj"
    ```

## Q115. mapでループする際に使える、keys, values, entriesについて知っていますか?

??? success
    ### map.keys()
    - キーに対する`iterable`を返す

    ### map.values()
    - 値に対する`iterable`を返す

    ### map.entries()
    - `[key, value]`の`iterable`を返す
    - `default`の場合、(`for...of`ループにおいて、`iterable`がmapの場合)、`entries()`扱いになる

    ### 例

    ```js
    const fruitMap = new Map([
      ["apple", 120],
      ["orange", 83],
      ["grape", 210],
    ])

    //key
    for(const fruit of fruitMap.keys()){
      console.log(fruit); //apple, orange, grape
    };

    //value
    for(const cost of fruitMap.values()){
      console.log(cost); //120,83,210
    }

    //entries
    for(const [key, value] of fruitMap){
      console.log(`${key}:${value}`);
      //apple:120
      //orange:83
      //grape:210
    }
    ```

    ### 挿入順について
    - `Map`は順番を保持する

    ### forEach((value, key, map)=>{}, thisArg)について
    - `object`にはない
    - `Map`にはある
    - `key`と`value`の順番が`entries`と逆であることに注意

    ```js
    fruitMap.forEach((value, key, map)=>{
      console.log(`${key}:${value}`);
    })
    ```

## Q116. ObjectとMapを相互変換する方法を知っていますか?

??? success
    ### key/値のペアを持つ配列(または`iterable`)がある場合
    - 初期値の指定で行った通り

    ```js
    const map = new Map([
      [1, "num"],
      ["1", "str"],
      [true, "bool"],
    ]);
    ```

    
    ### Object.entries
    - 通常のオブジェクトから`key/value`のペア配列を生成
    - `Map`の初期値として、この配列を使用して`Map`とする

    ```js
    const obj = {
      name : "Hoge",
      age : 30
    };

    const map = new Map(Object.entries(obj));
    console.log(map.get("age")); //30
    ```

    ### Object.fromEntries
    - `Map`からオブジェクトを作成
    - `Map`でなくとも、反復可能な`[key, value]`ペアの配列であればいい

    ```js
    const map = new Map();
    map.set("banana", 1);
    map.set("orange", 2);
    map.set("grape", 3);

    //map.entries() の箇所にはmapを指定してもいい
    const obj = Object.fromEntries(map.entries())

    const obj2 = Object.fromEntries([
      ["banana", 1],
      ["orange", 2],
      ["grape", 3],
    ])

    console.log(obj["banana"]); //1
    console.log(obj2["banana"]); //1
    ```

## Q117. 重複しない値を保持したいときに用いる、Setについて知っていますか?

??? success
    ### 概要
    - キーを持たない
    - 重複値を持たない

    ### 作成 new Set()

    ```js
    const set = new Set();
    ```

    ### 初期値 new Set(iterable)

    ```js
    const set = new Set([1,2,3,4,5,1]);
    ```

    ### 値の追加: set.add(value)
    - 返り値として自身を返すので、チェーン可能

    ```js
    set.add(1).add(2);
    ```

    ### 値の削除 set.delete(value)
    - 存在していればtrue, いなければfalse

    ```js
    set.delete(1)
    ```

    ### 値の存在確認 set.has(value)
    - 存在していればtrue, いなければfalse

    ```js
    set.has(1);
    ```

    ### 全要素削除 set.clear()

    ```js
    set.clear()
    ```

    ### 要素数 set.size

    ```js
    set.size
    ```

    ### 使用例
    - Userが複数回来ても重複しない

    ```js
    const set = new Set();

    const john = {name : "John"};
    const Ann = {name : "Ann"};
    const Bob = {name : "Bob"};

    set.add(john);
    set.add(Ann);
    set.add(Bob);
    set.add(Bob);

    console.log(set.size); //3
    ```

## Q118. Setがfor..ofやforEachでループできることを理解していますか?

??? success
    ### ループ
    - これも挿入順で処理される

    ```js
    const set = new Set([1,2,3,4,5,1,3]);

    for(const val of set){
      console.log(val); //1,2,3,4,5
    }

    set.forEach((value, valueAgain, set)=>{
      console.log(value); //1,2,3,4,5
    })
    ```

    ### valueAgainとは?
    -　`value`と同じ値
    - 引数が三つあるのは、`Map`との互換性のため
    - 特定ケースにおいて、条件分岐を書かなくていいので、後々助かる

    ### Mapとの互換性
    - `set.keys()`や、`.values()`、`.entries()`も損z内する
    - これも`Map`との互換性目的

## Q119. WeakMapとMapとの差異を理解していますか?

??? success
    ### WeakMap
    - `Key`はオブジェクトである必要あり
    - オブジェクトをキーとして使い、そのオブジェクトへの参照がほかにない場合、自動的に削除される

    ### 例

    ```js
    let hoge = {name : "Hoge"};
    const weakMap = new WeakMap();

    weakMap.set(hoge, "...");
    hoge = null;

    console.log(weakMap); //{} 
    ```

    ### weakMapがサポートしていないメソッド
    - 繰り返し(`.keys() .values() .entries()`)
    - サイズ取得(`.size`)

    ### サイズ等が取得できない理由
    - ガベージコレクションのタイミングはV8等のJSエンジンが決定する
    - そのため現在の要素数はわからない

    ### 使用シーン(cache)
    - ユーザ情報の保持(不要なユーザを削除して、メモリを解放可能)

## Q120. WeakSetとSetとの差異を理解していますか?

??? success
    ### WeakSet
    - オブジェクトのみ追加可能
    - `size`, 繰り返し関連のメソッドはサポート外
    - オブジェクトに対する参照がなければ削除

    ```js
    const visitedSet = new WeakSet();
    let hoge = {name : "Hoge"};
    let fuga = {name : "Fuga"};

    visitedSet.add(hoge);
    visitedSet.add(fuga);

    hoge = null;

    visitedSet.has(hoge); //false
    ```

## Q121. 通常のObjectもkeys, values, entriesを持っていることを知っていますか?

??? success
    ### Object.keys(obj)
    - `key`の配列を返す
    ### Object.values(obj)
    - `value`の配列を返す
    ### Object.entries(obj)
    - `[key, value]`の配列を返す

    ### mapとの違い
    - `map.keys()`は`iterable`
    - `Object.keys(obj)`は本物のArray

    ### つまり?
    - 配列になるので`map`などのメソッドを使えるようになる
    - `length`ももちろん使える

    ```js
    const map = new Map([
      ["name", "hoge"],
      ["age", 30],
    ]);
    map.keys(); //MapIterator {'name', 'age'}

    const obj = {
      name : "hoge",
      age : 30,
      isObj : true
    };

    Object.keys(obj);   //['name', 'age', 'isObj']
    Object.values(obj);   //['hoge', 30, true]
    Object.entries(obj);  //[['name','hoge'],['age':30],['isObj':true]]
   
    ```

    ### obj.keys()ではなく、Object.keys(obj)と呼ぶわけ
    - 複雑な独自のメソッドを定義しているオブジェクトにも対応できるようにするため

    ### 注意点
    - `Object.keys`等は、`Symbol`プロパティを無視する
    - 代替として`Reflect.ownKeys(obj)`などがある

## Q122. Objectに対して配列のメソッドを適用したい時、何が使えるか分かりますか？

??? success
    ### 手順
    1. `Object.entries(obj)`を用いて`obj`から`key/value`のペアを生成する
    2. その配列を対象に、メソッドを使用
    3. `Object.fromEntries(array)`で`Object`に戻す

    ```js
    const cart = {
      banana : 1,
      orange : 2,
      meat : 3
    };

    const doublePrices = Object.fromEntries(
      //listに対して分割構文を用いている。
      //array(obj)を第1引数としてmapに渡したことになる
      //別解: Object.entries(cart).map(i => [i[0], i[1]*2])
      Object.entries(cart).map(([key, value])=> [key, value * 2])
    );

    console.log(doublePrices.banana); //2
    ```