# クイズ(中級8:JSON)

## Q141 複雑なオブジェクトをネットワーク経由で送りたいときに、toString()で変換しない理由を理解していますか?

??? success
    ### 文字列を変換するにはtoString()でもよかった

    ```js
    const user = {
      name : "John",
      age : 30,

      toString(){
        return `{name : "${this.name}", age: ${this.age}}`;
      }
    };

    console.log(user); // {name: "John", age: 30}
    ```

    ### プロパティは追加されることもある
    - たとえば、新しいプロパティが追加された場合、その都度、`toString()`を書き換える必要がある
    - また、オブジェクトを送信する場合、受信側でそれを読み取るためのコードも必要になる

    ### JSON
    - `Javascript Object Notation`
    - 値とオブジェクトを表現するためのもの
    - データ交換として使われる

## Q142. JSON.stringifyを知っていますか？

??? success
    ### JSON.stringify
    - オブジェクトをJSONに変換

    ```js
    const student = {
      name : "John",
      age : 30,
      isAdmin : false,
      courses : ["html", "css", "js"],
      isLevel : null,
      isComplete : undefined
    };

    const json = JSON.stringify(student);

    console.log(json);

    //見やすいように調整してある。実際はスペースなどがない
    /*
    {
      "name":"John",
      "age":30,
      "isAdmin":false,
      "courses":["html","css","js"],
      "isLevel":null
    }
    */
    ```

    ### 説明
    - 結果のJSON文字列はシリアライズされたオブジェクトと呼ばれる。
    - 文字列にはダブルクォートを用いる

    ### 注意点
    - `JSON.stringify`はプリミティブに対しても適用可能
    - JS固有のオブジェクトプロパティの一部はスキップする
    - つまり、メソッド、シンボルキーと値、undefinedを格納しているプロパティは無視される(カスタマイズ可能)

    ```js
    const user = {
      sayHi(){
        alert("Hello")
      },
      [Symbol("id")] : 123,
      something: undefined,
    }

    JSON.stringify(user); //{}
    ```

    ### 入れ子であっても自動的に変換可能

    ```js
    const Monster = {
      race : {
        slime : ["red-slime", "blue-slime"],
        oak : ["red-oak"],
        human : ["esper", "normal"]
      }
    };

    console.log(JSON.stringify(Monster));

    //見やすいように調整してある。実際はスペースなどがない
    /*
    {
      "race": {
        "slime": [
          "red-slime",
          "blue-slime"
        ],
        "oak": [
          "red-oak"
        ],
        "human": [
          "esper",
          "normal"
        ]
      }
    }
    */
    ```

    ### 循環参照があると失敗する
    - 循環参照:複数のオブジェクトが互いを参照しループしている状態

    ```js
    const hoge = {
      number : 1,
    }

    const fuga = {
      title : "Fuga",
    }

    hoge.address = fuga;
    fuga.place = hoge;

    JSON.stringify(fuga); //Converting circular structure to JSON
    ```

## Q143. JSON.stringifyのreplacerで、シリアライズ化するプロパティを選択できることを知っていますか?

??? success
    ### json = JSON.stringify(value [, replacer, spaces])
    - `value`: エンコード対象
    - `replacer`:エンコードするプロパティの配列。または`function(key, value)`
    - `spaces`: フォーマットで用いるスペースの数

    ### 第2引数にプロパティの配列を渡した場合
    - そのプロパティだけがエンコードされる
    - プロパティリストはオブジェクト構造全体に適用されるので、内側のプロパティ名もリストに入れる必要がある

    ```js
    //循環参照を無視する例
    const room = {
      number : 1
    };

    const meetUp = {
      title : "Conference",
      participants : [{name: "John"}, {name : "Ann"}],
      place : room
    };

    room.occupiedBy = meetUp;

    const json = JSON.stringify(meetUp, ["title", "participants"]);
    //{"title":"Conference","participants":[{},{}]}

    const json1 = JSON.stringify(meetUp, ["title", "participants", "name"]);
    //{"title":"Conference","participants":[{"name":"John"},{"name":"Ann"}]}

    //occupiedByを除くすべてをシリアライズした例
    const json2 = JSON.stringify(meetUp, ["title", "participants", "name", "place", "number"]); 
    //{"title":"Conference","participants":[{"name":"John"},{"name":"Ann"}],"place":{"number":1}}

    ```

    ### replacerとしてfunction(key, value)を使う
    - プロパティのリストが長いとわかりにくいのでこちらを使う
    - 関数は全ての`(key, value)`ペアに対し、置換された`value`を返す
    - `replacer`内側での`this`は、現在のプロパティを含むオブジェクトを指す

    ```js
    const room = {
      number : 1
    };

    const meetUp = {
      title : "Conference",
      participants : [{name: "John"}, {name : "Ann"}],
      place : room
    };

    room.occupiedBy = meetUp;

    //occupiedByだけundefinedを返し、スキップする
    const json = JSON.stringify(meetUp, (key, value)=>{
      console.log(`${key}:${value}`)
      return key == "occupiedBy" ? undefined : value;
    })

    console.log(json);
    //{"title":"Conference","participants":[{"name":"John"},{"name":"Ann"}],"place":{"number":1}}
    ```

    ### 解説
    - ループ中の`key:value`の出力が以下である
    - 再帰的に呼び出されていることがわかる
    - 最初の呼び出しは特別なラッパーオブジェクト、`{"": meetUp}`
    - つまり、1度目の呼び出しはkey = 空。value = オブジェクト全体である。
    - 全てに対して`replace`を提供しているので、スキップ/置換なども容易

    ```
    :[object Object]
    title:Conference
    participants:[object Object],[object Object]
    0:[object Object]
    name:John
    1:[object Object]
    name:Ann
    place:[object Object]
    number:1
    occupiedBy:[object Object]
    ```

    !!! warning
        - `replacer`に`null`のような関数や配列でない値を指定すると、結果にはすべてのプロパティが含まれる

## Q144. JSON.stringifyのspacesでフォーマットが調整できることを理解していますか?

??? success
    ### spaces
    - 見やすい出力をしたいときに使われる

    ### spaces = Number
    - 上限10
    - インデントとして使う空白文字の数を意味する
    - 1より小さい場合は空白を使わないことを意味する

    ### spaces = String
    - 10文字上限
    - 該当文字がネストされたオブジェクトなどの前に挿入される
    - 文字列数値以外の場合、空白は使用しない

    ### 使用例

  
    ```js
    const user = {
      name : "John",
      age : 25,
      roles : {
        isAdmin : false,
        isViewer : true,
        isEditor : true,
      }
    };

    json = JSON.stringify(user, null, 2);

    console.log(json);

    /*
    {
      "name": "John",
      "age": 25,
      "roles": {
        "isAdmin": false,
        "isViewer": true,
        "isEditor": true
      }
    }
    */
    ```

## Q145. toJSONについて知っていますか?

??? success
    ### toJSON
    - オブジェクトはJSONへの変換メソッドであるtoJSONを持っている
    - `JSON.stringify`は利用可能である場合、自動でそれを呼び出す

    ### 例

    ```js
    new Date("1970-01-01").toJSON();
    //1970-01-01T00:00:00.000Z
    ```

    ### toJSONをカスタマイズする
    - カスタマイズ前

    ```js
    const room = {
      number : 1,
    }

    const meetUp = {
      title : "Conference",
      date : new Date(Date.UTC(1970,0,1)),
      room
    };

    const json = JSON.stringify(meetUp, null, 2);

    //roomの中身はそのまま入れ子のように返っている.
    /*
    {
      "title": "Conference",
      "date": "1970-01-01T00:00:00.000Z",
      "room": {
        "number": 1
      }
    }
    */
    ```

    ```js
    const room = {
      number : 1,
      toJSON(){
        return this.number * 10;
      }
    }

    const meetUp = {
      title : "Conference",
      date : new Date(Date.UTC(1970,0,1)),
      room
    };

    const json = JSON.stringify(meetUp, null, 2);

    //numberの値を10倍して、それだけを戻すようにする。
    /*
    {
      "title": "Conference",
      "date": "1970-01-01T00:00:00.000Z",
      "room": 10
    }
    */
    ```

## Q146 JSON.parseでJSON文字列をオブジェクトに戻せることを知っていますか?

??? success
    ### JSON.parse(str [, reviver])
    - `str`: パース対象
    - `reviver`: 値を変形するためのオプション関数

    ### 例

    ```js
    let numbers = "[0,1,2,3]";
    numbers = JSON.parse(numbers);
    console.log(numbers[1]); //1
    ```

    ### JSONを直書きする際の注意点
    !!! warning
        - 全体を文字列とする。
        - プロパティ名はダブルクォートで書く
        - 文字列の場合、値もダブルクォート
        - Dateのようなものは存在しない
        - __最後にカンマを書いてはいけない__
        - 数値やBoolean,配列はそのまま書ける
        - コメントは許可されていない

    ```js
    const json = `{
      "name" : "John",
      "isAdmin" : false,
      "date" : "2017-11-30T12:00:00.000Z",
      "number" : 1,
      "array": [0,1,2,3,4,5]
    }`;

    JSON.parse(json);
    //{name: 'John', isAdmin: false, date: '2017-11-30T12:00:00.000Z', number: 1, array: Array(6)}
    ```

## Q146. reviverを利用したことがありますか?

??? success
    ### エラー例
    - 現在、`date`はただの文字列なのでオブジェクトに戻しても、`Date`オブジェクト扱いはされず、エラーになる

    ```js
    const json = `{
      "name" : "John",
      "isAdmin" : false,
      "date" : "2017-11-30T12:00:00.000Z",
      "number" : 1,
      "array": [0,1,2,3,4,5]
    }`;

    const obj = JSON.parse(json);

    console.log(obj.date.getDate()); //obj.date.getDate is not a function
    ```

    ### dateがDateになるような関数をreviverに書く
    - 正常にデシリアライズした例
    - `reviver`は`replacer`と同様、再帰的に探索するので、入れ子になっていても可能

    ```js
    const json = `{
      "name" : "John",
      "isAdmin" : false,
      "date" : "2017-11-30T12:00:00.000Z",
      "nesting_date" : [
        {"date" : "2023-01-15T12:00:00.000z"}
      ],
      "number" : 1,
      "array": [0,1,2,3,4,5]
    }`;

    const obj = JSON.parse(json, (key, value)=>{
      if(key == "date") return new Date(value);
      return value;
    })

    console.log(obj.date.getDate()) //30
    console.log(obj.nesting_date[0].date.getDate()); //15
    ```