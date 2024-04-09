# 13-setState(array)の注意点

## オブジェクト同様
- state内にある配列は、「置き換え」る

| -        | 使わない             | 使える             |
| -------- | -------------------- | ------------------ |
| 追加     | push, unshift        | concat, [...arr]   |
| 削除     | pop, shift, splice   | filter, slice      |
| 要素置換 | splice, arr[i] = ... | map                |
| ソート   | reverse, sort        | 配列コピー後に操作 |

## 追加

### 概要
1. スプレッド構文で配列をコピーする
2. 要素を加える
3. 新しい配列をsetする

### コード例
=== "例"

    ```js
    import { useState } from "react";

    const App = () => {
      const [name, setName] = useState("");
      const [items, setItems] = useState([]);
      const [nextId, setNextId] = useState(0);
      return (
        <>
          <h1>Lists</h1>
          <input
            value={name}
            onChange={e => setName(e.target.value)}
          />
          <button onClick={() => {
            setItems([
              ...items,
              { id: nextId, name: name }
            ]); //(1)
            setNextId(nextId + 1);
          }}>Add</button>
          <ul>
            {items.map(item => (
              <li key={item.id}>{item.name}</li>
            ))}
          </ul>
        </>
      )
    }

    export default App;
    ```

    1. コード例は、末尾追加(push)。
    unshiftを行いたい場合は、以下の様に書ける
    ```js
    setItems([
      {id: nextId, name: name},
      ...items //同じ要素ではないので上書きはされない
    ])
    ```

## 削除

### 概要
0. `filter`は元配列を変更しない
1. `filter`を用い、要らない要素を取り除いた新しい配列を作る
2. 新しい配列をsetする

### コード例
=== "例"

    ```js
    import { useState } from "react";

    let initialItems = [
      { id: 0, name: "Hoge" },
      { id: 1, name: "Fuga" },
      { id: 2, name: "HogeFuga" },
    ];
    const App = () => {
      const [items, setItems] = useState(
        initialItems
      );

      return (
        <>
          <h1>Lists</h1>
          <ul>
            {items.map(item => (
              <li key={item.id}>{item.name + " "}
                <button onClick={() => {
                  setItems(
                    items.filter(i =>
                      i.id !== item.id //(1)
                    )
                  )
                }}>Delete</button>
              </li>
            ))}
          </ul>
        </>
      )
    }

    export default App;
    ```

    1. 配列を全探索し、item.idと同じIDを持っていれば、配列に加えない(filterはtrueの要素のみを採択するので)

## 変換

### 概要
- `arr[0] = "hoge"`のような代入は元の配列を書き換える(mutable)
- そのため`map`を用いて新しい配列を作成する
- `map`の第二引数は要素のindexとなる
### コード例
=== "例"

    ```js
    import { useState } from "react";

    let initialCounters = [
      0, 0, 0
    ];

    const App = () => {
      const [counters, setCounters] = useState(
        initialCounters
      );

      const handleIncrementClick = (index) => {
        const nextCounters = counters.map((c, i) => { //(1)
          if (i === index) {
            return c + 1; //変更したい要素ならインクリメント
          } else {
            return c; //他の要素はそのまま返す
          }
        });
        setCounters(nextCounters); //返された新しい配列をセットする
      }

      return (
        <ul>
          {counters.map((counter, i) => (
            <li key={i}>
              {counter}
              <button onClick={() => {
                handleIncrementClick(i); //(2)
              }}>+1</button>
            </li>
          ))}
        </ul>
      );
    }

    export default App;
    ```

    1. countersの各要素に対して操作を行うが、元配列は変更しない。結果は`nextCounters`に出力。
    2. indexを渡すことで、変更したい要素だけincrementできるようになる

## 挿入

### 概要
1. `slice()`で配列の要素を切り取る
2. `...`と組み合わせる

### コード例

=== "例"

    ```js
    import { useState } from "react";

    const initialItems = [
      { id: 0, name: "hoge" },
      { id: 1, name: "fuga" },
      { id: 2, name: "hogefuga" },
    ];

    const App = () => {
      const [name, setName] = useState("");
      const [items, setItems] = useState(
        initialItems
      );
      const [nextId, setNextId] = useState(3);

      const handleClick = () => {
        const insertAt = 1;
        const nextItems = [
          ...items.slice(0, insertAt), //(1)
          { id: nextId, name: name }, //(2)
          ...items.slice(insertAt) //(3)
        ];
        setItems(nextItems);
        setName("");
        setNextId(nextId + 1);
      }

      return (
        <>
          <h1>Items:</h1>
          <input
            value={name}
            onChange={e => setName(e.target.value)}
          />
          <button onClick={handleClick}>
            Insert
          </button>
          <ul>
            {items.map(item => (
              <li key={item.id}>{item.name}</li>
            ))}
          </ul>
        </>
      );
    }

    export default App;
    ```

    1. `array.slice(start, end-1)`なので、0番目まで
    2. 0番目の後ろに挿入。nextIdに関してはkeyが一意になるようにしている。
    3. 残りの要素を配置。結果的に、常にindex = 1の位置に挿入される。

## ソート(or リバース)

### 概要
- `reverse()`, `sort()`は元の配列を書き換えるので使えない
- そこで、一度配列をコピーしてからコピーに対して`sort()`等を行う

### コード例

=== "例"

    ```js
    import { useState } from "react";

    const initialItems = [
      { id: 0, name: "hoge" },
      { id: 1, name: "fuga" },
      { id: 2, name: "hogefuga" },
    ];

    const App = () => {
      const [list, setList] = useState(initialItems);

      const handleReverseClick = () => {
        const nextList = [...list]; //コピー (1)
        nextList.reverse(); //コピーに対して反転操作
        setList(nextList); //コピー(新しい配列)をsetする
      }

      //nameに対してアルファベット順にsortしている
      const handleSortClick = () => {
        const nextList = [...list]; //reverseと同様 
        nextList.sort((a, b) => {
          if (a.name < b.name) return -1; 
          if (a.name > b.name) return 1;
          return 0;
        });
        setList(nextList);
      }

      return (
        <>
          <button onClick={handleReverseClick}>Reverse</button>
          <button onClick={handleSortClick}>Sort</button>
          <ul>
            {list.map(item => (
              <li key={item.id}>{item.name}</li>
            ))}
          </ul>
        </>
      )
    }

    export default App;
    ```

    1. この時、`nextList[0]`と`list[0]`は同じオブジェクトを指しているので注意。仮に`nextList[0].id = 4`とすると、`list[0].id`も4に変更される。


## 配列内のオブジェクトを変更する

### 概要
- 配列内のオブジェクトは参照されているだけ
- そのため、`list[0].hoge = 3`の様にミューテートしてはいけない
- 実際には、`map`が使える

### コード例

=== "例"

    ```js
    import { useState } from "react";

    const initialItems = [
      { id: 0, name: "hoge", seen: false },
      { id: 1, name: "fuga", seen: false },
      { id: 2, name: "hogefuga", seen: false },
    ];

    const App = () => {
      const [myList, setMyList] = useState(initialItems);
      const [yourList, setYourList] = useState(initialItems);

      const handleToggleMyList = (itemId, nextSeen) => {
        setMyList(myList.map(item => {
          if (item.id === itemId) {
            return { ...item, seen: nextSeen }; //(1)
          } else {
            return item;
          }
        }));
      }
      const handleToggleYourList = (itemId, nextSeen) => {
        setYourList(yourList.map(item => {
          if (item.id === itemId) {
            return { ...item, seen: nextSeen };
          } else {
            return item;
          }
        }));
      }



      return (
        <>
          <h1>Lists</h1>
          <h2>My</h2>
          <ItemList
            items={myList}
            onToggle={handleToggleMyList}
          />
          <h2>Your</h2>
          <ItemList
            items={yourList}
            onToggle={handleToggleYourList}
          />
        </>
      )
    }

    const ItemList = ({ items, onToggle }) => {
      return (
        <ul>
          {items.map(item => (
            <li key={item.id}>
              <label>
                <input
                  type="checkbox"
                  checked={item.seen}
                  onChange={e => {
                    onToggle( //(2)
                      item.id,
                      e.target.checked　
                    );
                  }}
                />
                {item.name}
              </label>
            </li>
          ))}
        </ul>
      )
    }

    export default App;
    ```

    1. 新しいオブジェクトを作成して返しているので、yourListに悪影響を与えずに済む

    2. !!!info
        1. ユーザがクリックしたタイミングで、e.target.checkedの状態は変わっている。つまり`setMyList`等にはクリック後の値が渡される
        2. しかし、この状態ではまだクリックの見た目は変わっていない
        3. 実際にUIが変更されるのは、再レンダー時
        
        ※生のJSの場合は、`e.target.checked`と表示の変更は同期的に行われる

## Immerを使って楽をする
!!! warning
    - stateが深くネストされている時点で、再構成を考える必要があるかもしれない

!!! info
    Immerから渡される特別なdraftオブジェクト(proxy)を書き換えると、裏でコピーが為されるのでimmutableな変更が可能

!!! info
    - 一般的に関数が値を返すと予期される場合、`return`文が必要
    - しかし、Reactコンポーネント内のイベントハンドラや副作用を伴う関数では、明示的な`return`がなくとも(undefinedが返されても)問題にならない。これはreturnではなく、`setState`で状態を更新するから
    - これは`useImmer`も同様で、以下のように記述しても状態は正しく更新される
    ```js
    const handleChangeTodo = (nextTodo) => {
      updateTodos(draft =>{
        const todo = draft.find(t =>
          t.id === nextTodo.id
        )
        todo.title = nextTodo.title;
        todo.done = nextTodo.done;
      })
    }
    ```
=== "解法1"

    ```js
    import { useImmer } from "use-immer";

    const initialList = [
      { id: 0, name: "hoge", seen: false },
      { id: 1, name: "fuga", seen: false },
      { id: 2, name: "hogefuga", seen: true },
    ]

    const App = () => {
      const [myList, updateMyList] = useImmer(
        initialList
      );
      const [yourList, updateYourList] = useImmer(
        initialList
      );

      const handleToggleMyList = (id, nextSeen) => {
        updateMyList(draft => {
          const index = draft.findIndex(i => i.id === id); //該当indexを見つけている
          if (index !== -1) {
            draft[index] = { ...draft[index], seen: nextSeen }
          }
        });
      }
      const handleToggleYourList = (id, nextSeen) => {
        updateYourList(draft => {
          const index = draft.findIndex(i => i.id === id);
          if (index !== -1) {
            draft[index] = { ...draft[index], seen: nextSeen };
          }
        });
      }

      return (
        <>
          <h1>List</h1>
          <h2>My</h2>
          <ItemList
            items={myList}
            onToggle={handleToggleMyList}
          />
          <h2>Your</h2>
          <ItemList
            items={yourList}
            onToggle={handleToggleYourList}
          />
        </>
      );
    }

    const ItemList = ({ items, onToggle }) => {
      return (
        <ul>
          {items.map(item => (
            <li key={item.id}>
              <label>
                <input
                  type="checkbox"
                  checked={item.seen}
                  onChange={e => {
                    onToggle(
                      item.id,
                      e.target.checked
                    );
                  }}
                />
                {item.name}
              </label>
            </li>
          ))}
        </ul>
      );
    }

    export default App;
    ```

=== "解法2"

    ```js
    import { useImmer } from "use-immer";

    const initialList = [
      { id: 0, name: "hoge", seen: false },
      { id: 1, name: "fuga", seen: false },
      { id: 2, name: "hogefuga", seen: true },
    ]

    const App = () => {
      const [myList, updateMyList] = useImmer(
        initialList
      );
      const [yourList, updateYourList] = useImmer(
        initialList
      );

      const handleToggleMyList = (id, nextSeen) => {
        updateMyList(draft => {
          const item = draft.find(i => //(1)
            i.id === id
          );
          item.seen = nextSeen; //直接変更している
        });
      }
      const handleToggleYourList = (id, nextSeen) => {
        updateYourList(draft => {
          const item = draft.find(i =>
            i.id === id
          );
          item.seen = nextSeen;
        });
      }

      return (
        <>
          <h1>List</h1>
          <h2>My</h2>
          <ItemList
            items={myList}
            onToggle={handleToggleMyList}
          />
          <h2>Your</h2>
          <ItemList
            items={yourList}
            onToggle={handleToggleYourList}
          />
        </>
      );
    }

    const ItemList = ({ items, onToggle }) => {
      return (
        <ul>
          {items.map(item => (
            <li key={item.id}>
              <label>
                <input
                  type="checkbox"
                  checked={item.seen}
                  onChange={e => {
                    onToggle(
                      item.id,
                      e.target.checked
                    );
                  }}
                />
                {item.name}
              </label>
            </li>
          ))}
        </ul>
      );
    }

    export default App;
    ```

    1. 見つからない場合はundefinedとなるのでエラーハンドリングが要る