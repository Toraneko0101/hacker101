# 12-setState(object)の注意点

## ミューテーション
- 数値、文字列、真偽値の場合は、immutable(イミュータブル)なので、値が変わることはない。
- 再レンダー時には、「書き換え」るのではなく、値を「置き換え」ている
- オブジェクトの場合は、書き換え可能

=== "例"

    ```js
    const [position, setPosition] = useState({x:0, y:0});
    position.x = 5; //書き換え
    ```

- しかし、Reactのstate内にあるオブジェクトはイミュータブルなものとして扱うべき。つまり、常に置き換える

## なぜ置き換える必要があるのか
- Reactでは`setState()`が使用されない限り、再レンダーは行われず、stateが変更されたことを認識できないから。

## 結論
- 再レンダーをトリガーするためには、 __新しいオブジェクトを作成し、それを`setState()`に渡す__
- つまり、オブジェクトも数値などと同様に置き換える

=== "例"

    ```js
    onPointerMove ={e =>{
      setPosition({ //(1)
        x: e.clientX,
        y: e.clientY
      });
    }}
    ```

    1. positionを新しいオブジェクトに置き換えて、再レンダーせよ

=== "別解"

    ```js
    onPointerMove = {e =>{
      const nextPosition = {}
      nextPosition.x = e.clientX;
      nextPosition.y = e.clientY; //(1)
      setPosition(nextPosition); //(2)
    }}
    ```

    1. オブジェクトの書き換えが問題になるのは、それが`state`として存在している場合。`nextPosition`は、`state`ではなく、どこからも参照されていないので、「書き換え」ても問題はない。(=ローカルミューテーション)
    2. `state`を新しいオブジェクトで「置き換え」ている

## スプレッド構文とコピー(置き換え)

### 使いどころ
- 多くのフィールドがあり、1つだけ値を書き換えたいケース
- 全てのフィールドについて書くのは面倒
- よって、以下のようにする

=== "例"

    ```js
    setPerson({
      ...person, //(1)
      firstName = e.target.value //(2)
    });
    ```

    1. 全てのフィールドをコピーする
    2. 全てのフィールドには、`firstName`フィールドも含まれる。しかしフィールドが複数回登場した場合、後の方が優先されるため、このフィールドだけが書き換えられる。

=== "実用例"

    ```js
    import { useState } from "react";

    const App = () => {
      const [person, setPerson] = useState({
        firstName: "Tarou",
        lastName: "Yamada",
        email: "hogehoge@gmail.com"
      });

      //更新したいフィールドについてのみ書けば十分
      //同じような関数を三つも書いて冗長だと思った場合は隣タブ参照
      const handleFirstNameChange = (e) => {
        setPerson({
          ...person,
          firstName: e.target.value
        });
      }

      const handleLastNameChange = (e) => {
        setPerson({
          ...person,
          lastName: e.target.value
        });
      }

      const handleEmailChange = (e) => {
        setPerson({
          ...person,
          email: e.target.value
        });
      }
      return (
        <>
          <label>
            First name:
            <input
              value={person.firstName}
              onChange={handleFirstNameChange}
            />
          </label>
          <label>
            Last name:
            <input
              value={person.lastName}
              onChange={handleLastNameChange}
            />
          </label>
          <label>
            Email:
            <input
              value={person.email}
              onChange={handleEmailChange}
            />
          </label>
          <p>
            {person.firstName + " "
              + person.lastName + " "
              + person.email
            }
          </p>
        </>
      )
    }

    export default App;
    ```

=== "実用例(改良)"

    ```js
    import { useState } from "react";

    const App = () => {
      const [person, setPerson] = useState({
        firstName: "Tarou",
        lastName: "Yamada",
        email: "hogehoge@gmail.com"
      });

      //複数のフィールドに対して単一のイベントハンドラを指定している
      const handleChange = (e) => {
        setPerson({
          ...person,
          [e.target.name]: e.target.value //(1)
        });
      }

      return (
        <>
          <label>
            First name:
            <input
              name="firstName" //(2)
              value={person.firstName}
              onChange={handleChange}
            />
          </label>
          <label>
            Last name:
            <input
              name="lastName"
              value={person.lastName}
              onChange={handleChange}
            />
          </label>
          <label>
            Email:
            <input
              name="email"
              value={person.email}
              onChange={handleChange}
            />
          </label>
          <p>
            {person.firstName + " "
              + person.lastName + " "
              + person.email
            }
          </p>
        </>
      )
    }

    export default App;
    ```

    1. オブジェクト内で`[]`を使うことで、フィールド名を動的に指定できる。今回はname属性の値を利用した
    2. name属性でオブジェクトのフィールド名を指定しておく

## 1レベルの範囲でコピーできないオブジェクト

!!! warning
    - スプレッド構文`...`はswallowコピー
    - つまり、1レベルの範囲でコピーを行う
    - 以下のようなケースで、オブジェクトを正確にコピーしたいときは、複数回スプレッド構文を使う必要がある

    ```js
    let obj = {
      title: "Cultual heritage",
      castle : {
        prefecture: "Nagano",
        name: "Matsumoto"
      }
    }
    ```

=== "例"

    ```js
    import { useState } from "react";

    const App = () => {
      const [object, setObject] = useState({
        title: "Cultual heritage",
        castle: {
          prefecture: "Nagano",
          name: "Matsumoto"
        }
      })

      const handleNameChange = (e) => {
        setObject({
          ...object, //(1)
          castle: {
            ...object.castle, //(2)
            name: e.target.value //(3)
          }
        })
      }

      return (
        <>
          <p>Title: {object.title}</p>
          <p>Prefecture: {object.castle.prefecture}</p>
          <label>
            Name:
            <input
              value={object.castle.name}
              onChange={handleNameChange}
            />
          </label>
          <p>Name: {object.castle.name}</p>
        </>
      )
    }

    export default App;
    ```

    1. このスプレッド構文`...`では`castle`の中身はコピーできない
    2. `castle`の中身をスプレッド構文でコピー
    3. 置き換えたいフィールドについて記載

### ネストされたオブジェクトは存在しない
!!!info
    - 簡単のため、ネスト（入れ子）という言葉がしばしば用いられるが、実際のところ、以下の場合、`obj1`は`obj2`の内部にあるのではない

    ```js
    let obj2 = {
      title: "Cultual heritage",
      obj1 : {
        prefecture: "Nagano",
        name: "Matsumoto"
      }
    }
    ```

    - メモリを考えるとわかるが、`obj1`は`obj2`の中で参照されているだけにすぎない

    - そのため以下の様にも書き換えられる

    ```js
    let obj1 = {
      prefecture: "Nagano",
      name: "Matsumoto"
    }

    let obj2 = {
      title: "Cultual heritage",
      castle: obj1
    }

    let obj3 = {
      title: "My Favorite Space",
      castle: obj1 //(1)
    }
    ```

    1. この時、`obj3.castle.name`を置き換えると、`obj2.castle.name`と`obj1.name`に影響を与える。これは、これら3つが同一のオブジェクトであるからである


## ネストされている場合、スプレッド構文は面倒

### 解決策1
- フラットにする。
- しかし、stateの構造が変わるというデメリットがある

### 解決策2
- __Immer__ を使う。
- これは、`position.x = 12`のように、一見ミューテートするような記法をしても、コピーを自動的に作成してくれる優れものである
- 実際のところ、裏でコピーを作成しているので、`state`が書き換えられることはない

### Immerを試す

=== "導入手順"

    1. `npm install use-immer`でImmerを依存ライブラリとして追加
    2. `import {useState} from "react`を`import { useImmer } from "use-immer";`に書き換える

=== "例"

    ```js
    import { useImmer } from "use-immer";

    const App = () => {
      const [object, updateObject] = useImmer({
        title: "Cultual heritage",
        castle: {
          prefecture: "Nagano",
          name: "Matsumoto"
        }
      })

      const handleNameChange = (e) => {
        updateObject(draft => { //(1)
          draft.castle.name = e.target.value;
        })
      }

      return (
        <>
          <p>Title: {object.title}</p>
          <p>Prefecture: {object.castle.prefecture}</p>
          <label>
            Name:
            <input
              value={object.castle.name}
              onChange={handleNameChange}
            />
          </label>
          <p>Name: {object.castle.name}</p>
        </>
      )
    }

    export default App;
    ```

    1. !!!info
        - Immerから渡されるdraftは`Proxy`というタイプのオブジェクト。
        - これはそれに対して何を行ったのかを記録する。
        - つまり、`Proxy`であるdraftは、
        ```js
        draft.castle.name = e.target.value;
        ```
        - という操作が行われたことを把握し、この __編集内容を反映した新しいオブジェクトを生成して返す__
        - そのため、実際にはイミュータブルな更新が為されている

## immutableが大事なわけ

### デバッグのしやすさ
- stateはアプリの状態を示す。しかし、あちこちでstateを参照し、書き換えた場合は現状が分からなくなる
- デバッグを行っても、書き換えはエラーではないので、問題個所が特定できない。したがってこのようなことはするべきではない。

### 素早く再レンダー
- immutableの場合、変更前と変更後のオブジェクトがそれぞれ残されているので(ミュータブルの場合、元本が変わるので残らない)、変更履歴を辿らずとも、`変更前 === 変更後`とするだけで、trueかfalseか比較できる。
- そのため、再レンダーが必要か確かめるコストが低い

### UNDO
- immutableな変更の場合、以前の`state`がメモリ上に残っているので、これを再利用することで、簡単にUNDO機能を追加可能

### 新機能
- Reactはimmutableであることを前提に開発が進められているので、mutableで書くと将来困る

### 簡潔性
- mutableは複雑になる