# useState

### 概要
- stateを管理するためのReactフック
- stateとはアプリケーションが保持している状態を指す
- stateはpropsと違い、後から変更可能

=== "サンプル例"

    ```js
    import {useState} from "react"; 

    export default function Test(){
        const [status, setStatus] = useState("hoge"); //(1)

        const statusChange = () => {
            setStatus("fuga") //(2)
        }

        return(
            <>
                <span onClick={statusChange}>{status}</span>
            </>
        )
    }
    ```

    1.  useState("hoge")とした場合、初期値にhogeが入る
        デストラクチャリング(分割代入)で、1つ目に、現在の値。2つ目に現在の値を変更するための関数が入る。
    
    2.  setStatus("fuga"): 現在の値を引数に変更する　

        次のレンダリングのためのstate変数を更新する
        stateが変化するとコンポーネントは再レンダリングする

=== "実用例"

    ```js
    import React, { useState } from "react";

    function App() {
      /*1~10のrandomな値をinitialStateに設定, setInitialStateはinitialStateを変更するための関数 */
      const [initialState, setInitialState] = useState(Math.floor(Math.random() * 10) + 1);
      const [count, setCount] = useState(initialState);
      const [open, setOpen] = useState(true);
      const toggle = () => setOpen(!open);
      return (
        <>
          <button onClick={toggle}>{open ? "close" : "open"}</button>
          {open &&
            <div className={open ? "isOpen" : "isClose"}>
              <p>現在の数字は{count}です</p>
              {/*setCount()は、更新するための値を引数として受け取ることもできる */}
              <button onClick={() => setCount(prevState => prevState + 1)}>
                + 1
              </button>
              <button onClick={() => setCount(count - 1)}>- 1</button>
              <button onClick={() => setCount(0)}>0</button>
              <button onClick={() => setCount(initialState)}>最初の数値に戻す</button>
            </div>}
        </>
      )
    }

    export default App;
    ```