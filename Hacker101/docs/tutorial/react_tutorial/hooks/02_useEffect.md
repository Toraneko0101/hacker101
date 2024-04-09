# useEffect

### 概要
- 関数が実行されるタイミングをReactのレンダリング後まで遅らせるフック
(useEffectに渡された関数は、レンダリングの結果が画面に反映された後で動作する)

### 使用ケース
- DOMの書き換え、変数の代入、API通信などのUI構築以外の処理
- stateを変更し、レンダリングが行われた後で、useEffectが発火し関数処理が行われる等
- 非同期処理に適している。詳細については工事中

=== "サンプル例"

    ```javascript
    function App(){
      useEffect(()=>{
      // (1)
      }, []) //(2)
    }

    export default App;
    ```

    1. ここ(第1引数)に、実行させたい副作用関数を記述
    2. 第2引数に、副作用関数の実行タイミングを制御する依存データを記述

    | useEffect | 説明                                                                                                                                                  | データ型 |
    | --------- | ----------------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
    | 第1引数   | 副作用関数（戻り値は　クリーンアップ関数[^1] or void）                                                                                                | 関数     |
    | 第2引数   | 副作用関数の実行タイミングを制御する依存データが入る。初回レンダリング時のみ実行させたい場合は空配列[ ]を渡す。省略するとレンダリングの度に実行される | 配列     |

=== "実用例"

    ```js

    import React, { useState, useEffect } from "react";
    import ButtonGroup from "@mui/material/ButtonGroup"; //(1)
    import Button from "@mui/material/Button";
    import Input from "@mui/material/Input";

    const App = () => {
      const [count, setCount] = useState(0)
      const [name, setName] = useState({
        lastName: '',
        firstName: ''
      })
      useEffect(() => {
        document.title = `${count}回クリックされました` // (2)
      }, [count])

      return (
        <>
          <p>{`${count}回クリックされました`}</p>
          <ButtonGroup color="primary" aria-label="outlined primary button group">
            <Button onClick={() => setCount((prev) => prev + 1)}>
              ボタン
            </Button>
            <Button onClick={() => setCount(0)}>
              リセット
            </Button>
          </ButtonGroup>
          <p>{`私の名前は${name.lastName} ${name.firstName}です`}</p>
          <form className="hoge" noValidate autoComplete="off">
            <Input
              placeholder="姓"
              value={name.lastName}
              onChange={(e) => { setName({ ...name, lastName: e.target.value }) }} /> // (3)
            <Input
              placeholder="名"
              value={name.firstName}
              onChange={(e) => { setName({ ...name, firstName: e.target.value }) }} />
          </form>
        </>
      )
    }

    export default App
    ```

    1. UIのためのライブラリであり、内容とは関係がない
    2. Buttonをクリックすることで、setCountでcountの値が変化し、コンポーネントが再レンダリング
        その後に、第1引数内の処理が実行される
    3. nameはuseEffectの依存配列に含まれていないので、nameが変化してもuseEffectは発火しない

[^1]: クリーンアップ関数は、イベントリスナの削除や、タイマーのキャンセルなどを行う。クリーンアップ関数を設定することで、`useEffect()`内の処理は、毎回のレンダリング時に実行され、新たに副作用関数が実行される前に以前の状態に戻される。(実際には戻すような処理をクリーンアップ関数に記載する)このようなマウント処理とアンマウント処理の繰り返しをライフサイクルという。

### 補足
- eslint-plugin-react-hooksパッケージのexhaustive-depsルールを有効化することで、依存の配列が正しく記述されていない場合に警告を出すことができる
- 警告が出た場合、offにするのではなく、依存関係が誤っていないか（レンダリング前の古い値が参照されていないか）確認した方がよい