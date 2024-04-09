# 07-Stateの基本

## State
- Reactが各々のコンポーネントに紐づけている状態のことをstateという。
- stateはユーザ側の操作(click, hover等)をトリガーに、画面上の表示内容を変更したい場合に使われ、`useState`というフックを用いる事で追加できる

## どうして通常の変数ではうまくいかないのか

=== "失敗例"

    ```js
    const App = () =>{
      let count = 0;

      const handleClick = () =>{
        index += 1;
      }

      return(
        <>
          <button onClick={handleClick}>
            Count Up?
          </button>
          {/*(1)*/}
          <h1>{index}</h1> 
        </>
      )
    }
    ```

    1. 何度レンダーしても値は1のまま変わらない

=== "原因"

    ### ローカル変数はレンダリング間で保持されない
    - Reactがコンポーネントを再度レンダリングするときは、過去にローカル変数を変更したことは考慮されない。
    - ローカル変数をレンダリング合間に変えても、その記録はリセットされる
    ### ローカル変数を変更しても、レンダリングは為されない
    ローカル変数を更新した場合、表示に変化が出てほしいわけだが、ローカル変数の更新はレンダリングのトリガーにはなりえない。つまりデータは変わっているのに、古い表示がそのまま残り続ける可能性がある

## つまりどんな機能が必要か

1. 再度レンダリングを行っても、データが保持される機能
2. データに変化が起こったら、コンポーネントを再レンダリングして表示を変える機能

⇒ __useStateは上記の機能を提供する__
## useStateの機能

### state変数
- レンダー間でデータを保持する

### setState関数
- state変数を、引数にとった値で更新する
- state更新時に、(そのstate変数と紐づいた)コンポーネントが再レンダリングされるよう(Reactに)促す。

## useStateの基本
=== "基本例"

    ```js
    import {useState} from "react";

    //値更新時の挙動について (5)
    const App = () =>{
      const [index, setIndex] = useState(0); //(1) (2)

      const handleClick = () =>{
        setIndex(index + 1); //(4)
      }

      return(
        <>
          {/*(3)*/}
          <button onClick={handleClick}>Count Up</button>
          <h1>{index}</h1>
        </>
      )
    }

    export default App;
    ```

    1. 配列の分割代入。useStateから返される配列は常に2個の要素を持っていて、1つ目がstate変数、2つ目がsetState関数となる。また、`useState`に渡す引数はstate変数の初期値を意味する。コードでは初期値を0に設定している
    2. `const [hoge, fuga]`のように自由に命名することもできるが、慣習に従わないと理解しにくくなるので、非推奨
    3. ボタンを押すたびに表示が更新(再レンダリング)され、状態がレンダリング間で保存されていることが確認できる
    4. 値を更新するときは常に、setState関数を用いる。stateを直接変更してはいけない。stateを直接変更した場合、再レンダリングは為されないため、表示とデータの整合性が取れなくなる
    5. !!!example
          1. 基本例のコードを基準に考える
          2. コンポーネントが初めてレンダリングされた時、useStateは引数の初期値を利用するので、`[0, setIndex]`を返す
          3. ユーザがボタンをクリックすると、`handleClick`が実行され、`setIndex(index + 1)`の結果、Reactは次回のレンダーでindexをindex+1に更新する準備をする。
          4. 更新準備がトリガーになり、再レンダリングが行われ、コンポーネント内のコードが再び実行される。
          5. その際、ReactはuseState(0)というコードを見つけるが、3で更新準備をしていたため初期値ではなく、`[1, setIndex]`を返す
          6. 注意点: 3の時点ではindexはまだ更新されていない。更新するための「準備」をするだけである。実際に更新されるのは次回のレンダー時になる。詳細についてはチュートリアルの10番を参照のこと


## useから始まるもの(== フック)とその注意点
- `useState`や`useEffect`などのuseから始まるものはフックと呼ばれ、コンポーネントのトップレベル、またはカスタムフック（後述）内でのみ呼び出せる
- つまり、条件分岐、ループ文、ネストされた関数の中でフックを呼び出すことはできない。
- コンポーネントの先頭でReactの機能を`use`していると考える。
- これはちょうど、ファイルの先頭でモジュールを`import`するのと似ている。

## stateは複数使える
=== "例"

    ```js
    import { useState } from "react";
    //(1)
    const App = () => {
      const [index, setIndex] = useState(0);
      const [showMore, setShowMore] = useState(false);

      const handleNextClick = () => {
        setIndex(index + 1); 
      }

      const handleMoreClick = () => {
        setShowMore(!showMore);
      }


      return (
        <>
          <button onClick={handleNextClick}>Count Up</button>
          <button onClick={handleMoreClick}>
            {showMore ? "Hide" : "Show"} details
          </button>
          {showMore && <p>More Contents!</p>}

          <h1>{index}</h1>
        </>
      )
    }

    export default App;
    ```

    1. contentsの表示とcount upは互いに関連していないので、別のstateを用意した。2つのstate変数が一緒に更新される場合は、まとめた方がよい。例として以下はアンチパターンである
    ```javascript
    const [lastName, setLastName] = useState("");
    const [firstName, setFirstName] = useState("");
    //lastName + firstNameで管理できるのでstateは必要ない
    const [fullName, setFullName] = useState(""); 
    ```

## Reactはどのようにしてstateを管理しているのか
- `useState`は呼び出す際に識別子を持たない。
- 引数を見ると`useState(0)`となっており、初期値を渡しているだけ。それなのに、どうしてReactは再レンダリングを行った際に、保持している状態を戻せるのだろうか？
- ここで、フックがトップレベルでのみ呼び出されることが生きてくる
- フックをトップレベルに記載していた場合、これは（同一コンポーネントの各レンダー間で）必ず同一の順番で呼び出される。そのためReactは保持しているstateを順々に返していけば問題ないのだ
- ※内部的には、Reactはすべてのコンポーネントに対してstateのペア配列を保持しており、`useState`が呼び出されるたびにincrementすることで次のstateに備えている

## state == private

### stateはコンポーネントごとに独立している
- たとえば`<Gallary />`というコンポーネント内で`useState`が使われており、それを以下の様に呼び出した場合
```javascript

const App = () =>{
  return(
    <>
      <Gallary />
      <Gallary />
    </>
  )
}

```
- いずれかの`<Gallary />`内で`useState`が変化したとして、これはもう一方に影響を与えない。
- それぞれのstateは独立している

### stateはコンポーネントごとにprivateである
- 上記の例でいうと、AppコンポーネントはGallary内のstateの値を知ることができない。
- またstateが存在するかも知ることができない
- stateはそれを宣言したコンポーネントのみが知りうる情報であり、これがprops(親から子に受け渡される)との相違点でもある
- そのため任意のコンポーネントにstateを追加したり、削除したりしても他に影響は生じない
- なお、複数の子要素を、1つのstateで管理したくなった場合は、最も近い親(1つ上の親)にstateを追加し、それをpropsで子供に受け渡すなどの手段がある