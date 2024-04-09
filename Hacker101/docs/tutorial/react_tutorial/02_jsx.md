# 02-JSX

## JSX以前のWeb
- 以前はコンテンツをHTMLで書き、デザインをCSSで書き、ロジックをJavascriptで書いていた。そのためコンテンツとロジックは別ファイルに存在していた

## Reactにおいて
- Reactはロジックとコンテンツ(Markup)を同じ場所(コンポーネントの中)に記載する。
- ロジックについてはJSでコンポーネントとして記載し、同一ファイル内にコンテンツ(Markup)も記述したいのでJSXを使う

## JSXについて

- JSXはJavascriptの拡張構文で、これを使うとJSにMarkupを含むことができる。
- JSXはHTMLに似ているが、より構文が厳密で、動的な情報を表示することが可能という特徴がある
- JSXはReactとは別のもので、片方だけを使うこともできる

## HTMLとJSXの比較

=== "HTMLのコード例"

    ```html
    <h1>Hedy Lamarr's Todos</h1>
    <img 
      src="https://i.imgur.com/yXOvdOSs.jpg" 
      alt="Hedy Lamarr" 
      class="photo"
    >
    <ul>
      <li>Invent new traffic lights
      <li>Rehearse a movie scene
      <li>Improve the spectrum technology
    </ul>
    ```

=== "JSXのコード例"

    ```javascript
    // return内に直接コピペすると動かないので注意
    export default const App () => {
      return(
        <> //(1)
          <h1>Hedy Lamarr's Todos</h1>
          <img 
            src="https://i.imgur.com/yXOvdOSs.jpg" 
            alt="Hedy Lamarr" 
            class="photo" {/*(3)*/}
          /> //(2)
          <ul>
            <li>Invent new traffic lights
            <li>Rehearse a movie scene
            <li>Improve the spectrum technology
          </ul>
        </>
      )
    }
    ```

    1. コンポーネントは1つの要素しか返せない。そのため`<>`などで囲む。`<>`はDOMの構造に影響を与えないため、`<div>`などより進んで利用する。
    2. jsxではすべてのタグを明示的に閉じる必要がある。そのためhtmlでは`<img>`と書いていたものを、`<img/>`とする必要がある

    3. javascriptではclassは予約語なので使えない。classNameを使う。また殆ど全てはキャメルケースで書く。ただし`aria-*`と`data-*`はハイフン付きで書く。

    !!! info
        コンポーネントが2つ以上の要素を返せない理由は、JSXが裏でJavascriptオブジェクトに変換されるため。ここで関数から2つ以上の要素を返したい場合を思い出す。javascriptでは`#!javascript return a, b`とすることは出来ない。コンポーネントが2つ以上の要素を返せない理由も同じである。`<>`で囲むのは`#!javascript return [a,b]`としているのに似ている。


## HTMLからJSXへの変換が面倒な場合
[transform.tools](https://transform.tools/html-to-jsx)

## 値を動的に指定する
- マークアップの中で、テキストを動的に指定したい場合、波括弧を用いる
- 波括弧はテキストとして、JSXタグの中で使ったり、=記号の後ろで属性として記載する事ができる。
  
=== "基本例"

    ```javascript
    const App = () =>{
      const hogeUrl = "hoge.js";
      const description = "hogeを表示する";
      const text = "波括弧";
      return(
        <>
          <img
            className="hoge"
            src={hogeUrl} {/*(1)*/}
            alt={description}
          />
          <h1><{text}は神/h1> {/*(2)*/}
        </>
      );
    }
    ```

    1. 属性としてJSの変数を指定している
    2. テキストとしてJSの変数を指定している

=== "CSSを動的に指定する場合"

    ```javascript
    const App = () =>{
      return(
        <ul style={{ //(1)
          backgroundColor: "gray",
          color: "pink"
        }}>
          <li>test1</li>
          <li>test2</li>
          <li>test3</li>
        </ul>
      )
    }
    ```

    1. ダブル波括弧になるのは、属性としてオブジェクトを指定しているから。外側の波括弧はMarkUpの中でJSの構文を使用するための括弧で、内側の波括弧はオブジェクトとしての括弧
    ```javascript
    <ul style={
      {
        backgroundColor: "gray", //JSの構文なのでキャメルケース
        color: "pink"
      }
    }>
    ```

=== "CSSを動的に指定する場合2"

    ```javascript
    const baseUrl = ".js"
    const person = {
      name: "yamada hoge",
      src: "hoge",
      alt: "hogeの画像",
      theme: {
        backgroundColor: "gray",
        color: "pink"
      }
    };

    const App = () =>{
      return(
        <div style={person.theme}> {/*(1)*/}
          <h1>{person.name}'s genius</h1>
          <img
            className="hoge"
            src={"https://" + person.src + baseUrl} {/*(2)*/}
            alt={person.alt}
          />
          <ul>
            <li>test1</li>
            <li>test2</li>
            <li>test3</li>
          </ul>
        </div>
      );
    }
    ```

    1. 複数の値をオブジェクト内に記載して、JSXの波括弧内から参照している。
    2. !!! failure
        以下は誤り
        ```javascript
        src="https://"{person.src}{baseUrl}
        ```
        js内の変数を連結したい場合、または文字列と連結したい場合は全てを波括弧の中に入れて+演算子を用いる。



=== "失敗例"
    !!! failure
        タグの中で波括弧を使うことはできない
        ```
        <{tag}>test</{tag}>
        ```

## コンポーネントにpropsを渡す
- 親コンポーネントは子供に対して、propsというものを渡すことで情報を伝えることができる
- propsにはJSのあらゆる値(オブジェクトや配列、関数、変数等)を指定可能
- たとえば`<img/>`のようなブラウザ組み込みのコンポーネントの場合、渡すことができるpropsの種類は限られている(たとえばwidthやheight)
- 対して、自分で作ったコンポーネントの場合、任意のpropsを渡せる

=== "基本例"
    ```js

    const Avatar = ({person, size}) =>{ //(1) (2)
      return(
        <>
          <h1>{person.id + ":" + person.name}</h1>
          <p>{size}</p>
        </>
      )
    }

    const App = () =>{
      return(
        <Avatar
          person={{name: "yamada hoge", id:"179iue"}}
          size={100}
        />
      )
    }
    ```

    1. 通常、Reactコンポーネントはpropsというオブジェクトを唯一の引数として受け取る。しかし通常はpropsオブジェクト全体を必要とすることはないため、基本例で示したように分割代入を行う。
    ```javascript
    //分割代入を行わないと冗長になりがち
    const Avatar = (props) =>{
        <>
          <h1>{props.erson.id + ":" + props.person.name}</h1>
          <p>{props.size}</p>
        </>
      )
    }
    ```
    2. 分割代入の別解
    ```javascript
    const Avatar = ({ person: { id, name }, size }) => { //
      return (
        <>
          <h1>{id + ":" + name}</h1>
          <p>{size}</p>
        </>
      )
    }

    const App = () => {
      return (
        <Avatar
          person={{ name: "yamada hoge", id: "179iue" }}
          size={100}
        />
      )
    }

    export default App;
    ```

=== "デフォルト値"

    ```javascript
    const Avatar = ({person, size = 100}) =>{ //(1)
      return(
        <>
          <h1>{person.id + ":" + person.name}</h1>
          <p>{size}</p>
        </>
      )
    }
    ```

    1. 分割代入の中では、デフォルト値を記述できる。親コンポーネントで`<Avatar person = {...}/`>のように、`size`が指定されなかった場合、`size`は`100`にセットされる。また`size={undefined}`と指定した場合も同じ効果が得られる。__`size={null}`や`size={0}`の場合、デフォルト値は使われない__

=== "スプレッド構文"

    ```javascript
    const Profile = (props) =>{ //(1)
      return(
        <div className="card">
          <Avatar {...props} /> //(2)
        </div> 
      );
    }
    ```

    1. 今回の例ではpropsをそのまま子コンポーネントに転送しているだけなので、以下の表記は冗長
    ```javascript
    const Profile = ({person, size, isSepia, thickBorder}) =>{
      return(
        <div className="card">
          <Avatar 
            person={person}
            size={size}
            isSepia={isSepia}
            thickBorder={thickBorder}
          />
        </div>
      )
    }
    ```
    2. スプレッド構文を使うことで、propsの要素すべてを渡すことが可能。詳細は[Mozilla スプレッド構文](https://developer.mozilla.org/ja/docs/Web/JavaScript/Reference/Operators/Spread_syntax)を参照のこと

    !!! info
        スプレッド構文を使う時には、コンポーネントを分割してchildrenを渡せないか考える。大抵の場合、そちらの方がよい。(下記参照)

## childrenとしてJSXを渡す

ブラウザの組み込みタグと同様、自作コンポーネントもネストは可能
```javascript
<Card>
  <Avatar />
</Card>
```
上記のようにJSXタグの中で、コンポーネントをネストした場合、親側のコンポーネントはその中身を`children`というpropsとして受け取る。

### 例

=== "App.js"

    ```javascript
    import Avatar from "./Avatar.js";

    const Card = ({children}) =>{
      return(
        <div className="card"> //(1)
          {children}
        </div>
      );
    }

    const Profile = () =>{
      return(
        <Card>
          <Avatar
            size={100}
            person={
              {
                nane: "Yamada Hoge",
                imageId: "7dfose"
              }
            }
          />
        </Card>
      );
    }
    ```

    1. Cardコンポーネントは`<Avatar />`(子コンポーネントの中身)がセットされたchildrenプロパティを受け取り、div要素の内部にそれをレンダリングする。Cardコンポーネント側では何が渡されるのかは気にする必要がない(childrenはパネルやグリッドのような視覚的に何かを囲む要素に使われる)

=== "Avatar.js"

    ```javascript
    import {getImageUrl} from "./utils.js"

    const Avatar = ({person, size}) =>{
      return(
        <img
          className="avatar"
          src={getImageUrl(person)}
          alt={person.name}
          width={size}
          height={size}
        />
      );
    }

    export default Avatar;
    ```

=== "utils.js"

    ```javascript
    export const getImageUrl = ({person, size="s"}) =>{
      return(
        "https://i.imgur.com/" +
        person.imageId +
        size +
        ".jpg"
      );
    }
    ```

## props == immutable
- propsは常に固定とは限らない
- 時間経過とともに別のpropsを受け取る可能性もある
- しかしpropsの書き換えは行わない
- ユーザ入力に応じてUIを変更したい場合は、フックのuseStateを用いる。

## 条件を満たす場合にのみJSXを返したい場合
- たとえば`isPacked={true}`の場合にのみ、チェックマークの表示を行いたい場合、通常は`if/else`文の使用を考える。
- しかし、JSX内で(つまりコンポーネントのreturn内で)`if/else`文は使用できない。
- このためreturnの外で条件分岐を行うことになるが、条件が複数になると、煩雑になる。
- 従ってJSX内でも使える`&&`や`||`といった条件演算子。また三項演算子を用いて以下の様に書くことが推奨される
- なお、__条件分岐のための演算子があまりにも増えてきた場合__、それぞれを子コンポーネントとして切り出した方がいいと思われる。

### 三項演算子の例
=== "推奨されない"
    !!! warning

        ```javascript
        if(isPacked){ //(1)
          return <li className="item">{name} ✓</li>;
        }
        return <li className="item">{name}</li>
        ```

        1. この例が必ずしも悪いというわけではない。場合によっては柔軟性が保てるので良いとされる。最終的には(好みもしくは)コード規約に従う
    
=== "推奨"
    !!! success

        ```javascript
        return(
          <li className="item">
            {isPacked ? name + "✓" : name} //(1)
          </li>
        )
        ```

        1. `{条件 ? true : false}`

### 論理演算子の例

=== "trueの場合のみレンダリング"

    ```javascript
    return(
      <li className="item">
        {name} {isPacked && "✓"} {/*(1)*/}
      </li>
    )
    ```
    
    1. `isPacked = true`なら✓をレンダリングし、それ以外の場合には何もレンダリングしない

=== "真偽値の落とし穴"

    - javascriptは`0`を`false`として評価するため、左辺に数値を置きたくなることがある。しかし、たとえば1つ目のコードは、予想通りには動かない

    ```javascript
    //失敗例
    const App = () => {
      const count = 0;
      return (
        <>
          {/*(1)*/}
          {count && <p>New messages</p>}
        </>
      );
    }

    export default App;
    ```

    1. 0が画面に表示される。

    ```javascript
    //成功例
    const App = () => {
      const count = 0;
      return (
        <>
          {/*(1)*/}
          {count > 0 && <p>New messages</p>} 
        </>
      );
    }

    export default App;
    ```

    2. 左の値を真偽値にすれば、余計な0は表示されない

## リストのレンダー
- 時に似たようなコンポーネントを複数表示したいことがある
- JSの配列メソッドである`filter()`や`map()`が役に立つ
- 同時に`key`についても説明する

=== "App.js"

    ```js
    //配列からアイテムのリストを作成する
    import {people} from "./data.js";

    const App = () =>{

      const tokyoPersons = people.filter(person => //(1)
        person.address === "tokyo"
      );

      const listItems = tokyoPersons.map(person => //(2)
        <tr key={person.id}> {/*(4)*/}
          <td>{person.name}</td>
          <td>{person.address}</td>
        </tr>
      );

      //(3)
      return <table>{listItems}</table>;
    }

    export default App;

    ```

    1. 条件に合うpersonだけを返す
    2. 各要素に対して以下をreturnする
    3. 関数を渡している
    4. keyは、配列のどの要素がどのコンポーネントに対応するのかをReactが判断し、 __後から正しく更新できるようにするため__ に存在する。keyがない場合、配列の要素がsort等により移動もしくは挿入、削除されると正しく更新することができなくなる。(keyは元データのid等を使用して配列の中で一意なものとする。なお、明示的にkeyが指定されない場合、Reactは配列等のindexをデフォルト値として扱う)

=== "data.js"

    ```javascript
    export const people = [
      {
        id: 0,
        name: "tarou",
        address: "tokyo",  
      },
      {
        id: 1,
        name: "hanako",
        address: "tokyo",
      },
      {
        id: 2,
        name: "bob",
        address: "america"
      },
    ];

    ```

=== "補足(アロー関数とreturn)"

    ### 概要
    - アロー関数は`=>`の直後の式を自動的に返す(簡潔文体)
    - ただし`=>`の後ろに`{}`が続く場合は __return文を明示的に書く__ 必要がある(ブロック構文)
    - 言い換えると、関数が直接式を返す場合以外は、中括弧もreturnキーワードも必要
    - 詳細は[Mozila アロー関数式](https://developer.mozilla.org/ja/docs/Web/JavaScript/Reference/Functions/Arrow_functions)を参考のこと

    !!! warning
        - 簡潔文体を使ってオブジェクトを返したいと思う場合、問題が発生する。
        - これはオブジェクトの波括弧がブロック構文のそれとみなされるため
        - よって、簡潔文体を用いてオブジェクトを返したい場合、以下の様に書く
        ```javascript
        const func = () => ({foo: 1});
        ```


    ```javascript
    const hogeLists = hoges.map(hoge =>
      <li>...</li>
    )    

    const hogeLists = hoges.map(hoge =>{
      return <li>...</li>
    })    
    ```

=== "key==indexの欠点"
    
    ### 概要
    - mapに対してkeyを指定しない場合、警告が発せられる。
    - そのため、このようにして場当たり的な解決を行いたくなる

    !!! warning
        ```javascript
        todos.map((todo, index)=>
          <Todo {...todo} key={index} />
        )
        ```
    - 以上のようにすると、警告自体は取り除かれる。しかし、別の項目がリストにpushされたり、途中の項目が削除された場合、上記の例では問題がある。
    - (直感的に理解したい場合=>[key=indexの危険性](https://jsbin.com/wohima/edit?output))

    - 例として、以下のようなデータを用いてレンダリングを行っているとする
    ```javascript
    const animals = [
      "lion",
      "dog",
      "cat"
    ]
    ```
    - この時、key = indexとしていると、Reactは0番目のDOM要素をlion,1番目のDOM要素をdog,2番目のDOM要素をcatとして認識する。

    - ここで、例えば以下の様にする
    ```javascript
    animals.sort();
    ```
    - 本来keyは、 __配列のどの要素がどのコンポーネントに対応するのかをReactが判断するため__ にある。しかし、key=indexとした場合、sortを行うたびに対応関係が変わってしまう。(今回の場合0番目の要素はlionからcatになる)。これは不具合のもとになり、また非効率である。

    !!! warning
        同様の理由でキーにランダム値を割り当てることはご法度である。仮にこれを用いると、キーがレンダリングごとに異なるため、コンポーネントとDOMはそのたびに再生成を行い、またユーザの入力値も失われる。

    ### 結論
    - そのため各アイテムには永続的で固有のkeyを持たせるようにする。理想的には、元データにidの項を作り、以下の様にアイテムの作成時に割り当てる。もしくは1つ外のブロックの変数を使うという手も考えられる。

    !!! success

        ```javascript
        const animals = { //(1)
          {id:1, name:"lion"},
          {id:2, name:"dog"},
          {id:3, name:"cat"}
        }
        todos.map((todo)=>
          <Todo {...todo} key={todo.id} />
        );
        ```

        1. sortを行ってもidは変わらないため、対応関係は変わらない

=== "keyについて(補足)"

    ### 概要
    - 各アイテムが複数のDOMノードをレンダーする場合、下記のように書く必要がある

    ```javascript
    import {Fragment} from "react";

    const items = people.map(person=>
      <Fragment key={person.id}> //(1)
        <h1>{person.name}</h1>
        <p>{person.height}</p>
        <p>{person.weight}</p>
      </Fragment>
    )
    ```

    1. 短縮形`<>`ではkeyを渡せない
  
    ### keyのルール
    ・配列の中で一意でなければならない
    ・キーを変更してはいけない
    ・キーにindexを割り当ててはならない
    ・キーにランダム値(`Math.random()`)等を割り当ててはならない。

    ### keyが必要な理由(まとめ)
    ・要素の混同を防ぐため
    ・並び替えや削除、挿入対策