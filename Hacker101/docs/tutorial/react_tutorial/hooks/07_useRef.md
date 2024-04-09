# useRef

### 概要
- 要素への参照を行う
- `const hoge = useRef(initialvalue)`のようにして作成
- Refオブジェクト`({current: initialValue})`を生成し、その値をメモ化している
- 値の変更時は`hoge.current = 0`のようにする。値を直接変更しているだけなので、再レンダリングされないことが特徴

### 使いどころ
- コンポーネントの再レンダリングはしたくないが、内部に保持している値だけを更新したい場合
- useStateはstateが変更される度に、コンポーネントの再レンダリングが発生するが、useRefにはそれがない

=== "DOMを参照する"

    ```js
    import React, { useRef, useState } from "react";

    const App = () => {
      const inputElement = useRef(null);
      const [text, setText] = useState("");

      const handleClick = () => {
        setText(inputElement.current.value);
      };

      console.log("レンダリング")
      // (1)
      return (
        <>
          <input ref={inputElement} type="text" />{/*(2)*/}
          <button onClick={handleClick}>setText</button>
          <p>テキスト: {text}</p>
        </>
      )

    }
    export default App;
    ```

    1. たとえば以下が
    ```javascript
    <input 
      value={inputElement}
      onChange={(e) => setInputElement(e.target.value)}
      type = "text"
    >
    ```
    であり、上部で
    ```javascript
    const [inputElement, setInputElement] = useState("");
    ```
    としている場合、入力欄の表示が変わるたびに再レンダリングが行われる。(stateの中身が変わるため)今回の場合は、表示はinputタグが行い、再レンダリングはボタンを押したときだけで十分なので(内部で値を保持しておくだけでよい)useRefを使う
    
    2. (useRefとDOMの紐づけ)
    refプロパティにuseRefで生成したオブジェクトを渡すことで、`inputElement.current`はDOMノードを参照することになる。そのため、`inputElement.current.focus()`等でinputタグに対するフォーカスを設定するような真似ができる

### 注意事項

- レンダリング中に.currentを用いて値の読み書きを行ってはならない。
- useRefで参照しているDOM等に干渉したいときは、イベントハンドラまたはuseEffect(レンダリング後の動作なのでOK)内から行うようにする
- ※イベントハンドラはComponent内に実装されているが、レンダリング中には実行されないため純粋関数である必要はない。副作用(画面の更新、アニメーションの変更、データの変更等の変更)は、レンダリング時以外に発生させること