# クイズ(上級7:Proxy)

## Q144 Proxyの使い方とその基本構文を知っていますか?

??? success
    ### Proxy
    - 別のオブジェクトをラップして、プロパティ等の読み取り/書き取りの操作を横取りする

    ### 基本構文
    - `target`:　ラップするオブジェクト
    - `handler`: 操作を横取りするオブジェクト。トラップと呼ばれるメソッドを持つ。

    ```js
    let proxy = new Proxy(target, handler)
    ```

## Q145 トラップを持たないproxyの挙動について知っていますか?

??? success
    ### トラップがない場合

    ```js
    let target = {};

    //空のハンドラ = トラップ無し
    let proxy = new Proxy(target, {});
    proxy.test = 5; //書き込み
    console.log(proxy.test); //読み取り
    for(let key in proxy) console.log(key); //iterator
    ```

    ###　説明
    - トラップがない場合、`proxy`上のすべての操作は元オブジェクト(target)に転送される
    - つまり、トラップがない場合`proxy`は意味を持たないラッパー

## Q146 トラップの意味と、エキゾチックオブジェクトについて知っていますか?

??? success

    ### 内部メソッドとトラップ
    - 通常、オブジェクトに対して何かしらの操作が行われる時、JSの仕様で言う「内部メソッド」と呼ばれるものが動作する
    - たとえば`[[GET]]`はプロパティを読み取るための内部メソッドを意味する
    - `Proxy`で定義されたトラップは、これらの内部メソッドが呼び出された時、代わりに独自の動作を行わせる

    ### エキゾチックオブジェクト
    - 基本的なメソッド(値を取得/set等。内部プロパティで定義されている)の動作を変更することで、独自の操作を行う組み込みオブジェクト

    ### エキゾチックオブジェクトの例
    - `Array`もエキゾチックオブジェクト
    - 単に値を`push`しただけなのに、`length`プロパティがインクリメントしているのがその証拠

    ### 内部メソッドとハンドラメソッド(トラップ)一覧

    | 内部メソッド            | ハンドラメソッド         | 発生タイミング                                                                                                  |
    | ----------------------- | ------------------------ | --------------------------------------------------------------------------------------------------------------- |
    | `[[Get]]`               | get                      | プロパティ読み取り                                                                                              |
    | `[[Set]]`               | set                      | プロパティ書き取り                                                                                              |
    | `[[HasProperty]]`       | has                      | `in`演算子                                                                                                      |
    | `[[Delete]]`            | deleteProperty           | `delete`演算子                                                                                                  |
    | `[[Call]]`              | apply                    | 関数呼び出し                                                                                                    |
    | `[[Construct]]`         | construct                | `new`演算子                                                                                                     |
    | `[[GetPrototypeOf]]`    | getPrototypeOf           | `Object.getPrototypeOf`                                                                                         |
    | `[[SetPrototypeOf]]`    | setPrototypeOf           | `Object.setPrototypeOf`                                                                                         |
    | `[[IsExtensible]]`      | isExtensible             | `Object.isExtensible`                                                                                           |
    | `[[PreventExtensions]]` | preventExtensions        | `Object.preventExtensions`                                                                                      |
    | `[[DefineOwnProperty]]` | defineProperty           | `Object.defineProperty`<br/>`Object.defineProperties`                                                           |
    | `[[GetOwnProperty]]`    | getOwnPropertyDescriptor | `Object.getOwnPropertyDescriptor`<br/>`for...in`<br/>`Object.keys/values/entries`                               |
    | `[[OwnPropertyKeys]]`   | ownKeys                  | `Object.getOwnPropertyNames`<br/>`Object.getOwnPropertySymbols`<br/>`for...in`<br/>`Object.keys/values/entries` |

## Q147 内部メソッドとトラップが満たすべき、不変条件を知っていますか?

??? success
    ### `[[Set]]`
    - 値が正常に書き込まるか否かで`true/false`を返す

    ### `[[Delete]]`
    - 値が正常に削除されたか否かで`true/false`

    ### `[[GetPrototypeOf]]`
    - `target`の`[[GetPrototypeOf]]`と同じ値を返す

    ### `[[Construct]]`
    - 結果は`Object`

    ### その他
    - `MDN`を見ること

## Q148 getトラップを用いて、存在しない値を取得した場合にデフォルト値を返す方法を知っていますか？

??? success
    ### get(target, property, receiver)
    - `handler`に上記の構文を渡す
    - `target`: ターゲットオブジェクト
    - `property`:プロパティ名
    - `receiver`:ターゲットが`getter`の場合、呼び出し内で`this`として扱われるオブジェクト

    ### 使用例

    ```js
    let numbers = [0,1,2];

    numbers = new Proxy(numbers, {
      get(target, prop){
        if(prop in target){
          return target[prop];
        }else{
          return 0; //default値
        }
      }
    })

    console.log(numbers[3]); //0
    ```

    ### 使用例2
    - 辞書になければ翻訳しない

    ```js
    let dictionary = {
      "こんにちは" : "Hello",
      "さようなら" : "Bye",
    }

    dictionary = new Proxy(dictionary, {
      get(target, phrase){
        if(phrase in target) return target[phrase];
        else return phrase;
      }
    })

    console.log(dictionary["こんばんは"]); //こんばんは
    console.log(dictionary["こんにちは"]); //Hello
    ```

    ### 注意点
    !!! warning
        - `Proxy`はターゲットオブジェクトを完全に置き換える必要がある
        - つまり、`numbers = new Proxy(numbers, ...)`
        - 置き換えた後で、ターゲットオブジェクトを参照すると、`Proxy`を書いた意味がない

## Q149 setトラップを用いて、特定の型以外の値を追加しようとしたとき、エラーを出力するようなオブジェクトを作ることができますか?

??? success
    ### set(target, property, value, receiver)
    - `target`: ターゲットオブジェクト
    - `property`:プロパティ名
    - `value`:プロパティ値
    - `receiver`:ターゲットが`setter`の場合、`this`として扱われるオブジェクト

    ### 使用例
    - `push`や`unshift`は`[[Set]]`という内部メソッドを用いて追加処理を行っている。
    - そのため各メソッドを実行すると、`Proxy`の`handler`にひっかかる

    ```js
    let numbers = [];

    numbers = new Proxy(numbers, {
      set(target, prop, val){
        if(typeof val == "number"){
          target[prop] = val;
          return true;
        }
        else{
          return false; //falseを返すと、TypeErrorがトリガされる
        }
      }
    })

    numbers.push(1);
    numbers.push(2);
    numbers.push("test");
    //Uncaught TypeError: 'set' on proxy: trap returned falsish for property '2'
    ```

## Q150 ownkeysと、getOwnPropertyDescriptorトラップを用いて、アンダースコアで始まるプロパティをスキップするイテレータを書くことができますか?

??? success
    ### プロパティをイテレートする殆どのメソッド
    - `[[OwnPropertyKeys]]`という内部メソッドを利用してプロパティのリストを取得する
    - `handler`でいうと`ownKeys(target)`
    - `ownKeys`はリストを返す必要あり(不変条件)
    - 返すリストの中身は、オブジェクトに所有させるキー

    ### ownKeys(target)の使用例

    ```js
    let user = {
      name : "John",
      age : 30,
      _password : "****",
    };

    user = new Proxy(user, {
      ownKeys(target){
        return Object.keys(target).filter(key => !key.startsWith("_"));
      }
    })

    /*
    Object.getOwnPropertyNames
    Object.getOwnPropertySymbols
    for ... in
    Object.keys/values/entries　
    に影響がある
    */ 
    for(let key in user) console.log(key); //name, age
    console.log(Object.values(user)); //John, 30
    Object.getOwnPropertyNames(user); //name, age
    ```

    ### 注意点
    - `return ["a", "b", "c"]`のように存在しないキーを返した場合、それは`enumerable`フラグを持たないので、列挙可能プロパティを羅列する`Object.keys`などで取得できない

    ```js
    let user = {};

    user = new Proxy(user, {
      ownKeys(target){
        return ["a", "b", "c"]; 
      }
    })

    console.log(Object.keys(user)); //
    console.log(Object.getOwnPropertyNames(user)); //["a","b","c"]
    ```

    ### getOwnPropertyDescriptor(target, prop)トラップ
    - `Object.keys`などは`enumerable`フラグの確認のため、すべてのプロパティに対して`[[GetOwnProperty]]`を呼び出してディスクリプタ内のフラグを取得している。
    - `[[GetOwnProperty]]`の`handler`は`getOwnPropertyDescriptor`トラップ
    - つまり、`getOwnPropertyDescriptor`トラップ内で、`enumerable : true`を持つディスクリプタを返せばよい
  
    ### 改善例

    ```js
    let user = {};

    user = new Proxy(user, {
      ownKeys(target){
        return ["a", "b", "c"]; 
      },

      getOwnPropertyDescriptor(target, prop){
        return {
          value : prop, //property記述子に設定しただけで、実際の値には設定していないことに注意
          writable : true,
          enumerable : true,
          configurable : true,
        }
      }
    })

    console.log(Object.keys(user)); //a,b,c
    ```

## Q151 _から始まるプロパティに対して外からアクセスさせたくないとき、deletePropertyトラップ等で保護が可能であることを知っていますか?

??? success
    ### 使用するトラップ
    - `get`: アクセス不可のプロパティを読み込んだ時エラーをスロー
    - `set`: 書き込み不可のプロパティに書き込んだ時エラーをスロー
    - `deleteProperty`: `_`を削除時にエラーをスロー
    - `ownKeys`: `_`をイテレートから除外

    ### 例

    ```js
    let user = {
      name : "John",
      _password : "****",
    }

    user = new Proxy(user, {
      get(target, prop){
        if(prop.startsWith("_")) throw new Error("Access denied");

        let value = target[prop];
        return (typeof value === "function") ? value.bind(target): value
      },

      set(target, prop, val){
        if(prop.startsWith("_")) throw new Error("Access denied");

        target[prop] = val;
        return true;
      },

      deleteProperty(target, prop){
        if(prop.startsWith("_")) throw new Error("Access denied");

        delete target[prop];
        return true;        
      },

      ownKeys(target){
        return Object.keys(target).filter(key => !key.startsWith("_"))
      }
    });

    try{
      console.log(user._password);
    }catch(e){console.log(e.message)} //Access denied

    try{
      user._password = "Neko";
    }catch(e){console.log(e.message)} //Access denied

    try{
      delete user._password;
    }catch(e){console.log(e.message)} //Access denied

    for(let key in user) console.log(key); //name
    ```

    ### 以下の行の意味合いが分かりますか?

    ```js
    return (typeof value === 'function') ? value.bind(target) : value;
    ```

    ### そもそもメソッド呼び出しとは
    - メソッドのプロパティにアクセスしてから、呼び出している
    - 内部メソッドでいうと`[[Get]]`してから`[[Call]]`
    - そのため`get`トラップ、`apply`トラップの順で呼ばれる
    - `get`はプロパティにアクセスしたタイミング
    - `apply`は呼び出したタイミング

    ### オブジェクトメソッド対策
    - 通常の場合、`.`の前が`this`となるので、`Proxy`されたオブジェクトが`this`となる
    - これに対して、`_`で始まるプロパティを取得しようとすると、`get`トラップに引っかかる
    - よってメソッドを通じたアクセスの場合、元ターゲットを`this`にしておく。こうすることで、メソッドからは、`this`を用いて`_`から始まるプロパティにアクセス可能になる

    ### 注意点
    !!! warning
        -　以上のような解決策は使用するべきではない
        - 何がプロキシされたもので、何が元オブジェクトなのか、わからなくなるため
        - オブジェクトが何度もプロキシされるような場合は殊更

## Q152 hasトラップとin演算子を用いて与えられた数値が特定の範囲内にあるか確かめるコードを書くことができますか？

??? success
    ### has(target, property)

    ```js
    let range = {
      start : 1,
      end : 10,
    }

    range = new Proxy(range , {
      has(target, prop){
        return (
          prop >= target.start && prop <= target.end
        );
      }
    })

    console.log(7 in range); //true
    console.log(12 in range); //false
    console.log(-2 in range); //false

    ```

## Q153 applyトラップを用いて、強力なラッパーを作成することができますか?

??? success
    ### apply(target, thisArg, args)
    - `thisArg`: 呼び出しに対する`this`の値
    - `args`: 引数

    ### 通常のラッパー関数
    - `ms`ミリ秒後に`f`の呼び出しを行う

    ```js
    function delay(f, ms){
      return function(){
        setTimeout(()=> f.apply(this, arguments), ms);
      }
    }

    //applyなのでargumentsは展開されて、1番目の値が使われる
    function sayHi(user){
      console.log(`Hello, ${user}!`);
    }

    sayHi = delay(sayHi, 3000);
    sayHi("John"); //3秒後に表示
    ```

    ### ラッパー関数の問題点
    - プロパティの読み書き操作などは転送しない
    - つまり、ラップした後は、`name`や`length`などの元の関数のプロパティにアクセスすることはできない

    ```js
    console.log(sayHi.name); //""
    console.log(sayHi.length); //0
    ```

    ### Proxyの場合
    - プロキシ上のすべての操作はトラップがない限り、すべてそのまま`target`に転送される
    - そのため、ラッピング後にプロパティにアクセスした場合、正しい値が返る。
    - 呼び出しの際だけ、独自の動作を規定している

    ```js
    function delay(f, ms){
      return new Proxy(f, {
        apply(target, thisArg, args){
          setTimeout(()=>{
            target.apply(thisArg, args)
          }, ms);
        },
      });
    }

    function sayHi(user){
      console.log(`Hello, ${user}`);
    }

    sayHi = delay(sayHi, 3000);
    console.log(sayHi.length); //1
    sayHi("John"); //Hello, John
    //今回は関数単体で呼び出しているのでthisはundefined

    ```

    ### ラッパーを選択するケース
    - 既存のコードとの互換性目的
    - 高度な制御が不要である場合
    - メモリ使用量を少しでも減らしたい場合
    - 簡潔さを追い求めている場合

## Q154 Proxyの作成を簡単に行えるReflectについて知っていますか?

??? success
    ### 内部メソッドを直接呼び出す?
    - `Reflect`オブジェクトを使うことで、それに近い操作ができる
    - `Reflect`が持つラッパーは内部メソッドを最小限にラップしたものである

    ### Reflect呼び出し一覧
    - 最後の引数として`receiver`を渡すこともできる

    | 操作               | `Reflect`呼び出し                                     | 内部メソッド            |
    | ------------------ | ----------------------------------------------------- | ----------------------- |
    | `obj[prop]`        | `Reflect.get(obj,prop)`                               | `[[Get]]`               |
    | `obj[prop] = val`  | `Reflect.set(obj,prop,val)`                           | `[[Set]]`               |
    | `delete obj[prop]` | `Reflect.deleteProperty(obj,prop)`                    | `[[Delete]]`            |
    | `prop in obj`      | `Reflect.has(target, propKey)`                        | `[[HasProperty]]`       |
    | `new F(val)`       | `Reflect.construct(F, val)`                           | `[[Construct]]`         |
    | `obj.hoge()`       | `Reflect.apply(target, thisArg, args)`                | `[[Call]]`              |
    | 返り値は`Boolean`  | `Reflect.defineProperty(target, propKey, attributes)` | `[[DefineOwnProperty]]` |
    |                    | `Reflect.getOwnPropertyDescriptor(target, propkey)`   | `[[GetOwnProperty]]`    |
    |                    | `Reflect.getPrototypeOf(target)`                      | `[[GetPrototypeOf]]`    |
    |                    | `Reflect.setPrototyoeOf(target, prop)`                | `[[SetPrototypeOf]]`    |
    |                    | `Reflect.ownKeys(target)`                             | `[[OwnPropertyKeys]]`   |
    |                    | `Reflect.isExtensible(target)`                        | `[[IsExtensible]]`      |
    |                    | `Reflect.preventExtensions(target)`                   | `[[PreventExtensions]]` |

    ### 注意点
    - `Reflect.getOwnPropertyDescriptor()`は第1引数が`Object`でないなら`Error`
    - `Object.getOwnPropertyDescriptor()`は強制的に`Object`に変換するのでこれが違い

    ### Proxyとの比較
    - `Proxy`でトラップ可能な内部メソッドは、すべて`Reflect`でも行える

## Q155 Reflectを用いて、操作を元オブジェクトに転送することができますか?

??? success
    ### get/setトラップ内で元オブジェクトに操作を転送

    ```js
    let user = {
      name : "John",
    }

    user = new Proxy(user,{
      get(target, prop, receiver){
        console.log(`GET ${prop}`);
        //obj[prop]をしたのと同じことになる
        return Reflect.get(target, prop, receiver);
      },

      set(target, prop, val, receiver){
        console.log(`SET ${prop}=${val}`);
        //obj[prop]=valをしたのと同じことになる
        return Reflect.set(target, prop, val, receiver);
      }
    })

    let name = user.name;
    user.name = "Hoge";

    //GET name
    //SET name=Hoge
    ```

    ### 疑問
    - `Reflect.get(target, prop, receiver)`を`target[prop]`に置き換えることはできないのか?

    ### getterとthis

    - 以下の例は問題ない

    ```js
    let user = {
      _name : "Guest",

      get name(){
        return this._name;
      }
    };

    let userProxy = new Proxy(user,{
      get(target, prop, receiver){
        return target[prop];
      }
    });

    console.log(userProxy.name)
    ```

    - 継承すると、誤ったふるまいを返す

    ```js
    let user = {
      _name : "Guest",
      get name(){
        return this._name;
      }
    };

    let userProxy = new Proxy(user, {
      get(target, prop, receiver){
        return target[prop]; //ここでtargetはuser
      }
    });

    let admin = {
      __proto__ : userProxy,
      _name : "Admin"
    };

    console.log(admin.name); //Guest??????????????
    ```

    ### 説明
    - `admin`に`name`プロパティはないので、`userProxy`を参照する。これは既定の動作
    - ここで`name`プロパティを読み取ろうとすると、`get`トラップが発生するが、この際の`target`は第1引数より`user`

    ### 対策
    - `receiver`を用いる。
    - `get`トラップの`receiver`は`getter`があった場合に、呼び出し内で`this`として扱われるオブジェクト
    - 通常は`proxy`オブジェクト自身(あるいは`proxy`から継承している場合は、継承したオブジェクトとなる)
    - 今回は`__proto__`で継承しているので後者

    ### 修正案

    ```js
    let user = {
      _name : "Guest",
      get name(){
        return this._name;
      }
    }

    let userProxy = new Proxy(user, {
      get(target, prop, receiver){
        return Reflect.get(target, prop, receiver);
      }
    });

    let admin = {
      __proto__ : userProxy,
      _name : "Admin",
    }

    console.log(admin.name);  //Admin
    ```

    !!! info
        ### 以下の様に書くこともできる
        - これは`Reflect`がトラップと同様の引数を受け入れるおかげ

        ```js
        get(target, prop, receiver){
          return Reflect.get(...arguments);
        }
        ```

## Q156 プロキシにも制限があることを知っていますか?

??? success
    ### 基本
    - プロキシは既存のオブジェクトの動作を最も低いレベルで調整できるが......

    ### 組み込みオブジェクトと内部スロット
    - 内部スロット：`Map, Set, Date, Promise`等の組み込みオブジェクトが使用するもの。プロパティと違い、内部で予約されている。
    - たとえば`Map`は内部スロット`[[MapData]]`にアイテムを保存し、アクセスの際は`[[Get]],[[Set]]`を利用しない。そのため、`Proxy`はインターセプトできない

    ### 例
    - `Proxy`は`[[MapData]]`を持たないので、以下の処理が行えない

    ```js
    let map = new Map();
    let proxy = new Proxy(map, {});

    proxy.set("test", 1);
    //Uncaught TypeError: Method Map.prototype.set called on incompatible receiver #<Map>
    //Map.prototype.setはthis.[[MapData]]にアクセスしようとする。これは現在proxy.[[MapData]]なので存在しない。よってエラー
    ```

    ###　修正例
    - メソッドにアクセスする際に、元オブジェクトを`this`として使用するようにする
    - `target.[[MapData]]`は存在するのでエラーは出ない

    ```js
    let map = new Map();

    let proxy = new Proxy(map, {
      get(target, prop, receiver){
        let value = Reflect.get(...arguments);
        return typeof value == "function" ? value.bind(target) : value;
      }
    })

    proxy.set("test", 1);
    console.log(proxy.get("test"));
    ```

    !!! info
        ### 朗報
        - `Array`には内部スロットがないので、普通に`Proxy`を使用可能。

    ### プライベートフィールドと内部スロット

    ```js
    class User{
      #name = "Guest";

      getName(){
        return this.#name;
      }
    }

    let user = new User();
    user = new Proxy(user, {});

    console.log(user.getName()); //Error??????????
    ```

    ### 理由
    - `Private`フィールドは内部スロットを使用して実装されている。
    - アクセス時に`[[Get]], [[Set]]`は利用しておらず、`Proxy`に該当内部スロットは備わっていないのでエラーが出る

    ### 対策(組み込みメソッドと同じでバインド)

    ```js
    class User{
      #name = "Guest"

      getName(){
        return this.#name;
      }
    }

    let user = new User();
    user = new Proxy(user, {
      get(target, prop, receiver){
        //getter対策を兼ねたアクセス
        let value = Reflect.get(...arguments);
        //関数なら、this=targetでbindして呼び出す。
        //元ターゲットは内部スロットを持っているのでthis.#nameへのアクセスに成功する
        return typeof value == "function" ? value.bind(target) : value;
      }
    });

    console.log(user.getName()); //Guest
    ```

    ### 注意点
    - `value.bind(target)`の箇所で、元オブジェクトをメソッドに公開しているので、メソッドで不要な処理を行うと、プロキシされたほかの機能が破壊される。

## Q157 Proxyが厳密等価をインターセプトできないことを知っていますか?

??? success
    ### target != Proxy
    - 元オブジェクトとプロキシオブジェクトは異なる
    - そのため厳密等価を透過させることはできない
    - オブジェクトは自身以外と厳密に等しくはならない

## Q158 取り消し可能(revocable)なプロキシとその使いどころを知っていますか?

??? success
    ### revocableなプロキシ
    - 後からいつでも無効にできるプロキシ

    ### 構文
    - `proxy`: プロキシ
    - `revoke`: 関数。プロキシから元オブジェクトへのすべての内部参照を削除する。よって呼び出すとプロキシが機能しなくなる

    ```js
    let {proxy, revoke} = Proxy.revocable(target, handler)
    ```

    ### 使用例

    ```js
    let object = {
      data : "Valuable data",
    }

    let {proxy, revoke} = Proxy.revocable(object, {
      get(target, prop){
        console.log(`GET ${prop}`);
        return Reflect.get(...arguments);
      }
    })

    console.log(proxy.data); //GET data

    revoke();

    console.log(proxy.data); 
    //Uncaught TypeError: Cannot perform 'get' on a proxy that has been revoked
    ```

    ### Proxyが到達不能になったとき、一緒にrevokeも削除する
    - メモリを有効活用するため

    ```js
    //key,valueのペアとしてproxy,revokeを設定することで、簡単に削除できるようにする

    let revokes = new WeakMap();

    let obj = {
      data : "Valuable data",
    }

    let {proxy, revoke} = Proxy.revocable(object, {});
    revokes.set(proxy, revoke);

    //沢山のコード......

    revoke = revokes.get(proxy);
    revoke();

    console.log(proxy.data); //Error
    ```