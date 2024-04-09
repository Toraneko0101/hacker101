# クイズ(中級10:プロパティ)

## Q213 オブジェクトプロパティにvalue(値)以外のフラグがあることを知っていますか?

??? success
    ### プロパティは単純な`key:value`ペアではない
    - 3つの特別なフラグが存在する

    ### writableフラグ
    - `true`の場合、変更可能
    - `false`なら読み取り専用

    ### enumerableフラグ
    - `true`の場合、ループで列挙
    - `false`なら列挙されない

    ### configurableフラグ
    - `true`の場合、プロパティの削除や属性の変更が可能
    - `false`ならできない

    ### ディスクリプタという表現について
    - 本来これらは、プロパティディスクリプタと呼ばれるが、プロパティフラグの方が分かりやすいため本ドキュメントではこちらも用いる

## Q214. Object.getOwnPropertyDescriptorを使って、プロパティの完全な情報を取得できることを知っていますか?

??? success
    ### descriptor = Object.getOwnPropertyDescriptor(obj, properyName)
    - `obj`:フラグを取得するオブジェクト
    - `properyName`: フラグを取得するプロパティ
    - `descriptor`: プロパティディスクリプタオブジェクトと呼ばれる。値とすべてのフラグを含む

    ### 使用例

    ```js
    const user = {
      name : "John",
    };

    const descriptor = Object.getOwnPropertyDescriptor(user, "name");

    console.log(JSON.stringify(descriptor, null, 2));

    /*
    {
      "value": "John",
      "writable": true,
      "enumerable": true,
      "configurable": true
    }
    */
    ```

## Q215. Object.definePropertyを使ってフラグの変更ができることを知っていますか?

??? success
    ### Object.defineProperty(obj,propertyName, descriptor)
    - `descriptor`: 適用するプロパティディスクリプタ
    - 新しいプロパティを作りたい場合、該当フラグについて言及がない場合は、`false`となる
    - 既存のプロパティの場合は、変更しないものは記述する必要がない

    ### 例

    ```js
    const user = {};

    Object.defineProperty(user, "name", {
      value: "Hoge",
      writable : true,
    })

    let descriptor = Object.getOwnPropertyDescriptor(user, "name");

    console.log(JSON.stringify(descriptor, null, 2));

    /*
    {
      "value": "Hoge",
      "writable": true,
      "enumerable": false,
      "configurable": false
    }
    */
    ```

## Q216. writableフラグがfalseの場合、再代入が不可能であることを知っていますか?

??? success
    ### 使用例
    - 再度`defineProperty`で上書きすれば変更可能

    ```js
    "use strict";
    let user = {
      name : "John",
    };

    Object.defineProperty(user, "name", {
      writable : false,
      enunerable : true,
      configurable : true,
    })

    user.name = "Hoge"; //Error
    ```

    !!! warning
        - `strict mode`でない場合、エラーは表示されないが、操作は成功しない

## Q217. enumerableフラグがfalseの場合、列挙の対象にならなくなることを知っていますか?

??? success
    ### 例
    - 組み込みの`toString()`は列挙可能ではない
    - `toString()`をオブジェクトに追加すると、`for ... in`の対象になる

    ### コード例

    ```js
    const user = {
      name : "John",
      toString(){
        return this.name;
      }
    };

    for(let key in user) console.log(key);
    //name, toString
    ```

    ### 属性変更

    ```js
    const user = {
      name : "John",
      toString(){
        return this.name;
      }
    };

    Object.defineProperty(user, "toString", {
      enumerable: false,
    })

    for(let key in user) console.log(key);
    ```

    ### 列挙可能でない場合、Object.keysからも除外される

    ```js
    Object.keys(user); //["name"]
    Object.values(user); //["John"]
    ```

## Q218. configurableフラグがfalseの場合、プロパティフラグの変更やプロパティの削除ができなくなることを知っていますか?

??? success
    ### Math.PIの属性を見てみる

    ```js
    const descriptor = Object.getOwnPropertyDescriptor(Math, "PI");

    console.log(JSON.stringify(descriptor, null, 2));

    //つまり、Math.PIは
    //再代入不可、列挙不可、変更不可
    /*
    {
      "value": 3.141592653589793,
      "writable": false,
      "enumerable": false,
      "configurable": false
    }
    */
    ```

    ### 単体なら値の再代入は可能

    ```js
    const user = {
      name : "John",
    }

    Object.defineProperty(user, "name", {
      configurable : false,
    });

    //代入は可
    user.name = "Pete";

    //プロパティの削除は不可
    delete user.name; //Error

    //プロパティフラグの変更は不可(例外あり)
    Object.defineProperty(user, "name", {
      enumerable : false; //Error
    })

    //これも不可
    Object.defineProperty(user, "name", {value: "Hoge"})
    ```

    ### 例外
    - 値の変更を防ぐ目的で、後程`writable`を`false`に変更することはできる
    - 逆(`false -> true`)はできない

    ```js
    Object.defineProperty(user, "name",{
      writable: false,
    })
    ```

## Q219 Object.definePropertiesで一度に多くのプロパティが定義できることを知っていますか?

??? success
    ### Object.defineProperties(obj, {prop1: descriptor1,...})

    ```js
    const user = {};
    //再代入禁止なプロパティをまとめて設定
    Object.defineProperties(user, {
      name : {value: "John", enumerable : true, configurable: true},
      address : {value : "Tokyo", enumerable : true, configurable : true}
    })
    ```

## Q220. getOwnPropertyDescriptors(obj)で一度に多くのプロパティ情報を取得できることを知っていますか?

??? success
    ### 通常使用

    ```js
    const user = {};
    //再代入禁止なプロパティをまとめて設定
    Object.defineProperties(user, {
      name : {value: "John", enumerable : true, configurable: true},
      address : {value : "Tokyo", enumerable : true, configurable : true}
    })

    const descriptors = Object.getOwnPropertyDescriptors(user);
    console.log(JSON.stringify(descriptors, null, 2));

    /*
    {
      "name": {
        "value": "John",
        "writable": false,
        "enumerable": true,
        "configurable": true
      },
      "address": {
        "value": "Tokyo",
        "writable": false,
        "enumerable": true,
        "configurable": true
      }
    }
    */
    ```

## Q221. フラグを含めて、正確にオブジェクトをクローンしたい際、getOwnPropertyDescriptorsが使えることを知っていますか?

??? success
    ### フラグを含んだクローン
    - 空オブジェクトに対して、`defineProperties`の引数として、`Object.getOwnPropertyDescriptors(target)`を指定
    - `target`内のフラグを含んだ情報がオブジェクトの形で返り値となるので、空オブジェクトはそれをすべて定義できる

    !!! warning
        - [[Prototype]]についてはコピーしない。
        - 詳細は上級1を参照のこと

    ### 失敗例

    ```js
    const user = {
      name : "John",
      address : "Tokyo",
    }

    Object.defineProperty(user, "address", {
      writable : false,
    })

    const clone = {...user};

    const cloneDescriptor = Object.getOwnPropertyDescriptors(clone);

    console.log(JSON.stringify(cloneDescriptor, null, 2));

    /*
      {
        "name": {
          "value": "John",
          "writable": true,
          "enumerable": true,
          "configurable": true
        },
        "address": {
          "value": "Tokyo",
          "writable": true, ----> コピーできていない
          "enumerable": true,
          "configurable": true
        }
      }
    */

    ```
    ### 成功例

    ```js
    const user = {
      name : "John",
      address : "Tokyo",
    }

    Object.defineProperty(user, "address", {
      writable : false,
    })
    
    const clone = Object.defineProperties(
      {},
      Object.getOwnPropertyDescriptors(user),
    );

    const cloneDescriptor = Object.getOwnPropertyDescriptors(clone);

    console.log(JSON.stringify(cloneDescriptor, null, 2));

    /*
    {
      "name": {
        "value": "John",
        "writable": true,
        "enumerable": true,
        "configurable": true
      },
      "address": {
        "value": "Tokyo",
        "writable": false, // ---------> 正確
        "enumerable": true,
        "configurable": true
      }
    }
    */
    ```

    ### for ... inとの違い

    - `for ... in`はシンボルプロパティを無視する
    - `Object.getOwnPropertyDescriptors`はシンボリックなものを含むすべてのプロパティとその詳細を返す

    ```js
    const user = {
      from : 0,
      to : 5,

      [Symbol.iterator](){
        this.current = this.from;
        return this;
      },

      next(){
        if(this.current <= this.to){
          return {done : false, value : this.current++};
        }
        else{
          return {done: true};
        }
      }
    }

    const descriptors = Object.getOwnPropertyDescriptors(user);

    console.log(descriptors);
    //{from: {…}, to: {…}, next: {…}, Symbol(Symbol.iterator): {…}}

    //JSONで表示する場合は、
    //undefined, function, Symbokの値が有効なJSON値でないことを理解する事
    ```

## Q222. Object.preventExtensionsを使ってプロパティの追加を禁止できることを知っていますか?

??? success
    ### 使用例

    ```js
    "use strict";
    const user = {
      name : "John",
    }

    Object.preventExtensions(user);

    user.name = "nezimi"; 
    delete user.name;
    user.address = "tokyo";- //Cannot add property address, object is not extensible
    ```

    ### Object.isExtensible
    - プロパティの追加が禁止されているなら`false`

    ```js
    Object.isExtensible(user); //false
    ```

## Q223. Object.sealを使うと、プロパティの追加削除を禁止し、configurableフラグをfalseに設定できることを知っていますか?

??? success
    ### Object.seal(obj)

    ```js
    "use strict";
    const user = {
      name : "hoge",
    }

    Object.seal(user);

    user.name = "nezumi";
    delete user.name; //Cannot delete property 'name' of #<Object>
    user.addresss = "tokyo"; // Cannot add property address, object is not extensible
    ```

    ### Object.isSealed(obj)
    - プロパティの追加、削除が禁止されていて
    - かつ、既存のプロパティのすべてが`configurable: false`なら`true`を返す

    ```js
    Object.isSealed(user); //true
    ```

## Q224. Object.freezeを使うと、configurableとwritableフラグをfalseにセットできることを知っていますか?

??? success
    ### Object.freeze(obj)
    - プロパティの追加/削除/変更を許さない
    - なお、プロパティの値がオブジェクトそのものであった場合は、内部は変更可能。浅い凍結であることに注意。
    - `configurable : false`
    - `writable : false`

    ```js
    "use strict";
    const user = {
      name : "hoge",
    }

    Object.freeze(user);

    user.name = "fuga"; //Cannot assign to read only property 'name' of object
    delete user.name; //Cannot delete property 'name' of #<Object>
    user.addresss = "tokyo"; // Cannot add property address, object is not extensible        
    ```

    ### Object.isFrozen
    - プロパティの追加/削除/変更の禁止
    - 全ての既存のプロパティにおける、`configurable`と`writable`が`false`
    - これを満たせば`true`を返す

    ```js
    Object.isFrozen(user);
    ```

    ### 深い凍結
    !!! warning
        - オブジェクトを完全に凍結するためには、オブジェクトが他のプロパティを、再帰的に`freeze`する必要がある
        - 全てを凍結すると、凍結させてはならない`[window]`のようなオブジェクトまで凍結するので、注意する事

## Q225. アクセサプロパティとデータプロパティの違いが分かりますか?

??? success
    ### データプロパティ
    - データとしてそのまま存在するプロパティ

    ### アクセサプロパティ
    - 値の取得やセットを行う関数
    - 外部からは通常のプロパティのように見える

## Q226. get()とset()で値の取得、セットを行うことができますか?

??? success
    ### 概要
    - `get()`は対象のプロパティが読まれた時に動作
    - `set()`は対象のプロパティに値が割り当てられた時に動作

    ### 注意
    - getterしかない場合、値を割り当てようとしてもできない
    - setterしかない場合、値を読み込もうとしてもできない

    ```js
    const user = {
      name : "John",
      surName : "Smith",

      get fullName(){
        return `${this.name} ${this.surName}`;
      }

      set fullName(value){
        [this.name, this.surName] = value.split(" ");
      }
    }

    //読み込んでいるのでget fullName()が実行される
    console.log(user.fullName); //John Smith

    //書き込んでいるのでset fullName()が実行される
    user.fullName = "Taro Yamada";
    ```

## Q227. オブジェクトリテラル生成時に、通常のデータプロパティとアクセサプロパティを同名で持った場合、どうなるか知っていますか?

??? success
    ### 答え
    - 後から列挙したプロパティが優先される

    ### 普通のプロパティでもそう

    ```js
    const user = {
      name : "hoge",
      name : "fuga",
    }

    user.name; //fuga
    ```

    ### 使用例1(データ優先)

    ```js
    const user = {
      name : "John",
      surName : "Smith",

      get fullName(){
        return `${this.name} ${this.surName}`;
      },

      set fullName(value){
        [this.name, this.surName] = value.split(" ");
      }

      fullName : "Neko",
    }

    console.log(user.fullName); //Neko 
    ```

    ### 使用例2(アクセサ優先)
    - `get()`か`set()`のどちらかを定義した時点で、データプロパティは消える

    ```js
    const user = {
      name : "John",
      surName : "Smith",
      fullName : "Neko",

      set fullName(value){
        [this.name, this.surName] = value.split(" ");
      }

    }

    console.log(user.fullName); //undefined    
    ```

    ### 使用例3(アクセサを用いて定義後にデータプロパティ追加)
    - アクセサを利用している扱いになる

    ```js
    const user = {
      name : "John",
      surName : "Smith",

      get fullName(){
        return `${this.name} ${this.surName}`;
      },

      set fullName(value){
        [this.name, this.surName] = value.split(" ");
      }
    }

    user.fullName = "hoge";
    console.log(user); //{name : "hoge", surName : undefined}
    ```

## Q228. アクセサプロパティのフラグ(=ディスクリプタ)がデータプロパティのそれと異なることを理解していますか?

??? success
    ### データプロパティ
    - `value`
    - `writable`
    - `enumerable`
    - `configurable`

    ### アクセサプロパティ
    - `get`
    - `set`
    - `enumerable`
    - `configurable`

    ### 差異
    - `value`と`writable`がない
    - `get`と`set`がある

    ### アクセサをdefinePropertyで作る場合

    ```js
    const user = {
      name : "John",
      surName : "Smith",
    }

    Object.defineProperty(user, "fullName", {
      get(){
        return `${this.name}` ${this.surName};
      },

      set(value){
        [this.name, this.surName] = value.split(" ");
      },

      enumerable : true,
      configurable : true,
    })


    ```

    !!! warning
        - 既存のプロパティである場合は、変更しないフラグについては明示的に指定する必要はない
        - つまり、同名のデータプロパティがあった場合、`defineProperty`で、フラグを明示しない場合でも、識別子は引き継がれる

        ```js
        const user = {
            name : "hoge",
        }

        Object.defineProperty(user, "name", {
          get(){return "hoge"},
        });

        const descriptors = Object.getOwnPropertyDescriptors(user);

        //getterとsetterは飛ばされる
        console.log(JSON.stringify(descriptors, null, 2));

        /*
        {
          "name": {
            "enumerable": true,
            "configurable": true
          }
        }
        */
        ```

    ### プロパティはアクセサかデータかのいずれか
    - 両方の定義を満たしていないとエラーとなる

    ```js
    //valueはデータプロパティ
    //getはアクセサプロパティ
    Object.defineProperty({}, "prop", {
      get(){
        return 1
      },

      value : 2,
    })
    ```


## Q229. Getter/Setterを用いて、値をフィルタすることができることを知っていますか?

??? success
    ### パスワードの長さを制限する例

    ```js
    const user = {
      get name(){
        return this._name;
      },

      set name(value){
        if(value.length < 4){
          alert("Name is too short. Please use at least 4 characters.");
          return;
        }
        this._name = value;
      }
    }

    user.name = ""; //Name is...
    user.name = "HogeString";
    ```

    ### 問題点
    - `user._name`でアクセス可能
    - アンダースコアで始まるプロパティにアクセスしないというのは、あくまでも慣用
    - クラスなら`#`を用いてプライベートが実現可能(後記)

## Q230. 互換性のためにgetterとsetterを使用できることを知っていますか?

??? success
    ### 想定するシナリオ
    - プロパティの一部を書き直したいが、他のスクリプトから該当のプロパティに対してアクセスしており、それを書き直す権限がない

    ### 元のコード

    ```js
    function User(name, age){
      this.name = name;
      this.age = age;
    }

    const john = new User("John", 25);
    console.log(john.age);
    ```

    ### 影響を及ぼさないように変更する
    - `age`を`birthday`から算出するアクセサに変える

    ```js
    function User(name, birthday){
      //instanceを作成するときのthisは{}
      this.name = name;
      this.birthday = birthday;

      Object.defineProperty(this, "age", {
        get(){
          const today = new Date();
          const todayYear = today.getFullYear();
          const birthdayThisYear = new Date(todayYear, this.birthday.getMonth(), this.birthday.getDate())

          const age = today >= birthdayThisYear ?
            todayYear - this.birthday.getFullYear() :
            todayYear - this.birthday.getFullYear() - 1
          
          return age;
        }
      });
    }

    //今日が1月31日の場合
    //annの今年の誕生日はまだ過ぎていない
    const john = new User("John", new Date(1999,0,31));
    const ann = new User("Ann", new Date(1999,1,1));

    console.log(john.age); //25
    console.log(ann.age); //24

    ```