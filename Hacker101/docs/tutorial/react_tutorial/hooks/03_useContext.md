# useContext

### 概要
- データをグローバルに利用するためのもの
- これを利用することで、データをpropsとして(コンポーネント間で)やり取りする必要がなくなる

=== "サンプル例"

    ```javascript title="components/ContextSample/ContextA.js"
    import React from "react";
    import ContextB from "./ContextB"

    const ContextA = () => <ContextB/> //(1)
    ```

    1. 返り値としてContextBを返す

    ```javascript title="components/ContextSample/ContextB.js"
    import React from "react";
    import ContextC from "./ContextC";

    const ContextB = () => <ContextC/>
    export default ContextB;
    ```

    ```javascript title="components/ContextSample/ContextC.js"
    import React, {useContext} from "react";
    import {UserContext, HobbyContext} from "../../App";

    const ContextC = () =>{
      const user = useContext(UserContext); //(1)
      const hobby = useContext(HobbyContext);
      return(
        <p>{user.name}{user.age}歳:趣味は{hobby}です。</p>
      )
    }
    export default ContextC;
    ```

    1. useContextを用いて、UserContextのvalueに設定した値を使っている(複数UserContext.Providerがある場合は、自分から見て一番近い親の値が優先される)

    ```javascript title="App.js"
    import React, {createContext, useState} from "react";
    import ContextA  from "./components/ContextSample/ContextA"

    export const UserContext = createContext(); //(1)
    export const HobbyContext = createContext();

    function App(){
      const [user, setUser] = useState({
        name: "Arice",
        age: 10
      })

      const [hobby, setHobby] = useState("Fishing");
      return(
        <div className="App">
          <UserContext.Provider value={user}>{/*(2)*/}
            <HobbyContext.Provider value={hobby}>
              <ContextA/>
            </HobbyContext.Provider>
          </UserContext.Provider>
        </div>
      )
    }

    export default App
    ```

    1. Contextオブジェクトを作成している。
    2. Providerの下位コンポーネントは、Providerのvalueプロパティが変更されるたびに再レンダリングされる。