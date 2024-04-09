# クイズ(上級4:非同期)

## Q70 非同期のコードがブラウザによって、いつ読み込まれるか分からないときの対処法が分かりますか?

??? success
    ### 注意点1
    !!! warning
        - ブラウザ関連のメソッドを使っているので、わからない場合は、`クイズ(DOM1)`を読んでおくこと

    ### スクリプトをロードする関数を考える

    ```js
    function loadScript(src){
      //1.<script>タグを作る
      let script = document.createElement("script");
      //2.指定されたsrcのスクリプトを読み込む
      script.src = src;
      //<head>の子要素として<script>を追加
      document.head.append(script);
    }

    //このスクリプトが function myFunc(){}を持つとする
    loadScript("/test/script.js")

    myFunc(); //Error
    ```

    ### Errorになる原因
    - まだスクリプトが読み込まれていないため
    - スクリプトがいつ読み込まれるのか知りたい

    ### 対処したコード
    - 関数が完了した後に、引数に渡した`callback`の処理を実行する

    ```js
    function loadScript(src, callback){
      let script = document.createElement("script");
      script.src = src;

      //完全に読み込まれたら実行。
      script.onload = () => callback(script);
      document.head.append(script);
    }

    //lodashライブラリをloadしたら
    //そのグローバル関数を利用する
    loadScript(
      'https://cdnjs.cloudflare.com/ajax/libs/lodash.js/3.2.0/lodash.js',
      (script) =>{
        console.log(`${script.src} is loaded.`);
        console.log(_); //f loadsh(value){...}
      }
    )

    ```

## Q71. 複数のスクリプトを順々に読み込みたい場合の方法が分かりますか?(コールバック地獄を知っている場合は飛ばす)

??? success
    ### 自然な解決策
    - `callback`の中に2つ目の`loadScript`呼び出しを書く
    - そうすれば、1つ目の読み込みが済んだ後に呼び出すことができる

    ### コード例

    ```js
    //loadScriptの定義はQ70に記載

    loadScript("./test/script1.js", (script)=>{
      console.log(`${script.src} is loaded`);

      loadScript("./test/script2.js", (script)=>{
        console.log("the second script is loaded");
      })
    })
    ```

## Q72. スクリプト読み込みが失敗した場合の対応策が分かりますか?

??? success
    ### .onerror
    - 読み込みエラーを追跡

    ### コード例
    - `callback`の最初の引数はエラーが発生した用とする
    - 成功時は、エラーオブジェクトは`null`
    - 失敗時は、エラーオブジェクトを作成する
    - 分岐処理を書けるので、単一の関数で処理可能

    ```js
    function loadScript(src, callback){
      let script = document.createElement("script");
      script.src = src;

      //ロードが成功した場合
      script.onload = () => callback(null, script);
      //エラーが発生した場合(scriptはundefined)
      script.onerror = () => callback(
        new Error(`Script load error for ${src}`)
      )

      document.head.append(script);
    }

    loadScript("./test/script.js", (error, script)=>{
      if(error){
        //error処理
        console.log("something error");
      }else{
        //成功時の処理
        console.log("script loaded.")
      }
    })
    ```

## Q73 多数のスクリプトを順々に読み込んで行く時、Q71のようにネストした呼び出しを行うと、地獄を見る理由が分かりますか？

??? success
    ### 複数のスクリプトを入れ子で読み込んだ例

    ```js
    loadScript("test1.js", (error, script)=>{
      if(error){
        handleError(error);
      }else{
        //何かしらの処理
        loadScript("test2.js", (error, script)=>{
          if(error){
            handleError(error);
          }else{
            //何かしらの処理
            loadScript("test3.js", (error, script)=>{
              if(error){
                //何かしらの処理
                handleError(error);
              }else{
                //さらに続く
              }
            })
          }
        })
      }
    })
    ```

    ### 問題点
    - コードが深くなっていく
    - 5つも重なれば制御は不可能に近くなる
    - 上記のような例を __コールバック地獄__ という
  
    ### 対策?

    ```js
    loadScript("test1.js", step1);

    function step1(error, script){
      if(error){
        handleError(error);
      }else{
        //何かしらの処理
        loadScript("test2.js", step2);
      }
    }

    function step2(error, script){
      if(error){
        handleError(error);
      }else{
        //何かしらの処理
        loadScript("test3.js", step3);
      }
    }

    //...以下繰り返し
    ```

    ### 問題点
    - コードがバラバラになる
    - IDEでジャンプする羽目になる
    - 視点の行き来が発生し、意図が分かりにくい

    ### 本来の対策
    - `Promise`を使う(後述)

## Q74 Promiseの生成方法と、resolve/rejectが呼ばれるまでの流れを知っていますか?

??? success
    ### Promiseコンストラクタの構文
    - `executor`: `new Promise`が作成されると自動的に実行される関数

    ```js
    new Promise(executor)
    ```

    ### executorの中身
    - `resolve`: 関数。解決した時に呼び出す
    - `reject`: 関数。失敗したときに呼び出す

    ```js
    function((resolve, reject){
      //...
    })
    ```

    ### resolve, rejectの呼び出し方法
    - `value`:結果
    - `error`:エラーオブジェクト

    ```js
    resolve(value);
    reject(error);
    ```

    ### 要約すると
    1. `new Promise`が行われると、`execute`内の処理が実行される
    2. `resolve`が呼ばれると、ジョブ完了の合図
    3. `reject`が呼ばれると、失敗の合図

## Q75. promiseオブジェクトが持っている内部プロパティについて知っていますか?

??? success
    ### state([[PromiseState]])
    - `pending`: 初期状態。保留中を意味する
    - `fulfilled`: `resolve`が呼ばれた後。完了を意味する
    - `rejected`: `reject`が呼ばれた後。拒否を意味する

    ### result([[PromiseResult]])
    - `undefined`: 初期値
    - `value`: `resolve(value)`が呼ばれた場合
    - `error`: `reject(error)`が呼ばれた場合

    ### つまり?
    - `executor`によって、`promise`は以下の状態になる

    ```
    まだ処理中の場合
      state: pending
      result: undefined
  
    成功した場合
      state: fulfilled
      result: value
    
    失敗した場合
      state: rejected
      result: error
    ```

    ### 解決または拒否された状態について
    - `settled`(定まった状態)と呼ばれる
    - `settled`は`fulfilled or rejected`のことを指す

## Q76. 上記までの説明をもとに、resolve/rejectした結果を、promiseオブジェクトに返すことができますか？

??? success
    ### 使用例(resolve)

    ```js
    let promise = new Promise((resolve, reject)=>{
      //valueがdone!なので、これが[[PromiseResult]]となる
      setTimeout(()=> resolve("done!"), 1000);
    })

    //1秒後に見たpromise

    /*
    Promise {<fulfilled>: 'done!'}
    [[Prototype]]: Promise
    [[PromiseState]]: "fulfilled"
    [[PromiseResult]]: "done!"
    */


    ```

    !!! warning
        - 内部プロパティに直接アクセスすることはできない
        - 代替として`.then/catch`メソッドを利用する(後述)

    ### 使用例(reject)

    ```js
    let promise = new Promise((resolve, reject)=>{
      //errorが、new Error("failure")なので、これが[[PromiseResult]]となる
      setTimeout(()=>reject(new Error("failure")), 1000);
    })

    //1秒後に見たpromise

    /*
    Promise {<rejected>: Error: failure
        at <anonymous>:2:29}
    [[Prototype]]: Promise
    [[PromiseState]]: "rejected"
    [[PromiseResult]]: Error: failure at <anonymous>:2:29
      message: "failure"
      stack: "Error: failure\n    at <anonymous>:2:29"    
    */
    ```

    !!! warning
        ### rejectの引数はErrorオブジェクト以外でもいい?
        - `Error`オブジェクトを利用した方がいい 

## Q77 executor内でresolveやrejectが2つとも、または同種が2回以上登場した場合、2個目以降はすべて無視されることを知っていますか?

??? success
    ### コード例

    ```js
    let promise = new Promise((resolve, reject)=>{
      resolve("done");

      reject(new Error("some error"));
      setTimeout(()=> resolve("second done"));
    })

    console.log(promise);
    //Promise {<fulfilled>: 'done'}
    ```

    ### Promiseの状態は変わらないが、
    - 処理が実行されないわけではない

    ```js
    let promise = new Promise((resolve, reject)=>{
      resolve("done");
      console.log("neko");
    })

    //neko
    ```

## Q78 resolve/rejectが即時実行可能であることを知っていますか?

??? success
    ### 普通は非同期処理を行った後に呼び出すが
    - すぐに呼び出すことも可能

    ```js
    let promise = new Promise((resolve, reject)=>{
      resolve(true);
    })
    ```

    ### 即座に呼び出す場合とは?
    - ジョブを開始したが、既にすべての処理が完了していた場合
    - または、キャッシュが存在していた場合など



## Q79 executorから返したオブジェクトの内部プロパティにアクセスするために、.then, .catch, finallyが使えることを知っていますか?

??? success
    ### .then
    - 第1引数:`promise`が`resolve`された時に実行。引数はvalue
    - 第2引数:`promise`が`reject`された時に実行。引数はerror
    - いずれにせよ、引数には`[[PromiseResult]]`の値を用いる

    ```js
    promise.then(
      function(result){/*成功した結果を扱う*/},
      function(error){/*エラーを扱う*/};
    )
    ```

    ### resolveされた場合

    ```js
    let promise = new Promise((resolve, reject)=>{
      setTimeout(()=> resolve("done!"), 100);
    })

    promise.then(
      result => console.log(result), //doneを表示
      error => console.log(error) //実行されない
    )
    ```

    ### rejectされた場合

    ```js
    let promise = new Promise((resolve, reject)=>{
      setTimeout(()=> reject(new Error("failure")), 100);
    });

    promise.then(
      result => console.log(result),
      error => console.log(error) //エラーを表示
    )

    /*
    Error: failure
        at <anonymous>:2:30
    */
    ```

    ### 正常終了だけに関心がある場合
    - 第1引数だけ書く

    ```js
    promise.then(
      result => console.log(result);
    )
    ```

    ### エラーにだけ関心がある場合
    - 第1引数は`null`にする

    ```js
    promise.then(
      null,
      error => console.log(error);
    )
    ```

    ### catch
    - `.then(null, function)`を書き直せる
    - `.catch(function)`という形で

    ```js
    let promise = new Promise((resolve, reject)=>{
      setTimeout(()=> reject(new Error("failure")), 100);
    })

    promise.catch(
      error => console.log(error)
    )

    /*
    Error: failure
    at <anonymous>:2:30
    */
    ```

    ### finally
    - `Promise`が解決/拒否になったとき、必ず実行される
    - クリーンアップなどを実行するために使用する

    ```js
    new Promise((resolve, reject)=>{
      /*resolve or reject*/
      resolve(123);
    })
    .finally(()=>{/*クリーンアップ処理*/})
    .then(
      result => console.log(result),
      err => console.log(err)
    )
    ```

    ### Q71をpromiseを使って実装した場合の例
    - `loadScript`を呼び出すときに`callback`を書く必要がなく、`then`等で後から処理できるので、柔軟性に富む

    ```js
    //callbackは要らなくなる
    const loadScript = src =>{
      return new Promise((resolve, reject)=>{
        let script = document.createElement("script");
        script.src = src;

        script.onload = () => resolve(script);
        script.onerror = () => reject(new Error("Script load error: " + src));

        document.head.append(script);
      });
    }

    let promise = loadScript("https://cdnjs.cloudflare.com/ajax/libs/lodash.js/4.17.11/lodash.js")

    promise.then(
      script => console.log(`${script.src} is loaded!`),
      error => console.log(`Error: ${error.message}`)
    )

    promise.then(script => console.log("Another handler..."));
    ```

## Q80 settledであるpromiseにもハンドラがセットできることを知っていますか?

??? success
    ### 通常の場合
    - `promise`が`pending`であれば`.then/catch/finally`は結果を待つ

    ### 既にsettledの場合
    - 直ちに実行される

    ### コード例

    ```js
    let promise = new Promise(resolve => resolve("done"));

    promise.then(
      result => console.log(result) //done
    )
    ```

## Q81 順次実行されるタスクを、Promiseを用い、コールバック地獄に陥らずに処理することができますか?

??? success
    ### よくある失敗例
    - 以下では、単一の`Promise`に複数の`.then`を追加している
    - `.then`は`Promise`を返すが、元の`Promise`を変更するわけではない
    - 従って、それぞれが独立で処理を行うだけに終わる

    ```js
    let promise = new Promise((resolve, reject)=>{
      setTimeout(()=> resolve(1), 1000);
    })

    //[[PromiseResult]]の値は変わらない
    promise.then(result=>{
      alert(result); //1
      return result * 2; //2
    })

    promise.then(result=>{
      alert(result); //1
      return result * 2; //2
    })

    promise.then(result=>{
      alert(result); //1
      return result * 2; //2
    })    
    ```

    ### 成功例
    - `promise.then`の呼び出しは`promise`を返す
    - 値を返す場合、返り値が`value`となる
    - これを生かして、返り値に対して、また`then`を呼び出す
    - これを`promise`チェーンと呼ぶ

    ```js
    let promise = new Promise((resolve, reject)=>{
      setTimeout(()=> resolve(1), 1000);
    })

    promise
    .then(result=>{
      alert(result); //1
      return result * 2;
    })
    .then(result=>{
      alert(result); //2
      return result * 2;
    })
    .then(result=>{
      alert(result); //4
      return result * 2;
    })
    ```


## Q82. thenメソッド内で値を返しているにもかかわらず、返り値がPromiseになる理由が分かりますか?

??? success

    ### return result * 2;がPromiseを返す理由
    - `then`メソッドの引数に渡されたコールバック関数が値を返すと、その値は自動的に`Promise.resolve`にラップされる
    - なお、`Promise`オブジェクトそのものを`return`した場合は、その`Promise`がそのまま返される

    ```js
    Promise.resolve(result * 2);
    //Promise {<fulfilled>: 4}
    ```

    ### return errの場合は?

    ```js
    Promise.reject(err);
    /*
    Promise {<rejected>: Error: message
      at <anonymous>:1:16}
    */
    ```

## Q82. Promiseを用いて非同期アクションのチェーンを組み立てることができますか?

??? success
    ### .thenはsettledになるまで待機するので
    - 前の`then`メソッドが`settled`になり次第、次の`then`に`Promise`が渡される

    ```js
    new Promise((resolve, reject)=>{
      setTimeout(()=> resolve(1), 1000);
    })
    .then(result=>{
      alert(result); //1

      return new Promise((resolve, reject)=>{
        setTimeout(()=> resolve(result * 2), 1000)
      });
    })
    .then(result =>{
      alert(result); //2
      return new Promise((resolve, reject)=>{
        setTimeout(()=> resolve(result * 2), 1000);
      })
    })
    .then(result => alert(result)); //4
    ```

    ### Q73のコールバック地獄を書き直す

    ```js
    const loadScript = src =>{
      return new Promise((resolve, reject)=>{
        let script = document.createElement("script");
        script.src = src;

        //正常終了ならvalue = script
        script.onload = () => resolve(script);
        script.onerror = () => reject(new Error("Script load error: " + src));

        document.head.append(script);
      });
    }

    loadScript("test1.js")
      .then(script => loadScript("test2.js"))
      .then(script => loadScript("test3.js"))
      .then(script => loadScript("test4.js"))
      .then(script =>{
        //既に正常にloadしているので関数を正しく使える
        one();
        two();
        three();
        four();
      })
    ```

## Q83. thenableオブジェクトについて知っていますか?

??? success

    ### そもそも
    - JSにおいて`then`という名前のメソッドは特別な意味を持つ

    ### thenable
    - メソッド`.then()`を持つオブジェクトやインスタンス
    - `promise`と同等に扱われる

    ### 存在理由
    - `promise`互換なオブジェクトを実装するため
    - サードパーティが主に用いる

    ### その他利点
    - `Promise`から`then`等を継承することなく、カスタムしたオブジェクトやインスタンスを`promise`チェーンに組み込める


    ### 例

    ```js
    class Thenable{
      constructor(num){
        this.num = num;
      }

      //Promiseに対してthen
      then(resolve, reject){
        alert(resolve);// function() { native code }
        setTimeout(()=> resolve(this.num * 2), 1000);
      }
    }

    new Promise(resolve => resolve(1))
      .then(result =>{
        //1. thenというメソッドが呼び出し可能か調べる
        //2. 呼び出し可能ならresolve, rejectを引数とし、いずれかが呼び出されるまで待つ
        //3. ThenableはPromiseでラップされ、Promiseオブジェクトとして返される
        return new Thenable(result);//num = 1
      })
      .then(alert); //2
    ```

## Q84. リモートサーバから情報をロードするために、fetchメソッドが使えることを知っていますか?

??? success
    ### fetchの構文
    - `url`: ネットワークリクエストを行うURL
    - 返り値は`promise`
    - 正常に取得できた場合`[[PromiseResult]]`として`Response`を受け取る

    ```js
    let promise = fetch(url);
    ```

    ### 使用例

    ```js
    //promiseを返す
    fetch("https://pokeapi.co/api/v2/pokemon/ditto")
    //リモートサーバが応答したらこのthenを実行
    //まだ完全なテキストはダウンロードされていない
    .then(response => {
      //response.text()はレスポンスのダウンロードが完了したら新たなpromiseを返す
      return response.text();
    })
    //ダウンロード完了後、リモートファイルの中身を閲覧
    .then(text => alert(text));

    //fetchの中身について
    console.log(fetch("https://pokeapi.co/api/v2/pokemon/ditto"))
    /*
      Promise {<fulfilled>: Response}
      [[Prototype]]: Promise
      [[PromiseState]]: "fulfilled"
      [[PromiseResult]]: Response
      body: ReadableStream
      bodyUsed: false
      headers: Headers {}
      ok: true
      redirected: false
      status: 200
      statusText: ""
      type: "cors"
      url: "https://pokeapi.co/api/v2/pokemon/ditto"
      [[Prototype]]: Response
    */
    ```

    ### 使用例2
    - `fetch`してきたデータを見やすい`JSON`のフォーマットで見る

    ```js
    //response.json(); ResponseオブジェクトからJSON形式のデータを取得し、該当データをJSオブジェクトに変換
    fetch("https://pokeapi.co/api/v2/pokemon/ditto")
    .then(response => response.json())
    .then(data => {
        const prettyJSON = JSON.stringify(data, null, 2);
        console.log(prettyJSON);
    })
    .catch(error => console.error("Fetch error: ", error));
    ```

## Q85 非同期のアクションを行う際、常にPromiseを返した方がいい理由が分かりますか?

??? success
    ### 答え
    - 将来必要になったときに、簡単に`Promise`チェーンを追加できるようにするため

    ### 例


    ```js
    const loadJson = url =>{
      return fetch(url)
        .then(response => response.json());
    }

    const loadGithubUser = name =>{
      return loadJson(`https://api.github.com/users/${name}`);
    }

    const showAvatar = githubUser =>{
      return new Promise((resolve, reject)=>{
        let img = document.createElement("img");
        img.src = githubUser.avatar_url;
        img.className = "promise-avatar-example";
        document.body.append(img);

        setTimeout(()=>{
          img.remove();
          //resolveを呼び出しているので、
          //次のチェーンを作成した時、簡単にgithubUserのデータを転送可能
          resolve(githubUser);
        }, 3000)
      })
    }

    loadJson("./user.json")
      .then(user => loadGithubUser(user.name))
      .then(githubUser => showAvatar(githubUser))
      .then(githubUser => console.log(`showing ${githubUser.name}`))
    ```


## Q86 then(f1, f2)とthen(f1).catch(f2)が異なることを理解していますか?

??? success

    ### then(f1, f2)

    ```js
    //f1で出たエラーはf2には渡されない
    promise
      .then(f1, f2)
    ```

    ### then(f1).catch(f2)

    ```js
    //f1で出たエラーはcatchで処理される
    promise
      .then(f1)
      .catch(f2)
    ```

## Q87 promiseがrejectされた時の挙動について理解していますか?

??? success
    ### promiseがrejectされると
    - 制御は最も近い`reject`ハンドラに移る

    ### 直後に書く必要はない
    - 末尾に`.catch`を1つ書いた場合、すべてのエラーをキャッチする
    - エラーが出ない場合、この`.catch`は無視される

    ```js
    fetch("test.json") //rejectされたと仮定する
      .then(response => response.json())
      .then(json => console.log(JSON.stringify(json, null, 2)))
      .catch(err => console.log(err)); //制御はここに移動
    ```

## Q88. executorとpromiseハンドラが暗黙のtry...catchを持っていることを知っていますか?

??? success
    ### エラーが起きた場合
    - キャッチして`reject`として扱う
    - スクリプトが死ぬわけではない

    ### executorにおける暗黙のtry...catch
    - 1つ目のコードと2つ目のコードは同じ

    ```js
    //1つ目
    new Promise((resolve, reject)=>{
      throw new Error("Whoops");
    }).catch(alert);
    ```

    ```js
    //2つ目
    new Promise((resolve, reject)=>{
      reject(new Error("Whoops"));
    }).catch(alert);
    ```

    ```js
    //これでもコードは止まらない
    new Promise((resolve, reject)=>{
      hoge();
    }).catch(alert); //ReferenceError: test is not defined
    ```

    ### promiseにおける暗黙のtry...catch

    ```js
    //やはりスクリプトは死なない
    new Promise((resolve, reject)=>{
      resolve("ok");
    }).then(result =>{
      throw new Error("Hogeeee!");
    }).catch(alert); //Error: Hogeeee!
    ```

## Q89. promise.then(console.log)が成功する理由が分かりますか?

??? success
    ### 多分これは理解できると思う

    ```js
    new Promise((resolve, reject)=>{
      resolve("ok");
    }).then(result => console.log("ok"));
    ```

    ### ???????????

    ```js
    new Promise((resolve, reject)=>{
      resolve("ok");
    }).then(console.log); //ok
    ```

    ### 理解その1
    - `promise.then(fn)`における、`fn`は引数に関数を指定することを意味する。
    - つまり、関数を指定できればなんでもいい
    - 上の例は、無名関数
    - `console.log`も関数

    ```js
    typeof console.log //function
    ```

    ### たとえば
    - コールバック関数(引数として渡される関数)を考える
    - __`forEach`はコールバック関数に対して、要素の値、インデックス、配列そのものを引数として渡す__
    - このコールバック関数は自作してもいいし、組み込みのものを利用してもいい
    - いつもは自作していたため

    ```js
    [1,2,3].forEach((item, index, arr)=>{
      console.log(item);
    })
    ```

    - としていたわけだ
    - もちろん、自作する際は第2引数や第3引数を書かなくてもいい
    - そうした場合、渡すはずだった引数は使われない
    - これは以下からも明確だと思う

    ```js
    [1,2,3].forEach((...arr)=>{
      console.log(arr);
      //[1,0,Array(3)]
      //[2,1,Array(3)]
      //[3,2,Array(3)]
    })
    ```

    - ここで組み込みのものを利用してみる

    ```js
    [1,2,3].forEach(console.log);
    //1,0,[1,2,3]
    //2,1,[1,2,3]
    //3,2,[1,2,3]
    ```

    ### console.logというのは
    - 可変引数
    - つまり、全ての引数を受け取る(上の例でいう`...arr`と同じイメージ。実際には配列には格納しないが)

    ### 最初の話に戻ると
    - __promiseは1つの引数をfn(コールバック関数)に渡す__
    - これを`console.log`で受け取っているだけに過ぎない
    - `(a) => console.log(a)`とした場合は、受け取るはずの引数から1つを利用して、それを出力している
    - 単に`console.log`として場合は、すべての引数を受け取る。ただし今回は引数が1つなので同じ挙動となる

    ### alertの場合は?
    - promiseは1つの引数をfn(コールバック関数)に渡す
    - これを`alert`で受け取る

    ### 上手くいくわけ補足
    - console.logは`this`に強く依存していない

    ### もう1つコールバック関数の例
    - 関数に対して引数がちゃんと渡されていることを確認できる
    - ただし、このように渡すと、`this`を使うために`bind`する羽目になるので使用はほどほどに

    - arguments

    ```js
    function test(){
      console.log(arguments);
    }

    [1,2,3].map(test);
    //Arguments(3) [1, 0, Array(3), callee: ƒ, Symbol(Symbol.iterator): ƒ]
    //Arguments(3) [2, 1, Array(3), callee: ƒ, Symbol(Symbol.iterator): ƒ]
    //Arguments(3) [3, 2, Array(3), callee: ƒ, Symbol(Symbol.iterator): ƒ]

    //なにもreturnしていないので
    //[undefined, undefined, undefined]
    ```

    - args

    ```js
    function test(...args){
      console.log(args);
    }

    [1,2,3].map(test);

    // [1, 0, Array(3)]
    // [2, 1, Array(3)]
    // [3, 2, Array(3)]
    ```

    - おまけ

    ```js
    //つまり、thisを使わないなら、十分に使える

    function test(item, index){
      return [item, index];
    }

    [1,2,3].map(test);

    //[[1,0],[2,1],[3,2]]
    ```

## Q90 catchの中でエラーを投げたとき、制御が次の最も近いエラーハンドルに移ることを理解していますか?

??? success
    ### つまり再スロー

    ```js
    new Promise((resolve, reject)=>{
      throw new Error("Whoops");
    })
    .catch((error)=>{
      if(error instanceof SyntaxError){
        //error処理
      }
      else{
        throw error; //次の.catchに飛ぶ
      }
    })
    .then((result) => {
      console.log(result); //実行されない
    })
    .catch(error =>{
      console.log(error);
      /*
      Error: Whoops
          at <anonymous>:2:13
          at new Promise (<anonymous>)
          at <anonymous>:1:5
      */
    })
    ```

## Q91. 未処理のrejectを追跡する方法を知っていますか?

??? success
    ### 通常の場合
    - 末尾に`catch`または`then`の第二引数で処理するので、`reject`は正常に処理される

    ### 書き忘れた場合
    - `try...catch`の外でエラーが発生した場合と同じ

    ### 追跡する方法
    - ただしJSエンジンは未処理の`reject`を追跡する
    - ブラウザの場合、`unhandledrejection`でキャッチ可能

    ### 設定する意味
    - スクリプトが死ぬときに、情報を残すことができる

    ```js
    window.addEventListener("unhandledrejection", (event)=>{
        console.log(event.promise); //Errorを生成したpromise
        console.log(event.reason);//未処理のエラーオブジェクト
    });

    new Promise((resolve, reject)=>{
        throw new Error("Whoops");
    });

    /*
    Promise {<rejected>: Error: Whoops
        at <anonymous>:2:11
        at new Promise (<anonymous>)
        at <anonymous>:1:1}

    Error: Whoops
        at <anonymous>:2:11
        at new Promise (<anonymous>)
        at <anonymous>:1:1

    //この後に、実際にUncaught Errorでスクリプトは止まる
    */
    ```

## Q92. 非同期コールバックの中でthrowを用いてエラーを投げると、catchで補足できないことを知っていますか?

??? success
    ### これは正常に動く

    ```js
    new Promise(function(resolve, reject) {
      setTimeout(() => {
        reject(Error("Whoops!"));
      }, 1000);
    }).catch(alert);
    や
    new Promise(function(resolve, reject) {
        throw new Error("Whoops");
    }).catch(alert);
    ```

    ### これは動かない

    ```js
    new Promise(function(resolve, reject){
      setTimeout(() => {
        throw new Error("Whoops");
      }, 1000);
    }).catch(alert);
    ```
    
    ### 簡単な説明
    - `executor`と`promise`は確かに暗黙の`try...catch`を持っている
    - しかし`try...catch`は同期的な処理にしか対応していない
    - 従って、明示的に`reject`でエラーを囲む必要がある

    ### 理由
    - `setTimeout`は指定時間経過後に、イベントループにコールバックを追加する
    - そして、現在のスタックが空になると実行される
    - つまり、`setTimeout`が実行されるとき、既に`Promise`を作成したスタックは実行を終了しており、存在しない
    - そのため`setTimeout`から単にエラーを投げても`Promise`は処理できず、`throw`は`reject`として返されない
    - つまるところ、最初から`Promise.reject`として返せばいい。`Promise.reject`は常に新しい`Promise`で`error`をラップする

    ### まとめ
    - `Promise`のコールバック内にいるときは、いつでも`throw`を使用できる
    - ただ、他の非同期コールバック内(たとえば`setTimeout`)にいる場合は、`reject`を使用する必要がある


    ### メモ
    - 非同期でないときは`throw`を使い、
    - 非同期の時は`reject`を使うと
    - コードの可読性が上がる

## Q93 並列に複数のpromiseを実行し、すべてが完了するまで待ちたい場合に、Promise.allを使えることを知っていますか?

??? success
    ### promise = Promise.all(iterable)
    - `iterable`:通常は`promise`の配列を取る
    - `promise`:新しいpromiseの`iterable`
    - 全てが完了すると、promiseの`iterable`が返る

    ### 注意点
    - 格納される順番は、元のpromiseの順番と同じ
    - 解決/拒否される時間は関係ない

    ### 使用例1

    ```js
    let promise = Promise.all([
      new Promise((resolve, reject)=>
        setTimeout(()=> resolve(1), 3000)
      ),
      new Promise((resolve, reject)=>
        setTimeout(()=> resolve(2), 2000)
      ),
      new Promise((resolve, reject)=>
        setTimeout(()=> resolve(3), 1000)
      )
    ])

    /*2秒後*/
    //Promise {<pending>}
    //[[Prototype]]: Promise
    //[[PromiseState]]: "pending"
    //[[PromiseResult]]: undefined
    
    /*3秒後*/
    //Promise {<fulfilled>: Array(3)}
    //1,2,3
    ```

    ### 使用例2
    - 処理データの配列をPromiseの配列に`map`
    - それを`Promise.all`でラップする

    ```js
    let urls = [
      "https://api.github.com/users/Toraneko0101",
      "https://api.github.com/users/NekoInu-Nezumiko",
    ]

    //urlをpromiseのfetchへマップ
    let requests = url.map(url => fetch(url));

    //全てのジョブ(fetch)が解決されるまで待つ
    Promise.all(requests)
      .then(responses => responses.forEach(response =>{
        console.log(`${response.url}: ${response.status}`)
        //https://api.github.com/users/Toraneko0101: 200
        //https://api.github.com/users/NekoInu-Nezumiko: 200
      } 
      ))
    ```

## Q94. Promise.allにおいて、いずれかのpromiseがrejectされた場合、他のpromiseの結果は無視されることを知っていますか?

??? success
    ### 概要
    - いずれかの`promise`が`reject`されると、`Promise.all`の結果は即座に`rejected`になる

    ### 例

    ```js
    let promise = Promise.all([
      new Promise((resolve, reject)=>
        setTimeout(()=> reject(10), 3000)
      ),
      new Promise((resolve, reject)=>
        setTimeout(()=> reject(20), 2000)
      ),
      new Promise((resolve, reject)=>
        setTimeout(()=> resolve(30), 1000)
      )
    ])

    //1つのrejectが返された時点で、他の結果は無視される
    //Promise {<rejected>: 20}
    ```

## Q95. Promise.all(iterable)の中で非promiseの項目を使った場合、Promise.resolveでラップされることを知っていますか?

??? success
    ### 例

    ```js
    Promise.all([
      new Promise((resolve, reject)=>{
        setTimeout(()=> resolve(1), 1000);
      }),
      2, //Promise.resolve(2)と扱われる
      3, //Promise.resolve(3)と扱われる
    ])

    //setTimeout(()=>2,1000),の場合、timerIdが返されるので
    //非同期の場合は、きちんとPromiseを使う

    //setTimeout(()=> Promise.resolve(2), 1000)もダメ
    //以下を考えてみるとわかる
    //Promise.resolve(setTimeout(()=>Promise.resolve(2),1000)); //timerId
    //この場合(Promiseでなく他の非同期関数を指定した場合)も、Promise.resolveで囲まれる。
    ```

## Q96. いずれかのpromiseがrejectされてもすべてのpromiseが解決するまで待ちたい場合、Promise.allSettledが使えることを知っていますか?

??? success
    ### Promise.allとの違い
    - 1つ以上の`promise`が`reject`されても最後まで待つ
    - 結果の`promise`配列は、渡した配列と同じ大きさ

    ### 使用例
    - 結果は、`[[PromiseResult]]`の各要素に、`iterable`なオブジェクトとして格納され以下のプロパティを持つ
    - `status`: `fulfilled/rejected`
    - `fulfilled`の場合: `value`
    - `rejected`の場合: `error`

    ```js
    let promise = Promise.allSettled([
      new Promise((resolve, reject)=>
        setTimeout(()=> reject(new Error("hoge")), 1000)
      ),
      new Promise((resolve, reject)=>
        setTimeout(()=> resolve(2), 2000)
      ),
      new Promise((resolve, reject)=>
        setTimeout(()=> reject(new SyntaxError("fuga")), 3000)
      ),
    ])
    .then(results=>{
      results.forEach((result, num)=>{
        if(result.status === "fulfilled"){
          console.log(
            `${result.status}:${result.value}`
          );
        }else{
          console.log(
            `${result.status}:${result.reason.name}`
          );
        }
      })
    })

    //rejected:Error
    //fulfilled:2
    //rejected:SyntaxError

    /*thenメソッドの前までのpromise*/
    //0: {status: 'rejected', reason: Error: hoge at <anonymous>:3:32}
    //1: {status: 'fulfilled', value: 2}
    //2: {status: 'rejected', reason: SyntaxError: fuga at <anonymous>:9:32}
    ```

    !!! warning
        - promise配列(返り値)のstateは`rejected`にならない
        - 全て`reject`されて返したとしても
    
    !!! warning
        - `iterable`であって、本物の配列ではないので、`map`等を使いたい場合は、`Array.from`等を利用する事

    ### 補足
    - `Promise.allSettled`は比較的新しい機能
    - 実装されていないときは、以下の様に`Polyfill`を行っていた

    ```js
    if(!Promise.allSettled){
      const rejectHandler = reason => 
        ({status : "rejected", reason});
      
      const resolveHandler = value =>
        ({status: "fulfilled", value});

      Promise.allSettled = (promises) =>{
        //Promiseでないことを鑑みて、Promise.resolve
        //thenでfulfillとrejectを処理してオブジェクトで返す
        const convertedPromises = promises.map(p=>
          Promise.resolve(p)
          .then(resolveHandler, rejectHandler));

        return Promise.all(convertedPromises);
        
      }
    }
    ```

## Q97 最初に返される結果のみが必要な場合、Promise.raceが使えることを知っていますか?

??? success
    ### 使用例
    - 2番目以降の結果(エラーも)は無視される

    ```js
    let promise = Promise.race([
      new Promise((resolve, reject)=>{
        setTimeout(()=> resolve(1), 1000)
      }),
      new Promise((resolve, reject)=>{
        setTimeout(()=> reject(new Error("a")), 2000)
      }),
      new Promise((resolve, reject)=>{
        setTimeout(()=> resolve(3), 3000)
      }),
    ])

    //Promise {<fulfilled>: 1}

    promise.then(alert); //1

    ```

## Q98 最初のfulfilledされたpromiseを取得したい場合、Promise.anyが使えることを知っていますか?

??? success
    ### Promise.raceとの違い
    - `reject`は無視される

    ### 全部`reject`だったら?
    - `AggregateError`で`reject`される
    - これは`errors`プロパティにすべての`promise`エラーを格納する特別なオブジェクトである

    ### 使用例(1つ以上fulfilled)

    ```js
    let promise = Promise.any([
      new Promise((resolve, reject)=>{
        //1番早いがrejectなので無視
        setTimeout(()=> reject(new Error("hoge")), 1000)
      }),
      new Promise((resolve, reject)=>{
        setTimeout(()=> resolve(1), 2000)
      }),
      new Promise((resolve, reject)=>{
        setTimeout(()=> resolve(2), 4000)
      }),
    ])

    //Promise {<fulfilled>: 1}

    promise.then(alert); //1
    ```

    ### 使用例(全部rejected)

    ```js
    let promise = Promise.any([
      new Promise((resolve, reject)=> {
        setTimeout(()=> reject(new Error("Hoge")), 1000)
      }),

      new Promise((resolve, reject)=>{
        setTimeout(()=> reject(new Error("fuga")), 2000)
      }),
    ])

    promise.catch(error=>{
      console.log(error.constructor.name);
      //AggregateError
      console.log(error.errors[0]);
      //Error: Hoge
      //  at <anonymous>:3:32
      console.log(error.errors[1]);
      //Error: fuga
      //  at <anonymous>:7:32
    })

    /*promise*/
    //Promise {<rejected>: AggregateError: All promises were rejected}
    //[[Prototype]]: Promise
    //[[PromiseState]]: "rejected"
    //[[PromiseResult]]: AggregateError: All promises were rejected
      //errors: Array(2)
      //0: Error: Hoge at <anonymous>:3:32
      //1: Error: fuga at <anonymous>:7:32
      //length: 2

    ```

## Q99. Promise.resolve/rejectを用いてPromiseを返すことができますか?

??? success
    ### Promise.resolve(value)
    - 以下と同じ意味を持つ

    ```js
    let promise = new Promise(resolve => resolve(value));
    ```

    ### 例文
    - `cache`がある場合、`cache`から即座にコンテンツを返す
    - ない場合は、`fetch`して`cache`にコンテンツを格納する

    ```js
    let cache = new Map();

    const loadCached = url =>{
      if(cache.has(url)){
        return Promise.resolve(cache.get(url));
      }

      return fetch(url)
        .then(response => response.text())
        .then(text =>{
          cache.set(url, text);
          return text;
        })
    }
    ```
    
    ### Promise.reject(error)
    - 以下と同じ

    ```js
    let promise = new Promise((resolve, reject)=> reject(error))
    ```

## Q100. コールバックを受け付ける関数を、Promiseを返す関数へ、変換することはできますか？

??? success
    ### Promisification
    - コールバックを受け付ける関数からPromiseを返す関数へ変換を行うこと

    ### 元の関数

    ```js
    const loadScript = (src, callback)=>{
      let script = document.createElement("script");
      script.src = src;

      script.onload = () => callback(null, script);
      script.onerror = () => callback(new Error(`Script load error for ${src}`));

      document.head.append(script);
    }

    loadScript("test.js", (err, script)=>{
      if(err){
        //errの処理
      }
      else{
        //正常処理
      }
    })
    ```

    ### 書き換えたversion
    - コールバックの代わりにエラーを返す
    - Promiseの中で元の関数を呼び出しており、その中でreject/resolveが呼び出されるので、関数が返すPromiseは正常に解決されている

    ```js
    const loadScript = (src, callback)=>{
      let script = document.createElement("script");
      script.src = src;

      script.onload = () => callback(null, script);
      script.onerror = () => callback(new Error(`Script load error for ${src}`));

      document.head.append(script);
    }

    //Promiseの中で元関数を呼び、resolve/rejectを返す
    //promiseはreturnで返る
    const loadScriptPromise = src =>{
      return new Promise((resolve, reject)=>{
        loadScript(src, (err, script)=>{
          if(err) reject(err);
          else resolve(script);
        })
      })
    }

    //test.jsにはsumという関数があるとする
    loadScriptPromise("test.js")
    .then(value => console.log(sum(7,8)), alert)
    ```

## Q101. 複数の関数をpromise化したい場合、ヘルパー関数を使ってそれを実行することができますか?

??? success
    ### 使用例
    - `f`の引数が`callback(err, res)`で固定されている場合

    ```js
    /*loadScript*/
    const loadScript = (src, callback)=>{
      let script = document.createElement("script");
      script.src = src;

      script.onload = () => callback(null, script);
      script.onerror = () => callback(new Error(`Script load error for ${src}`));

      document.head.append(script);
    }

    /*ラッパー関数*/
    function promisify(f){
      return function(...args){

        return new Promise((resolve, reject)=>{
          function callback(err, result){
            if(err) return reject(err);
            else resolve(result);
          }
          args.push(callback); //引数末尾にPromiseを返却する用のコールバックを追加
          //Promiseの中で元の関数を呼び出しており、その中でreject/resolveが呼び出されるので、関数が返すPromiseは正常に解決されている
          f.call(this, ...args); 
        })

      }
    }


    let loadScriptPromise = promisify(loadScript);
    loadScriptPromise("test.js")
    .then(value => console.log(sum(7,8)), alert)
    ```

    ### 使用例
    - `f`の引数が可変の場合
    - `res`が1つなら上と同じように動作
    - 2つ以上なら、コールバックの結果を配列で返し、`resolve`

    ```js
    //loadScriptは省略
    function promisify(f, manyArgs = false){
      return function(...args){
        return new Promise((resolve, reject)=>{
          function callback(err, ...results){
            if(err) return reject(err);
            else resolve(manyArgs ? results: results[0])
          }

          //引数の末尾にcallback
          args.push(callback);
          f.call(this, ...args);
        })
      }
    }

    //適当な関数を指定
    f = promisify(loadScript, true);
    f("test.js")
    .then(value => console.log(sum(3,5)), alert)
    ```

    ### コールバックの形式が珍しい場合
    - ヘルパーを使わず、手動で`Promise`化する

    ### 補足
    - `Node.js`なら、`util.promisify`などを使うと、コールバック関数を伴う非同期関数を`Promise`を返すように出来る

## Q102. Promiseがコールバックの完全な置き換えにはならないことを理解していますか?

??? success
    ### Promise
    - 1つの結果のみを持つ
    - 単純な非同期処理に対して有用

    ### コールバック
    - 何度も呼ぶことができる
    - コールバックに渡される関数が複数回呼び出されるような非同期処理に対して有用

    ### Promisificationによる書き換え
    - コールバックを1度だけ呼ぶ関数が対象
    - それ以降の呼び出しは無視される
    - 以降の見出しでは、コールバックが有用なケースについて触れる

    ### ストリーム処理
    - データが逐次的に到着する場合
    - データの断片が利用可能になるたびにコールバックを呼び出す

    ### イベント駆動の処理
    - 特定のイベントが発生した際に、コールバックを呼び出す

    ### 非同期処理の進捗
    - 進捗があるたびに、コールバックを呼び出す

    ### 上記の具体例について
    - 後記

## Q103. 非同期コードが、同期コードよりも常に後に実行される理由を理解していますか?

??? success
    ### 例

    ```js
    let promise = Promise.resolve();
    promise.then(()=> alert("promise done!"));
    alert("code finished"); //此方が先に表示される
    ```

    ### 簡単な理由
    - `.then`, `.catch`, `.finally`は常に非同期
    - 非同期処理はキューに入れられ、メインスレッドが空になったときにキューから出され、実行される

    ### Microtasksキューから見る理由
    - マイクロタスクとは、コールスタックが空になった後（グローバルスコープの関数が完全に実行された後）にのみ実行される短い関数を指す
    - `API`や`Promise`の`then()`等の引数に渡すコールバック関数がマイクロタスクとして扱われる
    - キューである以上、入れたものから処理されるので、順番を考えるなら先にキューに入れればいい
    - なお、`Promise`の場合、`fulfill/reject`されたタイミングで、キューに入れられる。

    ### つまり、promise doneを先に表示したいなら

    ```js
    Promise.resolve()
      .then(()=> alert("promise done!"))
      .then(()=> alert("code finished!"))
    ```


## Q104. 未処理のrejectがあった場合、JSはunhandledrejectionイベントを発火しますが、その検知の仕組みを知っていますか?

??? success
    ### 概要
    - `unhandledrejection`は、`microtask`キューの最後(空になるタイミング)で`reject`がハンドルされていない場合に発生する

    ### イベントが発火する原因が分かりますか?

    ```js
    let promise = Promise.reject(new Error("Promise fail"));

    setTimeout(()=> promise.catch(err => alert("caught")), 1000);

    window.addEventListener("unhandledrejection", event => alert(event.reason));
    //Error: Promise fail
    ```

    ### 答え
    - `setTimeout`で1秒後に`reject`を`catch`しているが、既にこの時には`microtask`キューは空になっている
    - そのため先に、`unhandledrejection`イベントが発火する


## Q105. 関数の前にasyncキーワードを付けると、関数がpromiseを返すようになることを知っていますか?

??? success
    ### 構文
    - コード内に`return <非promise>`がある場合、JSは自動的に`resolve`された`promise`にラップする

    ```js
    async function f(){
      return 1;
    }

    let promise = f();
    console.log(promise); //Promise {<fulfilled>: 1}
    promise.then(alert); //1
    ```

    ### つまり
    - `async`は関数が`promise`を返すことを保証する

## Q106 async関数の中でのみ動作するawaitキーワードの意味を知っていますか?

??? success
    ### await
    - `promise`が確定するまで待機する。 __`fulfilled`ならその`value`が返り値__ となる。`reject`なら拒否された値で例外を発生させる。
    - 待機している間、JSエンジンは他のスクリプト等を処理する
    - `promise.then`と同義だが、分かりやすい

    ```js
    async function f(){
      let promise = new Promise((resolve, reject)=>{
        setTimeout(()=> resolve("done!"), 1000);
      });

      let result = await promise; //1秒待機
      alert(result);
    }

    f();
    ```

## Q107 Promiseとthen等で書いたコードをasync/awaitを用いて書き直すことができますか?

??? success
    ### CORSリクエストについて

    - 以下のようなエラーが出ているかもしれない
    ```
    Access to fetch at 'file:///C:/file_test/test/test.json' from origin 'null' has been blocked by CORS policy: Cross origin requests are only supported for protocol schemes: http, data, isolated-app, chrome-extension, chrome, https, chrome-untrusted.
    ```

    - 原因
    ```
    URLスキームがHTTPSでないため
    fill:///から始まっているのでpolicyに引っかかっている
    ```

    - 対処法
    ```
    HTTPSのURLを使用する
    具体的にはlocalserverを立てる
    ```

    - Nodeをインストールしていると仮定
    ```
    $ npm install -g http-server
    $ http-server
    ・http://localhost:8080にアクセス
    ・フォルダ/ファイルが表示されるので、何かしらのファイル(html等)を選ぶ
    ・開発者ツールにいつものようにコードを打ち込む
    ```




    ### 例(方針)

    ### 例(Promise &' then)

    ```js
    fetch("test.json")
      .then(response => response.json())
      .then(user => fetch(`https://api.github.com/users/${user.name}`))
      .then(response => response.json())
      .then(githubUser => new Promise((resolve, reject)=>{
        let img = document.createElement("img");
        img.src = githubUser.avatar_url;
        img.className = "promise-avatar-example";
        document.body.append(img);

        setTimeout(()=>{
          img.remove();
          resolve(githubUser);
        }, 3000);
      }))
      .then(githubUser => alert(`Finished showing ${githubUser.name}`));
    ```
    
    ### 例(async/await)
    - 個々の非同期処理の結果を直接変数に割り当てることができ、その後の処理で利用できるため可読性が高まる
    - 実際には非同期処理だが、同期的の様に書けるのもメリット

    ```js
    async function showAvatar(){
      //JSONを読み込む
      let response = await fetch("test.json");
      let user = await response.json();

      //githubユーザを読み込む
      let githubResponse = await fetch(`https://api.github.com/users/${user.name}`);
      let githubUser = await githubResponse.json();

      //アバターの表示
      let img = document.createElement("img");
      img.src = githubUser.avatar_url;
      img.className = "promise-avatar-example";
      document.body.append(img);

      //3秒待機.引数が指定されていないので返るpromiseはvalueがundefined
      await new Promise((resolve, reject)=>{
        setTimeout(resolve, 3000)
      });

      img.remove();
      return githubUser; //promiseを返す
    }

    showAvatar();
    ```

## Q108 awaitもthenableを許容することを知っていますか?

??? success
    ### 概要
    - `await`キーワードがついた式の値は、通常`promise`の値になる
    - ただ、`promise`互換であってもよい

    ### 例

    ```js
    class Thenable{
      constructor(num){
        this.num = num;
      }
      then(resolve, reject){
        //alert(resolve);
        setTimeout(()=> resolve(this.num * 2), 1000);
      }
    }

    async function f(){
      let result = await new Thenable(1);
      console.log(result);
    }

    f();
    ```

## Q109 クラスメソッドもasyncになれることを理解していますか?

??? success
    ### 使用例

    ```js
    class User{
      constructor(name){
        this.name = name;
      }
      async print(){
        //awaitの戻り値であるvalueは非プロミス値になる
        //return <非プロミス値>なので、またresolveでラップされる
        return await Promise.resolve(this.name);
      }
    }

    new User("Taro")
      .print()
      .then(alert); //Taro
    ```

## Q110 async/awaitでエラー処理を行う場合、try...catchが使えることを理解していますか?

??? success
    ### awaitはpromiseの結果がrejectなら例外を投げる

    - つまり

    ```js
    async function f(){
      await Promise.reject(new Error("hoge"));
    }
    ```

    - は、以下と一緒

    ```js
    async function f(){
      throw new Error("hoge");
    }
    ```

    ### エラーは`try...catch`でキャッチできる 

    ```js
    async function f(){
      try{
        let response = await fetch("http://not-found.com");
        let user = await response.json();
      }
      catch(err){
        //fetchとresponse.json、両方のエラーをcatch
        alert(err);
      }
    }

    f() //TypeError: Failed to fetch
    ```

    ### GET net::ERR_NAME_NOT_RESOLVEDについて
    -　これはネットワークエラー
    - `try...catch`はJSの例外をキャッチするためのものなので、ネットワークエラーはキャッチしない

    ### catchを追加し忘れた場合
    - `unhandledrejection`イベントが発火する

    ### .thenや.catchを使う機会はあるのか
    - コードのトップレベルにいる場合は、`await`を使えないので(モジュール除く)、`.then`や`.catch`を使う
  
## Q111. async/awaitとPromise.allを併用して、複数のPromiseを待てることを知っていますか?

??? success
    ### 使用例

    ```js
    //valueとして返るので、resultsはpromiseではない
    let results = await Promise.all([
      fetch("https://api.github.com/users/Toraneko0101"),
      fetch("https://api.github.com/users/Nekoinu-Nezumiko"),
    ])

    results.forEach(result=>{
      console.log(result.url);
    })

    //https://api.github.com/users/Toraneko0101
    //https://api.github.com/users/Nekoinu-Nezumiko


    ```

## Q112. 非asyncの関数内からasync呼び出しを行うようなコードを記述できますか?

??? success
    ### async呼び出しをPromiseとして扱う

    ```js
    async function wait(){
      //1秒待機
      await new Promise(resolve => setTimeout(resolve, 1000));

      return 10;
    }

    function f(){
      //awaitは使えないので、thenを使う
      //10がPromise.resolveでラップされて帰ってくるので
      wait().then(value => value**2).then(alert); //100
    }

    f();
    ```