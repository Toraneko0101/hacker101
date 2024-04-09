# useReducer

### useStateとの違い
- 状態遷移を行う際、`useState`では __次の状態__ を`setState()`に引数として渡すことで、値を更新している。(つまり、操作ではなく状態を直接渡している)
- `useReducer`では、状態ではなく __操作__ (アクション)を渡すことになる
- アクションの中で次の値を設定することで、操作と実装を分離可能
- 管理しておきたい状態が複数あるとき、`useReducer`を用いて操作を切り出すことで、読み込み速度を短縮できる可能性がある(詳細は`useMemo`参照)

=== "useStateの例"

    ```javascript 

    import {useState} from "react";

    export default function App(){
      const [state, setState] = useState(1);

      const countUp = (e) =>{
        e.preventDefault();
        setState(state + 1); // (1)
      };

      const countDown = (e) =>{
        e.preventDefault();
        setState(state - 1);
      }

      const countZero = (e) =>{
        e.preventDefault();
        setState(0);
      }

      return(
        <div>
          <p>{state}</p>
          <button onClick={countUp}>+1</button>
          <button onClick={countdown}>-1</button>
          <button onClick={countZero}>0</button>
        </div>
      );
    }
    ```

    1. 状態を、現在のもの+1に直接更新

=== "useReducerの例"

    ```javascript

    import {useReducer} from "react";

    const reducerFn = (state, action) =>{ // (1)
      switch(action.type){
        case "count up":
          return state + 1;
        case "count down":
          return state - 1;
        case "count zero":
          return 0;
      }
    }

    export default function App(){
      const [state, dispatch] = useReducer(reducerFn, 0);

      const countUp = (e) =>{
        e.preventDefault();
        dispatch({type: "count up"}); // (2)
      };

      const countDown = (e) =>{
        e.preventDefault();
        dispatch({type: "count down"});
      }

      const countZero = (e) =>{
        e.preventDefault();
        dispatch({type: "count zero"});
      }

      return(
        <div>
          <p>{state}</p>
          <button onClick={countUp}>+1</button>
          <button onClick={countDown}>-1</button>
          <button onClick={countZero}>0</button>
        </div>
      );
    }
    ```

    1. reducer関数はコンポーネント内の関数と関与しないので、コンポーネント内に書く必要はない

    2. dispatch関数は現在の状態(state)と引数に設定したactionを、reducer関数に送る。値を渡したい場合は、`#!javascript dispatch({type: "count up", hoge: e.target.value})`等と記載し、`action.hoge`等として使用する

=== "初期値object(useState)"

    ```js
    import React, {useState} from "react";
    const App = () =>{
      const [state, setState] = useState({
        fizz: 0,
        buzz: 0,
      })

      const setFizz = () => {
        setState(prevState => ({...prevState, fizz:state.fizz+1})) //(1)
      }

      const setBuzz = () =>{
        setState(prevState => ({...prevState, buzz:state.buzz+1})) // (2)
      }

      const setReset = () =>{
        setState({fizz:0, buzz:0})
      }

      return(
        <>
          <p>Fizz:{state.fizz}</p>
          <p>Buzz:{state.buzz}</p>
          <button onClick={setFizz}>Add Fizz</button>
          <button onClick={setBuzz}>Add Buzz</button>
          <button onClick={setReset}>Reset</button>
        </>
      );
    }
    ```

    1. 次の状態を渡す必要があるので、`...prevState`を記載しないと、buzzに関する状態が未定義になりエラーになる。つまり、useStateの初期値としてオブジェクトを渡すときは、状態遷移する際にスプレッド演算子を使い、更新後のすべてのフィールドの状態について明記する必要がある。(useReducerでもこれは同じ)なお、同じフィールドがあると後者の値で上書きされるので、これを利用して分割更新を行うことが多い

    2. Reactの状態更新は非同期であるため、引数としてオブジェクトを渡すと、すぐには反映されない可能性がある。（状態更新がバッチとして処理されるため）そのため左記のように関数で渡すようにする。関数を渡した場合は、前回の状態を引数にとり、新しい状態を返すため非同期の問題を避けることができる。引数として関数を渡すことは、「状態の更新は宣言的に行うべきである」というReactの設計方針と一致している。

=== "初期値object(useReducer)"

    ```js
    import React, { useReducer } from "react";
    
    const reducerFn = (state, action) => { //(1)
      switch (action.type) {
        case "fizz count up":
          return {
            ...state,
            fizz: state.fizz + 1,
          }
        case "buzz count up":
          return {
            ...state,
            buzz: state.buzz + 1,
          }
        case "reset":
          return {
            ...state,
            fizz: 0,
            buzz: 0,
          }
      }
    }

    function App() {
      const [state, dispatch] = useReducer(reducerFn, {
        fizz: 0,
        buzz: 0,
      });

      const fizzCountUp = () => {
        dispatch({ type: "fizz count up" }); //(2)
      }

      const buzzCountUp = () => {
        dispatch({ type: "buzz count up" });
      }

      const reset = () => {
        dispatch({ type: "reset" });
      }

      return (
        <>
          <p>Fizz:{state.fizz}</p>
          <p>Buzz:{state.buzz}</p>
          <button onClick={fizzCountUp}>Add Fizz</button>
          <button onClick={buzzCountUp}>Add Buzz</button>
          <button onClick={reset}>Reset</button>
        </>
      );
    }

    export default App;
    ```

    1. 状態の更新ロジックをコンポーネントから分離できるので、コンポーネント内のコードが読みやすくなる利点がある
    2. コンポーネント内ではアクションを選択するだけでいい