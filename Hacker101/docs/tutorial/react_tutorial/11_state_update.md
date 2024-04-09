# 11-stateを1回のイベントで複数回更新

## 通常の場合
- イベントハンドラ内で何度`setState()`を行ったとしても、最後の`setState()`のみが反映される
- つまり、Reactはイベントハンドラ内のすべてのコードが実行されるまでstateの更新処理を待機し、同じstate変数に対する命令が複数あれば、一番最後の命令だけを見て、再レンダーを要求する

=== "例"

    ```javascript
    const updateFunc = () =>{
        setIndex(index + 1); //反映されない
        setIndex(index + 1); //反映される
        setIsGrounded(false); //反映されない
        setIsGrounded(true); //反映される

        //全てのコードが実行されたら一度に反映
    }
    ```
- これにより、複数のstate変数があるとしても、再レンダー要求は1度だけで済み、変数のうち一部のみが更新された、中途半端なレンダーが行われることもなくなる
- これは __バッチ処理__ と呼ばれ、Reactの高速性を支えている
- しかし、次のレンダー前に、同じstate変数を複数回更新したいケースがある
- 以下ではその方法について述べる


## 次のレンダー前に、同じstate変数を複数回更新

=== "例"

    ```javascript
    import {useState} from "react";
    const Counter = () =>{
      const [number, setNumber] = useState(0);

      return(
        <>
          <h1>{number}</h1>
          <button onClick={()=>{
            setNumber(n => n+1); //(1)
            setNumber(n => n+1);
            setNumber(n => n+1);
          }}>+3</button>
        </>
      )
    }

    export default Counter;
    ```

    1. 更新用関数と呼ばれる。関数自体をqueueに入れ、レンダー時に反映している（詳細は隣タブ）

=== "更新用関数の動作"

    ### 概要
    1. 更新用関数を`setState`に渡す
    2. Reactはこの関数自体`n => n+1`自体をqueueに入れ、他のstateと同様、イベントハンドラ内の他のコードがすべて実行されるまで待つ
    3. 次のレンダー中にReactはqueueを1つずつ処理する

    | queue内の更新処理 | n   | 返り値 |
    | ----------------- | --- | ------ |
    | n=>n+1            | 0   | 0+1=1  |
    | n=>n+1            | 1   | 1+1=2  |
    | n=>n+1            | 2   | 1+1=3  |

=== "置き換えた後に更新"

    ### コード

    ```javascript
    import {useState} from "react";
    const Counter = () =>{
      const [number, setNumber] = useState(0);

      return(
        <>
          <h1>{number}</h1>
          <button onClick={()=>{
            setNumber(number + 5); //(1)
            setNumber(n => n+1); //(2)
          }}>+6</button> 
        </>
        {/*(3)*/}
      )
    }

    export default Counter;
    ```

    1. `number`は0なので、`setNumber(0+5)`。Reactはqueueに`5`に置き換えよという命令を追加する
    2. `n => n+1`という関数をqueueに追加する
    3. !!!info
        - `setState(5) == setState(n => 5)`
        - queue内の動作については「結果」に記載

    ### 結果
    | queue内の更新処理 | n   | 返り値 |
    | ----------------- | --- | ------ |
    | 5に置き換えよ     | 0   | 5      |
    | n=>n+1            | 5   | 5+1=6  |

=== "stateを更新した後に置き換える"

    ```javascript
    import {useState} from "react";
    const Counter = () =>{
      const [number, setNumber] = useState(0);

      return(
        <>
          <h1>{number}</h1>
          <button onClick={()=>{
            setNumber(number + 5);
            setNumber(n => n+1); 
            setNumber(42); //(1)
          }}>42</button> 
        </>
      )
    }

    export default Counter;
    ```

    1. 結果については下に記載

    ### 結果
    | queue内の更新処理 | n   | 返り値 |
    | ----------------- | --- | ------ |
    | 5に置き換えよ     | 0   | 5      |
    | n=>n+1            | 5   | 5+1=6  |
    | 42に置き換えよ    | 6   | 42     |

## 注意点
- 更新用関数は純関数である必要がある
- 更新用関数の中でさらに`state`をセットしたりしないこと
- 誤りを見つけたい場合、`strictMode`を使う。これを使うと、各更新用関数は2回実行される（ただし2回目の結果は破棄される）

## 更新用関数の命名規則
- 上記の`setNumber(n => n+1)`のように、更新したいstate変数の頭文字を使う
- もちろん、完全なstate変数名を繰り返してもよい

