# クイズ(中級1:Object)

## Q1. オブジェクトについて知っていますか?

??? success
    ### プリミティブ
    - 文字列や数値は単一の根源的な値を持つためこう呼ばれる

    ### オブジェクト
    - 様々なデータのコレクションや複雑な実体を格納するために使用される
    - オブジェクトは波括弧`{}`と任意のプロパティからなる。
    - プロパティは`key:value`のペアを持ち、`key`は文字列。`value`はどんな値でも構わない

## Q2. オブジェクトの作成方法を知っていますか?

??? success
    ### 空オブジェクトの場合
    - `new Object()`を用いる
    - `{}`:空波括弧を用いる。
    ```js
    const user = new Object();
    let user = {};    
    ```

    ### 通常オブジェクトの場合

    ```js
    const user = {
      name: "John",
      age: 30
    };
    ```

    ### 末尾のカンマについて
    - あってもなくても構わない
    - が、遭った方が、追加/削除/移動が簡単になる

    ```js
    const user = {
      name: "John",
      age: 30,
    };
    ```

## Q3. オブジェクトの各プロパティへのアクセス方法を知っていますか?

??? success
    ### ドット表記を使う

    ```js
    const user = {
      name : "John",
      age : 30
    };

    console.log(user.name); //John
    ```


## Q4. オブジェクトのプロパティを追加/変更する方法を知っていますか?

??? success
    ### 概要
    - `オブジェクト名.プロパティ名 = 値`

    ```js
    const user = {
      name : "John",
      age : 30
    };

    user.isAuth = true;

    console.log(user); //{name: 'John', age: 30, isAuth: true}
    ```

## Q5. プロパティを削除する方法を知っていますか?

??? success
    ### 概要
    - `delete`を使う

    ```js
    const user = {
      name : "John",
      age : 30
    };

    delete user.name;

    console.log(user);  //{age: 30}
    ```

## Q6. 複数単語からなるプロパティ名を設定する方法を知っていますか?

??? success
    - 引用符で囲む

    ### 例

    ```js
    let user = {
      name : "John",
      age : 30,
      "likes fruits" : "orange"
    };
    ```

## Q7. `[]`を利用したプロパティへのアクセス方法とその利点を知っていますか?

??? success
    ### 複数単語からなるプロパティへのアクセス
    - ドット表記の場合、JSが解釈できずエラーとなる

    ```js
    //user.likes fruits = "apple" //error

    user["likes fruits"] = "apple";
    ```

    ### 変数を用いたプロパティの設定
    - ドット表記の場合、変数名がそのまま`key`になってしまう

    ```js
    let key = "like fruits";

    let user = {
      name : "John",
      age : 30,
    }

    //user.key = "melon";
    //console.log(user);  //{name: 'John', age: 30, key: 'melon'}

    user[key] = "melon";
    console.log(user); //{name: 'John', age: 30, like fruits: 'melon'}
    ```

    ### 取得するプロパティの種類をユーザの入力に依存させることも可能

    ```js
    let user = {
      name : "John",
      age : 30
    };

    let key = prompt("Which property name do you want to get?", "name");

    alert(user[key]); //John or age
    ```

## Q8. `[]`と変数を使って、動的にプロパティ名を設定する方法を知っていますか?

??? success
    ### 基本

    ```js
    let fruit = prompt("input your favoite fruits", "apple");

    let cart = {
      [fruit] : 5,
    };

    console.log(cart); //{apple: 5} -> appleを入力した場合
    ```

    ### 角括弧の中では、式も使える

    ```js
    let fruit = "orange";
    let cart = {
      [`${fruit} and lemons`]: 5; 
    }

    console.log(cart); //{orange and lemons: 5}
    ```

## Q9. 変数名をプロパティ名として扱う場合に用いる、プロパティの短縮構文について知っていますか?

??? success
    ### 従来

    ```js
    let name = "John";
    let age = 30;

    const user = {
      name : name,
      age : age,
    }
    ```

    ### 短縮構文
    - 既存の変数名を`key`として用いる場合、`key`は省略できる

    ```js
    let name = "John";
    let age = 30;

    const user = {
      name,
      age,
    }

    console.log(user); //{name: 'John', age: 30}
    ```

## Q10. プロパティ名(key)として予約語を含むあらゆる値が設定できることを知っていますか?

??? success
    ### 予約語を使った場合

    ```js
    let obj = {
      for : 1,
      let : 2,
      return : 3,
    }

    console.log(obj); // {for: 1, let: 2, return: 3}
    ```

    !!! warning
        ### 数値の場合、文字列に変換される
        - `"0"`と`0`は同じプロパティ名になる。

        ```js
        let obj = {
          0 : "test",
        }

        obj["0"] = "hoge";

        console.log(obj); //{0: 'hoge'}
        ```

    !!! warning
        ### __proto__という特殊なプロパティ
        - 値にはオブジェクトしか設定できない

## Q11. inを使うことでプロパティの存在チェックができることを知っていますか?

??? success
    ### 単にアクセスすればわかるのでは?
    - JSはプロパティが存在しない場合でもエラーを返さない

    ```js
    let user = {}
    console.log(user["hogehoge"]); //undefined
    ```

    ### 上記の手法の問題点
    - 値が本当に存在しないのか、値にundefinedが設定されているのかわからない

    ```js
    let user = {
      hogehoge : undefined,
    }

    console.log(user["hogehoge"]); //undefined
    ```

    ### 解決策(inを使う)

    ```js
    let user = {
      hogehoge : undefined,
    }

    let key = "foo"

    console.log("hogehoge" in user); //true
    console.log("fugafuga" in user); //false
    console.log(key in user); //false
    ```

## Q12. for...inを使って、オブジェクトの各キーを取得できることを知っていますか?

??? success
    
    ### 概要

    ```js
    let user = {
      name : "John",
      age : 30,
      "likes fruits" : "orange"
    };

    for(const key in user){
      console.log(`${key} : ${user[key]}`);

      //outputs
      // name : John
      // age : 30
      // likes fruits : orange
    }
    ```

## Q13. 整数値に直せるキー名をfor ...inで取得した際の問題について知っていますか?

??? success

    ### 概要
    - 整数値に直されるキーの場合、ソートが行われる
    - 整数値に直されるキーとそれ以外が混じっていた場合、前者は前に出される

    ```js
    let numberProblem = {
      "41" : "hoge",
      "neko": "foobar",
      "43" : "fuga",
      "1" : "foo",
    };

    for(const key in numberProblem){
      console.log(key);
    };

    /*
    * outputs:
    * 1
    * 41
    * 43
    * neko
    */
    ```

    ### 対策
    - 整数値に直されないキーにする

    ### 例
    - 前に`+`を付ける。取得時に`+`を付けることで、`+"+49"`等と判断され、"+49"が数値型になり、求める形となる。

    ```js
    let numberProblem = {
      "+41" : "hoge",
      "+43" : "fuga",
      "+1" : "foo",
    };

    for(const key in numberProblem){
      console.log(+key); //41,43,1
    };
    ```

## Q14. JSのスタックとヒープについて知っていますか?

??? success
    ### スタック
    - 小さく整理されたメモリ領域
    - サイズは固定

    ### ヒープ
    - 大部分が構造化されていない
    - サイズは固定ではない

## Q15. Object本体がヒープに格納されることを理解していますか?

??? success
    ### 概要
    - 文字列や数字などのプリミティブな値は、スタックに格納される
    - オブジェクトはヒープに格納される
    - これはオブジェクトのサイズが、プロパティの個数などによって可変であるため

## Q16. `const user = {name = "John"}`のようなオブジェクトを`const user1 = user`とコピーしても、オブジェクトは複製されないことを理解していますか?

??? success
    ### オブジェクトに割り当てられた変数

    ```js
    const user = {
      name : "John",
    }
    ```

    - たとえば上記のようなオブジェクトがあるとき、オブジェクトに割り当てられた変数`user`はスタックに格納される
    - 変数`user`は指し示すオブジェクトが、ヒープ上のどこに位置しているのか、その参照情報（メモリ上のアドレス）のみを持っている。
    - つまり、変数`user`がオブジェクト本体を意味しているわけではない。(userはオブジェクトを参照している)
    - したがって、`user`をコピーした場合、 __コピーされるのはオブジェクトの参照情報__ で、オブジェクト自体は複製されない

    ### 具体例
    - たとえば`user.name`を取得した場合
    - JSエンジンは`user.name`のメモリ上のアドレスを見て、実際のオブジェクト上(ヒープ上)で操作を実行する。

## Q17. オブジェクトの比較が、参照情報の比較であることを理解していますか?

??? success
    ### 比較
    - オブジェクトの比較が等しいとみなされるのは、それらが同一のオブジェクトであった場合のみ
    - たとえプロパティの情報が全て同じでも、違うオブジェクトなら同じであるとはみなされない

    ### 例

    ```js
    const a = {};
    const b = {};

    const c = a;
    console.log(a == b); //false
    console.log(a == c); //true
    ```

## Q18. 参照と複製の違いが分かっていますか?

??? success
    ### 参照概要
    1. 2つの変数から1つのオブジェクトを参照している
    2. 片方の参照先からオブジェクトを変更する
    3. もう片方の参照先から見ても、オブジェクトが変わったことが確認できる
  
    ### コピーの場合
    - `b`を変えても、`a`には反映されていない

    ```js
    let a = 12;
    let b = a;
    b = 10;
    console.log(a); //12
    ```

    ### 参照の場合
    - `ref`を通してオブジェクトを変えると、`user`から見たときも、オブジェクトが変わったことが確認できる

    ```js
    let user = {name : "John"};

    let ref = user;

    ref.name = "Peter";

    console.log(user); //{name : "Peter"}
    ```

## Q19. Object.assignでオブジェクトを複製/マージできることを知っていますか?

??? success

    ### Object.assignでコピー
    - `Object.assign(dest [, src1, src2, ...])`
    `src1, src2, ..., srcN`のプロパティを`dest`にコピーする

    ```js
    let user = {name : "hoge"};
    let clone = Object.assign({}, user); //空オブジェクトにそっくりコピーする
    ```

    ### Object.assignでマージ

    ```js
    let user = {name : "hoge"};
    let permissions1 = {canView : true};
    let permissions2 = {
      canEdit : false,
      canSave : false,
    }

    Object.assign(user, permissions1, permissions2);

    console.log(user); //{name: 'hoge', canView: true, canEdit: false, canSave: false}
    ```

    !!! warning
        ### 同名のプロパティを持っていた場合
        - 上書きされる
        - 上書きされる都合上、後ろの引数が優先される

        ```js
        const user = {name : "John"};

        Object.assign(user, {name : "hoge"}, {name : "fuga"});
        console.log(user); //{name: 'fuga'}
        ```
    
    ### コラム
    - 後程登場するスプレッド構文と、`Object.assign`の違いは、`setter`を起動するか否か
    - `Object.assign`は起動する

## Q20. 入れ子になったオブジェクトをクローンする方法(structuredClone)について知っていますか?

??? success
    ### Object.assignではだめなのか
    - ダメ
    - 内側のオブジェクトは単に参照されているだけなので

    ```js
    const user = {
      name : "John",
      fruits : {
        apple : "yammy",
        orange : "I hate",
      },
    };

    const clone = Object.assign({}, user);
    clone.fruits["orange"] = "decent";

    console.log(user); //{name: "John", fruits: {apple : "yammy", orange: "decent" }}
    ```

    ### structuredClone
    - 入れ子になった内側のオブジェクトも複製する

    ```js
    const user = {
      name : "John",
      fruits : {
        apple : "yammy",
        orange : "I hate",
      },
    };

    const deepclone = structuredClone(user);
    clone.fruits["orange"] = "decent";

    console.log(user); //{name: "John", fruits: {apple : "yammy", orange: "I hate" }}
    ```

## Q21. constを指定しているのに、オブジェクトのプロパティを変更できている理由が分かりますか?

??? success
    ### そもそもconstとは
    - 変数の再代入を禁止するもの
    - オブジェクトのプロパティを変えても、それは変数の再代入を意味しない
    - なぜなら変数が指し示すオブジェクトは変わらないから

## Q22. オブジェクトのプロパティを変更できないようにしたい場合、Object.freezeが使えることを知っていますか?

??? success
    ### Object.freeze()
    - プロパティの変更を禁止する

    ### コード例
    - `use strict`がない場合、代入を行ってもエラーは出ない
    - ただし、エラーが出ない場合でも、値は変わらない

    ```js
    "use strict";
    const user = Object.freeze({name : "John"});
    user["name"] = "hoge"; //TypeError
    ```

## Q23. ガベージコレクションについて知っていますか?

??? success
    ### ガベージコレクション
    - 到達不可能になったオブジェクトを削除する

    ### 到達不可能とは?,
    - ルートからたどり着けないオブジェクト

    ### ルートとは?
    - 現在の関数のローカル変数とパラメータ
    - 呼び出しがネストされている場合、チェーン上にある、他の関数のローカル変数とパラメータ
    - グローバル変数

    ### つまり?
    - 現在Aという関数内にいて、とあるオブジェクトが、A内に存在する1つのローカル変数から参照されていた場合
    - Aを抜けると（ローカル変数はルートではなくなり）オブジェクトはルートから到達不可能になる。よって、削除の対象となる

    ```js
    function A(){
      const obj = {name : "John"};
      //ルートはobj
    }
    //ルートは「ない」-> オブジェクト削除
    ```

    - しかし、とあるオブジェクトがグローバル変数からも参照されている場合、Aを抜けても、オブジェクトはルートから参照可能であるため削除されない

    ```js
    obj = {name : "John"};

    function A(){
      const cloneObj = obj;
      //ルートはobj, cloneObj
    }

    //ルートはobj
    ```

    ### ガベージコレクションの例

    ```js
    const user = {name : "John"};
    user = null; // {name : "John"}を指すルートはない
    //ガベージコレクションにより、{name : "John"}は削除される
    ```

## Q24. ガベージコレクションの目的について知っていますか?

??? success
    ### 概要
    - データを削除することで、メモリを開放する

## Q25. ガベージコレクションの仕組みについて理解していますか?

??? success
    ### ガベージコレクタ
    - JSエンジンのバックグラウンドプロセス
    - 全てのオブジェクトを監視し、到達不可能になったオブジェクトを削除する

    ### mark-and-sweep
    1. 最初にルートを取得し、それらをマーク(記憶)する
    2. マークが参照しているオブジェクトをマークする
    3. マークが参照しているオブジェクトが参照しているオブジェクトをマークする
    4. マークの参照しているオブジェクトが...
    5. なお、一度マークされたオブジェクトは記憶されており、2度以上マークされることはない
    6. 全ての到達可能な箇所をマークしたら、マークされていないオブジェクトを削除する

## Q26. ガベージコレクションの最適化について理解していますか?

??? success
    ### 世代コレクション
    1. オブジェクトを2つに分ける
    2. 古いオブジェクトは、これからも長い間必要になると考え、間隔を置いて検査する
    3. 新しいオブジェクトはすぐに不要になる可能性が高いと考え、頻繁に検査する

    ### インクリメンタルコレクション
    - 全てのオブジェクトをマークしようとすると時間がかかる
    - そのため、ガベージコレクションを分割して行う
    - 結果、大きな遅延が一度に生じることはなくなる

    ### アイドルタイムコレクション
    - CPUがアイドル状態の時のみガベージコレクションを行い、実行への影響を減らす

    ### 参考
    - 2019年のV8のガベージコレクタに関する記事[Trash talk: the Orinoco garbage collector](https://v8.dev/blog/trash-talk)

## Q27. ガベージコレクションのタイミングに依存した処理を書くべきではない理由を理解していますか?

??? success
    ### 概要
    - JSエンジンによって、ガベージコレクションの仕様は異なる
    - そのためオブジェクトが削除されるタイミングも異なる

    
## Q28. 以下のオブジェクトのうち、ガベージコレクションの対象になるオブジェクトが何かわかりますか?

??? tips

    ```js
    const marry = (man, woman) => {
      woman.hasband = man;
      man.wife = woman;

      return {
        father : man,
        mother : woman,
      }
    }

    let family = marry(
      {name : "John"},
      {name : "Ann"}
    );

    delete family.father;
    delete family.mother.husband;
    ```

??? success

    ### 参考図
    ```mermaid
    graph LR;
    A[root] --> B[family]
    B --> D[family.mother]
    C[family.father] -->|wife| D
    ```

    ### 答え
    - marryの引数でいう`father`(`{name : "John}`)は、辿り着けないので、ガベージコレクションの対象となる
    - なお、`delete family`とした場合、`family`も`family.mother`も削除の対象となる

## Q29. メソッドについて理解していますか?

??? success
    ### 概要
    - オブジェクトのプロパティとなっている関数

    ### 使用例

    ```js
    const user = {
      name : "John",
      age : 30,

      sayHi : () =>{
        alert("Hello!");
      }
    }

    const sayBye = () =>{
      alert("Bye!");
    }

    user.sayHi(); //Hello
    user.sayBye = sayBye();
    user.sayBye(); //Bye!
    ```

    ### オブジェクトにおけるメソッドの短縮構文

    ```js
    const user = {
      name : "John",

      sayHi(){
        alert("Hello");
      }
    };

    console.log(user); //{name: 'John', sayHi: ƒ}
    ```

## Q30. メソッド中のthisが何を意味するか知っていますか?

??? success
    ### 答え
    - 関数宣言内の`this`キーワードは、メソッド(関数)を呼び出すために使われた(変数が参照している)オブジェクトを意味する。

    ### 例

    ```js
    let user = {
      name : "John",
      age : 30,

      sayHi(){
        console.log(this); //{name: 'John', age: 30, sayHi: ƒ}
        console.log(this.name);//John
      }
    };

    user.sayHi();
    ```

    ### thisの代わりに、オブジェクト名を使えばいいのでは?

    ```js
    let user = {
      name : "John",
      age : 30,

      sayHi(){
        console.log(user); //{name: 'John', age: 30, sayHi: ƒ}
        console.log(user.name);//John
      }
    };

    user.sayHi();
    ```

    ### 問題点
    - `user`を`null`にすると、`user`に依存している`sayHi`はメッセージを表示できなくなる

    ```js
    let user = {
      name : "John",
      age : 30,

      sayHi(){
        console.log(user.name);
      }
    }

    let another_user = user;
    user = null;

    another_user.sayHi(); //Error 
    ```

    ### 解決策
    - thisにした場合、`another_user.sayHi()`において、`sayHi`メソッドを呼び出したオブジェクトは、まだ`another_user`から参照されており、ガベージコレクションの対象にはなっていないため、普通に表示される
 
    ```js
    let user = {
      name : "John",
      age : 30,

      sayHi(){
        console.log(this.name);
      }
    }

    let another_user = user;
    user = null;

    another_user.sayHi(); //John 
    ```

## Q31. thisをメソッド外(任意の関数宣言内)でも使えることを知っていますか?

??? success
    ### メソッド外のthis
    - 関数宣言内のthisは、実行時に評価される

    ### 例
    - user.f()の場合、fを呼び出す際に用いられたオブジェクトはuser(が参照している)。よって、thisは`{name : "John"}`を意味する

    ```js
    let user = {name : "John"};

    function sayHi(){
      console.log(this.name);
    }

    user.f = sayHi();
    user.f(); //John
    ```

## Q32. オブジェクト無しでthisを呼び出したときの挙動について知っていますか?

??? success
    ### `use strict`あり
    - `undefined`扱いになる

    ### `use strict`無し
    - グローバルオブジェクトになる
    - 一般的にプログラミングエラーなので使用しないこと

## Q33. アロー関数が`this`を持たないことを知っていますか?

??? success
    ### アロー関数内でthisを書いた場合
    - 外部の通常の関数から取得される

    ### 例

    ```js
    "use strict";
    let user = {
      name : "John",
      sayHi(){
        let arrowFunc = () => console.log(this.name);
        //arrowFunc()はobj無しで呼び出されているが、外部の通常の関数からthisを使うのでuser.name扱いとなる。
        arrowFunc();
        //let normalFunc = function(){console.log(this.name)};
        //normalFunc(); //error
      }
    }

    user.sayHi(); //John
    ```

## Q34 メソッドチェーンを見たことがありますか?

??? success

    ### こんなやつ
    1. `ladder.up()`は`step`を1増やして`ladder`を返す
    2. 返り値のオブジェクトに対して`.up()`する
    3. 更に返り値のオブジェクトに対して`.down()`する
    4. 返り値のオブジェクトに対して`.showStep()`し、値を表示する

    このように連鎖的な呼び出しを行う記法をメソッドチェーンという

    ```js
    let ladder = {
      step : 0,
      up(){
        this.step++;
        return this;
      },
      down(){
        this.step--;
        return this;
      },
      showStep: function(){
        console.log(this.step);
      }
    };

    ladder.up().up().down().showStep();

    //以下の様に、読みやすく書くこともできる
    /*
    ladder
      .up()
      .up()
      .down()
      .showStep()
    */
    ```

## Q35. コンストラクタとnew演算子を使って、似たようなオブジェクトを多数作成したことがありますか？

??? success
    ### コンストラクタ関数
    - 名前は大文字で始める(慣習)
    - `new`演算子を使って実行する(慣習)

    ### 例1

    ```js
    function User(name){
      this.name = name;
      this.isAdmin = false;
    }

    let user = new User("John");

    console.log(user.name); //John
    console.log(user.isAdmin); //false
    ```

    ### new演算子の意味
    new演算子を用いて関数が実行されると、次の処理が行われる

    1. 新しい空のオブジェクトが作成される
    2. 空のオブジェクトがthisに割り当てられる
    3. 関数内の処理が実行される
    4. thisの値が暗黙的にreturnされる
    !!! warning
        - 本来は1と2の間に、新しいオブジェクトに`__proto__`が追加され、コンストラクタ関数のプロトタイプオブジェクトに結び付けられる処理があるが、後記

    ### 例1の場合
    5. `{}`が生成される
    6. `({}).name = "John"`;の処理が実行される
    7. `({name : "John"}).isAdmin = false`の処理
    8. `{name : "John", isAdmin : false}`がreturn
    9. returnされたオブジェクトが`user`に代入される
    10. 後は普通にプロパティを通じてアクセス

    ### コンストラクタ関数の利点
    - 以下の様に似たオブジェクトを大量に作り出せる

    ```js
    function User(name){
      this.name = name;
      this.isAdmin = false;
    }

    const user1 = new User("Jack");
    const user2 = new User("Ann");
    console.log(user1.name, user2.name); //Jack Ann
    ```

    ### 慣習について
    - アロー関数でなければ(つまり内部にthisを持つならば)、理論上どんな関数でもコンストラクタになりうる
    - つまり、どの関数を呼び出す際にもnew演算子を付けていい
    - しかしそれではコードの見通しが悪くなるので、コンストラクタ関数は先頭が大文字というルールを作り、先頭小文字はnew演算子を伴って呼ばれないことを明確にする。

    ### 丸括弧について
    - 引数がなければ省略も可能

    ```js
    //構文としては正しい
    let user = new User;
    let user = new User();
    ```

## Q36. new.targetを用いて、`new`演算子を用いて呼び出されたか否か判別できると知っていますか？

??? success
    ### new.target
    - 関数の中でこれを使うことで、newを使って呼び出されたのか否か確認可能
    - 通常の呼び出し: `undefined`
    - `new`を使った呼び出し: その関数と等しくなる

    ### 例

    ```js
    function User(name){
      console.log(new.target);
    }

    User(); //undefined
    new User(); //function User{...}
    ```

    ### 実用例
    - 通常呼び出しと`new`の場合で動作を分岐する

    ```js
    function User(name){
      if(!new.target)
        return new User(name);

      this.name = name;
    }

    let john = User("John"); //newを忘れて呼び出すとリダイレクト
    console.log(john.name); //John
    ```

## Q37. コンストラクタからreturnが明示的に返却された時の挙動について知っていますか?

??? success
    ### returnで他のオブジェクトが返された場合
    - 該当オブジェクトが優先

    ### returnでプリミティブ値、もしては空が返された場合
    - thisが優先

    ### 使用例

    ```js
    function User(name){
      this.name = name;
      return {name: "Hoge"};
    }

    const hoge = new User("John");
    console.log(hoge.name); //Hoge

    function AuthUser(name){
      this.name = name;
      this.auth = false;

      return false;
    }

    const taro = new AuthUser("Taro");
    console.log(taro.name); //Taro
    ```

## Q38. コンストラクタ内にメソッドを配置できることを知っていますか?

??? success
    ### 使用例

    ```js
    function User(name){
      this.name = name;
      this.sayHi = function(){
        console.log("My name is " + this.name);
      };
    }

    const john = new User("John");
    john.sayHi(); //My name is John
    ```

## Q39. JSには、多くの組み込みオブジェクトに関するコンストラクタ関数が用意されていることを知っていますか?

??? success
    ### 例えば
    - Date
    - Set

## Q40. 中間プロパティが存在しない場合でも、`?.`を使えば安全にアクセスできる事を知っていますか?

??? success
    ### エラーになる例
    - `address`プロパティがないので、`undefined.street`にアクセスすることになり、エラー

    ```js
    let user = {}
    console.log(user.address.street); //Cannot read properties of undefined
    ```

    ### 三項演算子を用いた場合
    - エラーは起きないが冗長

    ```js
    let user = {};
    console.log(user.address ? user.address.street : undefined);
    ```

    ### 深くネストされたプロパティの場合、おぞましいことになる

    ```js
    let user = {};
    console.log(user.address  ? 
      user.address.street ? 
        user.address.street.name : 
        null : 
      null)

    console.log(user.address && user.address.street && user.address.street.name)
    ```

    ### ?.(オプショナルチェーン)とは
    - `?.`の前が`undefined`または`null`なら`undefined`を返す

    ### ?.を用いた場合

    ```js
    let user = {}
    console.log(user.address?.street?.name); //undefined
    ```

    ### 関数の場合`?.()`

    ```js
    let userAdmin = {
      logging(){
        console.log("test message");
      }
    }

    let userGuest = {};

    userAdmin.logging?.(); //test message
    userGuest.logging?.(); //何も行われない
    ```

    ### []でプロパティを読み取りたい場合

    ```js
    let key = "firstName";
    
    let user = {
      firstName: "John"
    };

    let anotherUser = null;

    console.log(user?.[key]); //John
    console.log(anotherUser?.[key]); //undefined
    ```
## Q41. ?.を使う際の注意点について知っていますか?


??? success
    ### 使いすぎるとエラーか仕様かわからなくなる

    ```js
    const user = {name : "hoge"};
    //何らかの不具合でname = nullに
    user.name = null;

    //プログラムが止まらないのでデバッグが困難になる
    console.log(user?.name?.address); //undefined 
    ```

    ### ?.の前の変数は定義されている必要がある

    ```js
    user?.address //user is not defined
    ```

    ### ?.の仕様(短絡評価)
    - 左部分が存在しない場合は、即座に評価を停止する

    ```js
    let user = null;
    let x = 0;
    user?.sayHi(x++);

    console.log(x); //0
    ```
## Q42. ?.を使っても書き込みはできないことを知っていますか?

??? success
    ### 使用例

    ```js
    const user = {name : "hoge"};
    user?.name = "John"; //Error: Invalid left-hand side in assignment
    ```

## Q43. 一意な識別子を作成したいときに用いる、Symbolについて知っていますか?

??? success
    ### Symbol
    - ユニークな識別子を表現する

    ### 作成方法

    - 値を空で作成

    ```js
    const id = Symbol();
    ```

    - シンボルに説明(シンボル名)を与える

    ```js
    const id = Symbol("id");
    ```

    ### Symbolの利点
    - ユニークであることが保証される
    - 同じシンボル名でも、それは単なるラベルであり、異なる

    ```js
    const id1 = Symbol("id");
    const id2 = Symbol("id");

    console.log(id1 == id2); //false
    ```

    ### Symbolの注意点
    ??? warning
        ### 暗黙的な文字列への自動変換がない

        ```js
        const id = 12;
        alert(12); 

        const id1 = Symbol("id"); 
        alert(id1); //TypeError

        //明示的に変換する
        alert(id1.toString()); //Symbol(id)
        ```

    ### 説明のみを表示したい場合
    - `.description`を使う

    ```js
    const id = Symbol("id");
    console.log(id.description);
    ```

## Q43. Symbolの使い道について知っていますか?

??? success
    ### 複数スクリプトからファイルを読み込んで、オブジェクトに書き込む場合
    - オブジェクトが別のスクリプトに属している場合、任意のフィールドを追加すると、安全ではない
    - Symbolを用いれば衝突は発生しない。
    - つまりSymbolはオブジェクトの隠れたプロパティ(他のコードがアクセス・追加できない)を作成する際に用いられる

    ```js
    //index.js
    let user = {
        name : "John"
    }

    //hoge1.jsとhoge2.jsで書き込み終了
    console.log(user); //{name: 'John', Symbol(id): 1, Symbol(id): 2}
    ```

    ```js
    //hoge1.js

    //index.jsからuserを読み取る

    let id = Symbol("id");
    user[id] = 1;
    console.log(user[id]); //1
    ```

    ```js
    //hoge2.js

    //index.jsからuserを読み取る
    let id = Symbol("id");
    user[id] = 2;
    console.log(user[id]); //2

    //此方からはhoge1.jsで追加したプロパティにはアクセスできない(アクセスするにはexportしてくる必要がある)
    ```

    ### シンボルは本当に隠れている?
    ??? warning
        - `Object.getOwnPropertySymbols(obj)`:全てのシンボルを取得
        - `Reflect.ownKeys(obj)`:シンボルを含むすべてのキーを取得
        - しかし、敢えてこれで呼び出している以上、意味を理解していると考えられる

        ```js
        const id = Symbol("id");
        const id1 = Symbol("id");

        const user = {
          name : "hoge",
          [id] : 0,
          [id1] : 1,
        }

        Object.getOwnPropertySymbols(user);
        //[Symbol(id), Symbol(id)]

        Reflect.ownKeys(user);
        //[name, Symbol(id), Symbol(id)]
        ```
    
## Q44. for...inでSymbolがスキップされることを知っていますか?

??? success
    ### 概要

    ```js
    let id = Symbol("id");

    let user = {
      name : "John",
      age : 30,
      [id] : 123,
    }

    for(const key in user){
      console.log(key);

      //John
      //30
    }
    ```

## Q45. グローバルシンボルについて知っていますか?

??? success
    ### グローバルシンボル
    - 同名のシンボルを同じシンボルとして扱いたい場合に用いる
    - `Symbol.for("シンボル名")`で作成/アクセスが可能

    ```js
    const id = Symbol.for("id");
    const idAgain = Symbol.for("id");

    const user = {
      [id] : 123,
    }

    console.log(id === idAgain); //true
    console.log(user[idAgain]); //123
    ```

    ### グローバルシンボルとその他のシンボルを区別する
    - `Symbol.keyFor`

    ```js
    const hoge = Symbol.for("id");
    const fuga = Symbol("id");
    console.log(Symbol.keyFor(hoge)); //id
    console.log(Symbol.keyFor(fuga)); //undefined
    ```

## Q46. オブジェクトからプリミティブへ自動変換される際に重要になるhintという概念を知っていますか?

??? success
    ### hint
    - オブジェクトの型変換はhintに従って行われる
    - 文字列を期待している場合は、stringヒントが使われる
    - 数値を期待している場合は、numberヒントが使われる
    - 期待される型が分からない場合は、defaultヒントが使われる

    ### hint具体例

    - stringヒント

    ```js
    //出力
    alert(obj);
    //プロパティキーとして使う
    anotherobj[obj] = 123;
    ```

    - numberヒント

    ```js
    //明示的な型変換
    let num = Number(obj);
    //単項演算子
    let n = +obj;
    //減算
    let delta = date1 - date2;
    //オブジェクト同士の比較
    let greater = obj1 > obj2;
    ```

    - defaultヒント

    ```js
    //文字列合成か、加算かわからない
    let total = obj1 + obj2;

    //プリミティブやシンボルとの比較
    if (obj1 == 2){};
    ```

## Q47. 変換される際の手順とそれに伴い使われる、3種のメソッドについて知っていますか?

??? success
    ### 変換する際の手順
    1. (存在するのなら)`obj[Symbol.toPrimitive](hint)`を呼び出す
    2. ない場合、hintが`string`なら、`obj.toString()`を試す。返り値がプリミティブでないなら`obj.valueOf()`を試す
    3. hintが`string`でなく`number`なら`obj.valueOf()`を試す。返り値がプリミティブでないなら`obj.toString()`を試す

    ### Symbol.toPrimitive
    - プリミティブな値を返却する
    - プリミティブな値を返さない場合、エラーになる
    - `hint`は`string`か`number`か`default`を取る
    - これがある場合、ルールに従って変換する

    ```js
    const user = {
      name : "John",
      money : 1000,

      [Symbol.toPrimitive](hint){
        console.log(`hint : ${hint}`);
        return hint == "string" ? 
          `{name : ${this.name}}` :
          this.money
      }
    }
    console.log(user); //string {name: "John"}
    console.log(+user); // number 1000
    
    //内部的にはthis.moneyで1000が返り、1000 + 500が出力されている
    console.log(user + 500);// default 1500


    ```

    ### toString()
    - デフォルトの場合`"[object Object]"`を返す

    ### valueOf()
    - デフォルトの場合、オブジェクト自身を返却する

    ### コード例

    ```js
    const user = {name : "hoge"};
    console.log(user.toString()); //[object Object]
    console.log(+user); //NaN
    console.log(user.valueOf() === user); //true
    ```

    ### 上記のメソッドを上書きし、任意の値を返す

    ```js
    const user = {
      name : "John",
      money : 1000,
      toString(){
        return this.name;
      },
      valueOf(){
        return this.money;
      }
    }
    alert(user); //John : stringなので
    console.log(+user + 500); //1500 : numberなので
    console.log(user + user); //2000 : defaultなので
    ```

    ### デフォルトのvalueOfについて
    - デフォルトはObjectを返すので、上書きされていない限り無視される
    - つまり、`toString()`のみを記載すれば、`number`であっても、`default`であっても、`toString()`内の値が返される

    ```js
    let user = {
      name : "John",
      //toString()内のreturnで数値を返すことももちろん可能
      toString(){
        return this.name;
      }
    }

    //1. valueOfを試す -> Objectが返る
    //2. toStringを試す
    console.log(user + 500); //John500
    ```

    ### どちらもオブジェクトを返すように書かれていた場合
    - エラーを吐く

    ```js
    const obj = {
      toString: function() {
        return {custom : 'value1'};
      },
      valueOf: function() {
        return { custom: 'value' };
      }
    };

    const result = String(obj); //Cannot convert object to primitive value
    ```

    ### プリミティブへ自動変換された値が正しい型でない場合
    - 更に型変換される

    ```js
    let obj = {
      toString(){
        return "2"
      },
    }

    String(obj) // "2"
    ```