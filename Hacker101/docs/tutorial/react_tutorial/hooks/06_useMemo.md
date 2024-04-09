# useMemo

### 概要
- 関数の結果をメモ化する

### useMemo, useCallback, React.memoの差異
- React.memo: コンポーネントをメモ化（第一引数はコンポーネント）
- useCallback: 関数自体をメモ化(第一引数は関数)。レンダリングの度に関数のインスタンスが生成されることを避けるために使う
- useMemo: 関数の結果をメモ化（第一引数は値を生成する関数）。レンダリング時に計算を行う必要があり、その結果が同一である時に使う

=== "サンプル例"

    ```js
    import React, { useMemo, useState } from "react"
    //(1)
    const App = () => {
      const [count01, setCount01] = useState(0);
      const [count02, setCount02] = useState(0);

      const result01 = () => setCount01(count01 + 1);
      const result02 = () => setCount02(count02 + 1);

      const square = useMemo(() => { //(2)
        let i = 0;
        while (i < 1e9) i++
        console.log(i);
        return count02 * count02
      }, [count02])

      return (
        <>
          <div>result01: {count01}</div>
          <div>result02: {count02}</div>
          <div>square: {square}</div>
          <button onClick={result01}>increment</button>
          <button onClick={result02}>increment</button>
        </>
      )
    }

    export default App;
    ```

    1. stateの値が変わるたびにAppに対して再レンダリング処理が走るので、通常どんな動作をしてもsquare関数の処理が走り、重くなるという問題がある
    2. useMemoでラップしているため、依存配列である`count02`の値が変更されない限り、square関数の処理は実行されない。

### JSX生成とuseMemo
- ページ遷移時にContextから値をとってきて、その値を初回レンダリング時に使用する方法について考える
- useEffectはレンダリング後に処理が行われるため不適。useMemoがレンダリング前に処理を行うことを生かす

=== "ページ遷移とuseMemo"

    ```js
    import React, {useState, useContext, useEffect, useMemo} from "react";
    import {useParams} from "react-router-dom";

    const Hoge = () =>{
      const [d_item, setItem] = useState([]);
      const context = useContext(hogeContext);
      const params = useParams(); //(1)
      useMemo(()=>{ // (2)
        const item = context.find((item)=> item.id == params.id);
        setItem(item);
      },[params.id, context])

      return(
        {
          (params.id != d_item.id)?
          <div>ID:{param.id}は見つかりませんでした</div>
          :
          <table>
            <thead>
              <tr>
                <th>タイトル</th>
                <th>説明</th>
                <th>ステータス</th>
              </tr>
            </thead>
            <tbody>
              <tr>
                <td>{d_item.title}</td>
                <td>{d_item.description}</td>
                <td>{d_item.status}</td>
              </tr>
            </tbody>
          </table>
        }
      )
    }
    ```

    1. URLの動的に変更されるパラメータを取得
    2. useEffectはレンダリング後、つまりJSXが生成された後に呼び出されるため、
      ```javascript
      useEffect(()=>{
        const item = context.find((item)=> item.id == params.id)
        setItem(item)
      },[params.id, context])
      ```
    とすると、ページ遷移時のd_itemの値は空配列となり、d_item.titleはもちろん存在しないためエラーとなってしまう。反面、useMemoは、JSX生成前に処理が行われるため前述のエラーを避けることができる