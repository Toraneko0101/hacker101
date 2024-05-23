# クイズ(上級1:prototype)

## Q1. プロトタイプチェーンについて知っていますか?

??? success
    ### プロトタイプチェーン
    - `obj`内に、取得したい`property`が存在しないとき、JSは自動的に`[[Prototype]]`を参照し、プロパティを探しに行く
    - これをプロトタイプチェーンと呼ぶ

    ### 継承
    - 他のオブジェクトのメソッドやプロパティを利用できるので、プロトタイプ継承と呼ばれることもある


## Q2. `__proto__`を使って、[[Prototype]]を手動でセットできることを知っていますか?

??? success
    ### 概要
    - `obj.__proto__ = obj2`は
    - `obj.[[Prototype]] = obj2`を意味する。

    ### [[Prototype]]へのセット

    ```js
    let animal = {
      eats: true
    };

    let rabbit = {
      jumps: true
    };

    rabbit.__proto__ = animal;
    //rabbit.[[Prototype]] = animal
    ```

    ### 使用例

    ```js
    console.log(rabbit.eats);
    //1. 最初にrabbit内のpropertyを参照
    //2. eatsプロパティが存在しないため、[[Prototype]]参照に従い、animalのpropertyを探しに行く
    //3. 見つかったのでこれを参照する
    ```

    ```mermaid
    graph BT;

    A["rabbit\n-----\n jumps: true"] --> |"[[Prototype]]"| B["animal\n-----\n eats: true"]

    ```

    ### つまり?
    - `__proto__`で指定した(`[[Property]]`で指定されている)対象が、自分にないプロパティやメソッドを持っている場合、それは使用可能である。

    ### メソッドを[[Prototype]]から得る例

    ```js
    const animal = {
      eats: true,
      walk(){
        console.log("Animal walk");
      }
    }

    const rabbit = {
      jumps: true,
      __proto__ : animal,
    }

    rabbit.walk(); //Animal walk
    ```

    ```mermaid
    graph BT;

    A["rabbit\n-----\n jumps: true"] --> |"[[Prototype]]"| B["animal\n-----\n eats: true\n walk: function"]

    ```

## Q3. プロトタイプチェーンが連鎖可能であることを知っていますか?

??? success
    ### ない場合は、更に上位の[[Prototype]]を探しに行く

    ```js
    const living = {
      address : "Earth",
    }

    const animal = {
      isMove : true,
      walk(){
        alert("Animal walk!!!");
      },
      __proto__ : living,
    }

    const rabbit = {
      isJump : true,
      __proto__ : animal,
    }

    rabbit.walk(); //Animal Walk!!!; (animalから)
    console.log(rabbit.address); //Earth (livingから)
    ```

    ```mermaid
    graph BT;

    A["rabbit\n-----\n isJump: true"] --> |"[[Prototype]]"| B["animal\n-----\n isMove: true\n walk: function"]
    B --> |"[[Prototype]]"| C["living\n-----\n address"]

    ```

## Q4. `__proto__`にプリミティブが指定できないことや、参照の際の注意点を知っていますか

??? success
    ### 参照を循環させることはできない
    - 無限に参照するため

    ```js
    const user = {};
    const anotherUser = {};
    user.__proto__ = anotherUser;
    anotherUser.__proto__ = user; //Cyclic __proto__ value
    ```

    ### __proto__の値はオブジェクトかnull
    - プリミティブなら、無視される。(意味を持たない)

    ```js
    const user = {};
    user.__proto__ = "hoge"
    ```

    ### 多重継承はできない
    - `[[Prototype]]`は常に1つ

## Q5. `__proto__`が時代遅れであることを知っていますか?

??? success
    ### [[Prototype]]のgetter/setter
    - `__proto__`は`[[Prototype]]`を取得/設定できるので、`getter/setter`とみなすことができる

    ### 現在
    - 現在は`Object.getPrototypeOf`関数や
    - `Object.setPrototypeof`関数を使用することが推奨されている

    ### サポート対象
    - `__proto__`はブラウザのみでサポートされる
    - .....と言っているものの、`Node.js`のようなサーバサイドでも、安全に使うことができる

## Q6. プロトタイプはプロパティを読む（探す）ためだけに使われ、書き込み/削除操作には使用されないことを知っていますか?

??? success
    ### 書き込み
    - 書き込み時は、プロトタイプチェーンを辿ることなく、操作対象のオブジェクトにプロパティ名とその値を生成する
    - 一度書き込めば、オブジェクト内にプロパティが作られるため、次の読み込みからは、[[Prototype]]を辿る必要がない

    ```js
    const animal = {
      isMove : true,
      walk(){alert("Animal Walk!!!")},
    }

    const rabbit = {
      __proto__ : animal,
    }

    rabbit.walk = function(){
      alert("Rabbit Walk!!!")
    };

    rabbit.walk(); //Rabbit Walk!!!
    ```

    ### 削除
    - [[Prototype]]を辿って消されることはない

    ```js
    const animal = {
      isMove : true,
      walk(){alert("Animal Walk!!!")},
    }

    const rabbit = {
      __proto__ : animal,
    }

    delete rabbit.walk;
    console.log(rabbit.walk()); //Animal Walk!!!
    ```

## Q7. [[Prototype]]先のsetterを通じて、プロパティを書き込んだ場合、継承元と、継承先のプロパティの状態が分岐することを理解していますか?

??? success
    ### 基本
    - プロパティへの書き込みはプロトタイプチェーンを利用しない(プロトタイプ上にsetterが定義されている場合は別。本オブジェクトのプロパティとして直接代入される代わりに、これが呼び出される)

    ### 例(getter/setter)

    ```js
    const user = {
      name : "John",
      surname : "Smith",

      set fullName(value){
        [this.name, this.surname] = value.split(" ");
      },

      get fullName(){
        return `${this.name} ${this.surname}`;
      }
    };

    const admin = {
      __proto__ : user,
      isAdmin: true
    };

    //1. admin.fullNameはないので[[Prototype]]から辿る
    //2. 読み込みなのでgetterを利用する
    //2. thisは呼び出したオブジェクトなので、admin
    //3. admin.nameとadmin.surnameはないので、[[Prototype]]を辿る
    console.log(admin.fullName);

    //1. 本来、書き込み時はプロトタイプチェーンを辿らない
    //2. しかし、継承元にsetterがあるので書き込み時だが[[Prototype]]を辿り、setterが呼び出される
    //3. setterを呼び出しているオブジェクトは、adminなので、thisはadmin
    //4. admin.name, admin.surnameが定義される
    admin.fullName = "Taro Yamada";

    //1. admin.fullNameはないので[[Prototype]]から辿る
    //2. 読み込みなのでgetterを利用する
    //3. 今やadmin.nameとadmin.surnameは定義されているので自身から取得
    console.log(admin.fullName); //Taro Yamada
    console.log(admin); //{isAdmin: true, name: 'Taro', surname: 'Yamada'}

    //1. getterを利用
    //2. thisは呼び出したオブジェクトなのでuser
    //3. user.name, user.surnameの値を利用する
    console.log(user.fullName); //John Smith
    ```

    ### まとめ
    - `getter/setter`の場合、話は別で、存在しないプロパティに対して読み書きをすると、`[[Prototype]]`を参照して呼び出す

    ### thisについて
    - `this`は常にdotの前のオブジェクトを指す
    - どの`[[Prototype]]`から呼び出されたかは関係がない

## Q8. for ... in ループが継承したプロパティも繰り返し処理を行うことを理解していますか?(hasOwnとinの違いについて触れる)

??? success
    ### for ... in

    ```js
    const animal = {
      isMove : true,
    }

    const rabbit = {
      isJump : true,
      __proto__ : animal,
    };

    for(let key in rabbit) console.log(key);
    //isJump, isMove
    ```

    ### Object.keys等は継承したプロパティを無視する

    ```js
    const animal = {
      isMove : true,
    }

    const rabbit = {
      isJump : true,
      __proto__ : animal,
    };

    Object.keys(rabbit); //["isJump"]
    Object.values(rabbit); //[true]
    Object.entries(rabbit); //[["isJump", true]]
    ```

    ### 継承したプロパティを除きたい場合
    - `obj.hasOwnProperty(key)`を利用する
    - `.hasOwnProperty`は継承したものを含まない

    ```js
    const animal = {
      isMove : true,
    }

    const rabbit = {
      isJump : true,
      __proto__ : animal,
    };

    for(const key in rabbit){
      if(rabbit.hasOwnProperty(key)) console.log(key);
      //isJump
    }
    ```

    ### .hasOwnPropertyも継承したものであるはずなのに、なぜfor ... inで出力されないのか
    - 列挙不可だから
    - `for ... in`は列挙可能なプロパティのみをリストする
    - `hasOwnProperty`は`Object.prototype`の他のすべてのプロパティと同様`enumerable : false`フラグを持っている

    ### 継承元のenumerableをfalseにすると......

    ```js
    //列挙対象ではないのでfor ... inで引っかかることはなくなった
    const animal = {
      isMove : true,
    }

    Object.defineProperty(animal, "isMove", {
      enumerable: false,
    })

    const rabbit = {
      isJump : true,
      __proto__ : animal,
    };

    for(const key in rabbit){
      console.log(key); //isJump
    }
    ```

    ### hasOwnについて
    - `hasOwnProperty()`は、`obejct`が`hasOwnProperty()`という名前のメソッドを持っていた場合、書き換えられるというデメリットを抱えている。
    - そのため以下の様に使われていた

    ```js
    Object.prototype.hasOwnProperty.call(example, "property");
    ```

    - しかしこれは冗長
    - よって、`ES2022`以降では`hasOwn`が使われる

    ```js
    const example = {
      property: "hoge",
    }

    console.log(Object.hasOwn(example, "property")); //true
    ```

    ### inによるプロパティ存在チェックと、hasOwnの違い
    - `in`はプロトタイプチェーンを検索する
    - `hasOwn`は検索しない
 
## Q9. コンストラクタ関数のprototypeプロパティがオブジェクトを指す場合、new演算子を用いてインスタンスを作成すると、[[Prototype]]に該当オブジェクトが割り当てられることを知っていますか?

??? success
    ### F.prototype(コンストラクタ.prototype)
    - `null`でもいい。継承元がないことを意味する
    - プリミティブを設定した場合、動作しなくなる

    ```js
    const animal = {
      isMove : true,
    }

    function Rabbit(name){
      this.name = name;
    }

    Rabbit.prototype = animal;

    let whiteRabbit = new Rabbit("White Rabbit");
    //rabbit.__proto__ = animal

    console.log(whiteRabbit.isMove); //true
    ```

    ```
    whileRabbitの継承元はanimal

    Rabbit  --prototype--> animal
                              ↑  [[Prototype]]
                          whiteRabbit
    ```

    ### new F()で作成後に、F.prototypeを変更した場合
    - 既に存在するオブジェクトは古いものを参照したまま
    - 新しく作られるものは、変更後のものを[[Prototype]]として参照する

## Q10. デフォルトの場合、F.prototypeはコンストラクタ関数自身を指すことを知っていますか?

??? success
    ### デフォルト

    ```js
    function Rabbit(){};
    console.log(Rabbit.prototype);
    //{constructor: Rabbit}

    console.log(Rabbit.prototype.constructor === Rabbit) //true


    ```

    ```
    Rabbit --prototype--> [F.prototype]
          <-----------constructor
    ```

    ### デフォルトの場合の[[Prototype]]
    - new F()でインスタンスを作成すると、[[Prototype]]に該当オブジェクト、つまり`{constructor : F}`が割り当てられる
    - 生成されたインスタンスは、`constructor`プロパティを持っていないので、`インスタンス.constructor`とした場合、[[Prototype]]が参照され、その値は`F`である

    ```js
    //従って以下の様に、生成されたインスタンスからconstructor経由で、更にインスタンスを生成できる

    function Rabbit(name){
      this.name = name;
      alert(name);
    }

    let whiteRabbit = new Rabbit("White Rabbit");
    let blackRabbit = new whiteRabbit.constructor("Black Rabbit");

    //prototype内の情報が[[Prototype]]に転写される確認
    whiteRabbit.__proto__ === Rabbit.prototype

    ```

## Q11. インスタンスからconstructor経由でコンストラクタにアクセスするメリットとデメリットが分かりますか?

??? success
    ### メリット
    - オブジェクトは持っているが、どのコンストラクタからそれが生成されたか分からない場合
    - インスタンス経由で同種のオブジェクトを作成できる

    ### デメリット
    - デフォルトプロトタイプ全体が置き換えられた場合、`constructor`は消失する

    ```js
    function Rabbit(name){
      this.name = name;
      alert(name);
    };
    Rabbit.prototype = {
      jumps : true,
    }

    //[[Property]] jumps : true
    let whiteRabbit = new Rabbit("whiteRabbit");


    console.log(whiteRabbit.constructor == Rabbit); //false
    
    //constructorが関数自身を指していないので、
    //以下の様にしても生成できない
    new whiteRabbit.constructor("blackRabbit");
    //String {'blackRabbit'} === Object("blackRabbit")
    //プロトタイプチェーン上のconstructorを探していくと
    //今やRabbitにconstructorはないのでObject.prototypeに行きつく
    ```

    ### デメリットの回避策(方法1)
    - 単に追加する
    - `constructor`関連のプロパティは残す

    ```js
    function Rabbit(name){
      this.name = name;
      alert(name);
    };

    //参照内のオブジェクトを書き換えただけなので問題ない
    Rabbit.prototype.isJump = true;

    let whiteRabbit = new Rabbit("whiteRabbit");

    let blackRabbit = new whiteRabbit.constructor("blackRabbit");

    console.log(blackRabbit.name); //blackRabbit
    ```

    ### 方法2(constructorを含めて再定義する)

    ```js
    function Rabbit(name){
      this.name = name;
      alert(name);
    };

    Rabbit.prototype = {
      isJump : true,
      constructor : Rabbit,
    }

    let whiteRabbit = new Rabbit("whiteRabbit");

    let blackRabbit = new whiteRabbit.constructor("blackRabbit");

    console.log(blackRabbit.name); //blackRabbit    
    ```

## Q12. 通常のオブジェクトにおけるprototypeが意味のないものであることを知っていますか?

??? success
    ### 使用例
    - 特別なのはコンストラクタ関数に設定されており
    - new演算子で呼び出された場合のみ

    ```js
    const user = {
      name : "John",
      prototype : "Neko",
    }

    user.prototype;  //Neko
    ```

## Q12. obj = {}が、obj = new Object()と同一であることを知っていますか?

??? success
    ### new F()の場合.....
    - new演算子を用いてインスタンス(`obj`)を生成すると、その`[[Prototype]]`には`Object`が与えられる

    ### 例

    ```js
    const obj = {};
    console.log(obj); //[object Object]
    ```

    ### 例の説明
    - `[object Object]`はどのように導き出されたのか
    - おそらく`toString()`だろうと当たりが付く
    - しかし、`obj`には`toString()`というメソッドがない
    - つまり、`[[Prototype]]`を辿っている
    - 結果として、これは`Object.prototype.toString()`によるものである

    ### Object.prototype
    - `constructor`や`toString()`等を含む巨大なオブジェクト
    - 全てのオブジェクトが持つ、最も基本的なプロトタイプ
    - `Object.prototype`のプロトタイプは`null`でありプロトタイプチェーンの終わりに位置する

    ### {}時の動作
    - `F()`を`new`演算子で呼び出すと、`F.prototype`がインスタンスの`[[Prototype]]`になる
    - `Object()`を`new`演算子で呼び出すと、`Object.prototype`がインスタンスの`[[Prototype]]`になる

    !!! warning
        - コンストラクタそのものがインスタンスの`[[Prototype]]`になるわけではなく、コンストラクタ.prototypeが対象であるので注意する事

    ```js
    const obj = {};
    console.log(obj.__proto__ === Object.prototype); //true
    //ない場合は自動で、[[Property]]を辿る
    //__proto__は[[Property]]のgetter/setterのようなもの
    console.log(obj.toString === obj.__proto__.toString); //true
    console.log(obj.toString === Object.prototype.toString); //true

    ```

## Q13. Array等の他の組み込みオブジェクトもプロトタイプにメソッドを保持していることを知っていますか?

??? success
    ### [1,2,3]はArrayのインスタンス
    - 配列を作る際、`new Array()`が内部で使われる
    - よって、作成された配列は、その`[[Prototype]]`で`Array.prototype`を参照する

    ```js
    const arr1 = new Array(1,2,3)
    const arr2 = [1,2,3];
    ```

    ### 使用例(Array)

    ```js
    const arr = [1,2,3];
    console.log(arr.__proto__ === Array.prototype); //true
    console.log(arr.__proto__.__proto__ === Object.prototype); //true
    console.log(arr.__proto__.__proto__.__proto__ === null); //true

    ```

    ### 使用例(Function)

    ```js
    function f(){}; //内部でnew Functionが使われる

    console.log(f.__proto__ === Function.prototype); //true
    console.log(f.__proto__.__proto__ === Object.prototype); //true
    ```

    ### 使用例(Date)

    ```js
    const date = new Date();
    console.log(date.__proto__ === Date.prototype); //true
    console.log(date.__proto__.__proto__ === Object.prototype); //true
    ```

    ```mermaid
    graph LR;

    A[date]
    B[Date.prototype]
    C[func]
    D[Function.prototype]
    E[arr]
    F[Array.prototype]
    G[Object.prototype]
    A --> B
    C --> D
    E --> F
    B --> G
    D --> G
    F --> G
    ```

## Q14. プロトタイプチェーンのメソッドが重複した時、自身に近い方が採用されることを知っていますか?

??? success
    ### メソッドが見つかれば、これ以上の探索は行わない

    ```js
    let arr = [1,2,3];
    console.log(arr); 
    //Array.prototype.toString()が使われる
    //Object.prototype.toString()ではない
    ```

## Q15. プリミティブのプロパティへアクセスしようとした場合のプロトタイプチェーンについて理解していますか?

??? success
    ### ラッパー
    - 中級で述べた通り、プリミティブのプロパティへアクセスしようとした場合、ラッパーオブジェクトが作られる

    ### ラッパーオブジェクトの作られ方
    - 組み込みのコンストラクタから以下の形で作られる
    - `new String()`
    - `new Number()`
    - `new Boolean()`

    ### つまり?
    - 一時的なラッパーオブジェクトは`[[Prototype]]`として`String.prototype`等へアクセス可能
    - 例として、`Number.prototype`には`toFixed()`がある
    - ラッパーオブジェクトは`[[Prototype]]`を通じて操作を行い、返り値としてプリミティブを返して消滅する

    ### 例

    ```js
    console.log(3.1.__proto__ === Number.prototype); //true
    console.log(3.1.__proto__.toFixed === Number.prototype.toFixed); //true
    console.log(3.1.__proto__.__proto__ === Object.prototype);//true
    ```

    !!! warning
        - `null`や`undefined`はオブジェクトラッパーを持たない
        - 従って利用可能なメソッドやプロパティもない
        - プロトタイプも持たない

## Q16. String.prototype等のネイティブプロトタイプを変更すると、すべての文字列操作の際に影響が出ると理解していますか?

??? success
    ### 新しい組み込みメソッドを追加できる?
    - 文字列のラッパーは、`[[Prototype]]`で`String.prototype`を参照する
    - つまり、`String.prototype`自体を変更すると?

    ```js
    String.prototype.show = function(){
      alert(this); 
      //dotの前のオブジェクトは"Neko!!!"のラッパー
      //おそらくString.prototype.toString()は文字列をすべて出力するような仕組みになっている。
      //native codeについてわかったら後述
    }

    "Neko!!!".show(); //Neko!!!
    ```

    - function等でもそれは可能

    ```js
    //全ての関数実行をms秒遅延
    Function.prototype.delay = function(ms){
      setTimeout(this, ms)
    }

    function f(){
      alert("neko");
    }

    f.delay(1000); //1秒後にnekoを表示
    ```

    - 引数を許容する場合

    ```js
    Function.prototype.delay = function(ms){
      let f = this;
      return function(...args){
        setTimeout(()=> f.apply(this, args), ms);
      }
    } 

    function mul(a, b){
      console.log(a*b)
    }

    mul.delay(1000)(32, 46);
    ```

    ### 上記は非推奨(組み込みプロトタイプを拡張してはならない)
    - プロトタイプはグローバルなので衝突が発生する
    - 2つのライブラリが同じメソッドを追加した時のことを考える
    - 上書きが行われ、収拾がつかなくなる

    ### Polyfillの場合は許される
    - まだ、JSエンジンでサポートされていないが、ECMAscriptの仕様には存在する場合

    ```js
    //本来は存在する。native codeとして
    // "neko".repeat(3); //nekonekoneko
    if(!String.prototype.repeat){
      String.prototype.repeat = function(n){
        //空配列の区切り文字としてあてがう
        return new Array(n+1).join(this);
      }
    }

    console.log("neko".repeat(3)); //nekonekoneko
    ```

## Q17. ネイティブプロトタイプを拡張してしまったために起こった、SmooshGate事件について知っていますか?

??? success
    ### モンキーパッチ
    - ネイティブプロトタイプを拡張するような使い方はモンキーパッチと呼ばれる
    - これは前方互換性を損なっている
    - たとえば将来`Function.prototype`が`delay`というメソッドをサポートしたとして、それを書いていると、サポート時にコードが壊れる可能性がある

    ### SmooshGate事件
    - JSに`Array.prototype.flatten`という機能が追加される際に起こった問題
    - Firefoxブラウザの`Nightly`(開発中の試験用プラットフォーム)に機能が導入されると著名なウェブサイトが動作しなくなった

    ### 原因
    - 広く普及していた`MooTools`ライブラリで`.flatten`が使われていたこと
    - これ自体は原因ではないが、ライブラリで使われていた`.flatten`の仕様は導入された機能と異なっていた(depthなしで無限深度にflat化していた)

    ### 対策はしていた
    - `MooTools`は`flatten`がネイティブに実装されているかにかかわらず、`MooTools`の`flatten`でオーバーライド(メソッドを再定義)するようにしていた

    ### しかし......
    - `MooTools`は全てのカスタム配列メソッドを`for...in`を用いて`Elements.prototype`にコピーしていた
    - ※ここで、`Elements`は`MooTools`固有のAPI
    - `for ... in`は列挙可能なオブジェクトを[[Prototype]]を遡って(プロトタイプチェーンを辿りながら)反復する
    - ネイティブプロトタイプは通常`enumerable : false`なので反復されない
    - ネイティブプロトタイプを拡張した場合、それは`enumerable : true`であるため列挙される
    - しかし、列挙不可能なプロパティを上書き(オーバーライド)した場合、そのプロパティは`enumerable : false`のままである
    - 従って、ブラウザに`Array.prototype.flatten`が導入されたタイミングで、ネイティブプロトタイプの拡張は、上書きを意味することになり、`for ... in`の対象にならなくなった
    - よって、この瞬間`Elements.prototype.flatten`に依存するコードがすべて破損した

    ### 問題の例

    ```js
    //atは存在する
    String.prototype.at = function(){
      console.log("a");
    }

    String.prototype.hoge = function(){
      console.log("b");
    }

    const str1 = "";

    for(const s in str1){
      //hoge (atはenumerableなので出力されない)
      console.log(s);
    }
    ```

    ### 結局
    - 問題が大きくなることを懸念し、`flatten`は`flat`に名前を変えた。


    ### 根幹にある問題は
    - ネイティブプロトタイプを拡張したことにある

## Q18. メソッドの借用の柔軟性と、配列のメソッドを借用する場合、[]を使わない方法があることを理解していますか?

??? success
    ### そもそも
    - `[]`は内部的には`new Array()`によって生成されるので`[[Prototype]]`として`Array.prototype`にアクセス可能
    - `Array.prototype`の機能を生かして、配列の操作を行う
    - ここで`Array.prototype.join`は、正しいインデックスがあるかと、`length`プロパティがあるかどうかしかチェックしない
    - そのため、`array-like`のプロパティとして、`Array.prototype.join`を代入してやると使えるようになる

    ### 使用例

    ```js
    const obj = {
      0 : "Neko",
      1 : "Inu",
      length : 2,
    }

    obj.join = Array.prototype.join;

    console.log(obj.join(":")); //Neko:Inu
    ```
    
    ### メソッドの借用の利点(柔軟性について)
    - 異なるオブジェクトから機能を混ぜることが可能
    - `__proto__`の場合だと、一度に1つのオブジェクトからしか継承できない

    ```js
    const obj = {
      0 : "Neko",
      1 : "Inu",
      length : 2,
    }

    const anotherObj = {
      hogeMethod : function(){
        console.log("hogeMethod called");
      }
    }

    obj.join = Array.prototype.join;
    obj.hogeMethod = anotherObj.hogeMethod;

    console.log(obj.join(":")); //Neko Inu
    obj.hogeMethod(); //hogeMethod called
    ```

## Q19. `__proto__`の代わりに使える便利なメソッド三種について知っていますか?

??? success
    ### Object.create(proto[, descriptors])
    - 新しいオブジェクトを作る。その際、
    - `proto`: `[[Prototype]]`としてセットする
    - `descriptors`: 任意のプロパティフラグをセットする

    ### Object.getPrototypeOf(obj)
    - `obj`の`[[Prototype]]`を返す

    ### Object.setPrototypeOf(obj, proto)
    - `obj`の`[[Prototype]]`に`proto`をセットする

    ### 使用例

    ```js
    const animal = {
      isMove :true
    }

    const rabbit = Object.create(animal);
    console.log(rabbit.isMove); //true

    console.log(Object.getPrototypeOf(rabbit) === animal);
    Object.setPrototypeOf(rabbit, Object.prototype); //true

    //Object.prototypeの[[Prototype]]はnull
    console.log(Object.getPrototypeOf(Object.getPrototypeOf(rabbit)) === null) //true

    ```

    ### descriptorを使った場合
    - `defineProperties`と同じようにできる

    ```js
    const animal = {
      isMove : true,
    };

    const rabbit = Object.create(animal, {
      isJump : {
        value : true
        writable : false,
        enumerable : true,
        configurable : true,
      }
    })


    ```

## Q20. [[Prototype]]を含む正確なコピーを行いたい場合、Object.createとgetPrototyepOfがつかえることを理解していますか?

??? success
    ### 使用例

    ```js
    "use strict";
    const obj = {
      name : "Tarou",
      surname : "Yamada",
      address : "Tokyo",
      get fullName(){
        return `${this.name} ${this.surname}`;
      },

      set fullName(value){
        [this.name, this.fullName] = value.join(" "); 
      },
    }

    Object.defineProperty(obj, "address", {
      writable : false,
    })

    const clone = Object.create(
      Object.getPrototypeOf(obj), //[[Prototype]]をコピー
      Object.getOwnPropertyDescriptors(obj),  
    );

    console.log(clone.fullName);
    clone.address = "Nagoya"; //Error
    ```

## Q21. `__proto__`がgetPrototypeOfとsetPrototypeOfに置き換えられた理由を知っていますか?

??? success
    ### 歴史
    - 2012年に`Object.create`が登場
    - それに伴い、ブラウザがプロトタイプの取得/設定ができる、`__proto__`を実装
    - 2915年に`Object.getPrototypeOf`と`Object.setPrototypeOf`が登場

    ### 補足(そもそも[[Prototype]]を置き換えない方がいい)
    - 通常、`[[Prototype]]`はオブジェクト作成時に一度だけ作られる。
    - その場で、プロトタイプを変更するのはとてもコストが高い
    - これは`__proto__`を使おうが、`setPrototypeOf`を使おうが変わらない

    ### __proto__について
    - `Object.prototype`のアクセサ
    - アクセスされるオブジェクトの内部の`[[Prototype]]`もしくは`null`のいずれかを暴露する

    ### __proto__の奇妙な例
    - オブジェクトのkeyが__proto__の場合、値が正しく保存されない

    ```js
    const obj = {};
    const key = prompt("input __proto__ or something", "__proto__");

    //__proto__はObject.prototypeのgetter/setterであることを思い出す
    //プロトタイプチェーン上にgetter/setterがある場合、そちらが優先され代入操作は無視される
    //setterの結果、__proto__はobjかnullである必要があるので、下記では何も起こらない
    obj[key] = "hoge";

    console.log(obj[key]);
    ```

    ### 上記は何を意味するのか
    - `__proto__`はキーとして使われる場合に安全ではない。

    ### 対策
    - `Object.create(null)`を用い、プロトタイプチェーン上から`getter/setter`を消す

    ```js
    const obj = Object.create(null);

    const key = prompt("input __proto__ or something", "__proto__");
    obj[key] = "some value";

    console.log(obj[key]); //some value
    ```

    ### 純粋な辞書オブジェクト
    - `Object.create(null)`で作った`[[Prototype]]`が`null`な辞書オブジェクト
    - `{}`よりシンプル
    - 欠点として`toString()`などの通常`Object.Prototype`等で使われる組み込みメソッドがない
    - しかし、連想配列（辞書）として使う場合問題はない

    ### 補足
    - `Object.keys()`などの`Object.something()`のメソッドは、プロトタイプを参照しているわけではなく、オブジェクトを引数にとるだけなので通常通り機能し続ける

## Q22. 自身のシンボリックまたは、文字列または、すべてのプロパティを返す、Object等のメソッドを知っていますか?

??? success
    ### Object.getOwnPropertySymbols(obj)
    - 自身のシンボリックプロパティ名の配列を返す
    ### Object.getOwnPropertyNames(obj)
    - 自身の文字列プロパティ名の配列を返す
    ### Reflect.ownKeys(obj)
    - 自身のシンボリック+文字列プロパティ名の配列を返す
    ### obj.hasOwnProperty(key)
    - `obj`が`key`という名前のプロパティを持つ場合に`true`

    ### 補足
    - `Object.keys(obj)`: 列挙可能な文字列プロパティ名
    - `Object.keys(values)`: 列挙可能な文字列プロパティ値
    - `Object.keys(entries)`: 列挙可能な文字列キーペア

    ### 使用例

    ```js
    const obj = {
      from : 0,
      to : 5,
      hoge : "neko",
      [Symbol.iterator](){
        this.current = this.from;
        return this;
      },

      next(){
        if(this.current <= this.to){
          return {done: false, value : this.current++};
        }
        return {done : true};
      }
    }

    Object.defineProperty(obj, "hoge",{
      enumerable : false,
    })

    console.log(
      Object.getOwnPropertySymbols(obj)
    ); //[Symbol(Symbol.iterator)]

    console.log(
      Object.getOwnPropertyNames(obj)
    ); //['from', 'to', 'hoge', 'next']

    console.log(
      Reflect.ownKeys(obj)
    ); //['from', 'to', 'hoge', 'next', Symbol(Symbol.iterator)]

    console.log(obj.hasOwnProperty("hoge")); //true

    console.log(Object.keys(obj)); //hogeがない
    // ['from', 'to', 'next']
    ```