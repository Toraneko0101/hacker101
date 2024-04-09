# クイズ(12:インデックス)

## Q99 インデックスについて知っていますか?

??? success
    ### テーブルスキャン

    ```text
    ・インデックスがない状態で、特定のテーブルに対してクエリ 
    　を実行する場合、DBサーバはテーブルの行を全て調べる

    ・たとえばテーブルに100万行のrowがあった場合、
      DBサーバは100万行調べる必要があり、非常に時間がかかる
    ```

    ### インデックス

    ```text
    ・リソース内の特定のアイテムを高速で
      検索するためのメカニズム
    
    ・テーブルの行を特定するために使われる列と、
    　その行の物理的な配置場所を示す情報だけを含んでいる

    ・そのためテーブルの行を全探索するより早い

    ・インデックスの種類(たとえばB-tree:O(log2N))や、
      その仕組みについては後述
    ```

## Q100 インデックスの作成/削除方法について知っていますか?

??? success
    ### 作成方法

    ```sql
    -- email列に対するクエリや、
    -- emailを指定するupdate/delete文の速度を上げるため
    -- email列に対するindexを作成する

    -- customer.email列のindexを作成し
    -- indexにidx_emailという名前を付けている
    alter table customer
    add index idx_email(email)
    ;
    /*
    Query OK, 0 rows affected (0.079 sec)
    Records: 0  Duplicates: 0  Warnings: 0
    */
    ```

    !!! info
        ### テーブルの構成要素か否か

        ```text
        ・alter tableはテーブル構造を変更する際に用いる
        ・つまり、インデックスはテーブルの構成要素と
          みなされている
        
        ・一方、SQL ServerやOracle DBなどではcreate index
          が用いられる。これはインデックスを独立したスキーマ
          としてみなしていることを意味する

          create index idx_email
          on customer (email)
          ;
        
        ・なお、MySQL/Mariadbもcreate indexをサポートして
          いるが、コマンドはalter tableにマップされる
        
        ・さらに、以下のような主キーインデックスの作成を
          create indexで行うことはできない
          alter table customer
          add primary key (col_name)
          ;
        ```
    
    ### 削除方法

    ```sql
    alter table customer
    drop index idx_email;
    /*
    Query OK, 0 rows affected (0.060 sec)
    Records: 0  Duplicates: 0  Warnings: 0
    */

    /*
    DROP INDEX idx_email; (oracle)
    DROP INDEX idx_email on customer; (SQL Server)
    mysql/mariadbでもdrop indexは使える
    */
    ```

    ### インデックスがある場合のOptimizerの挙動

    ```text
    ・必ずしもインデックスを使うとは限らない
    ・Optimizerという名の通り、実行するSQL文にとって
      最も適切なインデックスを使用する（もしくは使わない）
    ```

## Q101 利用可能なインデックスを調べる方法を知っていますか?

??? success

    ### インデックス一覧

    ```sql
    show index from customer \G;
    /*
    *************************** 1. row ***********
            Table: customer
      Non_unique: 0
        Key_name: PRIMARY
    Seq_in_index: 1
      Column_name: customer_id
        Collation: A
      Cardinality: 599
        Sub_part: NULL
          Packed: NULL
            Null:
      Index_type: BTREE
          Comment:
    Index_comment:
          Ignored: NO
    *************************** 2. row *************
            Table: customer
      Non_unique: 1
        Key_name: idx_fk_store_id
    Seq_in_index: 1
      Column_name: store_id
        Collation: A
      Cardinality: 4
        Sub_part: NULL
          Packed: NULL
            Null:
      Index_type: BTREE
          Comment:
    Index_comment:
          Ignored: NO
    */

    -- 特定のカラムだけ表示したい場合
    -- information_schema.statisticsテーブルは
    -- テーブルインデックスの情報を提供する
    select
      index_name,
      column_name,
      index_type
    from
      information_schema.statistics
    where
      table_schema="sakila"
      and
      table_name="customer"
    ;

    /*
    +-------------------+-------------+------------+
    | index_name        | column_name | index_type |
    +-------------------+-------------+------------+
    | PRIMARY           | customer_id | BTREE      |
    | idx_fk_store_id   | store_id    | BTREE      |
    | idx_fk_address_id | address_id  | BTREE      |
    | idx_last_name     | last_name   | BTREE      |
    | idx_email         | email       | BTREE      |
    +-------------------+-------------+------------+
    5 rows in set (0.001 sec)
    */

    -- show create tableでも見られる

    show create table customer;

    /*
    show create table customer;
    +----------+--------------------------------------
    | Table    | Create Table                         
    +----------+--------------------------------------
    | customer | CREATE TABLE `customer` (
      `customer_id` smallint(5) unsigned 
        NOT NULL AUTO_INCREMENT,
      `store_id` tinyint(3) unsigned NOT NULL,
      `first_name` varchar(45) NOT NULL,
      `last_name` varchar(45) NOT NULL,
      `email` varchar(50) DEFAULT NULL,
      `address_id` smallint(5) unsigned NOT NULL,
      `active` tinyint(1) NOT NULL DEFAULT 1,
      `create_date` datetime NOT NULL,
      `last_update` timestamp 
        NOT NULL DEFAULT current_timestamp() 
        ON UPDATE current_timestamp(),
      PRIMARY KEY (`customer_id`),
      KEY `idx_fk_store_id` (`store_id`),
      KEY `idx_fk_address_id` (`address_id`),
      KEY `idx_last_name` (`last_name`),
      KEY `idx_email` (`email`),
      CONSTRAINT `fk_customer_address` 
        FOREIGN KEY (`address_id`) 
        REFERENCES `address` (`address_id`) ON UPDATE CASCADE,
      CONSTRAINT `fk_customer_store` 
        FOREIGN KEY (`store_id`) 
        REFERENCES `store` (`store_id`) ON UPDATE CASCADE
    ) ENGINE=InnoDB AUTO_INCREMENT=600 
      DEFAULT CHARSET=utf8mb4 
      COLLATE=utf8mb4_general_ci |
    */
    ```

## Q102 一意なインデックスの役割について知っていますか?

??? success

    ### unique index

    ```text
    ・新しい行が挿入 or indexがついている列が変更されると
      DBはunique indexをチェックして、指定された値が
      テーブルの他行に既に含まれていないか確認する
    
    ・含まれていた場合は、エラーを出す

    ・主キー列が一意かどうかは、DBサーバがあらかじめチェック
      するため、主キー列に対してunique indexを作成する必要
      はない
    ```

    ```sql
    alter table customer
    add unique idx_emial(email)
    ;
    ```

    ### UNIQUE制約との違い

    ```text
    ・unique indexは、クエリの高速化を目的とする
    ・unique constraintは、データの整合性を目的とする

    ・unique indexはindexを消すと一緒に消えるという特徴
      を持つため、整合性目的では心もとない
      整合性を保証したいなら、制約を付けるべき
      制約を削除する人間は、自分が何をしようとしているのか
      理解していることだろう

    ⇒ただし同じ構文で消せることを考えると、
      安全とは言い切れないかもしれない
    ```

    ```sql
    -- 追加/削除の方法は一緒
    create temporary table A (
      id int,
      name varchar(10)
    );

    -- unique index作成
    alter table A
    add unique idx_id(id)
    ;

    -- 制約作成
    alter table A
    add constraint UK1 unique(name)
    ;

    insert into A
    (id, name)
    values
    (1, "hoge");

    insert into A
    (id, name)
    values
    (1, "fuga")
    ;
    -- ERROR 1062 (23000): Duplicate entry '1' for key 'idx_id'  

    show create table A;

    /*
    +-------+-------------------------
    | Table | Create Table         
    +-------+-------------------------
    | A     | CREATE TEMPORARY TABLE `A` (
      `id` int(11) DEFAULT NULL,
      `name` varchar(10) DEFAULT NULL,
      UNIQUE KEY `idx_id` (`id`),
      UNIQUE KEY `UK1` (`name`)
    ) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 
      COLLATE=utf8mb4_general_ci |
    +-------+---------------------
    */

    -- unique indexの削除
    alter table A
    drop index idx_id
    ;

    -- unique constraintの削除
    alter table A
    drop constraint UK1
    ;

    show create table A;
    /*
    +-------+-------------------------
    | Table | Create Table         
    +-------+-------------------------
    | A     | CREATE TEMPORARY TABLE `A` (
      `id` int(11) DEFAULT NULL,
      `name` varchar(10) DEFAULT NULL,
    ) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 
      COLLATE=utf8mb4_general_ci |
    +-------+---------------------
    */    
    ```

## Q103 複数列のインデックスを作成する方法を知っていますか?

??? success
    ### 複合インデックス

    ```sql
    alter table customer
    add index idx_full_name(last_name, first_name)
    ;
    ```

    ### 注意点

    ```text
    ・2番目以降のインデックス単体では効果を持たない
      （説明は後述）

    ・たとえば上記の場合、以下のような索引スキャンを行う
      1 last_nameを条件でスキャン
      2 first_nameを条件でスキャン
    
    つまり、複合インデックスの先頭には以下を指定する
    ・検索条件としてよく利用するカラム
    ・カーディナリティが高いカラム

    ※カーディナリティ
      ・カラムに格納されているユニークな値の数
      ・多重度とも呼ばれる
      ・カーディナリティの低いカラム（たとえば男女などを
        インデックスに指定しても意味がない）
    ```

    !!! info
        ### 性別というカラム

        ```text
        ※そもそも性別情報をDBに格納するのは出来れば避けたいが
          出会い系アプリや、役所を考えると無視するのはまずい
          (⇒ISO 5218に従うといい)
        
        ※病院に関するDBの場合などでは、生物学的な
          性は必須だろうが、この場合は1,2になるだろうか

        +------+----------------+
        | code | value          |
        +------+----------------+
        |    0 | not known      |
        |    1 | male           |
        |    2 | female         |
        |    9 | not applicable |
        +------+----------------+

        ・勝手にMとかFとかotherとか格納するのはやめる
        ・not knownが0なので、default 0にしてnot null制約
          char(1)で0,1,2,9を取りうる(列挙はしない)
          とするのがいいだろうか
        
        ・アプリ側では、任意項目にして(審査ガイドラインに
          引っかかることを防ぐため)
          男女以外を答えたい場合、自由記述にし、
          自由記述の内容によって9を適用するのがよさげ
        ```

    ```sql
    /*
    2分探索で理解する(※実際の例とは異なる)

    2分探索を行うために、last_name, first_nameの順で
    定められた並び順をindexとして記憶しておき、
    クエリ実行の際はそれを用いる場合を考える
    つまり、last_nameが同値の場合、first_nameで
    評価する場合である

    ここで、last_nameを指定せず、
    first_nameのみで検索した場合を考える
    first_name単体で見ると
    ソートは行われていないので全件検索する羽目になる

    逆にlast_name, first_nameという順で評価する場合
    last_nameで等価条件を使った場合は特に、
    絞り込んだ後の2列目はソートされているので、
    簡単に2分探索が行える
    */
    select *
    from table
    where last_name = "hoge"
    order by first_name
    ```

## Q104 主キーインデックスについて知っていますか?


??? success
    ### 無駄なインデックスは作らない

    ```text
    ・主キーについては、DBが自動でインデックスを付ける
      削除はできない
    
    ・なお、ユニーク制約が存在する列に対しても、DBは
      自動でインデックスを付けている
      （値の重複チェック時に、インデックスを利用してデータを
      　ソートするため元よりインデックスが必要）
    
    ※データの一意性や整合性を保つため、
      主キーには変動せず重複しない値を指定する
      そのため氏名や住所、電話番号、生年月日、
      メールアドレスなどは主キーになりえない。
    
    ※複合主キーを考える場合も、主キーに含まれるすべての列
      は不変であった方がいい（特定列単体での一意性は不要）
      〇登録日時 + 一意な番号の複合主キー
      ✖生年月日 + 一意な番号の複合主キー
      ⇒生年月日は登録ミスによる修正の可能性がある
    
    ・PRMIARY KEY(column1, column2)とする場合、
      column1にはよりカーディナリティが高い列を置く
    
    ・この時、すでにcolumn1, column2のインデックスはDB
      によって作成されているので、敢えてインデックスを
      二重で作る必要はない
      (column2, column1)のインデックスなら有用
    
    ・もちろん、column1, column2単体のインデックスも作る
      必要はない
    ```

## Q105 B木インデックスについて知っていますか?
??? success
    ### B木インデックス(balanced-tree index)

    ```text
    ・MySQL/Mariadb/Oracle DB/SQL Server等、多くのDBサーバ
      はデフォルトでB木インデックスを扱う
    
    特徴
      ・データのinsert/delete時、バランスを保つために
        自動的にツリーを再構築する
      ⇒インデックスの効率が維持される（偏らない）

      ・データを効率的に検索するためにツリーを分割する
        分割することで、データを探すうえで必要な照会回数
        が減り、高速な検索が可能
      
      ・データをソート順に保持するため、範囲検索や
        ソート操作に対しても優れたパフォを発揮する
    
    検索時間
      ・O(logN)
      ・ランダウのO表記については、JS-tutorialのQ77を参照
      ・アルゴリズムとデータ構造の本を買えば最初に書いてある
    ```

    ### なぜB-treeなのか(二分探索木)

    ```text
    ・昇順リストから特定の数値を探す場合を考える
    ・先頭から探す場合、時間がかかるO(N)ので二分探索を使う

    ・二分探索木の問題点として、挿入・削除の際に
      木の高さが変動するため、最適でなくなるという問題がある
    
    ・また挿入時にソート済みのデータを与えると、線形になって
      しまい（すべて右側の子として登録されるため）、
      木の高さがNになるという問題点がある

    ※葉ノード：子が存在しないノード
    ※根ノード：親が存在しないノード

    二分探索木の探索
      1 根ノードから手順を開始
      2 着目しているノードと値が同じなら終了
      3 着目ノードが存在しない場合も終了
      4 目的の値 < 着目ノードなら左に進む
      5 目的の値 > 着目ノードなら右に進む
      6 2~5を繰り返す

      計算量は木の高さに依存する
      平衡状態であればO(logN)となるが......
    
    二分探索木の挿入
      1 根ノードから手順を開始
      2 着目ノードと目的の値を比較
      3 目的の値 < 着目ノードなら左が次の着目ノード
      4 目的の値 >= 着目ノードなら右が次の着目ノード
      5 現在の着目ノードが葉の場合、
        次の着目ノードの位置にデータ挿入
      6 現在の着目ノードが葉でないなら、
        次の着目ノードに移動
      
        ※今回は同値の場合、右の子として登録している
      
      計算量は木の高さに依存する
      平衡状態であればO(logN)
    
    二分探索木の削除
      1 根ノードから手順を開始
      2 目的の値 < 着目ノードなら左が次の着目ノード
      3 目的の値 >= 着目ノードなら右が次の着目ノード
      4 着目ノードが削除対象（削除ノード）の場合、以下を実行
      5 削除ノードが子を持たない場合、ノードを削除
      6 削除ノードの子が1つの場合、削除ノードを削除し、
        子ノードで置き換える
      7 削除ノードの子が2つの場合、左の子孫のうち最大の値を
        探索する
      8 削除ノードを削除し、探索してきたノードで置き換え
        探索ノードは、その左の子で置き換える
        左の子がない場合、探索ノードを削除
        (最大の値を探索しているので右の子がある可能性はない)
      9 次の着目ノードに移動。存在しないなら終了

      例は8を削除した場合
    ```

    ```mermaid
    graph TD;

    8 --> 3
    8 --> 10
    3 --> 1
    3 --> 6
    10 --> 14
    6 --> 4
    6 --> 7
    14 --> 13
    ```

    ```mermaid
    graph TD;
    7 --> 3
    7 --> 10
    3 --> 1
    3 --> 6
    10 --> 14
    6 --> 4
    14 --> 13
    ```

    ### なぜB-treeなのか(AVL木)

    ```text
    ・上記の通り、二分探索木の操作のコストは木の高さに比例
      するため高さを小さく保つことが望ましい
    
    ・平衡二分探索木は、木に対する変換（回転など）を行い
      木の高さを減らす
    
    ・平衡二分探索木には以下のような種類がある
      AVL木
      赤黒木
      AA木 ...
    
    AVL木
      ・どのノードをから見た、左右部分木の高さの差も1以下
        という条件を満たす二分探索木
      
      ・データを挿入/削除する際にはすべてのノードがこの条件を
        満たしているかを調べ、平衡が崩れていた時は
        回転操作を行う
      
      ・回転とは、木の中の1つのノードを上にし、別のノードを
        下にするというもので、例えば、以下は右回転の例である
        ※どちらが左右なのかについては諸説ある

        大きい部分木を持ち上げて、小さい部分木を下げることで
        全体の木の高さが低くなっていることが分かる

        なお、回転操作については本目的（SQLの理解）とは
        ずれているためデータ構造の項で触れることとする
    ```

    ```mermaid
    graph TD;

    5 --> 3
    5 --> 6
    3 --> 2
    3 --> 4
    2 --> 1
    ```

    ```mermaid
    graph TD;
    3 --> 2
    3 --> 5
    2 --> 1
    5 --> 4
    5 --> 6
    ```

    ### なぜB-treeなのか(BtreeとB+tree概要)

    ```text
    ・B-treeは1つのノードがm個(m>=2)の子ノードを持つ
      いわゆる多分岐の平衡木を指す
    
    ・実際にはDBサーバの多くはB木を改良した、B+木や
      B*木を利用している(その場合も表記はB-tree)
    
    ・AVL木と比べて、ハードディスクに対するアクセス回数
      （アクセス回数=木の高さ）を減らすことができる
      という利点がある
    
    ・詳細な構造はデータ構造の項に任せ、
      ここでは簡潔に(B木とB+木の違いについて)説明する
    
    ```

    ### なぜB-treeなのか(B-tree)

    ```text
    B木の特徴
      ・葉ノード以外はm(m>=2)の子ノードを持つ
      ・葉ノードはすべて同じレベル（高さ）になる
      ・m個のキーを持つノードは、M+1個の子ノードを持つ
      ・子ノードには小さい値と大きい値が入り、
        親ノードには中間値が入る
      ・なお、ノードはキーと、子ノードのポインタを含んでいる

      
      ※キーとは、ノード内のデータをソートし、
      　そのノードがどのように子ノードに分割されるかを
        決定するために使用されるもの
        （キー＝検索したい値ならそこで検索は終了するので、
          データそのものでもある）
      
      ※各キーは、キーより小さい値を持つ子ノードと、
        大きい値を持つ子ノードの間でデータを分割する

    ```

    ```mermaid
    graph TD;
    A["|・|15|・|空|〇|"]
    B["|・| 9|・|空|〇|"]
    C["|・|23|・|空|〇|"]
    D["|〇| 3|〇| 5|〇|"]
    E["|〇|10|〇|13|〇|"]
    F["|〇|17|〇|空|〇|"]
    G["|〇|25|〇|30|〇|"]

    A --> B
    A --> C

    B --> D
    B --> E

    C --> F
    C --> G

    ```

    ### B木の検索

    ```text
    値の検索
      1 根ノードから手順を開始
      2 キーに従い、ポインタを辿り、子ノードに移動
      3 目的の値(キー)が含まれるノードにたどり着いたら
        そこで検索終了
      4 含まれていない場合、更にポインタを辿る
    ```

    ### B木の挿入

    ```text
    1 キーに空きがある場合、ノードにそのまま追加
    2 空きがない場合、新しいノードを作成する
      その際、中間値を親ノードに追加し、
      子ノードに対するポインタも親ノードに追加する
    
    3 親ノードにも空きがない場合は、
      先祖ノードに波及する
      ※根ノードまで波及した場合、木の高さが1段階増加する
        この時、分割直後の新しい根はキー1つと子を2つだけ持つ
    
    ※以下は、18と20と32を追加する場合の図である
      なお、〇は空いているポインタ。空は空いているキーを指す
    ```

    ```mermaid
    graph TD;
    A["|・|15|・|空|〇|"]
    B["|・| 9|・|空|〇|"]
    C["|・|23|・|空|〇|"]
    D["|〇| 3|〇| 5|〇|"]
    E["|〇|10|〇|13|〇|"]
    F["|〇|17|〇|空|〇|"]
    G["|〇|25|〇|30|〇|"]

    A --> B
    A --> C

    B --> D
    B --> E

    C --> F
    C --> G
    ```

    ```mermaid
    graph TD;
    A["|・|15|・|空|〇|"]
    B["|・| 9|・|空|〇|"]
    C["|・|23|・|空|〇|"]
    D["|〇| 3|〇| 5|〇|"]
    E["|〇|10|〇|13|〇|"]
    F["|〇|17|〇|18|〇|"]
    G["|〇|25|〇|30|〇|"]

    A --> B
    A --> C

    B --> D
    B --> E

    C --> F
    C --> G
    ```

    ```mermaid
    graph TD;
    A["|・|15|・|空|〇|"]
    B["|・| 9|・|空|〇|"]
    C["|・|18|・|23|・|"]
    D["|〇| 3|〇| 5|〇|"]
    E["|〇|10|〇|13|〇|"]
    F["|〇|17|〇|空|〇|"]
    G["|〇|20|〇|空|〇|"]
    H["|〇|25|〇|30|〇|"]

    A --> B
    A --> C

    B --> D
    B --> E

    C --> F
    C --> G
    C --> H
    ```

    ```mermaid
    graph TD;
    A["|・|15|・|23|〇|"]
    B["|・| 9|・|空|〇|"]
    C["|・|18|・|空|・|"]
    D["|・|30|・|空|・|"]
    E["|〇| 3|〇| 5|〇|"]
    F["|〇|10|〇|13|〇|"]
    G["|〇|17|〇|空|〇|"]
    H["|〇|20|〇|空|〇|"]
    I["|〇|25|〇|空|〇|"]
    J["|〇|32|〇|空|〇|"]

    A --> B
    A --> C
    A --> D

    B --> E
    B --> F

    C --> G
    C --> H
    
    D --> I
    D --> J
    ```

    ### B木の削除

    ```text
    葉ノードの削除
      ・2つ以上のレコードがあるならそのまま削除
      ・値が1つしかない場合、
        1 同じ親ノードからぶら下がっている別ノードから
          値をとってきて、親ノードの中間値を置き換え
        2 別ノードに持ってこれる値が無いなら、中間値を
          親から持ってきて、親の中間値を削除

          ※根まで波及する場合、高さが1つ下がることもある
      以下は25, 17の順に削除しようとした場合
    ```


    ```mermaid
    graph TD;
    A["|・|15|・|空|〇|"]
    B["|・| 9|・|空|〇|"]
    C["|・|23|・|空|〇|"]
    D["|〇| 3|〇| 5|〇|"]
    E["|〇|10|〇|13|〇|"]
    F["|〇|17|〇|空|〇|"]
    G["|〇|25|〇|30|〇|"]

    A --> B
    A --> C

    B --> D
    B --> E

    C --> F
    C --> G
    ```

    
    ```mermaid
    graph TD;
    A["|・|15|・|空|〇|"]
    B["|・| 9|・|空|〇|"]
    C["|・|23|・|空|〇|"]
    D["|〇| 3|〇| 5|〇|"]
    E["|〇|10|〇|13|〇|"]
    F["|〇|17|〇|空|〇|"]
    G["|〇|30|〇|空|〇|"]

    A --> B
    A --> C

    B --> D
    B --> E

    C --> F
    C --> G
    ```


    ```mermaid
    graph TD;
    B["|・| 9|・|15|〇|"]
    D["|〇| 3|〇| 5|〇|"]
    E["|〇|10|〇|13|〇|"]
    F["|〇|23|〇|空|30|"]

    B --> D
    B --> E
    B --> F
    ```

    ```text
    葉ノード以外の場合
      ・削除する値を、右側の部分木の最小値で置き換える
      ・置き換えに使ったことで、ノード内の値が0個になった場合
        葉ノードを削除した時と同様の処理を行う

        以下では15を削除した例を扱う
    ```

    ```mermaid
    graph TD;
    A["|・|15|・|空|〇|"]
    B["|・| 9|・|空|〇|"]
    C["|・|23|・|空|〇|"]
    D["|〇| 3|〇| 5|〇|"]
    E["|〇|10|〇|13|〇|"]
    F["|〇|17|〇|空|〇|"]
    G["|〇|25|〇|30|〇|"]

    A --> B
    A --> C

    B --> D
    B --> E

    C --> F
    C --> G
    ```

    ```mermaid
    graph TD;
    A["|・|17|・|空|〇|"]
    B["|・| 9|・|空|〇|"]
    C["|・|25|・|空|〇|"]
    D["|〇| 3|〇| 5|〇|"]
    E["|〇|10|〇|13|〇|"]
    F["|〇|23|〇|空|〇|"]
    G["|〇|30|〇|空|〇|"]

    A --> B
    A --> C

    B --> D
    B --> E

    C --> F
    C --> G
    ```

    ### なぜB-treeなのか(B+tree)

    ```text
    ・B+treeは、RDBで広く使われているアルゴリズム
    ・挿入/削除について、基本的にはB木と同じロジックを持つ
    ・ただしデータが葉ノードに格納される特徴を持つ
    ・葉ノード同士もポインタでつながっている

    値の検索
      ・B木と同様にポインタを辿るが、
        データは葉にあるので、必ず葉まで行く必要がある
    
    範囲検索
      ・たとえば12 ~ 23の範囲検索を行う場合を考える
      ・その場合、小さな方の値、すなわち12を検索する
      ・葉ノードにたどり着いたら、葉ノード間のポインタを
        辿って23を上回ったら終了する
      
      ⇒葉ノード同士がポインタでつながっているため、
        いちいちルートから検索する必要がなく
        B木より効率がいいのが特徴
    ```

    ```mermaid
    graph TD;
    A["|・|15|・|空|〇|"]
    B["|・| 9|・|空|〇|"]
    C["|・|23|・|空|〇|"]
    D["|〇| 3|〇| 5|・|"]
    E["|・| 9|〇|12|・|"]
    F["|・|15|〇|空|・|"]
    G["|・|23|〇|24|〇|"]

    A --> B
    A --> C


    subgraph Reafs
      direction LR
      D --> E
      E --> F
      F --> G
    end

    B --> Reafs
    B --> Reafs
    C --> Reafs
    C --> Reafs
    
    ```

    ### 補足(B木を視覚的に理解する)

    ```text
    B-treesのURL(B+木でないことに注意)
    https://www.cs.usfca.edu/~galles/visualization/BTree.html
    ```

## Q106　ビットマップインデックスについて知っていますか?

??? success
    ### ビットマップインデックスの概要

    ```text
    ・B木インデックスは、カーディナリティが高いデータ
      に対して作成するのがよい
    
    ・一方、カーディナリティが低いデータに対しては不向き

    bitmap index
      ・Oracle DBに備わっているindex
      ・列に格納される値ごとにbitmapを生成する
      ・たとえばactive = 0 or 1が格納されるカラムの場合、
        そのindexは2つのbitmapを維持する
      ・active = 0の行が要求された場合、DBサーバは
        0のbitmapを調べることで、行を素早く取り出せる
    
    bitmap indexの作り方(Oracle)
      create bitmap index idx_active on customer(active);
    
    bitmap indexの利点
      ・ANDやORが得意
    
    bitmap indexの欠点
      ・組み合わせが多いと、bitの桁数が多くなり、
        更新のスピードが落ちる
      ・圧縮して保存しているため、更新時に再圧縮が必要
      ・ロックが起きやすい
      ・MySQL/Mariadbでは使えない
    ```
    ### bitmapの例
    
    ```text
    
    ID 名前 血液型 性別
    1  hoge   A    1
    2  fuga   B    2
    3  buz    O    1
    4  bar    A    2
    5  foo    O    1
    6  moko  AB    2

    血液型のbitmap

    ID  1  2  3  4  5  6
    A   1  0  0  1  0  0
    B   0  1  0  0  0  0
    O   0  0  1  0  1  0
    AB  0  0  0  0  0  1

    性別のbitmap
    ID  1  2  3  4  5  6
    1   1  0  1  0  1  0
    2   0  1  0  1  0  1

    ここで、以下の様にする
    select * from user
    where 血液型 = "A";

    すると、Aのbit列は100100なので、
    1つ目と4つ目のレコードを結果セットとして返す

    また、以下の場合
    select * from user
    where 
      血液型 = "O"
      or
      性別 = "2"
    ;
    
    血液型Oに該当するbit列は
    001010
    性別が2に該当するbit列は
    010101

    この時、論理和で簡単に結果が分かる
    011111
    つまり、1番以外のレコードがヒットする
      ※B木はNOTやORを苦手とするので、これは嬉しい
    ```

    ### ロックが起きやすいわけ

    ```text
    ・保存する際の圧縮で、
      01000001のような部分のうち、00000の箇所を圧縮
      するが、それが原因で0に対する更新の際に、
      00000の行全てをロックしてしまう可能性がある
      
    ⇒OLTP(Online Transaction Processing)
      では、多数のトランザクションが他所から来るため
      向いていないかもしれない
    ```

## Q107 テキストインデックスについて知っていますか?(Mroongaを用いた日本語全文検索の導入についても)

??? success
    ### FULLTEXT INDEX

    ```text
    ・通常のIndexは、値全体に対する検索に向いている
    ・つまり、name = "hoge"といったケースの場合、
      B木で問題ない
    
    ・しかし、textベースのカラムから、
      特定の文字列を検索する全文検索には向いていない
      (例えばLIKE, Regexpはindexのメリットを受けられない。
      そのためパフォーマンスを考慮しなくても良いケース等
      に使う。その場合でも、将来的にクエリが複雑になる場合
      の移行手段について考えておくことは大切だが)
    
    ・たとえば、DBにドキュメントを格納し、
      そのドキュメントで単語、フレーズを検索したい場合
      がこれに当たる
    
    ・全文検索を行う場合、FULLTEXT INDEXを使う
    ```


    ### FULLTEXT INDEXの仕組み

    ```text
    ・FULLTEXT INDEXが作成されると、対象のカラムの文字列
      を部分分割する
    
    ・部分分割の手法は言語によって変化し、
      英語の場合、space区切り
      日本語の場合、ngramやmecabが用いられる

    ・N-gramの場合、文字列をN個ごとに分割するため
    　ノイズが発生しやすい

    ・mecabの場合、辞書をベースに形態素解析を行うので
      ノイズは生じにくいが、
      検索漏れが生じやすいという点があげられる
      （たとえば解釈が複数ある言葉や、流行りの言葉など）
    
    ・いずれにせよ、分割した文字に対して
      インデックスを作成する

    ```

    ### MySQL/MariadbにおけるFULLTEXT INDEX

    ```text
    ・MySQLでFULLTEXT INDEXを利用する場合、match()が使える
    ・LIKE()の場合、FULLTEXT INDEXが利用できないので
      速度的に不利になる
    
    構文
      ・MATCH (col1, col2, ...) AGAINST (search_modifier)
      ・search_modifierに関しては検索オプションで、
        全文検索の種類を選択可能
    ```

    ### Mecab + Mroongaを使った日本語全文検索機能

    ```text
    ・MySQL/Mariadbで形態素解析を用いる場合、
      Mroongaというストレージエンジンを使うのが分かりやすい
    
    ・これは日本全文検索を実現するエンジンで、
      トークナイザをMecabにすることで、形態素解析が利用可能
    ```

    ### Mecabのinstall

    ```bash
    # Ubuntuで行うことに注意

    # Mecabのinstall(コマンドの意味については工事中)
    $ sudo apt install mecab
    $ sudo apt install libmecab-dev
    $ sudo apt insdtall mecab-ipadic-utf8

    # 固有表現に強い辞書のmecab-ipadic-neologdをinstall
    $ git clone https://github.com/neologd/mecab-ipadic-neologd.git
    $ cd mecab-ipadic-neologd
    $ sudo bin/install-mecab-ipadic-neologd

    # mecab-ipadic-neologdのinstall場所を
    # ipadicと同階層に移動
    $ sudo mv /usr/lib/x86_64-linux-gnu/mecab/dic/mecab-ipadic-neologd /var/lib/mecab/dic

    # mecabrcを編集し、辞書をipadicからneologdに変更
    ; dicdir = /var/lib/mecab/dic/debian 
    dicdir = /var/lib/mecab/dic/mecab-ipadic-neologd 

    # mecabを実行して、installされているか確認
    # 加えて固有名詞に強くなっているかも確認

    # ポケモン        名詞,固有名詞,一般,*,*,*,Pokemon!,ポケモン,ポケモン
    # や      助詞,並立助詞,*,*,*,*,や,ヤ,ヤ
    # デジモン        名詞,固有名詞,人名,一般,*,*,デジモン,デジモン,デジモン
    # は      助詞,係助詞,*,*,*,*,は,ハ,ワ
    # 固有名詞        名詞,一般,*,*,*,*,固有名詞,コユウメイシ,コユ ーメイシ
    # の      助詞,連体化,*,*,*,*,の,ノ,ノ
    # 1つ     名詞,一般,*,*,*,*,1つ,ヒトツ,ヒトツ
    # だ      助動詞,*,*,*,特殊・ダ,基本形,だ,ダ,ダ

    # 参考
    # https://qiita.com/kado_u/items/e736600f8d295afb8bd9
    ```

    ### Mroongaのinstall

    ```bash
    # 公式を見るのが早い
    # https://mroonga.org/ja/docs/characteristic.html

    # 此方も参考にした
    # https://gigazine.net/news/20210825-articles-search-system/

    # universeリポジトリと
    # セキュリティアップデートリポジトリを有効にする

    $ sudo apt-get install -y -V software-properties-common lsb-release
    $ sudo add-apt-repository -y universe
    $ sudo add-apt-repository "deb http://security.ubuntu.com/ubuntu $(lsb_release --short --codename)-security main restricted"

    # PPAをシステムに追加

    $ sudo add-apt-repository -y ppa:groonga/ppa
    $ sudo apt-get update

    # MariaDB用のMroongaをinstall
    # MySQL用の場合、mariadb -> mysqlに変えるだけ
    $ sudo apt-get install -y -V mariadb-server-mroonga

    # TokenizerとしてMecabを使うため
    # groonga-tokenizer-mecabをinstall
    $ sudo apt-get install -y -V groonga-tokenizer-mecab

    # mroongaストレージエンジンの
    # defaultのtokenizerをMecabに変更

    $ sudo vim /etc/mysql/mariadb.conf.d/50-server.cnf
    
    # [mysqlId]という項目内に以下を追加
    mroonga_default_tokenizer=TokenMecab
    ```

    ```sql
    -- mroongaの動作確認

    select mroonga_command("tokenize TokenMecab '東京都'");
    /*
    +---------------------------------------------------------------------------------------+
    | mroonga_command("tokenize TokenMecab '東京都'")                                       |
    +---------------------------------------------------------------------------------------+
    | [{"value":"東京都","position":0,"force_prefix":false,"force_prefix_search":false}]    |
    +---------------------------------------------------------------------------------------+
    */

    show engines \G;

    /*
    ******* 7. row ***
          Engine: Mroonga
        Support: YES
        Comment: CJK-ready fulltext search, column store
    Transactions: NO
              XA: NO
      Savepoints: NO
    */


    ```

    ### Mroongaを試してみる

    ```sql
    -- 
    create temporary table proverb (
      id int auto_increment not null primary key,
      name text,
      fulltext index idx_name (name)
    ) ENGINE = Mroonga
    ;

    insert into  proverb
    values 
    (1, '隣の客はよく柿食う客だ'), 
    (2, '後ろの客はよく柿食う客だ'),
    (3, '隣の客はよく西瓜食う客だ'),
    (4, 'すもももももももものうち')
    ;

    select *
    from proverb
    where
      match (name)
      against("+柿" in boolean mode);

    /*
    +----+--------------------------------------+
    | id | name                                 |
    +----+--------------------------------------+
    |  1 | 隣の客はよく柿食う客だ               |
    |  2 | 後ろの客はよく柿食う客だ             |
    +----+--------------------------------------+
    2 rows in set (0.003 sec)
    */
    ```

## Q108 Mroongaの使い方について知っていますか?

??? success
    ### 注意点

    ```text
    ・最低限の機能のみを紹介する
    ```

    ### バージョン確認

    ```sql
    show variables like "mroonga_version";

    /*
    +-----------------+-------+
    | Variable_name   | Value |
    +-----------------+-------+
    | mroonga_version | 13.05 |
    +-----------------+-------+
    1 row in set (0.008 sec)
    */
    ```

    ### モード

    ```text
    ・ストレージモードとラッパーモードの2つが存在する

    ストレージモード
      ・全文検索機能に加え、その他の機能についても
        Groongaを利用する
    
    ラッパーモード
      ・全文検索機能のみGronngaの機能を利用する
      ・InnoDBをMroongaでラップすることで
        高速で信頼性のあるDBとして利用可能
      ・全文検索機能以外は既存のストレージエンジンが処理する
      ・すべてのデータがMroongaを通るので、
        ここで、全文検索用のindexを作成する
    ```

    ### ストレージモードでテーブルを作成

    ```sql

    /*
    engine = engine名でストレージエンジンを選択可能
    ----------------
    fulltext index idx名 (column名)で
    fulltext indexを作成可能

    ------------------
    default charset utf8はテーブルのデフォルトの文字セット
    を変更するために使う。
    utf8mb4は絵文字やサロゲートペア文字に対して便利だが
    今回は使うつもりがないので、ストレージスペース削減
    の目的でutf8(3byteまで)とした
    それに伴い照合順序もutf8mb3_general_ciになっている

    後から変更したい場合は以下の様にできる
    なお、COLLATEは照合順序に関係している
    alter table <table_name>
    convert to charset 
    set utf8mb4
    collate
    utf8mb4_general_ci
    ;
    */
    create temporary table diaries (
      id int primary key auto_increment,
      content varchar(255),
      fulltext index idx_content (content)
    ) engine = Mroonga default charset utf8
    ;
    ```

    ### データを投入し、全文検索

    ```sql
    insert into diaries
    (content)
    values
    ("It'll be fine tomorrow."),
    ("It'll rain tomorrow.")
    ;

    /*
    selectやorder byの箇所にカラム名を書く代わりに
    match ... againstを使うことができる
    */
    select *
    from diaries
    where
      match(content)
      against("+fine" in boolean mode)
    ;

    /*
    +----+-------------------------+
    | id | content                 |
    +----+-------------------------+
    |  1 | It'll be fine tomorrow. |
    +----+-------------------------+
    1 row in set (0.028 sec)
    */
    ```

    ### ORDER BYとしてmatch/againstを利用した場合

    ```sql
    insert into diaries (content)
    values
    ("It's fine today. It'll be fine tomorrow as well."),
    ("It's fine today. But it'll rain tomorrow.")
    ;

    /*
    matchした回数でsortを行ってくれる
    ascだと少ないものが上位に来るのでdescを用いた
    +については後述
    */
    select
      *,
      match (content) 
      against ("+fine" in boolean mode) as fine
    from diaries
    order by
      match (content)
      against("+fine") desc
    ;

    /*
    +----+--------------------------------------------------+------+
    | id | content                                          | fine |
    +----+--------------------------------------------------+------+
    |  3 | It's fine today. It'll be fine tomorrow as well. |    2 |
    |  1 | It'll be fine tomorrow.                          |    1 |
    |  4 | It's fine today. But it'll rain tomorrow.        |    1 |
    |  2 | It'll rain tomorrow.                             |    0 |
    +----+--------------------------------------------------+------+
    4 rows in set (0.004 sec)
    */
    ```

    ### parserについて

    ```text
    ・mysql/mariadbは以下のような構文で
      全文検索用のパーサを指定する
      fulltext index (content) with parser <parser_name>

    ・しかし上記の場合、あらかじめすべてのパーサを
      mysql側に追加しておく必要がある

    ・Groonga側に動的に追加されるTokenizerを使用するため
      には以下のような独自の構文を使う

      fulltext index (content) 
      COMMENT 'tokenizer "TokenMecab"' 

      デフォルトの場合、tokenizerはTokenBigramである
    
    ・デフォルトのパーサを恒久的に変えたい場合
    　my.cnfで以下の様にする
      [mysqld]
      mroonga_default_tokenizer=TokenMecab

      set global 
      mroonga_default_tokenizer=TokenMecab;
      でも可能だが、再起動時に設定が消えることに注意
    ```

    ### 類似検索(in natural language mode)

    ```text
    in boolean mode
      一致した場合に取得
    
    in natural language mode
      類似している場合に取得
    ```

    ```sql
    create temporary table similarities (
      id int primary key auto_increment,
      title varchar(32),
      content varchar(255),
      fulltext index idx_content (content)
    ) engine = Mroonga default charset utf8;

    insert into similarities 
    (title, content)
    values
    ("東京", "私は東京に住んでいます。家賃は8万円です。"),
    ("岐阜", "私は岐阜に住んでいます。家賃は3万円です。"),
    ("その他", "類似文書"),
    ("地方", "三食お風呂付、敷金なし")
    ;

    select
      title
    from
      similarities
    where 
      match (content)
      against (
        '私は大阪に住んでいます。家賃は7万円です。' IN NATURAL LANGUAGE MODE
    )
    ;

    /*
    +--------+
    | title  |
    +--------+
    | 東京   |
    | 岐阜   |
    +--------+
    2 rows in set (0.016 sec)
    */
    ```

    ### Boolean mode

    ```text
    ・通常in natural lauguage modeより優れている
    ```

    ```sql
    create temporary table books (
      id int primary key auto_increment,
      title text,
      fulltext index title_index (title)
    ) engine=Mroonga default charset=utf8mb4
    ;

    insert into books (title)
    values
    ("Professional MySQL"),
    ("MySQL for Professional"),
    ("Mroonga = MySQL + Groonga")
    ;

    -- 演算子がない場合、キーワードのうちいずれかが
    -- 含まれていればヒット扱い

    select 
      title
    from 
      books
    where 
      match (title)
      against ("Mroonga for" in boolean mode)
    ;

    /*
    +---------------------------+
    | title                     |
    +---------------------------+
    | Mroonga = MySQL + Groonga |
    | MySQL for Professional    |
    +---------------------------+
    2 rows in set (0.020 sec)
    */

    -- keyword OR keyword
    -- いずれかが含まれていればhit
    -- ORは必ず大文字にする必要がある

    select
      title
    from
      books
    where
      match(title)
      against ("Mroonga OR for" in boolean mode)
    ;

    /*
    +---------------------------+
    | title                     |
    +---------------------------+
    | Mroonga = MySQL + Groonga |
    | MySQL for Professional    |
    +---------------------------+
    2 rows in set (0.002 sec)
    */

    -- + keyword
    -- 必ず含まれているものを指定する

    select title
    from books
    where
      match (title)
      against ("+MySQL +Groonga" in boolean mode)
    ;

    /*
    +---------------------------+
    | title                     |
    +---------------------------+
    | Mroonga = MySQL + Groonga |
    +---------------------------+
    1 row in set (0.002 sec)
    */

    -- -keyword
    -- 含まれてはいけないものを指定する

    select title
    from books
    where 
      match(title)
      against ("+MySQL -Mroonga" in boolean mode)
    ;

    /*
    +------------------------+
    | title                  |
    +------------------------+
    | Professional MySQL     |
    | MySQL for Professional |
    +------------------------+
    2 rows in set (0.003 sec)
    */

    -- prefix*
    -- prefixから始まるkeywordを含んでいる場合

    select title
    from books
    where
      match(title)
      against ("+M*" in boolean mode)
    ;

    /*
    +---------------------------+
    | title                     |
    +---------------------------+
    | Mroonga = MySQL + Groonga |
    | MySQL for Professional    |
    | Professional MySQL        |
    +---------------------------+
    3 rows in set (0.002 sec)

    */

    -- "phrase"
    -- フレーズが含まれていることが条件(space等を含む時に)

    select title
    from books
    where 
      match(title)
      against ('+"Professional MySQL"' in boolean mode)
    ;

    /*
    +--------------------+
    | title              |
    +--------------------+
    | Professional MySQL |
    +--------------------+
    1 row in set (0.002 sec)
    */

    -- () 式をグルーピングする
    /*
    +(Groonga OR Mroonga) +MySQLなら
    GroongaまたはMroongaのいずれかが含まれ
    MySQLが含まれている個所がhitする
    */

    select title
    from books
    where
      match(title)
      against(
        '+(Groonga OR Mronnga) +MySQL' 
        in boolean mode
      )
    ;

    /*
    +---------------------------+
    | title                     |
    +---------------------------+
    | Mroonga = MySQL + Groonga |
    +---------------------------+
    1 row in set (0.004 sec)
    */
    ```

    ### プラグマを使う

    ```sql
    -- プラグマとはクエリのパース方法を指定するためのもの
    -- 先頭に*を付けることで、プラグマ扱いになる

    /*
    Dプラグマ
      デフォルトの演算子を指定可能
      DOR -> デフォルト演算子はOR
      D+ --> デフォルト演算子はAND
      D- --> デフォルト演算子はNOT
    */

    select title
    from books
    where
      match(title)
      against ("*DOR MySQL Mroonga" in boolean mode)
    ;
    /*
    +---------------------------+
    | title                     |
    +---------------------------+
    | Professional MySQL        |
    | Mroonga = MySQL + Groonga |
    | MySQL for Professional    |
    +---------------------------+
    3 rows in set (0.004 sec)
    */

    select title
    from books
    where
      match(title)
      against ("*D+ MySQL Mroonga" in boolean mode)
    ;

    /*
    +---------------------------+
    | title                     |
    +---------------------------+
    | Mroonga = MySQL + Groonga |
    +---------------------------+
    1 row in set (0.002 sec)
    */

    -- MySQLは含むが、Mroongaは含まない
    select title
    from books
    where
      match(title)
      against ("*D- +MySQL Mroonga" in boolean mode)
    ;

    /*
    +------------------------+
    | title                  |
    +------------------------+
    | Professional MySQL     |
    | MySQL for Professional |
    +------------------------+
    2 rows in set (0.003 sec)
    */

    ```

    ### 最適化について

    ```text
    ・ストレージエンジンを使用した場合、Mroongaは指定された
      カラムの値だけを取得できる
    
    ・InnoDBの場合、1つのカラムが必要な場合でも
      実際にはすべてのカラムを取得しているので、
      Mroongaに切り替えることで、処理とI/Oが減少する
    -----------------------

    ・count(*)を処理する際、mysqlは不必要にもかかわらず
      全てのカラムの値を要求する
    
    ・Mroongaの場合、カラムの値を取得しないので
      時間の短縮が見込める
    ```

    ```sql
    select count(*)
    from books
    where
      match(title)
      against("+MySQL" in boolean mode)
    ;

    /*
    +----------+
    | count(*) |
    +----------+
    |        3 |
    +----------+
    1 row in set (0.003 sec)
    */

    -- count(*)の最適化が為された場合はvalueのフラグが立つ
    show status like "Mroonga_count_skip";     
    /*
    +--------------------+-------+
    | Variable_name      | Value |
    +--------------------+-------+
    | Mroonga_count_skip | 1     |
    +--------------------+-------+
    1 row in set (0.001 sec)
    */

    -- order by limit
    -- Mroongaはorder byとlimitを処理し、
    -- mysql/mariadbには処理した結果を渡すので
    -- matchレコード数が多い時、短縮が見込める

    select title
    from books
    where
      match (title)
      against ("+MySQL" in boolean mode)
    order by id limit 1
    ;

    -- 最適化が為された場合、フラグが1になる
    show status like "Mroonga_fast_order_limit";

    /*
    +--------------------------+-------+
    | Variable_name            | Value |
    +--------------------------+-------+
    | Mroonga_fast_order_limit | 1     |
    +--------------------------+-------+
    1 row in set (0.000 sec)
    */
    ```

## Q109 hashインデックスについて知っていますか?

??? success
    ### hash-index概要

    ```text
    ・データ格納時に、データにhash関数を実行し、
      配置場所を決める
    
    ・探す際も、同じhash関数を実行することで
      しまった場所を特定する
    
    メリット
      ・辿る必要がないので検索が早い
      ・index-sizeも小さい（無駄がないので）
    
    デメリット
      ・完全一致(=)にしか使えない
      ・数値が1違っても、hashは全く異なるので、
        sortや範囲検索も不可能
    ```

    !!! info
        ### 宇宙船演算子?

        ```text
        ・hashインデックスは、
          実は`<=>`に対しても使うことができる
        
        ・<=>はmysql/mariadb固有の機能で、
          ANSI標準ではない
        
        ・通常NULLはNULL = NULLのように比較できず
          is NULLやis not NULLを使う必要がある
        
        ・<=>はこれを解決する
        ・そのためNULL安全等価演算子とも呼ばれる
        ```

        ```sql
        select 
          1 <=> 1,
          1 <=> 0,
          null <=> null,
          null <=> 1
        ;

        -- 見ての通り、nullと比較しても結果がnullにならない
        /*
        +---------+---------+---------------+------------+
        | 1 <=> 1 | 1 <=> 0 | null <=> null | null <=> 1 |
        +---------+---------+---------------+------------+
        |       1 |       0 |             1 |          0 |
        +---------+---------+---------------+------------+
        1 row in set (0.001 sec)
        */
        ```

## Q110 インデックスの使い方について知識はありますか?

??? success
    ### 実行プランから最適なプランを実行しているか確認

    ```text
    ・explainを用いると、クエリの実行プランをDBサーバに
    　問い合わすことができる

    ・クエリ自体は実行されないので、重いクエリを実行しようと
      している場合でも安全
    ```

    ```sql
    -- 以下のクエリを考える
    select
      customer_id, first_name, last_name
    from
      customer
    where
      first_name like "S%"
      and
      last_name like "P%"
    ;
    /*
    +-------------+------------+-----------+
    | customer_id | first_name | last_name |
    +-------------+------------+-----------+
    |          84 | SARA       | PERRY     |
    |         197 | SUE        | PETERS    |
    |         167 | SALLY      | PIERCE    |
    +-------------+------------+-----------+
    3 rows in set (0.002 sec)
    */

    /*
    first_name, last_nameの順に条件に使用しているので
    idx (first_name, last_name)のような複合インデックスや
    idx (first_name)
    idx (last_name)
    があればよさそうに思える

    customerテーブルの行をすべてスキャンするのはまずい
    */
    ```

    ```sql
    -- explainを使う

    explain
    select
      customer_id, first_name, last_name
    from
      customer
    where
      first_name like "S%"
      and
      last_name like "P%"
    \G
    ;

    /*
    possible_keyssからidx_last_nameが選択でき、
    keyからそのindexが用いられたことがわかる
    しかし今回の場合、first_name, last_nameで
    indexを使ってほしい
    
    またtypeを見ると、rangeとあることが分かる
    これは、範囲スキャンが活用されていることを意味する
    つまり、indexがB+木である恩恵を十分に受けている
    ⇒1行ずつ取得していくのではなく、
      最小値をまず取得し、葉ノードを伝っているという意味
    */
    /*
    ******** 1. row *******
              id: 1
      select_type: SIMPLE
            table: customer
            type: range
    possible_keys: idx_last_name
              key: idx_last_name
          key_len: 182
              ref: NULL
            rows: 28
            Extra: Using index condition; Using where
    */
    ```

    ```sql
    -- indexを加えて考えてみる

    alter table customer
    add index idx_full_name (first_name, last_name)
    ;

    /*
    ************** 1. row ****
              id: 1
      select_type: SIMPLE
            table: customer
            type: range
    possible_keys: idx_last_name,idx_full_name
              key: idx_full_name
          key_len: 364
              ref: NULL
            rows: 35
            Extra: Using where; Using index
    1 row in set (0.008 sec)
    */

    -- 実行結果は0.001秒減少したが、誤差の範囲
    /*
    +-------------+------------+-----------+
    | customer_id | first_name | last_name |
    +-------------+------------+-----------+
    |         167 | SALLY      | PIERCE    |
    |          84 | SARA       | PERRY     |
    |         197 | SUE        | PETERS    |
    +-------------+------------+-----------+
    3 rows in set (0.001 sec)
    */
    ```

## Q111 インデックスの欠点について知っていますか?

??? success
    ### 多すぎても少なすぎてもいけない

    ```text
    ・特定のテーブルに対して、追加/削除を行うと
      B+treeを調整する必要がある
    
    ・insertの場合、この影響は顕著で
      B+treeはノードに空きがない場合、
      ノード分割を行う必要があり、そのタスクは重い
    
    ・indexが増えるほど、所要時間は増加する
    ```

    ### 補足

    ```text
    ・大量のデータをロードする際、一時的に
      全てのindexを削除するというやり方がある
    
    ・これはB+treeに対する更新クエリの所要時間が
      indexの数と関係しているためである
    
    ・insert文はwhere句等を持たず、indexの恩恵を
      受けられないため、スピードアップが可能
      (DWH: data warehouseではしばしば用いられる)
    
    ・上記の手順は以下の通り
    1 現在のテーブルのindexを削除する
    2 データを挿入する
    3 indexを元に戻す

    ※ただし、indexの再構築時間を考慮に入れる
      必要がある
    ※また、複数人が使用する場合は、
      インデックス再構築中の影響を加味する必要がある
    ```

    ### インデックス戦略

    ```text
    基本
      ・インデックスは本当に必要になったときのみ追加する
    
    DWHの場合
    　・データを夜間に読み込む前にデータを削除
    　・業務時間になる前にインデックスを再設定する
      (※業務時間中は、ユーザがレポートを作成したり、
      　臨機応変にクエリを実行するためにインデックスが必要) 
    
    メンテナンス時のみindexが必要な場合
      ・メンテナンス中のみインデックスを追加する
    
    一般
      ・主キーにindexがついていることを確認する
      ・主キー制約とは別の順序で別のindexを作成してもいい
      ・データの取得に頻繁に使われる列にindexをつける
      (日付列やカーディナリティが高い短い文字列が候補)
      ・外部キー制約に関連している全ての列にindexをつける
      (mysqlの場合、子テーブル側に対しても自動で適用される)
      (親テーブルについては元よりunique or primaryなので
      考慮する余地はないと思われる)

      子テーブル側
        ・DBサーバは親テーブルの行が削除/更新される時、
          整合性を保つため
          子テーブルに行が存在しないか確認する
        ・つまり、子テーブル側にindexがついていないと、
        　テーブル全体をスキャンすることになる
      
      親テーブル側
        ・DBサーバは子テーブル側に行を挿入する際、
          整合性を保つため親テーブルに行が存在するか確認する
    
    作成後
      ・テーブルで実際に実行されるクエリをexplainで調べ
      　実行プランを調べて調整する
    ```

## Q112 制約について知っていますか?

??? success
    ### 制約一覧

    ```text
    主キー制約(primary key constraint)
      ・レコードの一意性を保証する
    
    外部キー制約(foreign key constriant)
      ・1つ以上の列の値を、別のテーブルの主キーもしくは
        ユニーク制約がある列に含まれている値だけに制限する
        (つまり、親側[参照元]は主キーもしくは
        一意性制約がついている列である必要がある。)
    
    一意性制約(unique constraint)
      ・1つ以上の列の値を一意な値に制限する
      ・ただしNULLは許容される
    
    検査制約(check constraint)
      ・列に設定できる値を制限する
    ```

    ### 制約を使わないとどうなるか

    ```text
    ・DBの一貫性が疑問視される
    
    例)
      customerテーブルで顧客のIDを変更する
      rentalテーブルにも顧客のIDがあるがこれは変更されない
      ⇒有効な顧客データを指さないrentalデータが残る

      このようなデータを孤立行という

      主キー制約と外部キー制約が設定されている場合、
      他のテーブルで参照されているデータを変更/削除
      仕様とした際、errorもしくは変更を伝播させることが可能
    ```

    ### 制約の作り方を確認する

    ```sql
    -- create table時に作成する場合

    /*
    PRIMARY KEY (`customer_id`)
      主キー制約を作成する
    
    KEY `idx_fk_store_id` (`store_id`)
      インデックスの作成方法
    
    CONSTRAINT `fk_customer_address` 
    FOREIGN KEY (`address_id`) 
    REFERENCES `address` (`address_id`) 
    ON UPDATE CASCADE
      外部キーの作成方法
    
    構文
      [constraint <外部キーの名前>]
      foreign key (外部キーの対象になるカラム)
      references <参照先テーブル> (参照先テーブルのカラム)
      [on delete reference_option]
      [on update reference_option]

    */
    show create table customer;
    /*
    +----------+----------------------------
    | Table    | Create Table    
    +----------+----------------------------
    | customer | CREATE TABLE `customer` (
      `customer_id` smallint(5) unsigned NOT NULL AUTO_INCREMENT,
      `store_id` tinyint(3) unsigned NOT NULL,
      `first_name` varchar(45) NOT NULL,
      `last_name` varchar(45) NOT NULL,
      `email` varchar(50) DEFAULT NULL,
      `address_id` smallint(5) unsigned NOT NULL,
      `active` tinyint(1) NOT NULL DEFAULT 1,
      `create_date` datetime NOT NULL,
      `last_update` timestamp NOT NULL DEFAULT current_timestamp() ON UPDATE current_timestamp(),
      PRIMARY KEY (`customer_id`),
      KEY `idx_fk_store_id` (`store_id`),
      KEY `idx_fk_address_id` (`address_id`),
      KEY `idx_last_name` (`last_name`),
      KEY `idx_full_name` (`first_name`,`last_name`),
      CONSTRAINT `fk_customer_address` FOREIGN KEY (`address_id`) REFERENCES `address` (`address_id`) ON UPDATE CASCADE,
      CONSTRAINT `fk_customer_store` FOREIGN KEY (`store_id`) REFERENCES `store` (`store_id`) ON UPDATE CASCADE
    ) ENGINE=InnoDB AUTO_INCREMENT=600 DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_general_ci |
    +----------+---------------------------------------
    1 row in set (0.000 sec)
    */

    -- 後から設定する場合

    -- primary key
    alter table customer
    add primary key (column1);

    -- index
    alter table customer
    add index idx_column1 (column1);

    -- foreign key
    alter table customer
    add constraint fk_customer_address
    foreign key (address_id)
    references address (address_id)
    on delete restrict
    on update cascade
    ;

    -- unique key
    alter table customer
    add constraint UK1
    unique (column1)
    ;

    -- check
    alter table customer
    add constraint CK1
    check (column1 > 0)
    ;
    ```

    ### 外部キー制約について

    ```text
    on delete restrict
      ・親テーブルの行を削除した時に
        関連する子テーブルのデータがあればErrorを出す
      ・親テーブルの行を更新した時に
        更新前のdataと紐づく子テーブルのデータがあればError
      ⇒親テーブルから行を削除/更新する際、孤立行の発生を防ぐ
    
    on delete/update cascade
      ・親テーブルの行を削除した時に、
        関連する子テーブルのデータも自動的に削除する
      ・親テーブルの行を更新した時に、
        関連する子テーブルのデータも自動的に置き換える
    ⇒自動的に更新されることを連鎖(cascade)と呼ぶ
    
    on delete/update set null
      ・親テーブルの行を削除した時に、
        関連する子テーブルのデータがNULLに置き換わる
      ・親テーブルの行を更新した時に、
        更新前のdataと紐づく子テーブルのデータはnullになる
    
    no action
      ・restrictと同じ。
      ・デフォルト値であり、明示的に指定もできる
      ・mysql/mariadbの場合、遅延チェックも行わないので
        本当に同じ
    
    ※当然、deleteとupdateで異なるオプションを付けることも
      可能
    ```

    !!! warning
        ### 見落としやすい注意点

        ```text
        ・一時テーブルに外部キー制約はつけられない
        
        ・親子は同じストレージエンジンを使用する必要がある
        
        ・参照先/元の型は一致している必要がある。たとえば
          親でdouble、子でfloatを使用するなどはダメ
        ```


    ### restrict

    ```sql
    -- 試してみる

    create table parent (
      id int primary key auto_increment,
      last_name varchar(30)
    );

    create table children (
      id int primary key auto_increment,
      parent_id int,
      first_name varchar(30),
      constraint fk_children_parent 
      foreign key (parent_id)
      references parent (id)
      on delete restrict
      on update restrict
    );

    insert into parent
    (last_name)
    values
    ("琴葉"), ("結月"), ("紲星")
    ;

    insert into children
    (parent_id, first_name)
    values
    (1, "茜"),
    (1, "葵"),
    (2, "ゆかり"),
    (3, "あかり")
    ;

    select
      concat(last_name, " ", first_name) full_name
    from parent p
    inner join children c
    on p.id = c.parent_id
    ;

    /*
    +------------------+
    | full_name        |
    +------------------+
    | 琴葉 茜          |
    | 琴葉 葵          |
    | 結月 ゆかり      |
    | 紲星 あかり      |
    +------------------+
    4 rows in set (0.002 sec)
    */

    -- 外部キー制約にひっかかる削除(子が参照している)
    delete from parent
    where id = 1
    ;
    /*
    Cannot delete or update a parent row: 
    a foreign key constraint fails 
    (`sakila`.`children`, CONSTRAINT `fk_children_parent` 
    FOREIGN KEY (`parent_id`) REFERENCES `parent` (`id`))
    */

    -- 外部キー制約にひっかかる更新(子が参照している)
    update
      parent p
    set
      p.id = 4
    where
      p.id = 1
    ;

    /*
    Cannot delete or update a parent row: 
    a foreign key constraint fails (`sakila`.`children`, 
    CONSTRAINT `fk_children_parent` FOREIGN KEY 
    (`parent_id`) REFERENCES `parent` (`id`))
    */

    -- 外部キー制約にひっかかる挿入(親にないのに挿入)

    insert into children
    (parent_id, first_name)
    values
    (4, "ユキ")
    ;

    /*
    Cannot add or update a child row: a foreign key 
    constraint fails (`sakila`.`children`, CONSTRAINT 
    `fk_children_parent` FOREIGN KEY (`parent_id`) 
    REFERENCES `parent` (`id`))
    */

    -- parentから消すと当然外部キー制約にひっかかる
    drop table children;
    drop table parent;
    ```

    ### cascade

    ```sql
    create table parent (
      id int primary key auto_increment,
      last_name varchar(30)
    );

    create table children (
      id int primary key auto_increment,
      parent_id int,
      first_name varchar(30),
      constraint fk_children_parent 
      foreign key (parent_id)
      references parent (id)
      on delete cascade
      on update cascade
    );

    insert into parent
    (last_name)
    values
    ("琴葉"), ("結月"), ("紲星")
    ;

    insert into children
    (parent_id, first_name)
    values
    (1, "茜"),
    (1, "葵"),
    (2, "ゆかり"),
    (3, "あかり")
    ;

    -- 外部キー制約にひっかかる削除
    delete from parent
    where id = 1
    ;

    -- parent_id = 1に関連する子テーブルの行が消えている
    /*
    +----+-----------+------------+
    | id | parent_id | first_name |
    +----+-----------+------------+
    |  3 |         2 | ゆかり     |
    |  4 |         3 | あかり     |
    +----+-----------+------------+
    2 rows in set (0.001 sec)
    */

    -- 外部キー制約にひっかかる更新
    update parent p
    set id = 4
    where id = 2
    ;

    -- parent_id = 2に関連する行が更新されている
    /*
    +----+-----------+------------+
    | id | parent_id | first_name |
    +----+-----------+------------+
    |  3 |         4 | ゆかり     |
    |  4 |         3 | あかり     |
    +----+-----------+------------+
    2 rows in set (0.000 sec)
    */

    -- ※insertの場合は同じなので省略(set nullの場合も)
    ```

    ### set null

    ```sql
    create table parent (
      id int primary key auto_increment,
      last_name varchar(30)
    );

    create table children (
      id int primary key auto_increment,
      parent_id int,
      first_name varchar(30),
      constraint fk_children_parent 
      foreign key (parent_id)
      references parent (id)
      on delete set null
      on update set null
    );

    insert into parent
    (last_name)
    values
    ("琴葉"), ("結月"), ("紲星")
    ;

    insert into children
    (parent_id, first_name)
    values
    (1, "茜"),
    (1, "葵"),
    (2, "ゆかり"),
    (3, "あかり")
    ;

    --　外部キー制約に抵触するdelete
    delete from parent
    where id = 1
    ;

    select * from children;
    -- 参照している個所がNULLになる
    /*
    +----+-----------+------------+
    | id | parent_id | first_name |
    +----+-----------+------------+
    |  1 |      NULL | 茜         |
    |  2 |      NULL | 葵         |
    |  3 |         2 | ゆかり     |
    |  4 |         3 | あかり     |
    +----+-----------+------------+
    4 rows in set (0.001 sec)
    */

    -- 外部キー制約に抵触するupdate
    update parent
    set id = 4
    where id = 2
    ;

    select * from children;
    -- 同じく参照している部分がnullになる
    /*
    +----+-----------+------------+
    | id | parent_id | first_name |
    +----+-----------+------------+
    |  1 |      NULL | 茜         |
    |  2 |      NULL | 葵         |
    |  3 |      NULL | ゆかり     |
    |  4 |         3 | あかり     |
    +----+-----------+------------+
    4 rows in set (0.000 sec)
    */
    ```

    !!! warning
        ### 外部キー制約の変更

        ```text
        ・mysql/mariadbにおいて
          外部キー制約をmodifierで変更することはできない。
        　alter tableを使う場合は、一度dropしてから
        　変更内容をaddする必要がある
        ```
    
    ### 外部キーとMySQL

    ```sql
    /*
    参照元（親側）の場合、主キーor一意性制約の列であること
    からすでにindexが備わっている

    一方、子テーブルの参照列に対して、
    mysql/mariadbは外部キー制約作成時に自動的にindexを貼る

    ただし、postgresqlなどでは、この仕様は異なる
    （子テーブルに対してインデックスは貼られない）
    */
    create table parent (
      id int primary key auto_increment,
      salary_group int unique,
      last_name varchar(30)
    );

    create table children (
      id int primary key auto_increment,
      parent_id int,
      first_name varchar(30),
      constraint fk_children_parent 
      foreign key (parent_id)
      references parent (salary_group)
      on delete set null
      on update set null
    );

    show create table children;
    -- KEY `fk_children_parent` (`parent_id`)

    /*
    +----------+--------------
    | Table    | Create Table  
    +----------+-------
    | children | CREATE TABLE `children` (
      `id` int(11) NOT NULL AUTO_INCREMENT,
      `parent_id` int(11) DEFAULT NULL,
      `first_name` varchar(30) DEFAULT NULL,
      PRIMARY KEY (`id`),
      KEY `fk_children_parent` (`parent_id`),
      CONSTRAINT `fk_children_parent` FOREIGN KEY (`parent_id`) >
    ) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_gene>
    +----------+---------
    */
    ```

    ### ユニーク制約とcheck制約について(基本)

    ```sql
    -- value unique等ともできるが、show create table
    -- 式の記法に合わせた

    create temporary table number (
      id int primary key auto_increment,
      value int check (value > 0),
      unique key idx_val (value)
    );

    insert into number
    (value)
    values
    (100), (200), (300)
    ;

    -- unique制約
    insert into number (value)
    values (100);
    --  Duplicate entry '100' for key 'idx_val' 

    -- Check制約
    update number
    set
      value = -100
    where
      value = 100
    ;
    -- CONSTRAINT `number.value` 
    -- failed for `sakila`.`number`
    ```

    ### check制約の種類

    ```sql
    /*
    カラムレベルの制約
      ・a int check (a > 2)
      ・参照できるのは定義されているカラムのみ
    
    テーブルレベルの制約
      ・constraint a_greater check (a > b)
      ・テーブル内のカラムを参照できる
      ・下記の例のようにforward参照をすることも可能
        （また定義されていないカラムに対しても定義可能）
    */
    create temporary table t1 (
      constraint a_greater check (a > b),
      a int check (a > 2),
      b int check (b > 2)
    );

    insert into t1 
    (a, b)
    values
    (2, 3)
    ;
    -- CONSTRAINT `t1.a` failed for `sakila`.`t1`

    insert into t1
    (a, b)
    values
    (3, 4)
    ;
    -- CONSTRAINT `a_greater` failed for `sakila`.`t1`
    ```

    !!! warning
        ### そもそもやらないと思うけど

        ```text
        ・auto_increment列に対して、check制約を適用する
        　ことは許可されていない
        ```