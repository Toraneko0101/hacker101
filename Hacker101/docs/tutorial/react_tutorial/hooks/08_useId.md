# useId

### 概要
- useIdは一意のIDを生成する
- リスト内のキーを生成するためには呼び出せない(フックはコンポーネントまたは独自のフックのトップレベルにしか記述できないため)
- HTMLではIDをハードコーディングすることがあるが、Reactでは基本的にコンポーネントは再利用される。また、再度レンダリングされることを考慮に入れる必要もある。それを踏まえてIDを一意にする必要があるので、useIdを用いる

### 注意点
- React18からしか使えないので、17以下である場合はversionを上げること

=== "サンプル例"

    ```js
    import { useId } from "react";

    const PasswordField = () => {
      const passwordHintId = useId();

      return (
        <>
          <label>password:
            <input
              type="password"
              aria-describedby={passwordHintId}
            />
          </label>
          <p id={passwordHintId + "pass"}>{/*(1)*/}
            The password should contain at least 18 characters
          </p>
          <p id={passwordHintId + "pass1"}>test</p>
        </>
      )
    }

    const App = () => {
      return (
        <>
          <h2>Choose password</h2>
          <PasswordField />
          <h2>Choose password</h2>
          <PasswordField />{/*(2)*/}
      )
    }

    export default App;

    ```

    1. idは例えば、`:r1:pass`という風になる。再度呼んだ場合、`:r1:`の部分の値は変化する
    2. コンポーネントを2回呼んだ場合、1回目のコンポーネントと2回目のコンポーネント内のidは異なる