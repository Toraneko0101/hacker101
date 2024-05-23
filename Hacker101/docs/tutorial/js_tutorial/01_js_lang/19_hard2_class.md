# クイズ(上級2:クラス)

## Q23 クラス(Class)の基本的な意味を知っていますか?

??? success
    ### 概要
    - クラスはオブジェクト生成
    - プロパティの初期値の提供
    - メソッドの実装
    - 等の役割を持つ

    ### コンストラクタ関数ではだめなのか?
    - `class`はもっと高度
    - 完全な糖衣構文ではない

## Q24. classの基本構文とその意味が分かりますか?

??? success
    ### 基本構文
    - `constructor()`: `new`演算子により呼び出され、オブジェクトの初期化に使用する

    ```js
    class MyClass{
      constructor(){}
      method1(){}
    }
    ```

    ### 使用例

    ```js
    class User{
      constructor(name){
        this.name = name;
      }

      sayHi(){
        console.log(this.name);
      }
    }

    const user = new User("Hoge");
    user.sayHi(); //Hoge
    ```

    ### 注意点
    !!! warning
        - 使用例からもわかる通り、クラスメソッドの間にカンマは不要。
        - 書くと構文エラーになる

    ### 挙動の説明
    - `class`：クラス宣言は関数を作成する。
    - 関数部分は、`constructor`メソッドが担う
    - `constructor`がない場合は、関数部分は空となる。つまり、`function User(){}`
    - __クラスのメソッド(上記でいうと`sayHi`)は、`F.prototype`に格納される__

    ### つまり、インスタンスを作成すると
    - `F.prototype`を思い出す
    - `prototype`にはメソッドが含まれているので、インスタンスは`[[Prototype]]`を辿ってそれを使うことができる

    ```js
    typeof User; //function
    ```

    ### 挙動の確認

    ```js
    class User{
      constructor(name){this.name = name;}
      sayHi(){console.log(this.name);}
    }

    console.log(typeof User); //fuinction

    console.log(User === User.prototype.constructor); //true
    console.log(User.prototype.sayHi); 
    // ['constructor', 'sayHi']
    console.log(Object.getOwnPropertyNames(User.prototype));
    //ƒ sayHi(){console.log(this.name);}
    ```

## Q25. コンストラクタ関数とClassの重要な違いを知っていますか?

??? success
    ### 特別な内部プロパティ
    - `class`キーワードを用いて生成された関数は`[[IsClassConstructor]]:true`という内部プロパティを持つ
    - この内部プロパティが`true`である場合、`new`無しで呼び出せなくなる(コンストラクタ関数は呼び出せる)

    ### クラスメソッドは列挙不可
    - コンストラクタ関数で作った場合: 明示無しだと列挙可能
    - クラス定義は`prototype`にあるすべてのメソッドに対して、`enumerable:false`と設定する
    - これにより、`for ... in`で反復するのが楽になる

    ### use strict
    - クラス構造内のコードは自動的にstrictモードになる

    ### 使用例
    - コンストラクタ関数

    ```js
    //関数をプロトタイプに設定するわけではない
    //コンストラクタ関数で作成されたインスタンスは、コンストラクタに書かれた関数を使う際、[[Prototype]]経由無しでアクセス可能
    function User(name){
      this.name = name;
      this.sayHi = ()=>{console.log("Hi")};
    };

    const user = new User("neko");

    for(const key in user){
      console.log(key); //name ,sayHi
    }

    console.log(user); //User {name: 'neko', sayHi: ƒ}

    ```

    - Class

    ```js
    class User{
      constructor(){
        this.name = name;
      }

      sayHi(){
        console.log("hi");
      }
    }

    const user = new User("neko");
    
    for(const key in user){
      console.log(key); //name
    }

    console.log(user); //User {name: 'hoge'}
    ```

    ### なお、constructorに書いた場合は通常通り
    - `prototype`内に設定しているわけではないので

    ```js
    class User{
      constructor(){
        this.name = name;
        this.sayHi = ()=>{console.log("hi");}
      }
    }

    const user = new User("neko");
    
    for(const key in user){
      console.log(key); //name, sayHi
    }

    console.log(user); //User {name: 'hoge', sayHi: ƒ}
    ```

## Q26. まるで関数式の様に、クラスも式の中で定義し、渡したり、返却も可能であることを知っていますか?

??? success
    ### クラス式

    ```js
    const User = class {
      sayHi(){
        alert("Hello");
      }
    }
    ```

    ### 名前付きクラス式

    ```js
    let User = class MyClass{
      sayHi(){
        console.log(MyClass);
      }
    }

    const user = new User();
    User = null;

    console.log(user.sayHi())

    /*
    class MyClass{
      sayHi(){
        console.log(MyClass);
      }
    }
    */
    ```

    ### 新しいクラスを返す

    ```js
    function makeClass(phrase){
      return class {
        sayHi(){
          alert(phrase);
        }
      }
    };

    let User = makeClass("Hello");
    const user = new User()
    user.sayHi(); //Hello
    ```

## Q27. classにgetter/setterを作成できることを知っていますか?

??? success
    ### 内部から見ればメソッドの一種なので
    - `F.prototype`に`getter/setter`を作成しているのと同じ
    - 作成されたインスタンスは、`[[Prototype]]`経由で`getter/setter`を利用する

    ### 使用例
    - コンストラクタ関数内に`get name()`等を書くことはできない

    ```js
    class User{
      constructor(name){
        //setterを呼び出す
        //thisを用いているので、インスタンスにプロパティが作成される
        this.name = name;
      }

      get name(){
        return this._name;
      }

      set name(value){
        this._name = value;
      }
    }

    let user = new User("Neko");
    console.log(user.name); //Neko
    console.log(user); //{_name : "Neko"}

    Object.getPrototypeOf(user);
    /*
    {constructor: ƒ}
    constructor: class User
    name: (...)
    get name: ƒ name()   // ----> 他のメソッドと同じように、prototypeに含まれている
    set name: ƒ name(value)
    [[Prototype]]: Object
    */
    ```

## Q28. classのメソッド名を[]を利用し、動的に作成できることを知っていますか?

??? success
    ### 使用例

    ```js
    class User{
      ["say" + "Hi"](){
        alert("Hello");
      }

      [`ID:${Math.random()}`](){
        alert("Neko");
      }
    }

    const user = new User();
    
    console.log(user.__proto__); 

    /*
    {
      constructor: ƒ, 
      sayHi: ƒ, 
      ID:0.07368778495316564: ƒ
    }
    */
    ```

    !!! warning
      - 計算されたメソッド名はクラス定義時点で1度だけ評価される
      - つまり、2つのインスタンスが計算された名前によって異なるメソッド名を持つことはない

## Q29. クラスはフィールド(任意のプロパティ)も持てることを知っていますか?

??? success
    ### クラスメソッドとの違い
    - メソッドと異なり`prototype`から参照されるわけではなく、個々のオブジェクトにセットされる
    - つまり、`enumerable: false`の対象になることもない

    ### 使用例

    ```js
    class User{
      name = "John"
    }

    let user = new User();
    console.log(user.name); //John
    ```

    ### 関数呼び出しで値を代入することも可能

    ```js
    function setAddress(country = "japan", city = "tokyo"){
      return `${country}:${city}`
    }
    class User{
      name = prompt("Input your name", "Anonymous");
      address = setAddress("japan", "Nagoya");
    }

    new User().address; //japan:Nagoya
    ```

## Q30. クラスフィールドを用いて、オブジェクトにバインドされたメソッドを作成できる事を知っていますか?

??? success
    ### 通常の場合
    - 関数を渡して、呼び出すと、オブジェクト無しで呼び出されたことになるので失敗する

    ```js
    class Button{
      constructor(value){this.value = value;}

      click(){
        console.log(this.value);
      }
    }

    let button = new Button("Hello");
    //ok
    button.click(); //Hello

    //ng
    setTimeout(button.click, 1000); //undefined;

    //修正方法
    setTimeout(()=> button.click(), 1000);
    ```

    ### クラスフィールドとして作成
    - アロー関数であるため`this`は自身が生成された時の実行コンテキストを参照する
    - クラス作成時の流れを参照するとわかるが、thisはインスタンス自身
    - つまり、`setTimeout`等に関数として渡しても、`this`はインスタンスを参照し続ける


    ```js
    class Button{
      //thisは新しく作成されたobjを指す
      constructor(value){this.value = value;}

      click = () =>{
        console.log(this.value);
      }
    }

    let button = new Button("hello");
    setTimeout(button.click, 100); //hello
    ```

    ### クラス作成時の流れ
    - `new`キーワードを使用してクラスを呼ぶ
    - 新しい空オブジェクトが作成。`this`はこのオブジェクトを指す
    - クラスフィールドの初期化式が評価され、その結果がオブジェクトに追加される。なお、`this`は新規オブジェクトを指す
    - 次に`constructor`メソッドが評価される
    - `this`が返される

## Q31. extendsキーワードを用いて、クラスの継承が可能であることを理解していますか?

??? success
    ### クラスの継承
    - 基底クラスの機能を取り入れた、派生したクラスを実装する
    - `extends`キーワードを使用する

    ### 例

    ```js
    class Animal{
      constructor(name){
        this.speed = 0;
        this.name = name;
      }

      turbo(speed){
        this.speed += speed;
        console.log(`${this.name} runs with speed ${this.speed}.`);
      }

      stop(){
        this.speed = 0;
        console.log(`${this.name} stopped.`)
      }
    }

    class Rabbit extends Animal{
      jump(){
        console.log(`${this.name} jumps!`);
      }
    }

    let rabbit = new Rabbit("White Rabbit");

    rabbit.turbo(5); //White Rabbit runs with speed 5.
    rabbit.turbo(4); //White Rabbit runs with speed 9.
    rabbit.jump(); //White Rabbit jumps!
    ```

    ### extendsキーワードの動作
    - 内部的にはプロトタイプチェーン
    - `A extends B`の場合、`B.prototype`を`A.prototype`の`[[Prototype]]`にセットする。

    ### メソッドチェーン図

    ```mermaid
    graph LR;
    
    A["Animal"]
    B["Animal.prototype"]
    C["Rabbit"]
    D["Rabbit.prototype"]
    E["new Rabbit"]

    A --> |prototype| B
    C --> |prototype| D
    E --> |"[[Prototype]]"| D
    D --> |"[[Prototype]]"| B

    ```

    !!! warning
        - `Animal.prototype`は`Rabbit.prototype`の`[[Prototype]]`
        - `Animal`が`Rabbit`の`[[Protoytpe]]`であるわけではない
        - これは`Object`と`Array`等の関係性と同じ。

        ```js
        
        //Rabbit.prototypeの[[Prototype]]はAnimal.prototype
        Object.getPrototypeOf(Rabbit.prototype) === Animal.prototype; //true

        Array.prototype.__proto__ === Object.prototype; //true
        ```

## Q32. extendsの後にクラス名を返す、関数呼び出しを指定することが可能であることを知っていますか?

??? success
    ### 呼び出しの結果クラスが変えればよい

    ```js
    function f(phrase){
      return class{
        sayHi() {console.log(phrase)}
      }
    }

    class User extends f("Hello"){};

    new User().sayHi(); //Hello
    ```

## Q33. 関数宣言とクラス宣言の違いを知っていますか?

??? success
    ### デッドゾーン
    - クラス宣言は、`let`や`const`のように一時的なデッドゾーンの制約を受けるので、宣言前には使用できない

    ```js
    new MyClass(); //Error

    class MyClass{};
    ```

## Q34. オーバーライドとそのやり方を知っていますか?

??? success
    ### オーバーライド
    - 親(継承元)メソッドと同名のメソッドを配置すると、`[[Prototype]]`を辿る必要がないため、子のメソッドが優先される

    ### superキーワード
    - `super.method()`とすると、子の中で、親のメソッドを呼び出せる
    - `super()`とすると、親のコンストラクタを呼び出せる

    !!! warning
        - `super()`はコンストラクタの内側でのみ使える

    ### 例

    ```js
    class Animal{
      constructor(name){
        this.speed = 0;
        this.name = name;
      }

      turbo(speed){
        this.speed += speed;
        console.log(`${this.name} runs with speed ${this.speed}.`);
      }

      stop(){
        this.speed = 0;
        console.log(`${this.name} stopped.`)
      }
    }

    class Rabbit extends Animal{
      jump(){
        console.log(`${this.name} jumps!`);
      }

      stop(){
        super.stop();
        this.jump(); //親の呼び出しの後でjumpする
      }
    }

    const rabbit = new Rabbit("White Rabbit");
    rabbit.turbo(5); //White Rabbit runs with speed 5.
    rabbit.stop(); 
    //White Rabbit stopped.
    //White Rabbit jumps!
    ```

## Q35. アロー関数がsuperを持たないこととその利点を理解していますか?

??? success
    ### 関数内から`super`を使用可能

    ```js
    class Animal{
      constructor(name){
        this.speed = 0;
        this.name = name;
      }

      turbo(speed){
        this.speed += speed;
        console.log(`${this.name} runs with speed ${this.speed}.`);
      }

      stop(){
        this.speed = 0;
        console.log(`${this.name} stopped.`)
      }
    }

    class Rabbit extends Animal{
      stop(){
        setTimeout(()=>{
          super.stop(); //外部の関数から取得するのでうまく動く
          console.log("stopped delay")
        }, 1000)
      }
    }
    

    let rabbit = new Rabbit("White Rabbit");
    rabbit.stop();
    //White Rabbit stopped.
    //stopped delay
    ```

## Q35. コンストラクタのオーバーライドとその注意点が分かりますか?

??? success
    ### 派生クラスが、constructorを持たない場合
    - 内部では以下の様になる

    ```js
    class Rabbit extends Animal{
      constructor(...args){
        super(...args);
      }
    }
    ```

    ### 説明
    - 全ての引数を渡して親の`constructor`を呼び出している

    ### 派生クラスが、constrcutorを持つ場合
    - __派生したクラスのコンストラクタは`this`を使う前に、`super()`を呼び出す必要がある__

    ### 理由
    - 派生コンストラクタ関数("derived constructor")は特別な内部プロパティ`[[ConstructorKind]]: "derived"`を持っている
    - これは`new`のふるまいに影響を与える
    - 通常、関数が`new`で実行される場合、空のオブジェクトが生成され、`this`に割り当てられる
    - しかし、このフラグがある場合、オブジェクトの生成(派生クラスインスタンスの生成)と`this`の割り当ては、親(継承元)コンストラクタが行う。
    - つまり、`super()`を呼ばない限り、`this`のオブジェクトは生成されず、結果、`super()`の前に派生コンストラクタ内で`this`の動作をすると、エラーが出る

    ### 使用例

    ```js
    class Animal{
      constructor(name){
        this.speed = 0;
        this.name = name;
      }
    }

    class Rabbit extends Animal{
      constructor(name, earLength){
        super(name);
        this.earLength = earLength;
      }
    }

    let rabbit = new Rabbit("White Rabbit", 20);
    console.log(rabbit.name); //White Rabbit
    ```

## Q36. クラスフィールドもオーバーライドできることを知っていますか?(またその際のトリッキーな振る舞いも)

??? success
    ### 一見、不可解に思える例
    - オーバーライドされたフィールドが、親コンストラクタの中で使用されている場合に生じる。

    ```js
    class Animal{
      name = "animal";

      showName(){
        alert("animal");
      }

      constructor(){
        alert(this.name);
        this.showName();
      }
    }

    class Rabbit extends Animal{
      name = "rabbit";

      showName(){
        alert("rabbit");
      }

      //constructor(){
      //  super();
      //  alert(this.name); //rabbit
      //}
    }

    new Animal(); //animal, animal
    new Rabbit(); //animal, rabbit
    ```

    ### 原因
    クラスフィールドは以下のタイミングで初期化される

    - 基底クラスのコンストラクタの前
    - 派生クラスの`super()`の直後

    ### つまり?
    1. 基底クラスのコンストラクタの前で、フィールドが初期化される`name = "animal"`
    2. 基底クラスのコンストラクタが実行される`alert(this.name) == animal`
    3. フィールドが初期化される`name = "rabbit"`

    ### 対策
    - メソッドを利用する(`[[Prototype]]`を利用するかしないかなので、初期化タイミングは関係ない)
    - `getter/setter`を利用する

## Q37. 技術的にsuperが内部でどのように動作しているか知っていますか?

??? success
    ### superの内部動作について考える
    - 親のmethodを呼び出す場合にはどうすればいいのか?
    - `this.__proto__`で可能だろうか?

    ### 一見うまくいくように見える

    - 単純なオブジェクトの例

    ```js
    let animal = {
      name : "Animal",
      eat(){
        alert(this.name + " eats.");
      }
    };

    let rabbit = {
      __proto__ : animal,
      name : "Rabbit",
      eat(){
        this.__proto__.eat.call(this);
      }
    }

    rabbit.eat(); //Rabbit eats.
    /*
    1. rabbit.eat()内で、thisはrabbit
    2. rabbit.__proto__はanimal
    3. つまり、animal.eat.call(rabbit)
    4. animalのeatメソッドをthisはrabbitで呼び出す
    5. よって、rabbit.name + eats.が返る
    6. 結果は、Rabbit eats.
    */
    ```

    ### もう1つ追加してみる

    ```js
    let animal = {
      name : "Animal",
      eat(){
        alert(this.name + " eats.");
      }
    };

    let rabbit = {
      __proto__ : animal,
      eat(){
        this.__proto__.eat.call(this);
      }
    };

    let longEar = {
      __proto__  : rabbit,
      eat(){
        this.__proto__.eat.call(this);
      }
    }

    longEar.eat(); //Error
    /*
    1. longEar.eat()内でthisはlongEar
    2. longEar.__proto__はrabbit
    3. rabbit.eat.call(longEar)
    4. rabbit.eat()内でthisはlongEar
    5. つまり、
    6. longEar.__proto__.eat.call(longEar)
    7. longEar.__proto__はrabbit
    8. rabbit.eat.call(longEar)
    9. 以下、無限ループ
    */
    ```

    ### 原因
    - `this = longEar`に固定されるので、無限ループする

    ### 解決策と[[HomeObject]]
    - `[[HomeObject]]`: メソッドが定義された時、各メソッドに登録される。`[[HomeObject]]`の値はメソッドが属するオブジェクトとなる

    ### `super`について
    - `super`キーワードが呼ばれると、`[[HomeObject]].[[Prototype]]`が取得される
    - `[[Prototype]]`を辿りながら、親クラスのプロトタイプを探す。メソッドが見つかるまで再帰的にたどる。
    - `this`と異なり、`[[HomeObject]]`の値は、どのクラスのメソッド内にいるかで変わるので、現在どの位置にいるのかわかり、高次までチェーンを遡ることができる。

    ### 使用例

    ```js
    let animal = {
      name : "Animal",
      eat(){      //[[HomeObject]] == animal
        alert(this.name + " eats.");
      }
    }

    let rabbit = {
      __proto__ : animal,
      name : "Rabbit",
      eat(){    // [[HomeObject]] == rabbit
        super.eat(); //rabbit.[[Prototype]]からeat()を探す。(※rabbit.__proto__等でアクセスできるオブジェクト == animal からeat()を探す)
      }
    }

    let longEar = {
      __proto__ : rabbit,
      name : "Long Ear",
      eat(){    // [[HomeObject]] == longEar
        super.eat(); //LongEar.[[Prototype]]からeat()を探す
      }
    }

    longEar.eat(); // Long Ear eats.
    ```

## Q38. メソッドが[[HomeObject]]によってオブジェクトに束縛されているため、superを用いると、メソッドのコピーが上手くいかなくなることがあることを知っていますか?

??? success
    ### 一般的な関数の場合
    - オブジェクトにバインドされていないので、別の`this`で呼び出し可能

    ### メソッドの場合(superの使用あり)
    - `super`を使用するとうまくいかない
    - 理由は`super`が参照する`[[HomeObject]]`が、メソッド定義時に確定し、関数をコピーしても変わらないため

    ### メソッドの場合(superを使用しない)
    - `super`が`[[HomeObject]]`を記録しているのは変わらない
    - しかし、`super`を使用していないのでコピーしても挙動に違和感はない

    ### 使用例

    ```js
    let animal = {
      name : "animal",
      sayHi(){alert("Hello animal"); alert(this.name)}
    }

    let rabbit = {
      name : "rabbit",
      __proto__ : animal,
      sayHi(){super.sayHi();}
    }

    let plant = {
      sayHi(){alert("Hello plant");}
    }

    let tree = {
      name : "tree",
      __proto__ : plant,
      sayHi : rabbit.sayHi
    }

    tree.sayHi(); //Hello animal
    //super.sayHi()なので、plantをさす...とはならない
    //superは[[HomeObject]]を参照するため
    //これはコピーされても変わらない
    /*
    1. tree.sayHiはrabbitからコピーされている
    2. rabbitから作られているので、[[HomeObject]]はrabbit
    3. つまり、super.sayHi()は、rabbit.__proto__.sayHi()となるので、
    4. Hello animalが出力される
    */
    ```

    ### 結論
    - あるオブジェクトから別のオブジェクトへ、`super`を持つメソッドをコピーしてはならない

## Q39. オブジェクトでsuperを用いたい場合、`method : function()`という書き方が不適切なことを知っていますか?

??? success
    ### method: function(){}とした場合
    - `[[HomeObject]]`はセットされない
    - `[[HomeObject]]`を参照できないため`super`は使えない
    - つまり、メソッドとして定義されているかがポイント

    ### 正解
    - `method()`とする
  
    ### ダメな例
    - 関数式内では`[[HomeObject]]`が正しく設定されていないのでエラーが発生する。(function()なのでコンテキストが作られるため)

    ```js
    let animal = {
      eat : function(){
        console.log("animal");
      }
    }

    let rabbit = {
      __proto__ : animal,
      eat : function(){
        super.eat();
      }
    }

    rabbit.eat(); //Error
    ```

    ### 可能な例

    ```js
    let animal = {
      eat : function(){
        console.log("animal"); //superがないので関係ない
      }
    }

    let rabbit = {
      __proto__ : animal,
      eat(){
        super.eat();
      }
    }

    rabbit.eat();
    ```

## Q40. extendsがコンストラクタ関数のprototype間と、コンストラクタ関数自身の間の2種を継承することを分かっていますか?

??? success
    ### 使用例

    ```js
    class Rabbit extends Object{};
    let rabbit = new Rabbit();

    //コンストラクタ関数のprototype間
    console.log(
      Rabbit.prototype.__proto__ === Object.prototype
    );//true

    //コンストラクタ関数自身
    //これは組み込みには当てはまらない。Array.__proto__ === Object; //false
    console.log(
      Rabbit.__proto__ === Object
    ); //true

    //組み込みのオブジェクトコンストラクタとFunction.prototypeの関係
    console.log(
      Object.__proto__ === Function.prototype;
    ); //true

    //コンストラクタをnewで呼び出したときの動作
    console.log(
      rabbit.__proto__ === Rabbit.prototype
    ); //true

    ```

    ### 継承しなかった場合
    - 継承していなくても、`call`, `bind`等は使える

    ```js
    class Rabbit{};

    Rabbit.__proto__ === Function.prototype; //true
    Rabbit.__proto__.__proto__ === Object.prototype; //true
    ```

## Q41. staticキーワードを用いると、クラス全体にメソッドを割り当てることが可能であることを知っていますか?

??? success
    ### 使用例

    ```js
    class User{
      static staticMethod(){
        alert(this === User)
      }
    }

    User.staticMethod(); //true
    ```

    ### クラス名に直接プロパティとして割り当てることも可能
    - `this`はドットの前のオブジェクト、つまり、クラスコンストラクタ自身を指す
    - `(User.staticMethod())`等と呼ばれるので

    ```js
    class User{};

    User.staticMethod = function(){
      alert(this === User)
    }
    ```

    ### 使用シーン
    - クラスには属したい
    - 特定のオブジェクトには属させたくない
    - そんな関数を実装したいときに`static`を使う

    ### 使用例
    - 2つのオブジェクトを比較する関数を生成

    ```js
    class Article{
      constructor(title, date){
        this.title = title;
        this.date = date;
      }

      static compare(articleA, articleB){
        return articleA.date - articleB.date;
      }
    }

    let articles = [
      new Article("HTML", new Date(2004, 1, 1)),
      new Article("CSS", new Date(2004, 0, 28)),
      new Article("JS", new Date(2004, 3, 4)),
    ];

    //日付順にsort
    articles.sort(Article.compare);

    console.log(articles[0].title); //CSS
    ```

    ### 使用例2
    - インスタンスを生成するメソッドを生成

    ```js
    class Article{
      constructor(title, date){
        this.title = title;
        this.date = date;
      }

      static createTodays(){
        const today = new Date();
        return new this(`ArticleID: ${today.getFullYear()}-${today.getMonth() + 1}-${today.getDate()}`, today);
      }
    }

    const article = Article.createTodays();
    console.log(article.title); //ArticleID: 2024-2-2
    console.log(article.date); //Fri Feb 02 2024 17:57:02 GMT+0900 (日本標準時)
    ```

    ### その他使用例
    - 記事を管理して、検索、保存、削除を行うようにもできる

## Q42. staticキーワードを用いて、クラス共通で管理する静的プロパティが作成可能であることを知っていますか?

??? success
    ### 先頭にstaticを付けるだけ

    ```js
    class Article{
      static publisher = "Toraneko 0101";
    }

    //此方でも可
    Article.purpose = "learning";

    console.log(Article.publisher); //Toraneko 0101
    console.log(Article.purpose); //learning
    ```

## Q43. 静的プロパティとメソッドは、いずれも継承可能であることを知っていますか?

??? success

    ### 使用例

    ```js
    class Animal{
      static planet = "Earth";

      constructor(name, speed){
        this.name = name;
        this.speed = speed;
      }

      turbo(speed = 0){
        this.speed += speed;
        console.log(`${this.name} runs with speed ${this.speed}.`)
      }

      static compare(animalA, animalB){
        return animalA.speed - animalB.speed;
      }

    }

    class Rabbit extends Animal{
      jump(){
        alert(`${this.name} jumps!`)
      }
    }

    let rabbits = [
      new Rabbit("White Rabbit", 10),
      new Rabbit("Black Rabbit", 7),
    ]
    rabbits[1].turbo(5);

    rabbits.sort(Rabbit.compare); 

    console.log(Rabbit.planet); //Earth
    console.log(rabbits[0].name); //White Rabbit
    ```

    ### 説明
    - `Rabbit.compare`を呼び出すと、`Animal.compare`が呼び出される
    - `Rabbit.planet`を呼び出すと、`Animal.planet`が呼び出される
    - つまり、コンストラクタ同士の継承関係が`static`の継承に役立っている。

    ```js
    //静的メソッドとフィールドで効果を発揮する
    Rabbit.__proto__ === Animal; //true
    console.log(Rabbit.__proto__.planet); //Earth

    //通常のメソッドは、(コンストラクタの.prototypeに属するので)こっち
    Rabbit.prototype.__proto__ === Animal.prototype; //true
    console.log(Rabbit.__proto__.prototype === Animal.prototype);// true
    console.log(Animal.prototype)
    //{constructor: ƒ, turbo: ƒ}

    //インスタンスからも使える
    rabbits[0].__proto__ === Rabbit.prototype; //true

    //通常のフィールドは個々のインスタンス対象
    ```

## Q44. privage/protectedという単語と、それを使う意味合いを知っていますか?

??? success
    ### 内部インターフェース
    - クラスの他のメソッドからはアクセス可能
    - 外部からはアクセスできない

    ### 外部インターフェース
    - クラスの外部化もアクセス可能

    ### 内部インターフェースを使うわけ1(エラー対策)
    - 外部からの変更を意図していない場合、それが勝手に変更されるとエラーの要因になる
    - 内部インターフェースはそれを防止する
    - つまり、安全性のため

    ### 内部インターフェースを使うわけ2(シンプル)
    - 人々はシンプルなものを好む
    - 実装がどれだけ複雑であろうが、シンプルな機能と、ドキュメントがあれば、文句は言わない


    ### public
    - どこからでもアクセス可能
    - JSでは普通に書くとこうなる

    ### private
    - クラス内部からのみアクセス可能
    - 先頭に`#`を付ける

    ### protected
    - クラス及び、このクラスを継承したサブクラスの内部からアクセス可能
    - 言語レベルではJSにはないが、慣用的な導入は可能

## Q45. 慣用的なprotectedフィールド/メソッドの作り方を知っていますか?

??? success
    ### publicの例

    ```js
    class CoffeeMachine{
      waterAmount = 0;

      constructor(power){
        this.power = power;
        alert(`Created a coffee-machine, power: ${power}`);
      }
    }

    const coffeeMachine = new CoffeeMachine(100);
    coffeeMachine.waterAmount = 200;
    ```

    ### protectedの例
    - `waterAmount`と`power`を`protected`にする
    - `_`で初めて、外部からアクセスしないようにしてもらう
    - 代わりに関数を通じて取得する

    ```js
    class CoffeeMachine{
      _waterAmount = 0;

      setWaterAmount(value){
        if(value < 0){value = 0};
        this._waterAmount = value;
      }

      getWaterAmount(){
        return this._waterAmount;
      }

      getPower(){
        return this._power;
      }

      constructor(power){
        this._power = power;
      }
    }

    let coffeeMachine = new CoffeeMachine(100);
    coffeeMachine.setWaterAmount(-10); //0にset
    ```

    ### 説明
    - `power`は`getter`しかないので、取得しかできない

    ### 言語的には単なるフィールドに過ぎないので
    - 継承もできる

    ```js
    class Machine{
      _waterAmount = 0;

      setWaterAmount(value){
        if(value < 0){value = 0};
        this._waterAmount = value;
      }

      getWaterAmount(){
        return this._waterAmount;
      }

      getPower(){
        return this._power;
      }

      constructor(power){
        this._power = power;
      }
    }

    class CoffeeMachine extends Machine{
      _espressoAmount = 0;

      getEspressoAmount(){
        return this._espressoAmount;
      }

      setEspressoAmount(value){
        if(value < 0) value = 0;
        this._espressoAmount = value;
      }

      concentration(){
        const sumAmount = this._espressoAmount + this._waterAmount; //継承しているのでthis._waterAmountが使える
        if(sumAmount == 0){
          return "Error";
        }
        return this._espressoAmount / sumAmount;
      }
    }

    let coffeeMachine = new CoffeeMachine(100);
    coffeeMachine.setWaterAmount(-10); //0にset
    coffeeMachine.setEspressoAmount(20);
    console.log(coffeeMachine.concentration()); //1
    ```

    ### 注意点
    !!! warning
        - 言語レベルでは`protected`のルールがないので、クラス外からでもインスタンス経由で使えてしまう
        - 継承していないクラス内からは、言語レベルでも使うことはできない

## Q46. privateフィールド/メソッドの作り方を知っていますか?

??? success
    ### 宣言方法
    - 先頭に`#`: `#waterLimit`
    - 使用方法: `this.#waterLimit`等
    - メソッドの場合も同じ

    ### 使用例

    ```js
    class CoffeeMachine{
      #waterLimit = 200;
      #waterAmount = 0;

      #fixWaterAmount(value){
        if(value < 0) return 0;
        if(value > this.#waterLimit){
          return this.#waterLimit;
        }
      }

      setWaterAmount(value){
        this.#waterAmount = this.#fixWaterAmount(value);
      }

      getWaterAmount(){
        return this.#waterAmount;
      }
    }

    let coffeeMachine = new CoffeeMachine();
    coffeeMachine.#fixWaterAmount(123); //Private field '#fixWaterAmount' must be declared in an enclosing class
    //in等を使って存在チェックを行うこともできない
    console.log("#fixWaterAmount" in coffeeMachine);//false
    coffeeMachine.setWaterAmount(400);
    coffeeMachine.getWaterAmount(); //200
    ```

    ### 注意点(開発者ツール)
    !!! warning
        - 開発者ツールで試している場合、`private`にアクセスできてしまい、驚くかもしれない
        - `Node.js`や`js`ファイルに実際に記載すれば、問題なく実行可能

    ### 注意点([])
    !!! warning
        - `Private`フィールドに対して、`this[fieldname]`でアクセスすることはできない
        - つまり、`this["#name"]`は動作しない。
        - これは`private`であることを維持するための構文上の制限らしい

## Q47. 組み込みのクラス(たとえばArray等)を拡張する方法を知っていますか?

??? success
    ### 普通に継承する
    - `Array`や`Map`などは組み込みのクラスなので

    ```js
    class PowerArray extends Array{
      isEmpty(){
        return this.length === 0;
      }
    }

    let arr = new PowerArray(1,2,4,10,50);
    console.log(arr.isEmpty()); //false
    let filteredArr = arr.filter(item => item >= 10);
    console.log(filteredArr); // PowerArray [10, 50]

    console.log(filteredArr.isEmpty()); //0
    ```

    ### 興味深い点
    - `filter`や`map`等の組み込みのメソッドは、継承された型と同じ型の新しいオブジェクトを返す
    - 具体的には、オブジェクトの`constructor`プロパティを使用している。
    - 上の例でいう、`arr`の`constructor`は(デフォルトの場合`F.prototype`がコンストラクタ自身を指す事を思い出すと、)`PowerArray`なので、
    - そのため、そのオブジェクトでも拡張した機能を使い続けることができる

## Q48. 組み込みのメソッドに、拡張前のオブジェクトを返してもらいたい場合、Symbol.speciesが使えることを知っていますか?

??? success
    ### Symbol.species
    - 組み込みクラスが特定のメソッドを呼び出す際、`Symbol.species`の値が参照されて、新しいインスタンスが作成される
    - サブクラスでこれをオーバーライドすることで、組み込みメソッドの動作を制御可能
    - 返り値が生成されたインスタンスの型になる
    - つまり、返り値として`Array`を指定することで、拡張していても、`Array`が返る

    ### 使用例

    ```js
    class PowerArray extends Array{
      isEmpty(){
        return this.length == 0;
      }

      static get [Symbol.species](){
        return Array;
      }
    }

    let arr = new PowerArray(1,2,3,10,59);
    console.log(arr.isEmpty());

    let filteredArr = arr.filter(item => item >= 10);
    console.log(filteredArr); //[10,59]

    filteredArr.isEmpty(); //filteredArr.isEmpty is not a function
    ```

    ### static getについて
    - クラスに静的な`getter`を定義するための構文

    ### static getの例

    ```js
    class PowerArray extends Array{
      isEmpty(){
        return this.length == 0;
      }

      static get [Symbol.species](){
        return Array;
      }
    }

    const constructorFunction = PowerArray[Symbol.species]; 
    console.log(constructorFunction);
    //ƒ Array() { [native code] }


    const newArray = new constructorFunction(1,2,3);
    //[1,2,3]
    ```

## Q49. well-known symbolについて知っていますか?

??? success
    ### Well-Known symbol
    - これらのシンボルは、プロパティキーとして使用でき、オブジェクトをどのように扱うかを定めることができる

    ### 以下について
    - 理解不要(特にwithと正規表現については)
    - 登場した時にその都度触れる

    ### Symbol.asyncIterator
    - このプロパティがオブジェクトに設定されていれば、非同期反復可能
    - `for await ...of`ループで使用できるようになる

    ### Symbol.hasInstance
    - コンストラクタ(オブジェクト)が、そのインスタンスのオブジェクトとして認識されるかどうかを決定する
    - `instanceof`のカスタマイズ用

    ### Symbol.isConcatSpreadable
    - `Array.prototype.concat()`を使った際、オブジェクトを配列の要素に平坦化する場合の設定として使用

    ### Symbol.iterator
    - オブジェクトの規定のイテレータを指定
    - `for...of`に使う

    ### Symbol.match
    - 文字列に対して正規表現のマッチを指定
    - `String.prototype.match()`が呼び出す

    ### Symbol.matchAll
    - 文字列に対する正規表現の一致を生成するイテレータを返す
    - `String.prototype.matchAll()`が使用

    ### Symbol.replace
    - 文字列の一致した部分を置き換えるメソッドを指定
    - `String.prototype.replace()`が呼び出す

    ### Symbol.search
    - 正規表現に一致する文字列内の位置を返すメソッドを指定
    - `String.prototype.search()`が呼び出す

    ### Symbol.species
    - 組み込みクラスが特定のメソッドを呼び出す際、`Symbol.species`の値が参照されて、新しいインスタンスが作成される
    - サブクラスでこれをオーバーライドすることで、組み込みメソッドの動作を制御可能

    ### Symbol.split
    - 正規表現に一致する位置で文字列を分割するメソッドを返す
    - `String.prototype.split()`が使用

    ### Symbol.toPrimitive
    - オブジェクトを対応するプリミティブ値に変換する

    ### Symbol.toStringTag
    - オブジェクトを説明する、既定の文字列の作成に使用
    - `Object.prototype.toString()`が内部的にアクセスする

    ### Symbol.unscopables
    - withにおいて、オブジェクトのプロパティがスコープ対象になるか否かを指定
    - withについては後述

    ### その他(Symbol())
    - コンストラクタとしては不完全
    - `new`に対応していないため

## Q49. 組み込みクラスは、互いから静的なプロパティを継承しないことを知っていますか?

??? success
    ### 本来
    - 静的メソッド(static)
    - 非静的メソッド(.prototypeに格納される)
    - の2種が継承される

    ### 組み込みクラスの場合
    - 非静的メソッド(.prototypeに格納される)のみ

    ### たとえば
    - `Array`は`Object`を継承しているはずだが

    ```js
    console.log(
      Array.prototype.__proto__ === Object.__prototype
    ); //true

    console.log(
      Array.__proto__ === Object.__proto__
    ); //false

    ```

    ### これは何を意味するのか
    - `Object.keys()`: 存在する
    - `Array.keys()`: 存在しない
    - 組み込みのクラスは、継承先オブジェクトに使用させたくないものを、`static`で実装している

    ### 補足(フィールド)
    - 静的フィールドは継承されない。
    - フィールドは継承できる

## Q50. instanceofを使うと、オブジェクトがどのクラスに属しているか確認できることを知っていますか?

??? success
    ### 構文
    - `obj`が`Class`に属している場合は`true`を返す

    ```js
    obj instanceof Class
    ```

    ### Classがコンストラクタ関数でも動作する

    ```js
    function Rabbit(){}

    console.log(new Rabbit() instanceof Rabbit); //true
    ```

    ### 組み込みクラスでも動作する

    ```js
    let arr = Array.from({length:3}, (_, i) => i);
    console.log(arr instanceof Array); //true
    ```

## Q51. instanceofが継承先のClassとチェックした場合でもtrueになることを知っていますか?

??? success
    ### 理由
    - 通常、チェックのためにプロトタイプチェーンを利用するから
    - プロトタイプチェーン上に存在すれば、通常は`true`となるい

    ### 継承元をチェックした例

    ```js
    const arr = [1,2,3];
    console.log(arr instanceof Object); //true
    ```

## Q52. instanceofの挙動が、Symbol.hasInstanceでカスタムできることを知っていますか?

??? success
    ### instanceofのアルゴリズム
    - もしstaticメソッドである、`Symbol.hasInstance`があれば`Class[Symbol.hasInstance](obj)`を用いる
    - これは`true`または`false`を返す
    - 持っていなければ、`obj instanceof class`は`Class.prototype`が`obj`のプロトタイプチェーンのうちの1つと等しいかをチェックする

    ### 例

    ```js
    class Animal{}
    class Rabbit extends Animal{}

    let rabbit = new Rabbit();
    console.log(rabbit instanceof Animal);

    /*
    1. rabbit.__proto__ == Animal.prototype; //false
    2. rabbit.__proto__.proto__ == Animal.prototype; //true
    3. trueを返す
    4. もし、rabbit.__proto__.__proto__とチェーンを続けていった先が、nullになればfalseを返す
    */
    ```

    ### Symbol.hasInstanceを設定した場合

    ```js
    class Animal{
      static [Symbol.hasInstance](obj){
        if (obj.canEat) return true;
      }
    }

    let obj = {canEat : true};
    console.log(obj instanceof Animal); //true
    //Animal[Symbol.hasInstance](obj)が呼ばれる
    ```

    ### あくまでもClassのprototypeをチェックしているので......
    - `prototype`を変更すると狂う

    ```js
    function Rabbit(){}
    let rabbit = new Rabbit();

    console.log(rabbit instanceof Rabbit); //true
    Rabbit.prototype = {};
    console.log(rabbit instanceof Rabbit); //false

    ```

    ### objA.isPrototypeOf(objB)との違い
    - これは`objA`が`objB`のプロトタイプチェーンのどこかにあれば`true`を返す
    - こちらはあくまでも、`obj`同士をチェックすることに注意
    - `obj instanceof Class`は`Class.prototype.isPrototypeOf(obj)`と置き換えられる。
    - 違いは、`Symbol.hasInstance`を解釈できるか


    ### 例&継承のまとめ

    ```js
    class Animal{};
    class Rabbit extends Animal{};
    let rabbit = new Rabbit();

    //prototypeを継承する
    Animal.prototype.isPrototypeOf(Rabbit.prototype);//true

    //組み込みでないextendsなのでコンストラクタも継承
    Animal.isPrototypeOf(Rabbit);//true

    //instance生成の際に、prototypeを継承
    Rabbit.prototype.isPrototypeOf(rabbit);//true

    //コンストラクタ自体とはつながっていない
    Rabbit.isPrototypeOf(rabbit);//false

    //組み込みだがprototypeは継承している
    Object.prototype.isPrototypeOf(Array.prototype); //true

    //組み込みなのでコンストラクタは継承していない
    Object.isPrototypeOf(Array); //false
    ```

## Q53. toStringを拡張して、instanceof/typeofの代替として使えることを知っていますか?

??? success
    ### 組み込みのtoStringの場合(Object.prototype.toString)
    - 結果はその値に依存する

    ```js
    console.log({}); //[object Object]
    ```

    ### toStringの仕様説明
    - 内部的には`this`を検査し、対応する結果を返すので`call`などの`this`を束縛するメソッドが役に立つ

    ```js
    let s = Object.prototype.toString;
    let arr = [];

    console.log(Object.prototype.toString.call("neko"));
    //[object String]
    console.log(s.call(2)); //[object Number]
    console.log(s.call(true)); //[object Boolean]
    console.log(s.call(null)); //[Object Null]
    console.log(s.call(undefined)); //[Object Undefined]
    console.log(s.call(arr)); //[object Array]
    console.log(s.call(alert)); //[object Function]
    ```

    ### Symbol.toStringTag
    - `toString`のふるまいをカスタマイズする
    - 値が`object`の後に表示される

    ### 例

    ```js
    let user = {
      [Symbol.toStringTag]: "User"
    };
    //{}にtoStringはないので、[[Prototype]]を辿り、Object.prototype.toStringを用いる。
    console.log({}.toString.call(user)); //[object User]
    ```

    ### 環境固有のオブジェクトはこのプロパティを持っている

    ```js
    console.log(window[Symbol.toStringTag]);
    //window

    console.log(XMLHttpRequest.prototype[Symbol.toStringTag]);
    //XMLHttpRequest

    console.log({}.toString.call(window));
    //[object Window]
    console.log({}.toString.call(new XMLHttpRequest()));
    //[object XMLHttpRequest]
    ```

## Q54. 多重継承したいときに、その代替として使えるミックスインについて知っていますか？

??? success
    ### オブジェクトの[[Prototype]]は1つしかない
    - 複数のクラスから機能を取り込みたい場合は?

    ### mixins
    - 親クラスではないが、他クラスで使用するメソッドを含むクラス
    - 単独では使わない

    ### 例
    - 役立つメソッドを持つオブジェクトを作る
    - `Object.assign`で`Class`の`prototype`に追加する

    ```js
    //mixin

    let sayHiMixin = {
      sayHi(){
        alert(`Hello ${this.name}`);
      }

      sayBye(){
        alert(`Bye ${this.name}`)
      }
    }

    //取り込むクラス
    class User{
      constructor(name){
        this.name = name;
      }
    }

    //Userのprototype(methodを格納する場所)に
    //sayHiMixinのプロパティを全コピー
    //※フィールドとメソッドはどちらもオブジェクトのプロパティとして分離される
    Object.assign(User.prototype, sayHiMixin);
    ```

    ### 補足
    - 継承ではなく、単純なメソッドのコピーなので、これとは別に継承可能

    ### Mixinの内部で継承を使うことも可能

    ```js
    let sayMixin = {
      say(phrase){
        alert(phrase);
      }
    }

    let sayHiMixin = {
      __proto__ : sayMixin,
      sayHi(){
        super.say(`Hello ${this.name}`);
      },
      sayBye(){
        //superが参照する[[HomeObject]]はsayHiMixin
        //[[HomeObject]]は定義時に固定されるので
        //この[[Prototype]]を探していくので
        //sayMixin.sayを参照する
        super.say(`Bye ${this.name}`);
      }
    }

    class User{
      constructor(name){
        this.name = name;
      }
    }

    Object.assign(User.prototype, sayHiMixin);

    const user = new User("neko");
    user.sayHi(); //Hello neko
    ```

    !!! warning
        - Mixinで誤って既存のクラスメソッドを上書きしてしまう可能性があるので、命名についてはよく考えて行う

    ### 実用例(方針)
    - Mixinを用いて、イベントに関連する関数を任意のclass/objectに追加する
    - イベントを生成するためのメソッドは`.trigger(eventName [,data])`とする。全ての割り当てられたハンドラを呼び出し、引数を渡す
    - イベントのリスナーとして`.on(eventName, handler)`も提供する。`name`のイベントが発生したときに、`handler`を実行する
    - `handler`を削除するための`.off(eventName, handler)`も追加する
    - イベント名にhandlerを紐づけてから、実際にイベントを生成する感じ

    ### 実用例(コード)

    ```js
    let eventMixin = {
      /**
      *
      * menu.on("select", function(item){})
      */
      on(eventName, handler){
        if(!this._eventHandlers) this._eventHandlers = {};
        if(!this._eventHandlers[eventName]){
          this._eventHandlers[eventName] = [];
        }
        //イベントにハンドラ割り当て
        this._eventHandlers[eventName].push(handler);
      },

      /**
      *
      * menu.off("select", handler)
      */
      off(eventName, handler){
        let handlers = this._eventHanlders && this._eventHandlers[eventName];
        if(!handlers) return;

        //該当イベント名の該当ハンドラを削除
        for(let i = 0; i < handlers.length; i++){
          if(handlers[i] == handler){
            handlers.splice(i--, 1);
          }
        }
      },

      /**
      * イベントの生成とデータのアタッチ
      * this.trigger("select", data1, data2, ...,)
      */
      trigger(eventName, ...args){
        if(!this._eventHandlers || !this._eventHandlers[eventName]){
          return; //イベントに対応するハンドラが無いなら
        }

        //イベントに紐づいたハンドラを引数をもとに呼び出す
        this._eventHandlers[eventName]
          .forEach(handler => handler.apply(this, args));
      },
    }

    //クラスを生成
    class Menu{
      choose(value){
        this.trigger("select", value);
      }

      test(value){
        this.trigger("test", value);
      }
    }

    Object.assign(Menu.prototype, eventMixin);
    let menu = new Menu();

    //ハンドラ呼び出し
    menu.on("select", value => alert("Value selected: " + value));
    menu.on("select", value => alert("Second handler: " + value));
    //イベントのトリガ
    menu.choose("123");
    //Value selected: 123
    //Second handler: 123

    menu.on("test", value => alert(`test${value} passed.`));

    menu.test(3); //test3 passed.
    ```

## Q55. 静的イニシャライザーブロックについて知っていますか?

??? success
    ### static{}
    - `{}`内にコードを記述する

    ### 使い道
    - 静的プロパティよりも、柔軟な初期化処理を行いたいときに使用する
    - 非静的フィールドは、インスタンスごとに設定されるので関係ない
    - 1つの値から複数のフィールドを設定したりもできる
    - コンストラクタより先に実行されるのがいいところ

    ### 使用例

    ```js
    class MyClass{
      static #myProperty;
      static #name;
      static #surname;

      static{
        const json = JSON.parse(`{"someField" : "hoge"}`);
        this.#myProperty = json.someField;
        [this.#name, this.#surname] = "Taro Yamada".split(" ");
      }

      constructor(){
        console.log(MyClass.#myProperty);
        console.log(MyClass.#name);
        console.log(MyClass.#surname);
      }
    }

    new MyClass(); 
    //hoge
    //Taro
    //Yamada
    ```

    ### 補足
    - 静的イニシャライザーブロックは、任意の数置くことができる
    - 親クラスの静的な初期化は、サブクラスの初期化より先に行われる。親クラスのコンストラクタより前に
    - 静的フィールドと静的初期化ブロックは、追加した順に評価される

    ### 補足(コード例)
    - フィールド初期化子と、静的イニシャライザーブロックの`this`はそのクラスを指す

    ```js
    class MyClass{
      static name;

      static { //static nameより前にある場合、うまく初期化されない
        this.name = "Taro";
      }
      static surname;



      static {
        this.surname = "Yamada";
      }

      constructor(){
        console.log(MyClass.name);
        console.log(MyClass.surname);
      }
    }

    class ExtendsClass extends MyClass{
      static address;

      static{
        this.address = "Tokyo"; 
      }

      constructor(){
        super();
        //静的フィールドと初期化子の実行タイミング
        //ここでthisがつかわれるので、super()を書くこと
        console.log(ExtendsClass.address);
      }
    }

    new ExtendsClass();
    //Taro
    //Yamada
    //Tokyo
    ```


