# 05-イベント

## イベントハンドラの追加

### 使用シーン
- ユーザ側のアクション（Clickやhover、フォーム入力へのfocus等）に応じてUIを変化させたい場合に使う。

=== "サンプル"

    ```js
    const App = () => {

      const handleClick = () => { //(1)
        alert("You clicked me!");
      }

      return (
        <>
          <button onClick={handleClick}>Click me</button>
          <button onClick={() => { alert("You clicked me!") }}> {/*(2)*/}
            Click me
          </button>
        </>
      )
    }

    export default App;
    ```

    1. イベントハンドラ関数は通常、コンポーネントの内部で定義される。慣習的に、イベント名の先頭にhandleが付いた名前にする
    2. アロー関数を用いて記述もOK

!!! Failure
    イベントハンドラに渡す関数は呼び出すべきではない
    ```javascript
    <button onClick={handleClick()}>
    ```
    以上のようにすると、クリックを必要とせず、関数はレンダリングの際にすぐに実行されてしまう。同様にインラインで関数を書く場合、以下の様に書かないよう注意
    ```javascript
    <button onClick={alert("...")}>
    ```
    インラインで定義したい場合は無名関数。通常は関数名を記載する事で、あとで呼び出されるべき関数を作成し、渡すことになる。

## イベントハンドラをpropsとして渡す
- 親のコンポーネントで、子のイベントハンドラを指定するケースを考える。子供は親から受け取ったpropsの一部をイベントハンドラとして渡す
- 独自のコンポーネントを作成する場合、イベントハンドラとなるpropsはユーザが命名できる。が、慣習としてイベントハンドラのプロップは`on`から始まり、次に大文字の文字を続けるようにする。

=== "サンプル"

    ```js

    // 概要:(4)
    const Button = ({ onClick, children }) => { //(3)
      return (
        <button onClick={onClick}>
          {children}
        </button>
      );
    }

    const PlayButton = ({ movieName }) => {
      const handlePlayClick = () => {
        alert(`Playing ${movieName}`);
      }

      return (
        <Button onClick={handlePlayClick}>
          Play "{movieName}"
        </Button>
      );
    }

    const UploadButton = () => {
      return (
        <Button onClick={() => alert("Uploading!")}> {/*(2)*/}
          Upload Image
        </Button>
      );
    }

    const App = () => {
      return (
        <div>
          <PlayButton movieName="Hogehoge Service" /> {/*(1)*/}
          <UploadButton />
        </div>
      )
    }


    export default App;
    ```

    1. movieNameをPlayButtonコンポーネントに渡している
    2. 無名関数をButtonのonClickプロパティとして渡している
    3. propsとしてonClickを受け取っている。childrenは特別なpropsで{children}の箇所に親要素で挟んだ要素をそのまま受け取れる。今回の場合Upload messageなどのメッセージを組み込みの<button>タグ内の要素として渡している。
    4. デザインをButtonコンポーネントで行い、EventはPlayButtonやUploadButtonのようなコンポーネントから渡す

=== "イベントを複数渡す例"

    ```javascript
    const App = () => { //(1)
      return (
        <Toolbar
          onPlayMovie={() => alert("Playing!")}
          onUploadImage={() => alert("Uploading!")}
        />
      );
    }

    const Toolbar = ({ onPlayMovie, onUploadImage }) => {
      return (
        <div>
          <Button onClick={onPlayMovie}>
            Play Movie
          </Button>
          <Button onClick={onUploadImage}>
            Upload Image
          </Button>
        </div>
      );
    }

    const Button = ({ onClick, children }) => {
      return (
        <button onClick={onClick}>
          {children}
        </button>
      );
    }

    export default App;
    ```

    1. Appコンポーネントは、ToolbarがonPlayMovieやonUploadImageをどう扱うかについて、知る必要がない。そのためToolbarは現在ButtonのonClickとして渡しているそれを、たとえばonHoverに対して使うよう変更することができる。

### イベント伝播
- イベントハンドラは通常、どの子のイベントもキャッチする。イベントは通常発生した場所から始まり、ツリーを上に向かって進んでいく。
- ただし`onScroll`はそれをアタッチしたJSXタグでのみ機能し、伝播しない
- イベントハンドラはイベントオブジェクトを引数として受け取る。慣習的にそれは`event`を意味する`e`と表される。

=== "イベント伝播(バブリング)の防止"

    ```js
    const Button = ({ onClick, children }) => {
      return (
        <button onClick={e => {
          e.stopPropagation(); //(1)
          onClick();
        }}>
          {children}
        </button>
      )
    }

    const App = () => {
      return (
        <div className="Toolbar" onClick={() => {
          alert("You clicked on the toolbar!")
        }}>
          <Button onClick={() => alert("Playing!")}>
            Play Movie
          </Button>
          <Button onClick={() => alert("Uploading!")}>
            Upload Image
          </Button>
        </div>
      );
    }

    export default App;


    ```

    1. イベント(今回の場合Clickイベント)がさらにバブリングされる(親に伝わる)のを防ぐ。伝播が停止されたので親である`<div>`にイベントは伝達されず、結果`<div>`のonClickハンドラは実行されない。

=== "デフォルト動作のキャンセル"

    ```javascript
    const App = () =>{
      return(
        <form onSubmit={e => {
          e.preventDefault(); //(1)
          alert("Submitting!");
        }}>
          <input />
          <button>Send</button>
        </form>
      )
    }
    ```

    1. `<form>`のsubmitイベントは通常、ボタンclick時にページリロードを行う。これを防止したい場合`e.preventDefault()`を使う。

## イベントのまとめ
- イベントは、要素に関数をpropsとして渡すことで実現する
- イベントハンドラは呼び出すのではなく、渡す
- 親でイベントハンドラを宣言して子に渡したりもできる
- 通常イベントは上方向に伝播するので、それを防ぎたい場合e.stopPropagation()を使う
- デフォルトのブラウザ動作を好まない場合、e.preventDefault()を使う
- イベントハンドラは副作用のための場所である。レンダー関数は純関数にし、イベントハンドラで変更するのがベスト。