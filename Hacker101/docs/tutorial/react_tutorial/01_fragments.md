# 01-Fragment(<>について)

## 概要
- `<Fragment></Fragment>`。`#!javascript import {Fragment} from "react"`とする必要がある。短縮形を使う場合はimportは必要ない。短縮形については使用例(基本形)を参照
- __コンポーネントは1つの要素のみを返すことができる。__ 複数の要素をreturnしたい場合は、フラグメントを使用して要素をグループ化して返す

## div等でラップするのと何が違うのか
- `<Fragment>`はDOMとしてみなされないので、階層が深くならない
- 開発者ツール等で確認すると、`<Fragment>`が出現していないことが分かる

## 使用例

=== "基本形"

    ```javascript
    function Post(){
        return(
            <> {/*(1)*/}
              <PostTiTle />
              <PostBody />
            </>
        )
    }
    ```
    
    1. `<Fragment></Fragment>`の省略形。`<Fragment>`を用いる場合は、`key`オプションを追加可能。例`<Fragment key={your_key}></Fragment>`

=== "propsとして渡す例"

    ```javascript
    //不明点がある場合は、先にjsxの項を参照する事
    const Dialog = () =>{
      const buttons = (
        <>
          <OKButton />
          <CancelButton />
        </>
      );

      return(
        <AlertDialog buttons={buttons}>
          本当にこのページを離れてもいいですか?
        </AlertDialog>
      );
    }
    ```

=== "ループ内で複数要素をレンダリングする例"

    ```javascript
    //{}やkeyなど、不明点がある場合は、先にjsxの項を参照する事
    import {Fragment} from "react";
    const posts = [
      {id : 1, title: "An update", body: "hogehoge"},
      {id : 2, title: "My new blog", body: "Hello"}
    ];

    const Blog = () =>{
      return posts.map((post)=>
        <Fragment key={post.id}> {/*(1)*/}
          <PostTitle title={post.title} />
          <PostBody body={post.body} />
        </Fragment>
      );
    }

    const PostTitle = ({title}) =>{
      return <h1>{title}</h1>
    }

    const PostBody = ({body}) =>{
      return(
        <article>
          <p>{body}</p>
        </article>
      );
    }

    export default Blog;
    ```

    1. keyを用いるため、短縮形ではなく`<Fragment>`の方を利用している。`< key={post.id}>`という風にはできない
