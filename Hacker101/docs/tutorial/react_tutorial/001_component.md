# 00-Component

- コンポーネント = UIの構成部品
- 下記の様に関数の形で記述する
- コンポーネントを呼ぶことでreturn内のDOM要素が画面に表示される
```javascript
const App = () =>{
  return(
    <div>
      <span>hoge</span>
    </div>
  )
}
```
- 作成したコンポーネントを使いたい場合、例えばHTMLの中で以下の様に書く
```js
<App />
```
- また、コンポーネントのreturn文の中に、自作のコンポーネントを書くこともできる。(具体例参照)




## コンポーネントを使うメリット
- 再利用可能であること

## 具体例
- たとえば下記のようなHTMLの要素を他ファイルで利用したいとする
- この時、これをコピー&ペーストするのは明らかに非効率である
- コンポーネントを使えば、既に書いた部品を再利用することで開発速度を向上させられる
=== "元になる要素"

    ```html
    <article>
      <h1>My First Component</h1>
      <ol>
        <li>Components: UI Building Blocks</li>
        <li>Defining a Component</li>
        <li>Using a Component</li>
      </ol>
    </article>
    ```

=== "コンポーネントを使った場合"

    ```js
    const Article = () =>{ //(1)
        return(
          <article>
            <h1>My First Component</h1>
            <ol>
              <li>Components: UI Building Blocks</li>
              <li>Defining a Component</li>
              <li>Using a Component</li>
            </ol>
          </article>
        )
    }

    const App = () =>{
      return( //(3)
        <div>
          <Article /> //(4)
          <Article />
          <Article />
        </div>
      )
    }

    export default App; //(2)
    ```

    1. コンポーネントは大文字から始める必要がある。小文字から始めた場合、ReactはHTMLタグをとして解釈する。大文字をつかうことで初めて、独自コンポーネントを使おうとしているのだと理解できる。
    2. これは標準的なJSの構文。ファイル内のメインの関数をマークして、他のファイルからそれをimportできるようにする
    3. 要素が1つの場合、return文は括弧無しで1行で書ける
    ```javascript
    return <img src="hogehoge" alt="hogeだよ">
    ```
    要素が2つ以上、または2行以上で書きたい場合は左記のように括弧で囲む。括弧がない場合、コードは無視される。

    4. ??? 例
        ```html
        <article>
          <h1>My First Component</h1>
          <ol>
            <li>Components: UI Building Blocks</li>
            <li>Defining a Component</li>
            <li>Using a Component</li>
          </ol>
        </article>
            <article>
          <h1>My First Component</h1>
          <ol>
            <li>Components: UI Building Blocks</li>
            <li>Defining a Component</li>
            <li>Using a Component</li>
          </ol>
        </article>
            <article>
          <h1>My First Component</h1>
          <ol>
            <li>Components: UI Building Blocks</li>
            <li>Defining a Component</li>
            <li>Using a Component</li>
          </ol>
        </article>
        ```
    他のコンポーネント内でコンポーネントを使っている
    html単体で書いた場合、上記の様になる

## 注意点
- コンポーネントの定義をネストしてはいけない

=== "誤り"
    !!! failure
        以下のコードはとても遅く、またバグの原因になる

        ```javascript
        const hoge = () =>{
          // ....
          const fuga = () =>{
            // ...
            return (
              // ...
            )
          }
          return (
            //...
          )
        }
        ```

=== "正解例"

    !!! success
        代わりに、コンポーネントはすべてトップレベルで定義する

        ```javascript
        const hoge = () =>{
          return (
            // ...
          )
        }

        const fuga = () =>{
          return (
            // ...
          )
        }
        ```

## 別ファイルに定義したComponentを使う

=== "App.js"

    ```javascript
    import Gallary from "./Gallery.js" //(1)

    const App = () =>{
      return(
        <Gallery />
      );
    }
    ```

    1. `Gallery.js`で`export default`した、`Gallary`コンポーネントを使うために`import`している
    ```javascript
    import Gallary from "./Gallary"
    ```
    でも一応動作する。


=== "Gallery.js"

    ```javascript
    const Profile = () =>{ //(1)
      return(
        <img
          src="hoge.jpg"
          alt="hogeの画像"
        />
      );
    }

    const Gallery = () =>{
      return(
        <section>
          <h1>Amazing hoge</h1>
          <Profile />
          <Profile />
          <Profile />
        </section>
      );
    }

    export default Gallery;
    ```

    1. 同一ファイルでしか使わない場合、exportは不要

=== "export default vs export"
    !!! info
        - `export default`はファイルに1つしか置けない。
        - `export`なら幾つでも書ける(named-export)

        - defaultの場合、以下の様に書く
        ```javascript
        //export
        export default const Button = () =>{}
        //import
        import Button from "./Button.js";
        ```
        - namedの場合、
        ```javascript
        //export
        export const Button = () =>{}
        //import
        import {Button} from "./Button.js";
        ```
        - ファイルがコンポーネントを1つだけexportする場合はexport defaultが使われる
        - 複数のコンポーネントや値をexportする場合は、named-exportがよく使われる。
        - どちらを使う場合でも、コンポーネント名は常に意味が分かるようなものにする

        ### named-export(名前付きエクスポート)の例
      
        ```javascript title="App.js"
        import Gallery from "./Gallery.js";
        import {Profile} from ".Gallery.js";
        const App = () =>{
          return(
            <Profile />
          );
        }
        ```

        ```javascript title="Gallery.js"
        export const Profile = () =>{ //(1)
          return(
            <img
              src="hoge.jpg"
              alt="hogeの画像"
            />
          );
        }

        const Gallery = () =>{
          return(
            <section>
              <h1>Amazing hoge</h1>
              <Profile />
              <Profile />
              <Profile />
            </section>
          );
        }

        export default Gallery;
        ```

        1. 名前付きexport

## tips
```
Q. 再利用しない場合、Componentは必要ない?
A. (たとえ1回しか使わなくとも)整理するために有用

Q. ルートコンポーネント(一番の親)は複数あってもいい?
A. いい。create-react-app等を使う場合、ルートはpages/index.jsに作られる。Next.jsを使っている場合、ルートはページごとに異なる

Q. HTMLファイルはどこ?
A. Next.jsの場合、HTMLファイルはReactコンポーネントから自動生成されるので無い。この利点は後述

```
## 補足(レンダーという言葉について)

### コンポーネントをレンダーするとは?
- コンポーネントを関数として呼び出すこと

### レンダーするとどうなるのか?
- 初回レンダー時(コンポーネントが初めて呼び出された時)は、return文の中身が全て画面に表示される
- 2回目以降は更新部分のみが反映される（2回目以降のコンポーネント呼び出し=必ず更新ではない。詳しくは08に記述予定）
- 全てを再生成するわけではないため効率的

### 初回(ページの読み込み時)以外に、いつレンダーされるのか
- 07-stateや、08を参照のこと