# クイズ(発展)

## Q1. Promiseについて理解していますか?

??? success

    - `Promise`は、非同期処理を提供します
    - `then()`や`catch()`のようなメソッドを提供します
    - `Promise`の状態は`pending`(待機), `fulfilled`(履行), `rejected`(拒否)のいずれかになります
    - 最終的に`fulfilled` or `rejected`のコールバックを返します

## Q2. Promiseの基本構文を記述できますか?

??? success
    - `Promise`は`resolve`と`reject`を引数に持ちます

    ```js
    const promise = new Promise((resolve, reject) =>{
      reject(new Error("Something went Wrong"));
    })

    //const promise2 = Promise.resolve("Promise 2 fulfilled"); 
    //const promise3 = Promise.reject("Promise 3 rejected"); //(4)

    promise
      .then((result) =>{
        console.log(result); //(1)
      })
      .catch((error)=>{
        console.log(error.message); //(2)
      })
      .finally(()=>{
        console.log("Finally Promise has settled"); //(3)
      })

    //output
    //Something went Wrong
    //Finally Promise has settled
    ```

    1. fulfilledの場合、呼ばれる
    2. rejectedの場合、呼ばれる
    3. いずれの場合でも、呼ばれる
    4. このように、書くこともできる

## Q3. Promise内のエラーを扱う方法について知っていますか?

??? success
    - `catch`メソッド、あるいは`then`メソッドの第二引数を使う


    ### catchを使った場合

    ```js
    const promise = new Promise((resolve, reject) =>{
      reject(new Error("Sometion went wrong"));
    })

    promise
      .then((result) =>{

      })
      .catch((error) =>{
        console.log(error.message);
      })
    ```
    ### thenの第二引数を使った場合

    ```js
    const promise = new Promise((resolve, reject) =>{
      reject(new Error("Sometion went wrong"));
    })

    //第1引数はpromiseがresolvedの時に呼び出され
    //第二引数はrejectedの時に呼び出される。
    promise.then(
      (result) =>{
        console.log(result);
      },
      (error) =>{
        console.log(error.message);
      }
    )
    ```

## Q4. async/awaitの目的について知っていますか？

??? success
    - 主に非同期処理に使われます
    - `async`関数は常に`promise`を返します
    - `async`関数の中で`await`を使うことで、`promise`の処理が終了するまで、実行を待つことができます。
    - 下記のコードにおいて、fetchは常にPromiseを返し、エラーがないなら`resolve`に、あるなら`reject`になります

    ```js
    const initialURL = "https://www.example.com";
    const fetchData = async() =>{
      let res = await getData(initialURL); //Promiseが返されたらその後のコードを実行する

      return res;
    }

    const getData = (url) =>{
      return fetch(url) //fetchは常にPromiseを返す
        .then((res) => res.json);
    };
    ```

## Q5. async/awaitでエラーを処理する方法について知っていますか?
??? success

    ### promiseで明示的にrejectした例

    - `catch`で`Promise`が`rejected`された時、またはブロック内でエラーがスローされた時のエラーハンドリングを実施します

    ```js
    const promise = new Promise((resolve, reject) =>{
      reject(new Error("Something went Wrong"));
    })

    async function main() {
      try {
        const result = await promise;
        console.log(result);
      } catch (error) {
        console.log(error.message); //promiseの結果がrejectだったらこちらを実行
      }
    }
    ```

    ### 実用例

    ```js
    const initialURL = "https://www.example.com";
    const fetchData = async() =>{
      try{
        let res = await getData(initialURL); //処理が終わるまで待機。rejectが返されたらcatch節に移行
        return res;
      }catch(error){
        console.error("Error fetching data:", error);
      }
    }

    const getData = (url) =>{
      return fetch(url) //(1)
        .then((res) =>{
          if(!res.ok){ //(2)
            throw new Error(`HTTP error! Status: ${res.status}`);
          }
          return res.json();
        })
    };
    ```

    1. fetchは常にPromiseを返す。例外が投げられればrejectが、成功時にはresolveが返る
    2. statusが正常でなければ、例外を返す。例外が発生した場合、getData()はエラーの発生したPromiseをrejectとして受け取る。


## Q6. callback地獄という言葉に聞き覚えはありますか？
??? success

    ### 概要
    - 入れ子になったコールバックが複雑になり、保守が難しくなること。
    - 前操作の完了に依存した、複数の非同期操作を実装したいときに良く発生する
    - コードがピラミッド型の形状になるので、別名Pyramid of Doom

    ### callback地獄の例

    ```js
    callAsync1()
      .then(() => {
        return callAsync2()
          .then(() => {
            return callAsync3()
              .then(() => {
                return callAsync4()
                  .then(() => {
                    return callAsync5()
                      .catch((err) => console.error(err));
                  })
                  .catch((err) => console.error(err));
              })
              .catch((err) => console.error(err));
          })
          .catch((err) => console.error(err));
      })
      .catch((err) => console.error(err));
    ```

    ### プロミスチェーンで回避する

    ```js
    callAsync1()
      .then(() => callAsync2()) //(1)
      .then(() => callAsync3())
      .then(() => callAsync4())
      .then(() => callAsync5())
      .catch((err) => console.error(err)); //(2)
    ```

    1. `callAsync1`の処理が完了するまで待機し、`callAsync1`が成功したら、次に`callAsync2`を呼び出す。また完了するまで待機する
    2. 処理が失敗したらこちらに進む
   
    ### async/awaitで回避する

    ```js
    //async function asyncCall(){
    const asyncCall = async() =>{
      try{
        await callAsync1();
        await callAsync2();
        await callAsync3();
        await callAsync4();
        await callAsync5();
      }catch (err){
        console.error(err);
      }
    }
    ```

## Q7. JSのガベージコレクションについて知っていますか?
??? success
    - JSエンジンはガベージコレクションを使用します
    - これは、不要になったオブジェクトを解放して、メモリを自動的に管理するためのものです
    - ガベージコレクションは、mark-and-sweepというアルゴリズムによって実装されています
    - これは、あるオブジェクト（ルート）から参照を行い、辿り着いた場合はmarkをつけ、最終的にmarkがついていないオブジェクトをsweep(破棄)するというものです

## Q8.MapとWeakMapの違いが分かりますか？

??? success
    | -                        | Map          | WeakMap                    |
    | ------------------------ | ------------ | -------------------------- |
    | キーの挿入順序           | 記憶される   | 記憶されない               |
    | キーとして使用できるもの | 任意の値     | オブジェクトのみ           |
    | ガベージコレクション     | ない         | 他の参照を持たない場合削除 |
    | 反復                     | できる       | できない                   |
    | sizeプロパティ           | 持っていない | 持っていない               |

    ### Map

    ```js
    //要素の追加
    const myMap = new Map();
    const keyObj = {};
    const keyFunc = function(){};
    const keyString = "keyString";
    const keyNum = 10;

    myMap.set(keyObj, "apple");
    myMap.set(keyFunc, "banana");
    myMap.set(keyString, "peach");
    myMap.set(keyNum, "grape");

    //key(挿入した順番に表示される)
    for(const key of myMap.keys()){
      console.log(key);
    }
    //値
    for(const value of myMap.values()){
      console.log(value);
    }
    //key + 値
    for(const [key, value] of map){
      console.log(key, value);
    }
    //size
    console.log(myMap.size); //4

    //get
    myMap.get("keyString"); //peach
    //delete
    myMap.delete("keyString"); //true

    //has
    myMap.has("keyString"); //false

    ```

    ### WeakMap

    ```js

    //map
    const myMap = new Map();
    const keyFunc = functon(){};
    myMap.set(keyFunc, "hoge");
    //keyが削除されても削除されていない
    keyFunc = null;
    console.log(myMap); //{f(){}, "hoge"}

    
    //weakmap
    const myWeakMap = new WeakMap();
    const keyFunc = function(){};
    const keyString = "keyString";
    //myWeakMap.set(keyString, "peach"); //error

    myWeakMap.set(keyFunc, "hoge");
    //keyが削除されるとvalueも削除されている
    keyFunc = null;
    console.log(myWeakMap) // {} //(1)
    ```

    1. ガベージコレクションのタイミングはJS依存なので、即座に削除されるとは限らないことに注意

## Q9. closure(クロージャ)について理解していますか?

??? success

    - クロージャは、外側の関数の変数と引数を記憶し、アクセスを可能にします。
    - 内側の関数が外側の関数の変数にアクセスできることを利用しています

    ```js
    const outer = () =>{
      let count = 0;

      const inner = () =>{
        count++;
        console.log(count);
      }

      return inner;
    }

    const myClosure = outer(); //(1)
    myClosure(); //1
    myClosure(); //2
    debugger;
    myClosure(); //3 //(2)
    ```

    1. `myClosure`は、外側の関数の変数である`count`の情報を保持しています。
    2. debuggerで見ると、この行が実行される前の段階で`Closure (outer) {count: 2}`であることが確認できる。[[Scopes]]の中に情報があった


## Q10. IIFEについて知っていますか?

??? success

    - IIFE(Immediately Invoked Function Expression)は、定義されるとすぐに実行されるJSの関数です
    - IIFEは通常、関数が他のスコープと分離されていることを保証するために使用されます

    ### プライベートスコープとして使用する

    ```js
    (function(){
      var privateVariable = "IIFE Example";
      console.log(privateVariable);
    })();

    //(1)
    //console.log(privateVariable) //error

    ```

    1. 外部からはprivateVariableにアクセスできません。


    ### アロー関数で書いた場合

    ```js
    (() =>{
      var privateVariable = "IIFE Example";
      console.log(privateVariable); 
    })();
    ```

## Q11. オブジェクトを不変にする方法を知っていますか?

??? success

    - 単に`const`とした場合、プロパティは変更できてしまいます

    ```js
    const obj = {
      name: "javascript",
    };

    obj.name = "typescript";
    console.log(obj) //{name: 'typescript'}
    ```

    - `Object.freeze()`を用いることで、プロパティ値の変更や、新たなプロパティの追加が防止できます

    ```js
    const obj = {
      name: "javascript",
    };

    Object.freeze(obj);
    obj.name = "typescript"; //(1)
    obj.address = "100-0000";
    console.log(obj) //{name: 'javascript'} //(2)
    ```

    1. strict-modeの場合、エラーを吐く
    2. 変更されていないことが確認できる。

## Q12. Promise.all()とPromise.allSettled()の違いについて説明できますか?

??? success

    - `Promise.all()`は、Promiseのいずれかが拒否(reject)された場合は、すぐにreject扱いされます
    - `Promise.allSettled()`は、全てのPromiseが(resolve or reject)されるまで待ってから結果を返します。

    ### 初期化

    ```js
    //resolveされたpromise
    const promise1 = Promise.resolve("Promise 1 resolved");
    console.log(promise1) // Promise {<fulfilled>: 'Promise 1 resolved'}
    //rejectされたpromise
    const promise2 = Promise.reject("Promise 2 rejected");
    console.log(promise2) //Promise {<rejected>: 'Promise 2 rejected'}
    ```

    ### Promise.all()の使用例
    - `Promise.all()`は入力としてPromiseの集合の反復可能オブジェクトを取り、単一の`Promise`を返す
    - 入力された`Promise`のいずれかが拒否されると、最初の拒否理由とともに、拒否が返リ、処理は終了する

    ```js
    Promise.all([promise1, promise2])
      .then((values) =>{
        console.log(values);
      })
      .catch((error) =>{
        console.log("An error occurred in Promise.all():", error)
      })

      //output
      //An error occurred in Promise.all(): Promise 2 rejected
      //(1)
    ```

    1. Promise2の結果が分かった途端、すぐにrejectを返しているので、`console.log()`が実行されることはない

    ### Promise.allSettled()の使用例

    - `Promise.allSettled()`は、入力された`Promise`のいずれかが拒否されたかにかかわらず、すべての入力された`Promise`が完了するのを待つ。
    - プロパティ`status`は、`fulfilled`(履行)、`rejected`(拒否)のいずれかを取り、そのPromiseの最終的な状態を示す
    - プロパティ`value`は、statusが`fulfilled`の場合のみ存在し、Promiseが履行された値を示す
    - プロパティ`reason`はstatusが`rejected`の場合のみ存在し、Promiseが拒否された理由を明示する

    ```js
    Promise.allSettled([promise1, promise2]).then((results) =>{
      results.forEach((result, index) =>{
        if(result.status === "fulfilled"){
          console.log(
            `Promise ${index + 1} was fulfilled with value:`, result.value
          );
        }
        else{
          console.log(
            `Promise ${index + 1} was rejected with reason: `, result.reason
          );
        }
      });
    });

    //output
    //Promise 1 was fulfilled with value: Promise 1 resolved
    //Promise 2 was rejected with reason:  Promise 2 rejected
    //(1)
    ```
    
    1. 全てのPromiseが解決してから値を返しているので、どちらのPromiseも処理されている


## Q13. reduceを用いた下記の例が、非効率である理由が分かりますか?
??? tips
    ### 補足
    - `Object.hasOwn(obj, property)`: 指定されたobjectが、指定されたpropertyを持っている場合にtrueを返す。(継承時の挙動については省略)

    ```js
    const names = ["Alice", "Bob", "Charry", "Bruce", "Alice"];

    const countedNames = names.reduce((allNames, name) =>{
      const currCount = 
        //プロパティを持っているか確認し、持っている場合は現在の値を、持っていない場合は0を返している
        Object.hasOwn(allNames, name) ? allNames[name] : 0;
        
        return{
          ...allNames,
          [name] : currCount + 1, //該当の名前におけるcountを1だけ加算している。結果的に、プロパティがなかった場合は要素が加わる。
        };
    }, {});
    ```

??? success

    ### 問題点
    - 配列内の要素を探索するたびに、`allNames`をコピーしているので、`names`の要素が多くなった場合、計算量が莫大になる

    ### 解決案
    - 普通に`for ... of`を使う

    ```js
    const names = ["Alice", "Bob", "Charry", "Bruce", "Alice"];
    const countedNames = Object.create(null); //nullオブジェクトを生成 (1)
    for(const name of names){
      const currCount = countedNames[name] ?? 0; //存在しなければ0
      countedNames[name] = currCount + 1; //要素を加える
    }
    ```

    1. `Object.create(null)`は, `{}`と異なる。前者はpropertyを一切持たないので、`toString()`等も利用できない

## Q14. イベントループについて知っていますか?

??? success
    - イベントループは、JSで非同期コードを使用できる理由であり、効率よくノンブロッキングを実行できる理由でもある
    - イベントループはメインスレッドで実行される
    - これは、実行する必要があるタスクがあるかどうかを（コールバックキューとコールスタックを巡回することで）常に確認している
    - タスクがあればそれを実行し、ない場合はタスクが到着するまで待機する
## Q15.setTimeoutを用いた下記の例で、二行目が先にログに記録される理由を説明できますか？

??? tips

    ```js
    setTimeout(()=> console.log("Hello from the timer"), 0);
    console.log("Hello from the main code");
    ```

??? success
    ### 概要
    - イベントループには、コールスタックとコールバックキューという2つの主要な構成要素がある

    ### コールスタック
    - 実行する必要のあるタスクを格納する
    - LIFOなので、最後に追加されたタスクが最初に実行される
    
    ### コールバックキュー
    - 完了して実行の準備が済んだタスクを格納する
    - FIFOなので、準備できたものから実行される

    ### イベントループの実行順序
    1. コールスタックからタスクを実行する
    2. 非同期タスクの場合、バックグラウンドで実行される。その場合、JSは次のタスクに進む
    3. 非同期タスクが完了すると、そのコールバック関数が、コールバックキューに追加される
    4. コールスタックが空になり、コールバックキューにタスクがある場合、イベントループは最初のタスクをキューからスタックに送って実行する。
   
    ### 補足
    - JSは基本的にシングルスレッドで、メインのスレッドのみで処理が為される。
    - 非同期の場合、その処理を一度メインスレッドから切り離す(キューに移す)
    - メインスレッドはコールスタックによって管理されている
    - これは先述したとおり後入れ先出しなので、コールスタックが積まれていると、割り込むことになる。非同期処理はそれは行わないので待機する
    - イベントループは、コールスタックとキューを監視して、空であればそのことをキューに伝えて、割り込みOKだという
    - 割り込みOK(コールスタックが空)なら、非同期処理の結果がキューからスタックに返される
    - 実行される

    ### 具体例(再掲)

    ```js
    setTimeout(()=> console.log("Hello from the timer"), 0);
    console.log("Hello from the main code");
    ```

    ### の実行順序
    1. コールスタックには、`setTimeout`と2行目のコンソール文の順で存在している
    2. 先に`setTimeout`が実行され、タイマーの継続時間は0ミリ秒なのですぐにタスクは完了し、結果そのコールバックはコールバックキューに配置される。
    3. しかし、`setTimeout`のコールバックはコールスタックが空でないため、キューから送られない
    4. 次の行である`console.log("Hello from the main code");`がログに記録される
    5.  2行目のコンソール文がコールスタックから削除される
    6.  コールスタックが空になったので、`setTimeout`のコールバックはキューからコールスタックに移され実行される
    7.  結果として、2行目の`console.log()`が先にログに記録されることになる。

## Q16. 非同期処理の実行順序に優先順位があることを知っていますか?

??? success

    ### 注意書き
    - この項で触れる`setTimeout`はNode.jsのそれであり、ブラウザの`setTimeout`とは異なる
    - その他についても、バックエンドについて書かれている

    ### 復習
    - 同期タスクは常に非同期タスクよりも早く実行される。これは、非同期タスクが一度メインスレッドからキューに移され、メインスレッドが空くまで返ってこれないから

    ### libuv
    - Node.jsにイベントループ機能を提供している

    ### イベントループの仕組み
    - Node.jsが起動するとイベントループが初期化される
    - ただし開始前に次の処理が行われる(この順序について後述)
    - Timerのスケジュール設定
    - 同期タスクの実行
    - process.nextTick等の実行
    - 非同期APIの呼び出し

    ### イベントループのフェーズ
    以下の6つのフェーズが存在する
    - timers
    - pending callbacks
    - idel, prepare
    - poll
    - check
    - close callbacks

    ###　上のフェーズの意味
    - JSの実行はidle, prepareを除く、どこかのフェーズで実行される。
    - それぞれのフェーズは、実行するコールバックのFIFOジョブキューを持っている
    - 各々のフェーズに入ると、そのフェーズの処理が実行され、キューが処理される
    - キューが空になるか、コールバックの上限に達した場合、イベントループは次のフェーズに移行する

    ### libuvとフェーズ
    - libuvは、各フェーズ毎にフェーズの結果をNodeに伝達する
    - この時、`nextTickQueue`と`microTaskQueue`に入れられたイベントのキュー（これはNodeが提供しているキュー）をチェックする
    - もし上記のキューが空でない場合は、メインのイベントループのフェーズに移行する前に、そちらのキューを先に空にする
    - つまり、`nextTickQueue`と`microTaskQueue`は、フェーズが移行する毎に、存在すれば先に実行される

    ```mermaid
    graph LR;
        classDef accent fill:orange,fill-opacity:0.3

    A[Tick処理]:::accent --> B[Timer]
    B --> C[Tick処理]:::accent
    C --> D[pending callbacks]
    D --> E[Tick処理]:::accent
    ```
    
    ### nextTickQueue
    - process.nextTickを使用して、登録されたコールバックを保持する
    - 全ての非同期タスクの中で最速
    - 再帰的に呼び出すとNodeをブロックする危険性があるので注意

    ### microTaskQueue
    - Promiseオブジェクトのコールバックがここに所属する
    - nextTickQueueが空になり次第実行される

    ### 問題
    - 以下のコードの実行順序は?
    - ※processはNodeなので、jsファイルを作成し、`node hoge.js`等として試す事
    ```js
    process.nextTick(() => console.log(1));
    Promise.resolve().then(() => console.log(2));
    process.nextTick(() => console.log(3));
    Promise.resolve().then(() => console.log(4));
    process.nextTick(() => console.log(5));
    ```
    ```
    //それぞれのtickはqueueなのでFIFO(先入先出)
    1
    3
    5
    2
    4
    ```

    ### Timer Phase
    - イベントループの開始フェース
    - `setTimeout`や`setInterval`のタイマーのコールバックを実行する
    - Nodeは有効期限が切れたタイマーを確認し、コールバックを呼ぶ
    - 期限が切れたタイマーが存在する場合、queueに登録した順番に実行される(FIFO)
    - このようにほかのコールバック（またはOSのスケジューリング）によって遅延が発生する可能性があるので、`setTimeout`や`setInterval`は指定された時間を超えたできるだけ近い時間で呼びだされる。

    ```js
    const start = process.hrtime(); //[second, nanoseconds]

    setTimeout(() => {
      const end = process.hrtime(start);
      //本来[1,0]となってほしいが......
      console.log(
        `timeout callback executed after ${end[0]}s and ${
          end[1] / Math.pow(10, 9)
        }ms`,
      );
    }, 1000);
    ```
    - 毎回異なる結果となる
    ```
    timeout callback executed after 1s and 0.0159282ms
    timeout callback executed after 1s and 0.0040151ms
    timeout callback executed after 1s and 0.0019831ms
    ```

    ### Pending callbacks Phase
    - イベントループのpending_queueに存在するコールバックを実行する。
    - 完了、エラー、I/O操作のコールバックが実行される

    ### idle, Prepare Phase
    - libuvによって内部的に呼び出されるフェーズ
    - 次のフェーズであるPoll Phaseの準備をする

    ### Poll Phase
    - サーバの応答、まだ返されていないI/Oイベントの待機のために使用される問い合わせ時間
    - 新しいソケットコネクトやファイルの読み込みなどの新しいI/Oイベントを取得し、実行する

    ### Check Phase
    - `setImmediate`のコールバック専用フェーズ
    - `setImmediate`で登録されたすべてのコールバックを実行
    - pollフェーズで実行されていたコールバック内に`setImmediate`と`setTimeout`が存在すれば、次の`timer`フェーズで実行される予定の`setTimeout`より先に呼ばれる

    ### Close Callbacks Phase
    - すべての`close`イベントのコールバックが処理される
    - キューに処理するものがなければループは終了する
    - 存在すれば、また`timer`フェーズに移行

    ### 実際の例で考える

    ```js
    const { readFile } = require("fs"); //(1)

    const timeoutScheduled = Date.now(); //(2)

    setTimeout(() => {
      console.log(`delay: ${Date.now() - timeoutScheduled}ms`);
    }, 100);

    readFile(__filename, () => { //(3)
      const startCallback = Date.now();

      while (Date.now() - startCallback < 500) {}
    });
    ```

    1. ファイルを扱うためのモジュール。ファイルに書き込んだり、ファイルから書き出すときに用いる
    2. UNIX時間
    3. 現在のモジュールの絶対パスを取得する(I/O操作)

    ```
    # output
    delay: 502ms
    ```

    ### 解説(100msではない理由)
    4. スクリプトが最初のイベントループに入る
    5. `setTimeout`は100なので、まだ有効期限は切れておらず、実行可能なI/Oコールバックも存在しない
    6. そのため`poll`フェーズに移行。ファイルの読み込み結果を待つ
    7. ファイルの読み込みは軽量なので、Timerよりも早く結果を取得する
    8. スクリプトが2回目のイベントループに入る
    9.  既にファイルは取得できているので、pending callbacksフェーズに入る
    10. pending callbacksフェーズでは完了、エラー、I/O操作のコールバックが実行される
    11. 上の例を見ると、コールバック内では500msの同期処理が実行されている( __本来は、I/O操作のコールバック内でCPUを占有すると、I/O非同期の利点が帳消しになるのでブロックしてはいけない__ )
    12. 次のフェーズへ移行するためにはキューを空にする必要があるので、ここで500msの遅延が発生する
    13. 500msが経った場合、キューが空になるので次のイベントループ平行
    14. 3回目のイベントループ
    15. 既にタイマーの有効期限は切れているので、delayが出力され、イベントループは終了する

    ### ~~矛盾?~~
    ```js
    setTimeout(() => console.log("setTimeout"));
    setImmediate(() => console.log("setImmediate"));
    ```
    この出力は以下の様になる
    ```
    setImmediate
    setTimeout
    ```
    ```js
    setTimeout(() => console.log("setTimeout"));
    setImmediate(() => console.log("setImmediate"));
    process.nextTick(() => console.log("nextTick"));
    ```
    この出力は以下の様になる
    ```
    nextTick
    setTimeout
    setImmediate
    ```
    - 例を見ると、`process.nextTick(() => console.log("nextTick"));`を記載したことで、`setTimeout`と`setImmediate`の順番が逆転しているようだ。これはどういうことだろうか？

    ### 解説
    - `setTimeout`の第2引数に何も指定しなかった場合、値0を使用する。しかし実際にはdelayは1~2147483647の間をとり、設定値が1以下の場合、delayは1に設定される。つまり、0を指定しても実際には、1ms待機する。(ブラウザの場合、遅延時間は4ms~で、ブラウザ依存)
    - そのため`process.nextTick()`が書かれていない場合、すぐにTimerフェーズに突入するが、1ms経っておらず、先に一回目のループのcheckフェーズで`setImmediate`が実行される
    - 上で説明したように、`process.nextTick()`の処理は残り2つの処理よりも優先される。
    - つまり、`process.nextTick()`の処理に1ms以上かかり、結果、1回目のイベントループに入ったときに既に1m以上経過しているため`Timer`の有効期限が切れていたと考えられる(ということは`process.nextTick()`の処理に1ms以上かからなかった場合、出力結果は異なるものになる、ということでもある)

## Q17. JSエンジンのヒープとスタックについて理解していますか?

??? success

    ### JSエンジンのスタック
    - 小さく整理されたメモリ領域
    - プリミティブ値、関数呼び出し、ローカル変数が保存される
    - 関数の呼び出しごとに、関数のローカル変数、戻りアドレス、およびその他のコンテキストデータを含む、新しいスタックフレームが作られる
    - スタックに最後に追加された項目が最初に削除される(関数ならば最後に呼び出されたもの)

    ### JSエンジンのヒープ
    - 大部分が構造化されていない
    - `objects`や`array`,`function`が格納される
    - 数値やブール値等のプリミティブ型を宣言すると、通常はスタックで管理される
    - オブジェクトや配列、関数内のプリミティブ型の場合、実際のデータはヒープ内に存在し、そのデータへの参照がスタックに保持される(参照のサイズは一定なので、各々のオブジェクトのメモリサイズを考える必要がなく、スタックのメモリ管理が容易になる)

    ### 例

    ```js
    const name = "Javascript"; //スタックに直接配置
    const hoge  {name: "JS"} //(1)
    ```

    1. 実際の{name : "JS"}はヒープに存在。hogeはスタックに配置され、ヒープ内のオブジェクトを参照する

## Q18. プロトタイプチェーンを知っていますか？

??? success

    ### プロトタイプ
    - JSにおいて、すべてのオブジェクトは、プロトタイプと呼ばれる組み込みのプロパティを持っている
    - プロトタイプはそれ自体がオブジェクトでもあるので、自分自身でもプロトタイプを持つ
    - そしてそのプロトタイプも、またプロトタイプを持っていて......、と、このように連鎖するプロトタイプを、プロトタイプチェーンと呼ぶ
    - プロトタイプにnullを持つ、プロトタイプに到達すると、その連鎖は終了する。

    ### プロパティ・メソッドの検索
    - オブジェクトのプロパティまたはメソッドにアクセスすると、JSはまずオブジェクト自体をチェックする
    - そこにない場合、オブジェクトのプロトタイプ内のプロパティやメソッドを検索する
    - 見つかればそこで終了し、見つからなければチェーンの終わり(null)に到達するまで検索は続く
    - プロトタイプチェーンはJSのプロトタイプ検証モデルの基礎であり、オブジェクトが他のオブジェクトからプロパティやメソッドを継承できる所以である

    ### 継承の例
    ??? warning
        - `Object.setPrototypeOf(obj, prototype)`を用いると、プロトタイプチェーンが動的に変更されるので、パフォーマンスの低下が生じる可能性があります。
        - プロトタイプチェーンのコストは高コストなので、より効率的な例を求める場合は、Q15の「プロトタイプベースで書き直した例」を合わせて参照してください
    ```js
    const roadmap = {
      getUrl(){
        console.log(`https://www.example.com/${this.slug}`);
      }
    }

    const javascript = {
      name: "JS Roadmap",
      description: "Learn JS",
      slug: "JS",
      greet(){
        console.log(`${this.name} - ${this.description}`);
      },
    };

    Object.setPrototypeOf(javascript, roadmap); //(1)

    javascript.getUrl(); //(2)
    javascript.greet();
    ```

    1. 第一引数はプロトタイプを設定するObject。第二引数はオブジェクトの新しいプロトタイプ
    2. javascriptオブジェクトには、getUrlメソッドが見つからなかったため、Prototypeを検索。
  

## Q19. classの継承を知っていますか?

??? warning
    - JSのクラスは、実際にはプロトタイプを利用して継承やプロパティの共有を行っている
    - これはJSがプロトタイプベースの言語であるため
    - クラスはプロトタイプの糖衣構文にすぎない

??? success
    - 継承とは既存のクラスから新しいクラスを作成する方法
    - 新しいクラスは既存のクラスからすべてのプロパティとメソッドを継承する
    - 新しいクラスは子(クラス)、既存のクラスは親(クラス)と呼ばれる

    ### 例

    ```js
    class Roadmap{
      constructor(name, description, slug){ //(1)
        this.name = name;
        this.description = description;
        this.slug = slug;
      }

      getUrl(){
        console.log(`https://www.example.com/${this.slug}`);
      }
    }

    class Javascript extends Roadmap{ //(2)
      constructor(name, description, slug){
        super(name, description, slug);
      }

      greet(){
        console.log(`${this.name} - ${this.description}`);
      }
    }

    const js = new Javascript(
      "Javascript Roadmap",
      "Learn Javascript",
      "javascript"
    );

    js.getUrl(); //https://www.example.com/javascript
    js.greet(); //Javascript Roadmap - Learn Javascript
    ```

    1. コンストラクタ
    2. 継承

    ### プロトタイプベースで書き直した例(Q14より効率的)

    ```js
    const createRoadmap = (name, description, slug) =>{
      return { //keyと変数名が同じ場合、省略可能なので、省略している
        name,
        description,
        slug,
        getUrl(){
          console.log(`https://www.example.com/${this.slug}`);
        },
      };
    }

    const createJavascriptRoadmap = (name,description,slug) =>{
      const roadmap = createRoadmap(name, description, slug);

      return {
        ...roadmap, //既存に加えて、メソッドを追加
        greet(){
          console.log(`${roadmap.name} - ${roadmap.description}`);
        },
      };
    }

    const js = createJavascriptRoadmap(
      "Javascript Roadmap",
      "Learn Javascript",
      "javascript"
    );

    js.getUrl();
    js.greet();
    ```

## Q20.明示的なバインドについて理解していますか?

??? success
    - 明示的なバインドは、`call()`, `apply()`, `bind()`を使用して、`this`キーワードが何にバインドされるかを明示的に示す方法
    ??? warning
        - アロー関数は、アロー関数が定義されているスコープに基づいて、thisの値を決定しているため、上記のメソッドを使っても有益ではない
        - 関数の呼び出し方によって、thisが変わる場合にのみ、上記のメソッドは効力を発揮する

    ### call()
    - callは関数を呼び出す際に、その関数内で使われる`this`の値を指定する
    - そのあとで、引数を順番に指定する

    ```js
    function hello(address, pet){
      console.log(`こんにちは ${this.name} 
                    住所:${address} 
                    ペット:${pet}`);
    }

    const yamada = {name : "yamada"};

    hello.call(yamada, "100-0000", "cat"); 
    ```

    ### apply()
    - applyは関数を呼び出す際に、その関数内で使われる`this`の値を使用する
    - そのあとで、引数を指定するが、引数は配列として渡される

    ```js
    function hello(address, pet){
      console.log(`こんにちは ${this.name} 
                    住所:${address} 
                    ペット:${pet}`);
    }

    const yamada = {name : "yamada"};

    hello.apply(yamada, ["100-0000", "cat"]); 
    ```

    ### bind()
    - thisや引数を固定した新しい関数を作成する
    - 即座に呼ばれないことが特徴
    - 前2つと比べて比較的使い道がある

    ```js
    function hello(){
      console.log("こんにちは、" + this.name);
    }

    const yamada = hello.bind({name: "yamada"});

    yamada()
    ```

    ### bindで引数を固定した場合
    - 第二引数に引数がある場合、引数は固定される
    - 固定した引数が、呼び出し側の引数より少なかった場合、呼び出し側の引数が順次あてはめられる。

    ```js
    function hello(name, age){
      console.log(`こんにちは、${name}:${age}歳`);
    }

    const yamada = hello.bind(null, "yamada");
    yamada("sasaki", 12); //こんにちは、yamada:sasaki歳
    ```

## Q21. 独自のカスタムイベントをJSで実装する方法を知っていますか?

??? success
    - `CustomEvent`コンストラクタを用いると、カスタムイベントが作成可能
    - `CustomEvent`コンストラクタはイベント名と、イベントデータを含むオブジェクト（通常はdetailプロパティを含む）を引数にとる。
    - `dispatchEvent`を用いることで、ターゲット要素/ドキュメント上でカスタムイベントを配置可能

    ### カスタムイベントの作成/配置

    ```js
    const event = new CustomEvent("hogeUpdated", {
      detail : {name : "JS"},
    });

    element.dispatchEvent(event);　//(1)
    ```

    1. dispatchEventはイベントリスナーを起動する

    ### カスタムイベントのリスニング

    ```js
    element.addEventListener("hogeUpdated", (event)=>{
      console.log(event.detail);
    })
    ```

    ### イベントリスナーの削除

    ```js
    function handleEvent(event) {
      console.log(event.detail); // { name: 'JavaScript' }
    }

    element.removeEventListener("hogeUpdated", handleEvent);
    ```

    ### 実用例
    - 🚧🚧工事中🚧🚧