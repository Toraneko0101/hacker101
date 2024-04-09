# useTransiton

### 概要
- UIをブロックせずに状態の更新が可能なReact Hook
- `#!javascript const [isPending, startTransition] = useTransition()`

### StartTransition
- 遷移を監視するための関数
- 複数のstateを更新する必要があるとき、その優先順位を設定できる

### isPending
- 遅延された更新が完了するまでの間、trueになる
- 更新完了時にfalseに戻る
- UIにローディング用のスピナーを表示する際に役立つ

### 使用した感想
- useStateのみを用いた場合、Reactは内部のリストと表示の整合性が取れるように振る舞う
- つまり、画面の更新は内部のリストの更新と同期的に行われるので、ユーザが数字を素早く入力しようとした場合、内部の処理が終わるまでの間の遅延が発生し、フリーズしたように感じられる
- ここでuseTransitionを使い、useStateのset関数をラップして状態を更新するようにする。ラップされたset関数の優先度は低くなる(今回の場合、setFilterWord)ので、このstateの更新の優先度は低くなり、内部計算が終わるより先に、inputTextの更新が反映され、ユーザがフリーズしたように感じることはなくなる

### 問題点(状態と描画の整合性が取れてなくてもいいの?)
- isPendingが解決してくれる。
- isPendingは遅延中か否かを判別するbool値を持っているので、これをもとにuserに計算途中であることを伝えることが可能。

### サンプル

=== "App.js"

    ```js
    import React, { useState, useTransition } from "react";
    import ProductList from "./ProductList";
    import { generateProducts } from "./data";

    const dummyProducts = generateProducts();

    const filterProducts = (filterWord) => {
      if (!filterWord) return dummyProducts;

      return dummyProducts.filter(
        (products) => products.includes(filterWord)
      );
    }

    const App = () => {
      const [isPending, startTransition] = useTransition();
      const [filterWord, setFilterWord] = useState("");

      const filteredProducts = filterProducts(filterWord);

      const updateFilterHandler = ({ target: { value } }) => {
        //stateの更新を遅らせ、userのinputを優先
        startTransition(() => { //(1)
          setFilterWord(value)
        })
      };

      return (
        <div id="app">
          <h1>useTransition</h1>
          <input
            type="text"
            placeholder="ここに数字を入力"
            onChange={updateFilterHandler}
          />
          <p>
            {/*stateの更新を遅らせている間はisPending = true */}
            {isPending && (
              <span style={{ color: "red" }}>
                Now Loading...
              </span>
            )}
          </p>
          <ProductList products={filteredProducts} />
        </div>
      );
    };

    export default App;
    ```

    1. useStateを用いた場合、以下の様になる
    ```diff
     const updateFilterHandler = ({target: {value}}) =>{
    -  startTransition(()=>{
         setFilterWord(value);
    -  });
     };
    ```
    実際に試したところ、確かに応答性の面で違いがあった

=== "data.js"

    ```js
    export const generateProducts = () => {
      //dummy * 10000
      const products = [];
      for (let i = 0; i < 10000; i++) {
        products.push(`Product ${i + 1}`);
      }
      return products;
    }
    ```

=== "ProductList.js"

    ```js
    import React from "react";

    const ProductList = ({ products }) => {
      return (
        <ul>
          {products.map((product, index) => (
            <li key={index}>{product}</li>
          ))}
        </ul>
      );
    };
    export default ProductList;
    ```

