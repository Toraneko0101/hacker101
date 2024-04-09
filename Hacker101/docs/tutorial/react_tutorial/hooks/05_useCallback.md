# useCallback + React.memo

### 概要(useCallback)
- パフォーマンス向上のためのフック。メモ化したコールバック関数を返す。`useEffect`と同様、依存配列の要素のうちいずれかが変化した場合に、 メモ化[^1] した値を再計算する
- useCallbackは関数自体をメモ化する

### 概要(React.memo)
- コンポーネントが返したReact要素を記録し、再レンダリングされそうになった時に等価性の判断を行い、必要な場合のみ（等しくない場合は）再レンダリングする
- React.memoはメモ化したいコンポーネントをラップするように使用する
- React.memoの第2引数にはオプションとして関数を渡すことができ、
```javascript
  const MyMemoComponent = React.memo(OriginComponent, (prevProps, nextProps) => {/* true or false*/})`
```
という風に記載できる。この関数は前回のpropsと今回のpropsを受け取り、等価性を判断し、真偽値を返す。

=== "メモ化しない場合"

    ```js
    import React, { useState } from "react";

    //Titleコンポーネント
    const Title = () => {
      console.log("Title component")
      return (
        <h2>useCallBackTest vol.1</h2>
      )
    }

    //Buttonコンポーネント
    const Button = ({ handleClick, value }) => {
      console.log("Button child component", value)
      return <button type="button" onClick={handleClick}>{value}</button>
    }

    //Countコンポーネント
    const Count = ({ text, countState }) => {
      console.log("Count child component", text)
      return <p>{text}:{countState}</p>
    }

    //Appコンポーネント (1)
    const App = () => {
      const [firstCountState, setFirstCountState] = useState(0);
      const [secondCountState, setSecondCountState] = useState(10);

      const incrementFirstCounter = () => setFirstCountState(firstCountState + 1)
      const incrementSecondCounter = () => setSecondCountState(secondCountState + 10)

      return (
        <>
          <Title />
          <Count text="+1ボタン" countState={firstCountState} />
          <Count text="+10ボタン" countState={secondCountState} />
          <Button handleClick={incrementFirstCounter} value={"+1ボタン"} />{/*(2)*/}
          <Button handleClick={incrementSecondCounter} value={"+10ボタン"} />{/*(3)*/}
        </>
      );
    }

    export default App;
    ```

    1. ボタンを押すとすべてのcomponentがレンダリングされる。実際の出力は次の通り

        ```console
        # 何かしらのボタンを押した場合
        Count child component +1ボタン
        Count child component +10ボタン
        Title child component +1ボタン
        Title child component +10ボタン
        ```

    2. 関数への参照がプロパティとして渡されると、その関数が新しいインスタンスを生成するたびに親コンポーネントが再レンダリングされ、結果としてすべての子コンポーネントが再レンダリングされる。これを防ぐためには、`useCallback`を用いて関数のメモ化を行い、依存配列に指定する値が同一の場合、新しいインスタンスを生成しないように変更する必要がある

    3. 特定の数を渡したい場合`setIncrementSecondCounter(10)`としてはいけない。アロー関数で渡すようにする。理由としてはreactコンポーネントが、setStateされたタイミングでrenderされる事があげられる。上記の場合、renderされたタイミングでまた値をsetしていることになるため無限ループに陥ってしまう。

=== "メモ化した場合"

    ```js
    import React, { useState, useCallback } from "react";

    //Titleコンポーネント
    const Title = React.memo(() => { // (1)
      console.log("Title component")
      return (
        <h2>useCallBackTest vol.1</h2>
      )
    })

    //Buttonコンポーネント (2)
    const Button = React.memo(({ handleClick, value }) => {
      console.log("Button child component", value)
      return <button type="button" onClick={handleClick}>{value}</button>
    })

    //Countコンポーネント
    const Count = React.memo(({ text, countState }) => {
      console.log("Count child component", text)
      return <p>{text}:{countState}</p>
    })

    //Appコンポーネント　(3)
    const App = () => {
      const [firstCountState, setFirstCountState] = useState(0);
      const [secondCountState, setSecondCountState] = useState(10);
      
      const incrementFirstCounter = useCallback(// (4)
        () => setFirstCountState(firstCountState + 1), [firstCountState]
      )
      const incrementSecondCounter = useCallback(
        () => setSecondCountState(secondCountState + 10), [secondCountState]
      )

      return (
        <>
          <Title />
          <Count text="+1ボタン" countState={firstCountState} />
          <Count text="+10ボタン" countState={secondCountState} />
          <Button handleClick={incrementFirstCounter} value={"+1ボタン"} />
          <Button handleClick={incrementSecondCounter} value={"+10ボタン"} />
        </>
      );
    }

    export default App;
    ```

    1. React.memoでラップしている
    2. useCallbackを使わず、単にReact.memoでラップしただけである場合、propsに関数が含まれていると、これは通常毎回異なるインスタンスとして生成されるので、React.memoは等価でないとみなして再レンダリングする。メモ化すれば依存配列の値が変わらない限り、同一の関数インスタンスが再利用されるので、React.memoはreturn内の要素が変更されたと認識しなくなる
    3. ボタンを押すとすべてのcomponentがレンダリングされる。実際の出力は次の通り
   
        ```powershell
        #+1ボタンを押した場合
        Count child component +1ボタン
        Title child component +1ボタン

        #+10ボタンを押した場合
        Count child component +10ボタン
        Title child component +10ボタン
        ```
        
    4. useCallbackフックで関数のメモ化を行い、依存配列の値が同一である場合関数インスタンスの生成を防ぐ

[^1]: 同じ結果を返す処理について、初回時のみ処理を実行・記録しておき、2回目以降は前回の処理結果をもとに、計算無しで呼び出し値を得られるようにすること。