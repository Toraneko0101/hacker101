# 04-コンポーネントとツリー構造

## ツリー構造とは
- アイテム間の関係を表すモデルの一種
- DOM（Document Object Model）もその一種
- たとえば以下の場合、図のような関係性となる

=== "html"
    ```html
    <body>
      <h1>hoge</h1>
      <h1>fuga</h1>
      <div>
        <span>neko</span>
      </div>
    </body>
    ```
=== "ツリー構造図"
    ```mermaid
    graph LR;

      C[body] --> A[h1]
      C[body] --> B[h1]
      C[body] --> D[div]
      D[div] --> E[span]
    ```


## レンダーツリー
- Reactもツリー構造を用いて、アプリ内のコンポーネント間の関係を管理し、モデル化する
- ツリーを見ればReactアプリ内をデータがどのように流れるかが理解でき、レンダリングを最適化する際の有用なツールとなる

=== "Component関係図の例"

    ```mermaid
        graph LR;
            classDef accent fill:orange,fill-opacity:0.3
        App --> GrandParent
        GrandParent --> Parent
        Parent --> ArticleById:::accent
        ArticleById --> Child1
        ArticleById --> Child2
    ```

- Reactレンダーツリーのルートノードは、アプリのルートコンポーネントとなる。上記の場合、ルートコンポーネントはAppである
- Reactアプリにおいてトップレベルのコンポーネントは、下に位置するすべてのコンポーネントのレンダリングパフォーマンスに影響を与え、しばしば複雑になる
- 下の方にあるコンポーネントは、頻繁に再レンダリングされる
- ツリーの矢印をたどっていくと、子コンポーネントの親はどれなのか、確かめることができる。

!!! info
    各コンポーネントがレンダーするHTMLタグを、レンダーツリーに記載する必要はない

## 依存関係ツリー
- モジュールの依存関係もツリー構造で表現できる
- 依存関係ツリーは、Reactアプリを実行するうえで度のモジュールが必要かを判断するために役立つ
- Reactアプリを本番環境用にビルドする際には、必要なJSをすべてバンドルにまとめるというビルドステップがある。
- そのため使ってもいないモジュールをいつまでもimportしていると、バンドルサイズが大きくなる。通常はESLintなどが警告を発してくれるが、その警告を無視していると、クライアント実行時に時間がかかり、ページ読み込み速度の低下から不満につながる
=== "モジュール依存関係図の例"

    ```mermaid
        graph LR;
            classDef accent fill:orange,fill-opacity:0.3
        App.js -->|imports| InspirationGenerator.js 
        App.js -->|imports| A[FancyText.js]
        App.js -->|imports| Copyright.js
        InspirationGenerator.js -->|imports| B[FancyText.js]
        InspirationGenerator.js -->|imports| Color.js
        InspirationGenerator.js -->|imports| inspirations.js
    ```