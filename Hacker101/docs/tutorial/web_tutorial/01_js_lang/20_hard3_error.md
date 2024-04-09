# クイズ(上級3:Error)

## Q56 try...catchでエラーをキャッチして、その情報を出力できることを知っていますか?

??? success
    ### 構文

    ```js
    try{
      //...some code
    }catch (err){
      //error handling
    }
    ```

    ### 仕様
    1. `try`節のコードが実行
    2. エラーがなければ(実行が`try`の最後に達すれば)`catch(err)`は無視される
    3. エラーが発生した場合、`try`の実行は停止し、`catch(err)`の先頭に移動する
    4. `err`は発生した事象に関する詳細を持つ、エラーオブジェクトを意味する

    ### 例

    ```js
    "use strict";
    try{
      alert("neko!");
      hogehoge; //strict-modeで未定義変数なのでエラーが出る
      alert("実行されないはず");
    }catch(err){
      alert("Error has occured!");
    }
    
    //neko!
    //Error has occured!
    ```

## Q57. try...catchが実行時エラー以外をキャッチできないことを知っていますか?

??? success
    ### parse-time error
    - コードの読み込み中(解析中)にエラーが出た場合は回復不能
    - エンジンがコードを理解できていないため

    ### 実行時エラー
    - 有効なコードの中でエラーが起きた場合は、例外と呼ばれる

## Q58. try...catchが非同期的に動作しないことを知っていますか?

??? success
    ### 理由
    - 関数が実行された時には、エンジンが`try...catch`構造を抜けているため

    ### 使用例
    - `0ms`でもダメ

    ```js
    try{
      setTimeout(()=>{
        noSuchVariable;
      }, 0);
    }catch (e){
      console.log("won't work");
    }

    //Uncaught ReferenceError: noSuchVariable is not defined
    ```

    ### 対策
    - その関数の中に、`try...catch`を書く

    ```js
    setTimeout(()=>{
      try{
        noSuchVariable
      }catch(e){
        console.log("won't work");
      }
    }, 1000);

    //won't work
    ```

## Q59. Errorオブジェクトが持っているプロパティについて知っていますか?

??? success
    ### Errorオブジェクト
    - エラーが発生したときに、生成されるオブジェクト
    - `catch`の引数として渡される

    ### プロパティ一覧
    - `name`: エラー名
    - `message`: エラー詳細に関するテキストメッセージ
    - `stack`: 現在のコールスタック(非標準)

    ### 例

    ```js
    "use strict";

    try{
      hoge;
    }catch(err){
      console.log(err.name);
      //ReferenceError
      console.log(err.message);
      //hoge is not defined
      console.log(err.stack);
      //ReferenceError: hoge is not defined
      // at <anonymous>:2:7
      // anonymousは、グローバルコンテキスト

      console.log(err);//全体を表示
      //ReferenceError: hoge is not defined
      //at <anonymous>:2:7
    }
    ```

## Q60. エラーの詳細が必要ない場合、catchがそれを省略できることを知っていますか?

??? success
    ### ES2019で追加

    ```js
    "use strict";
    try{
      hoge;
    }catch{
      console.log("hogeeeee");
    }
    ```

## Q61. throwを用いて柔軟にエラーを投げることができることを知っていますか?

??? success
    ### 構文的には正しいケースを考える
    - たとえば、`name`プロパティを後程使うので、これが無ければエラーとしたい。

    ```js
    let json = '{"age" : 30}';

    try{
      let user = JSON.parse(json);
      console.log(user.name); //undefined
    }catch(e){
      //errorではないので、こちらは実行されない
      console.log("doesn't execute");
    }
    ```

    ### Throw演算子
    - エラーを生成する
    - `throw <error object>`
    - `<error object>`には、プリミティブや適当なオブジェクトを入れることもできる
    - しかし、`name`と`message`プロパティを持つオブジェクトを使うのがよい。そうすれば組み込みのエラーとの互換性を保てる

    ### 組み込みのエラーオブジェクトを生成する
    - 組み込みのエラーの場合、`name`はコンストラクタの名前
    - `message`は引数

    ```js
    let error = new Error("Something message");
    console.log(error.name); //Error
    console.log(error.message); //Something message

    let error = new SyntaxError("構文エラー");
    let error = new ReferenceError("未定義変数エラー");
    ```

    ### 改めて、nameプロパティがなければエラーとする

    ```js
    let json = '{"age" : 30}'

    try{
      let user = JSON.parse(json);
      if(!user.name){
        throw new SyntaxError("Incomplete date: no name");
      }

      alert(user.name);
    }catch(e){
      console.log("JSON error: " + e.message);
    }

    //JSON error: Incomplete date: no name
    ```

## Q62. catchするエラーの種類を制限する方法を知っていますか?

??? success
    ### その性質上
    - `catch`はデフォルトの場合、`try`空のすべてのエラーを取得する。
    - たとえばQ61の場合、`JSON`関連のエラーでなくとも、`JSON error`が表示されてしまう。これはよろしくない

    ### 再スロー
    1. 全てのエラーをキャッチする
    2. `instanceof`などでエラーの種類をチェックし、条件に一致しない場合は、再度`throw err`でエラーを投げる
    4.　エラークラス名と比較したい場合、`error.name`や`error.constructor.name`が使える。

    ### catch節で`throw`を投げた場合
    - `try...catch`を抜けて、外部の`try...catch`を探しに行く。存在すれば、そこでエラーを`catch`する
    - 存在していなければエラーでスクリプトは終了する

    ### 再スローの例(一つ外)
    - `if`文で条件分岐すればいいので、本来こんなことをする必要はない

    ```js
    function readData(){
      let json = '{"age" : 30}'
      try{
        try{
          hoge();
        }catch(e){
          if(e.name != "SyntaxError"){
            throw e;
          }
        }
      }catch(e){console.log(e)}
    }

    readData();

    /*
    ReferenceError: hoge is not defined
    at readData (<anonymous>:5:9)
    at <anonymous>:14:7
    */
    ```

    ### 再スローの例(呼び出し元)
    - 関数内のエラーが`try...catch`で補足されない場合、その後の処理は実行されず、エラーは即座に呼び出し元に伝播する

    ```js
    function readData(){
      let json = '{"age" : 30}'

      try{
        hoge();
      }catch(e){
        if(e.name != "SyntaxError"){
          throw e;
        }
      }
    }

    try{
      readData();
    }catch(e){
      console.log("Some error: " + e.stack);
      //Some error: Some error: ReferenceError: hoge is not defined
      //at readData (<anonymous>:5:9)
      //at <anonymous>:14:7
    }
    ```

    !!! warning
        - 上記で`"Some error: " + e`とすると、スタック情報が出力されなくなる
        - つまり、`e`ではなく、`e.stack`と明示する必要はある

## Q63. エラーがあっても、なくとも何かしらの処理を実行したい場合、finallyが使えることを知っていますか?

??? success
    ### 使いどころ
    - ファイルを閉じるなど

    ### 構文

    ```js
    try{
      //...somecode
    }catch{
      //...someerror
    }finally{
      //常に実行
    }
    ```

    ### 使用例

    ```js
    try{
      alert("try");
      if(confirm("Test Error?")) BAD_CODE();
    }
    catch{
      alert("catch");
    }
    finally{
      //ここで変数を使いたいときは、スコープに注意。
      alert("finally");
    }

    //try
    //Test Error --Yes--> catch
    //finally
    ```

## Q64. tryでreturn。catchでthrowが行われても、finallyは実行されることを知っていますか?

??? success
    ### finallyとreturn/throw
    - `finally`は`try...catch`からの任意の終了に対応する
    - `return`や`throw`が行われた場合、制御が外部コードに戻る前に実行される

    ### 使用例(return)

    ```js
    function func(){

      try{
        return 1;
      }catch{

      }finally{
        console.log("finally");
      }
    }

    console.log(func());
    //finally
    //1
    ```

    ### 使用例(throw)

    ```js

    function func(){

      try{
        hoge();
      }catch(e){
        if(e.name != "SyntaxError"){
          throw e;
        }
        console.log(e.message);
      }finally{
        console.log("still inside the function");
      }
    }

    try{
      func()
    }catch(e){
      console.log(e.message);
    }

    //still inside the function
    //ReferenceError: hoge is not defined
    //at func (<anonymous>:4:9)
    //at <anonymous>:16:7
    ```

## Q65. catch句がない、`try...finally`も成立することを知っていますか?

??? success
    ### 使いどころ
    - スクリプトが死ぬ(もしくは外部の`try...catch`に処理が移る)前に処理を行える

    ### コード例

    ```js
    function func(){
      try{
        hoge();
      }finally{
        console.log("still alive");
      }
    }

    func();
    //still alive
    //Uncaught ReferenceError: hoge is not defined
    //at func (<anonymous>:3:9)
    //at <anonymous>:9:5
    ```

## Q66. try...catchの外側で致命的なエラーが起きたときに対処する、グローバルなcatchというものを知っていますか?

??? success
    ### Node.jsの場合
    - `process.on("uncaugutException")`

    ### ブラウザの場合
    - `window.onerror`プロパティ
    - これは`catch`されなかった例外がある場合
    - またはコンパイル時エラーが発生した場合にトリガされる

    ### ブラウザの場合の構文

    ```js
    window.onerror = function(message, url, line, col, error){}
    ```

    ### 説明
    - `message`:エラーメッセージ
    - `url`:エラーが起きたスクリプトのURL
    - `line, col`:エラーが起きた行と列番号
    - `error`:エラーオブジェクト


    ### 使用例

    ```js
    //<script>
      window.onerror = function(message, url, line, col, error){
        console.log(`${message}\n At ${line}:${col} of ${url}`);
        console.log(error);
      }

      function readData(){
        hoge();
      }

      hoge();
    //<script>
    ```

    ### 結果

    ```powershell
    Uncaught ReferenceError: hoge is not defined
    At 18:5 of file:///C:/file_test/test/index.html

    ReferenceError: hoge is not defined
        at index.html:18:5

    #通常のエラー
    Uncaught ReferenceError: hoge is not defined
        at index.html:18:5
    ```

    !!! warning
        - 開発者ツールで出来ない場合は、適当なファイルを用意して試す事

    ### どうしても開発者ツールで試したい場合の回避策(非推奨)

    ```js
    window.onerror = function(message, url, line, col, error){
      console.log(`${message}\n At ${line}:${col} of ${url}`);
      console.log(error);
      }

    setTimeout(()=> {hoge()}, 0);

    /*
    Uncaught ReferenceError: hoge is not defined
    At 6:24 of 
    ReferenceError: hoge is not defined
        at <anonymous>:6:24
    VM7078:6 Uncaught ReferenceError: hoge is not defined
        at <anonymous>:6:24
    */
    ```

## Q67. window.onerrorの役割と、有料のエラートラッキングサービスについて知っていますか?

??? success
    ### window.onerror
    - 開発者にエラーメッセージを送るため
    - 実行の回復ではない

    ### エラートラッキングサービス
    - 大抵有料
    - 例としては、`Rollbar`や`Sentry`
    - 個人開発でないなら、費用対効果はある

    ### 有料のエラートラッキングサービスの大まかな手順
    1. サービスに登録する
    2. ページに挿入するためのURLやJSスクリプトを得る
    3. 該当スクリプトはカスタムされた`window.onerror`を持っている
    4. エラーが起きたときは、そのサービスへネットワークリクエストを送信する
    5. サービスにログインしてエラーを閲覧


    ### Sentry概要
    - エラーの発生を`Slack`経由等で確認し、その時の状況を分かりやすい形で確認可能

    ### Sentry用語
    - `Projects`: エラーイベントのトラッキング対象となるアプリの単位。複数言語でアプリが構成されている場合、別のプロジェクトとする

    - `Issues`: 同じエラーイベントをグルーピングする
    - `Quota`:　エラーイベント発生数

    ### 参考URL
    - [Sentryで始めるエラー監視](https://zenn.dev/apgun/articles/798661f7eb7c86)
    - [Sentryで超簡単！楽々エラー監視-Sentry登録からアプリへの導入方法のすべて-](https://techblog.cartaholdings.co.jp/entry/archives/4091)

    ### 体験談
    - 工事中
    - React,Next.jsの一問一答記載後に記載

    ### errorpush
    - オープンソースのエラー監視ツール
    - 機能は最小限
    - 詳細は試してから記載する事 

## Q68 Errorをextendsで拡張して、独自のエラークラスを作ることができることを理解していますか?

??? success
    ### たとえば
    - `DbError`: データベース関連のエラー
    - `NotFoundError`: 検索操作の場合
    - `HttpError`: ネットワーク操作の場合
    - このようなエラーは存在していない
    - また`name`,`message`,`stack`以外の独自のプロパティを持ちたいときがくるかもしれない

    ### 任意の引数で`throw`できるから問題ないのでは?
    - `Error`から継承しておけば`obj instanceof Error`が使える
    - 大きなアプリケーションを開発する場合、階層的にエラーを形成した方が分かりやすい
    - 例) `HttpTimeoutError extends HttpError`

    ### Errorの拡張例
    - 構文的には正しいが、フォーマットが誤っている場合、`Error`を継承した`ValidationError`を送出するようにする

    ### コード例

    ```js

    //Errorの疑似コード
    /*
    class Error{
      constructor(message){
        this.message = message;
        this.name = "Error";
        this.stack = <nested calls>
      }
    }
    */

    class ValidationError extends Error{
      constructor(message){
        super(message);
        // 親のコンストラクタで設定されているプロパティを上書き
        this.name = "ValidationError"
      }
    }

    function test(){
      throw new ValidationError("blunder");
    }

    try{
      test();
    }catch(err){
      console.log(err.message);//blunder
      console.log(err.name);//ValidationError
      console.log(err.stack);
      //ValidationError: blunder
      //at test (<anonymous>:9:13)
      //at <anonymous>:13:7
    }

    function readUser(json){
      let user = JSON.parse(json);

      if(!user.age){
        throw new ValidationError("No filed: age");
      }
      if(!user.name){
        throw new ValidationError("No field: name");
      }

      return user;
    }

    try{
      let user = readUser('{"age": 25}');
    }catch(err){
      //errに応じて処理を分岐する
      if(err instanceof ValidationError){
        console.log(`Invalid data: ${err.message}`);
      }
      else if(err instanceof SyntaxError){
        console.log(`JSON Syntax Error ${err.message}`);
      }
      else{
        throw err; //未知のエラーの場合は投げる
      }
    }

    //Invalid data: No field: name
    ```

    ### なぜ`err.name == "ValidationError"`にしないのか。
    - 将来、`ValidationError`を拡張して派生クラスを作るときのことを考える
    - `===`なら引っかからないが、`instanceof`なら`ValidationError`から派生したクラスをまとめて引っ掛けることができる

    !!! warning
        ### err.name === を使う場合
        - サードパーティのライブラリから来たエラーオブジェクトを持っていて、そのクラスを取得するすべがない

    ### 実際に拡張してみる

    ```js
    class ValidationError extends Error{
      constructor(message){
        super(message);
        this.name = "ValidaitonError";
      }
    }

    //欠落しているプロパティについて、追加の情報を保持する
    class PropertyRequiredError extends ValidationError{
      constructor(property){
        super("No property: " + property);
        this.name = "PropertyRequiredError";
        this.property = property;
      }
    }

    function readUser(json){
      let user = JSON.parse(json);

      if(!user.age){
        throw new PropertyRequiredError("age");
      }

      if(!user.name){
        throw new PropertyRequiredError("name");
      }

      return user;
    }

    try{
      let user = readUser('{"age" : 25}');
    }catch(err){
      if(err instanceof ValidationError){
        console.log("Invalid data: " + err.message);
        console.log(err.name);
        console.log(err.property);
      }else if(err instanceof SyntaxError){
        console.log("JSON Syntax Error: " + err.message);
      }else{
        throw err;
      }
    }

    //Invalid data: No property: name
    //PropertyRequiredError
    //name
    ```

    ### 複数回this.nameを書くのが面倒に感じた場合
    - 基本エラークラスを作り、それを継承させる

    ```js
    class MyError extends Error{
      constructor(message){
        super(message);
        /*
        1. thisは常にdotの前のオブジェクトなので、エラークラスから生み出されたインスタンス
        2. obj.constrcutorは[[prototype]]チェーンより、コンストラクタ。
        3. コンストラクタ.nameはコンストラクタ名
        */
        this.name = this.constructor.name;
      }
    }

    //this.nameについて改めて書く必要がない
    class ValidationError extends MyError{};

    class PropertyRequiredError extends ValidationError{
      constructor(property){
        super("No property: " + property);
        this.property = property;
      }
    }

    //本体コード
    function readUser(json){
      let user = JSON.parse(json);
      if(!user.name){
        throw new PropertyRequiredError("name");
      }
      if(!user.age){
        throw new PropertyRequiredError("age");
      }
      return user;
    }

    try{
      let user = readUser('{"name" : "Taro"}');
    }catch(err){
      if(err instanceof ValidationError){
        console.log("Invalid data: " + err.message);
        console.log(err.name);
        console.log(err.property);
      }
      else if(err instanceof SyntaxError){
        console.log("JSON Syntax Error: "+ err.message);
      }else{
        throw err;
      }
    }

    //Invalid data: No property: age
    //PropertyRequiredError
    //age
    ```

## Q69. 全てのエラータイプをif文でチェックするのにうんざりした場合、例外のラッピングというテクニックが使えることを知っていますか?

??? success
    ### 全てのエラータイプをifでチェックする必要は?
    - 多くの場合はない
    - たとえば、Q68の場合、「データ読み込み時エラー」が発生したかどうかが知りたいだけ
    - 必要な場合にのみ、エラーの詳細を取得したい

    ### 例外のラッピング(wrapping exceptions)の挙動
    1. 汎用的なデータ読み込みエラーを表現するクラス:`ReadError`を生成
    2. データ読み込み時のエラーをキャッチしたら、再スローを行い、まとめて`ReadError`でキャッチする
    3. `source`プロパティを持ち、ここで元のエラーを参照する
    4. 必要な場合のみ`source`プロパティをチェックする

    ### 例外のラッピングの狙い
    - 関数が低レベルの例外を処理
    - 高レベルのエラーを生成
    - 低レベルの例外は高レベルのエラーのプロパティとなり、必要に応じて取得する

    ### 使用例

    ```js
    class MyError extends Error{
      constructor(message){
        super(message);
        this.name = this.constructor.name;
      }
    }

    class ReadError extends MyError{
      constructor(message, source){
        super(message);
        this.source = source;
      }
    }

    class ValidationError extends MyError{}

    class PropertyRequiredError extends ValidationError{
      constructor(property){
        super("No property: " + property);
        this.property = property;
      }
    }

    function validateUser(user){
      if(!user.age){
        throw new PropertyRequiredError("age");
      }
      if(!user.name){
        throw new PropertyRequiredError("name");
      }
    }

    function readUser(json){
      let user;

      try{
        user = JSON.parse(json);
      }catch(err){
        if(err instanceof SyntaxError){
          //SyntaxErrorならReadErrorを投げる
          //causeには元のエラーを格納
          throw new ReadError("Syntax Error", err);
        }else{
          throw err;
        }
      }

      try{
        validateUser(user);
      }catch(err){
        if (err instanceof ValidationError){
          //ValidationErrorならReadErrorを投げる
          //causeには元のエラーを格納
          throw new ReadError("Validation Error", err);
        }else{
          throw err;
        }
      }
    }

    try{
      readUser('{bad json}');
    }catch(e){
      if(e instanceof ReadError){
        console.log(e);
        console.log("Original error: \n" + e.source);
      }else{
        throw e;
      }
    }

    /*
    readUser('{bad json}');

    ReadError: Syntax Error
        at readUser (<anonymous>:40:17)
        at <anonymous>:58:7

    Original error: 
    SyntaxError: Expected property name or '}' in JSON at position 1 (line 1 column 2)
    */

    /*
    readUser('{"name": "hoge"}');

    ReadError: Validation Error
        at readUser (<anonymous>:54:17)
        at <anonymous>:62:7

    Original error: 
    PropertyRequiredError: No property: age
    */
    ```