# クイズ(中級6:分割代入)

## Q123. 分割代入について知っていますか?

??? success
    ### 分割代入の使いどころ
    - ObjectやArrayを関数に渡すとき、全体は要らないことがある
    - 分割代入は、配列やObjectの中身を複数の変数にアンパックする(展開して代入する)ようにする構文である

## Q124. 分割代入の構文を知っていますか?

??? success
    ### 例
    - 配列自体は変更しない

    ```js
    const arr = ["Taro", "Yamada"];
    //firstName = arr[0];  surName = arr[1]
    const [firstName, surName] = arr;

    console.log(firstName); //Taro
    ```

    ### 文字列から分割代入する
    - `split`で一度配列に

    ```js
    const array = "Taro Yamada".split(" ");
    const [firstName, surName] = array;
    console.log(firstName); //Taro
    ```

    ### 1文字ずつでいいなら

    ```js
    const [a,b,c] = "abc";
    console.log(a); //a
    ```

    ### 配列以外でも、iterableなら可能

    ```js
    const [one, two, three] = new Set([1,2,3]);
    console.log(two); //2
    ```

    ### iterableなら可能な理由
    - 内部的には分割代入は、右辺の値に対して`iterator`を呼び出す
    - `for ... of`を呼び出して、値を代入しているのと一緒

    ### オブジェクトのプロパティにも割り当て可能

    ```js
    const user = {};
    [user.name, user.surname] = "Taro Yamada".split(" ");

    console.log(user.surname); //Yamada
    ```

    ### .entries()を思い出す
    - これも分割代入(返り値の`Array` -> 変数代入)

    ```js
    const user = {
      name : "Hoge",
      age : 30,
    };

    for(const [key, value] of Object.entries(user)){
      console.log(`${key}:${value}`);

      //name:Hoge
      //age:30
    }
    ```


## Q125. 分割代入を用いた変数を入れ替えるトリックを知っていますか?

??? success
    ### 例(swapped)

    ```js
    let guest = "Hoge";
    let admin = "Fuga";

    [guest, admin] = [admin, guest];

    console.log(admin); //Hoge
    ```

    ### 説明

    1. 右辺の、`[admin guest]`を評価することで、一時的に新しい配列がヒープに作成される
    2. 左辺で分割代入
    3. 一時的な配列はガベージコレクションの対象となり、メモリ解放

## Q126. 残りの要素をすべて取得する、...について知っていますか？

??? success

    ### そもそも
    - 代入する変数よりも、配列の要素数が多い場合、余分な項目は省略される

    ```js
    const [num1, num2] = [1,2,3,4,5,6,7];
    ```

    ### 例
    - `...`を書く

    ```js
    const [num1, num2, ...rest] = [1,2,3,4,5,6,7];
    console.log(rest); //[3,4,5,6,7];
    ```

## Q127. 分割代入にデフォルト値が設定できることを知っていますか?

??? success
    ### デフォルト値がない場合
    - エラーにはならない

    ```js
    const [firstName, surName] = [];
    console.log(firstName); //undefined
    ```

    ### デフォルト値

    ```js
    const [name = "Guest", surName = "Anonymous"] = ["Hoge"];

    console.log(name); //Hoge
    console.log(surName); //Anonymous
    ```

    ### default値を関数呼び出しにする
    - 値がある場合は呼ばれない

    ```js
    const [name = prompt("Name?"), age = prompt("Age?")] = ["Toraneko"];

    console.log(age); //Promptで入力した値
    ```

## Q128. オブジェクトに対して分割構文を用いた際の注意点について知っていますか?

??? success
    ### {}を用いる
    - 以下の場合、`{}`内にはプロパティ名と同名の変数しか設定できない

    ```js
    const obj = {
      name : "Hoge",
      age : 30,
      address : "Tokyo",
    }

    const {name = "default", age, address} = obj;
    console.log(name); //Hoge
    ```

    ### 別名の変数に代入したい場合

    ```js
    const obj = {
      name : "Hoge",
      age : 30,
      address : "Tokyo",
    }

    const {name : n, age : a, address} = obj;
    console.log(n); //Hoge
    ```

    ### `...`を使った場合

    ```js
    const options = {
      title : "Menu",
      height : 200,
      width : 100,
    };

    const {title, ...rest} = options;
    console.log(rest.height); //200
    ```

    ### 既存の変数に代入したい場合の注意点
    - `let`がないと、コードブロックの意味合いの`{}`と勘違いする
    - そのため全体を`()`で囲む

    ```js
    let title, width, height;

    ({title, width, height} = {title : "Menu", height: 300, width : 200});

    console.log(title); //Menu
    ```

## Q129. 入れ子構造になっているオブジェクトに対して、分割代入を使うことができますか？

??? success
    ### 注意点
    - 入れ子になっている場合は、その分、分割する必要がある

    ```js
    const options = {
      size : {
        width: 200,
        height: 100,
      },
    };

    //これでは得られない
    //let {width} = options;

    //正解(sizeを得る代わりに中身を得ているので、変数としてsizeは得られない)
    let {size : {width}} = options; //200

    //sizeを取得したい場合
    let {size} = options; //{width : 200, height : 100}
    ```

    ### 使用例

    ```js
    const options = {
      size : {
        width: 200,
        height: 100,
      },
      items: ["Cake", "Donut"],
      extra : true,
    };

    let {
      size : {
        width,
        height,
      },
      items: [item1, item2],
      title = "Menu",
    } = options;

    console.log(width); //200
    ```

## Q130. 関数の引数として分割代入を使う利点を理解していますか?

??? success
    ### 使わなかった場合
    - 以下の例では、引数の順番を(IDEの助けなしでは)覚えられない

    ```js
    function hoge(title = "Untitled", width = 200, height = 100, items = []){
      //...
    }
    ```

    ### 分割代入を使う
    - 何が必要かだけわかっていればいい
    - 引数の順番を考える必要がない

    ```js
    function showMenu({
      title = "Untitled",
      width : w = 100,
      height : h = 200,
      //※配列などの複合型のパラメータに対して、個別にデフォルト値を設定することはできない
      items : [item1, item2] = ["item1", "item2"]
    } = {}){
      console.log(`${title}:${w}*${h}`);
      console.log(item1, item2);

    }

    const options = {
      title : "My menu",
      items : ["banana", "apple"],
    }

    showMenu(options)
    //My menu:100*200
    //banana apple
    ```

    ### なぜ`={}`が必要なのか
    - 分割構文を書いている以上、`showMenu()`は引数として、オブジェクトを前提にしている
    - そのため`={}`がない状態で、`showMenu()`とすると、オブジェクトが渡されないため失敗する
    - 今回は、全体のデフォルト値として`{}`を設定しているので、`showMenu()`でも通る

