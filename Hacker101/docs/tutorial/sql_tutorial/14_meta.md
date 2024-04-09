# クイズ(14:メタ&動的)

## Q117 メタデータについて知っていますか?

??? success
    ### メタデータ概要

    ```text
    ・DBサーバは、データの他に、
      テーブル構造、ビュー、インデックスに関する情報を
      保存しておく必要がある
    
    ⇒テーブルに格納されるデータ以外の情報を
      metadata(メタデータという)
    ```

    ### メタデータの例

    ```text
    ・テーブル名
    ・テーブルのストレージ情報(表領域、初期サイズ)
    ・ストレージエンジンの種類
    ・列の名前
    ・列のデータ型
    ・列のデフォルト値
    ・not null列制約
    ・主キー列
    ・主キーの名前
    ・主キーのインデックスの名前
    ・インデックスの名前
    ・インデックスタイプ
    ・インデックスが作成されている列
    ・インデックスが作成されている列のソート順序
    ・インデックスのストレージ情報
    ・unique列制約
    ・uniqueインデックスの名前
    ・外部キーの名前
    ・外部キー列
    ・外部キーが紐づけるテーブルと列
    ```

    ### 各DBサーバのメタデータ保存方法

    ```text
    Orable DB
      ・user_tables, all_constraintsなどのビュー
    
    SQL Server
      ・sp_tables等のシステムストアドプロシージャ
    
    MySQL/Mariadb
      ・information_schemaという特別なDB
      ※sql serverもinformation_schemaを使うことができる
    ```

## Q118 information_schemaの簡単な使い方について知っていますか?

??? success

    ### 基本

    ```text
    ・information_schemaは特別な「テーブル」

    ・テーブルなので、以下のことが可能
      SELECTで取得できる行を制限　⇒　OK
      ORDER BYで順序を並び替え　⇒　OK
      式を適用　⇒ OK
      関数を適用　⇒ OK

    ・ただし、ユーザからの書き込みは許可されない
      読み取りのみが可能

    ```

    ### テーブル一覧を取得したい場合

    ```sql
    -- information_schema.tablesを使う

    /*
    -- 主なカラム --
    table_schema : 
      ・テーブルが属するスキーマ(DB)の名前
      ・tablesに限らず、information_schema命令では大抵使える
    table_name
      ・テーブル名
      ・tablesに限らず、information_schema命令では大抵使える
    table_type
      ・テーブルならBASE TABLE
      ・ビューならVIEW
      ・information_schemaテーブルならSYSTEM VIEW
      ・※一時テーブルは登場しない
    ENGINE
      ・ストレージエンジン
    ROW_FORMAT
      ・行のサイズが可変か否か。VARCHAR等に影響
    TABLE_ROWS
      ・InnoDBの場合、概算。
      ・正確な数を取得する場合、count(*)を使う
    */
    select
      table_name, table_type
    from
      information_schema.tables
    where
      table_schema = "sakila"
      and
      table_type = "BASE TABLE"
    order by table_name
    ;

    /*
    +---------------+------------+
    | table_name    | table_type |
    +---------------+------------+
    | actor         | BASE TABLE |
    | address       | BASE TABLE |
    | category      | BASE TABLE |
    | city          | BASE TABLE |
    ...
    16 rows in set (0.002 sec)
    */
    ```

    ### VIEWに関する情報を取得したい場合

    ```sql
    /*
    table_schema
      ・同様
    table_name
      ・同様
    view_definition:
      ・VIEWの定義
      ・(with check option等は書かれない)
    definier
      ・VIEW定義を作成したアカウント
    SECURITY_TYPE
      ・DEFINER-> 実行時にはVIEWを定義したユーザの権限を使用
      ・INVOKER-> 実行時には実行するユーザの権限を使用
    IS_UPDATABLE
      ・ビューが更新可能か否かか
    CHECK_OPTION
      ・WITH CHECK OPTIONに設定した属性の種類
      ・13章では解説しなかったが、LOCALやCASCADEがある
    */
    select
      table_name, is_updatable
    from information_schema.views
    where
      table_schema = "sakila"
      and
      table_name regexp "^[ac]"
    order by 1
    ;

    /*
    +--------------------+--------------+
    | table_name         | is_updatable |
    +--------------------+--------------+
    | active_customer_vw | YES          |
    | actor_info         | NO           |
    | customer_list      | YES          |
    | customer_vw        | YES          |
    | cust_vw            | YES          |
    +--------------------+--------------+
    5 rows in set (0.008 sec)
    */
    ```

    ### カラムの情報を取得したい場合

    ```sql
    /*
    -- 主なカラム--

    column_name
      ・カラム名
    optional_position
      ・テーブル内のカラムの位置
    colum_default
      ・カラムのデフォルト値
    is_nullable
      ・nullが格納できるか否か
    data_type
      ・カラムのデータ型(精度などの情報はない)
    character_maximus_length
      ・文字列カラムの場合最大長(文字数単位)
    character_octet_length
      ・文字列カラムの場合、最大長（バイト単位)
    number_precision
      ・数値の精度(数値カラム)
    numeric_scale
      ・数値スケール(数値カラム)
    datetime_precision
      ・小数秒精度(時間カラム)
    character_set_name
      ・文字セット名(文字カラム)
    collation_name
      ・照合順序名(文字カラム)
    column_type
      ・カラムのデータ型(タイプ名以外も含む)
    column_key
      ・カラムがインデックス付けされているか否か
      ・空
          ・インデックス付けされていない
          ・非一意インデックスのセカンダリカラム
        PRI
          ・PRIMARY KEY
          ・PRIMARY KEYの複数カラム
        UNI
          ・UNIQUEインデックス
          ・複合ユニーク制約の場合、その最初のカラム
          ⇒後でテーブルを作成して試す
        MUL
          ・非一意インデックスの最初のカラム
          ・特定の値が複数回出現することが許される
    extra
      ・auto_increment
      ・on update current_timestamp(): 自動更新
      ・default_generated: 式のデフォルト値
       等の情報
    privileged
      ・カラムに対して持っている権限
      ・全部持っていればselect,insert,update,references
    column_comment
      ・カラム定義に含まれるコメント
    

    */
    select
      column_name, 
      column_type,
      column_key
    from information_schema.columns
    where 
      table_schema = "sakila"
      and
      table_name = "city"
    ;

    /*
    +-------------+----------------------+------------+
    | column_name | column_type          | column_key |
    +-------------+----------------------+------------+
    | city_id     | smallint(5) unsigned | PRI        |
    | city        | varchar(50)          |            |
    | country_id  | smallint(5) unsigned | MUL        |
    | last_update | timestamp            |            |
    +-------------+----------------------+------------+
    4 rows in set (0.001 sec)
    */
    ```

    ### テーブルのインデックスに関する情報を取得したい

    ```sql
    /*
    -- 主なカラム -- 
    column_name
      ・indexが張られているカラム名
    non_unique
      ・一意なら0, 非一意インデックスなら1
    index_schema
      ・インデックスが属するDBの名前
    index_name
      ・インデックスの名前。主キーの場合、常にprimary
    seq_in_index
      ・インデックス内のシーケンス番号
      ・複合インデックスの参照順
    collation 
      ・インデックス内のカラムのソート方法
      ・A(昇順), D(降順), NULL(ソート無し)
    cardinality
      ・一意の値の数の推定値
      ・推定値なので、正確とは限らない
      ・カーディナリティが高いほど結合時にindexとして
        使用されやすい
    sub_part
      ・カラムが部分的にindex付けされている場合は
        その文字数。全体がindex付けされているならNULL
    nullable
      ・NULL値を含めることができればYES。ダメなら空文字
    index_type
      ・BTREEやFULLTEXT等
    ignored(mariadb)/ is_visible(mysql)
      ・optimizerがindexを参照できるか否か
      ⇒indexを削除して再追加するとコストがかかる場合
        非表示、可視化という手順を踏むことで高速になる
    */
    select
      index_name,
      non_unique,
      seq_in_index,
      column_name
    from
      information_schema.statistics
    where
      table_schema = "sakila"
      and
      table_name = "rental"
    ;

    /*
    +---------------------+------------+--------------+--------------+
    | index_name          | non_unique | seq_in_index | column_name  |
    +---------------------+------------+--------------+--------------+
    | PRIMARY             |          0 |            1 | rental_id    |
    | rental_date         |          0 |            1 | rental_date  |
    | rental_date         |          0 |            2 | inventory_id |
    | rental_date         |          0 |            3 | customer_id  |
    | idx_fk_inventory_id |          1 |            1 | inventory_id |
    | idx_fk_customer_id  |          1 |            1 | customer_id  |
    | idx_fk_staff_id     |          1 |            1 | staff_id     |
    +---------------------+------------+--------------+--------------+
    7 rows in set (0.001 sec)
    */
    ```

    ### テーブルの制約に関する情報を取得したい

    ```sql
    /*
    constraint_type
      ・制約のタイプ
      ・unique/primarykey/foreign key/check
    constraint_name
      ・制約名
    */
    select
      constraint_name name,
      constraint_type type
    from
      information_schema.table_constraints
    where
      table_schema = "sakila"
      and
      table_name = "rental"
    ;

    /*
    +---------------------+-------------+
    | name                | type        |
    +---------------------+-------------+
    | PRIMARY             | PRIMARY KEY |
    | rental_date         | UNIQUE      |
    | fk_rental_customer  | FOREIGN KEY |
    | fk_rental_inventory | FOREIGN KEY |
    | fk_rental_staff     | FOREIGN KEY |
    +---------------------+-------------+
    5 rows in set (0.000 sec)
    */
    ```

    ### その他のチュートリアルレベルで使用したもの

    ```sql
    -- .plugins : 特定のプラグインが動いているか
    select
      plugin_name,
      plugin_status
    from information_schema.plugins
    where 
      plugin_name = "Mroonga"
    ;
    /*
    +-------------+---------------+
    | plugin_name | plugin_status |
    +-------------+---------------+
    | Mroonga     | ACTIVE        |
    +-------------+---------------+
    1 row in set (0.001 sec)
    */

    -- .engines: 利用可能なストレージエンジン
    select
      engine,
      transactions,
      savepoints
    from
      information_schema.engines
    ;

    /*
    +--------------------+--------------+------------+
    | engine             | transactions | savepoints |
    +--------------------+--------------+------------+
    | CSV                | NO           | NO         |
    | MRG_MyISAM         | NO           | NO         |
    | MEMORY             | NO           | NO         |
    | Aria               | NO           | NO         |
    | MyISAM             | NO           | NO         |
    | SEQUENCE           | YES          | YES        |
    | Mroonga            | NO           | NO         |
    | PERFORMANCE_SCHEMA | NO           | NO         |
    | InnoDB             | YES          | YES        |
    +--------------------+--------------+------------+
    9 rows in set (0.000 sec)
    */
    
    -- .processlist: 実行中のプロセス
    -- ※\Gは;を含むので[;]は要らない

    select
      *
    from
      information_schema.processlist
    \G

    /*
    ************* 1. row ******
                ID: 47
              USER: hogehoge
              HOST: localhost
                DB: sakila
            COMMAND: Query
              TIME: 0
              STATE: Filling schema table
              INFO: select * from information_s...
            TIME_MS: 0.468
              STAGE: 0
          MAX_STAGE: 0
          PROGRESS: 0.000
        MEMORY_USED: 144560
    MAX_MEMORY_USED: 5534192
      EXAMINED_ROWS: 0
          QUERY_ID: 1070
        INFO_BINARY: select * from information_s...
                TID: 128066
    1 row in set (0.001 sec)
    */

    -- DB一覧
    select
      schema_name
    from information_schema.schemata
    ;

    /*
    +--------------------+
    | schema_name        |
    +--------------------+
    | information_schema |
    | performance_schema |
    | mydb               |
    | sakila             |
    | mysql              |
    | sys                |
    +--------------------+
    6 rows in set (0.001 sec)
    */

    -- 他にも user_privilagesでスキーマレベルの権限を見たり
    -- triggersでテーブルトリガーの一覧を見ることもできる
    -- この章の後半では、関数の一覧を見るためのroutinesが活躍する
    ```

## Q119 information_schemaを用いたスクリプトの生成方法について知っていますか?

??? success
    ### create table文を生成するクエリ

    ```text
    ・本来は手続き型言語を使ってスクリプトを生成する方が
      何百万倍も楽だけど...
    
    ・information_schemaのビューでクエリを実行
      ⇒スクリプト生成という手段を取ることもできる
    ```

    ```sql
    -- categoryテーブルのcreate文を真似てみる
    show create table category;

    /*
    +----------+------------
    | Table    | Create Table    
    +----------+--------
    | category | CREATE TABLE `category` (
      `category_id` tinyint(3) unsigned 
        NOT NULL AUTO_INCREMENT,
      `name` varchar(25) NOT NULL,
      `last_update` timestamp NOT NULL 
        DEFAULT current_timestamp() 
        ON UPDATE current_timestamp(),
      PRIMARY KEY (`category_id`)
    ) ENGINE=InnoDB AUTO_INCREMENT=17 
      DEFAULT CHARSET=utf8mb4 
      COLLATE=utf8mb4_general_ci |
    +----------+-----------------------------
    */

    select
      "CREATE TABLE category (" create_table_statement
      union all
    select 
        cols.txt
    from
      (
        select
          -- 基本情報
          concat(" ", column_name, " ", column_type,
          -- null関連
          case
            when is_nullable = "NO" then " not null"
            else ""
          end,
          -- 補足情報
          -- (increment等)があるか否か
          -- デフォルト値として自動更新が含まれているなら
          -- 最初のcase文に引っかかる
          case
            when 
              extra is not null 
              and 
              -- mysql
              -- extra like "DEFAULT_GENERATED%"
              extra like "on update current_timestamp()"
            then
            -- mysql
            --  concat (
            --    " DEFAULT ", 
            --    column_default, 
            --    substr(extra, 18)
            --  )
              replace(
                concat (
                  " DEFAULT ",
                  column_default,
                  " ",
                  extra
                ),
                "()",
                ""
              )
            when
              extra is not null
            then
              concat(" ", extra)
            else ""
            end,
          ",") txt
        from information_schema.columns
        where
          table_schema = "sakila"
          and
          table_name = "category"
        order by
          ordinal_position
      ) cols
    union all
    -- 制約
    select
      concat (" constraint primary key (")
    from information_schema.table_constraints
    where
      table_schema = "sakila"
      and
      table_name = "category"
      and constraint_type = "PRIMARY KEY"
    union all
    select
      cols.txt
    from
      (
        -- primaryキーの処理
        select
          -- 末尾のカンマはつけないように分岐
          concat (
            case
              when ordinal_position > 1
              then " ,"
              else " "
            end,
            column_name
          ) txt
        from 
          information_schema.key_column_usage
        where
          table_schema = "sakila"
          and
          table_name = "category"
          and
          constraint_name = "PRIMARY"
        order by
          ordinal_position
      ) cols
    union all
    select ")"
    union all
    select ")"
    ;

    /*
    +----------------------------------------------------------------------------------------+
    | create_table_statement                                                                 |
    +----------------------------------------------------------------------------------------+
    | CREATE TABLE category (                                                                |
    |  category_id tinyint(3) unsigned not null auto_increment,                              |
    |  name varchar(25) not null ,                                                           |
    |  last_update timestamp not null DEFAULT current_timestamp on update current_timestamp, |
    |  constraint primary key (                                                              |
    |  category_id                                                                           |
    | )                                                                                      |
    | )                                                                                      |
    +----------------------------------------------------------------------------------------+
    8 rows in set (0.001 sec)
    */
    ```

    ### 補足

    ```text
    ・engine等の情報についても、information_schemaから
      取ってくることができる

    ・show create table構文の場合、
    　\Gを付けることで、テーブルの囲いをなくし、
      コピーすることができる
    
    ・ただし上記のように作成したスクリプトの場合、
      select文の生産物なので囲いをなくすのは面倒くさい
      (出来ないわけではないが)
    
    ・よって大人しく、手続き型言語を使った方がいいと思われる
      (もしくは後述する動的クエリを使用するか)
    ```

    ### 実際に試してみる

    ```sql
    -- 重複するので名前は変更した
     CREATE TABLE category2 ( 
      category_id tinyint(3) 
        unsigned not null auto_increment,
       name varchar(25) not null ,   
        last_update timestamp not null 
        DEFAULT current_timestamp 
        on update current_timestamp,
        constraint primary key ( 
          category_id
        )
     )
     -- Query OK, 0 rows affected (0.033 sec)

     drop table category2;
     -- Query OK, 0 rows affected (0.011 sec)
    ```

## Q120 information_schemaを用いたデプロイメントの検証について知っていますか?

??? success
    ### 思想

    ```text
    ・多くの組織では、DBのメンテナンス時間があり、
    　その間に、既存のDBオブジェクトの管理や
      新しいスキーマやコードの導入を行う

    ・メタデータを閲覧し、作成したスキーマオブジェクトの
      列、インデックス、主キーなどが適切であるか確認する事で
      デプロイメントの成否をチェックできる
    ```

    ### 例

    ```sql
    /*
    sakila DB内のテーブルごとに相関サブクエリを用いて
    そのカラム数とインデックス数と、主キー制約数を出力
    */
    select
      tbl.table_name,
      (
        select
          count(*)
        from
          information_schema.columns clm
        where
          clm.table_schema = tbl.table_schema
          and
          clm.table_name = tbl.table_name
      ) num_columns,

      (
        select 
          count(*)
        from 
          information_schema.statistics sta
        where
          sta.table_schema = tbl.table_schema
          and
          sta.table_name = tbl.table_name
      ) num_indexes,

      (
        select 
          count(*)
        from
          information_schema.table_constraints tc
        where
          tc.table_schema = tbl.table_schema
          and
          tc.table_name = tbl.table_name
          and
          tc.constraint_type = "PRIMARY KEY"
      ) num_primary_keys
    
    from information_schema.tables tbl
    where
      tbl.table_schema = "sakila"
      and
      tbl.table_type = "BASE TABLE"
    order by 1
    ;

    /*
    +---------------+-------------+-------------+------------------+
    | table_name    | num_columns | num_indexes | num_primary_keys |
    +---------------+-------------+-------------+------------------+
    | actor         |           4 |           2 |                1 |
    | address       |           8 |           2 |                1 |
    | category      |           3 |           1 |                1 |
    | city          |           4 |           2 |                1 |
    | country       |           3 |           1 |                1 |
    | customer      |           9 |           6 |                1 |
    | film          |          13 |           4 |                1 |
    | film_actor    |           3 |           3 |                1 |
    | film_category |           3 |           3 |                1 |
    | film_text     |           3 |           3 |                1 |
    | inventory     |           4 |           4 |                1 |
    | language      |           3 |           1 |                1 |
    | payment       |           7 |           4 |                1 |
    | rental        |           7 |           7 |                1 |
    | staff         |          11 |           3 |                1 |
    | store         |           4 |           3 |                1 |
    +---------------+-------------+-------------+------------------+
    16 rows in set (0.305 sec)
    */
    ```

## Q121 動的SQLについて知っていますか?

??? success
    ### ストアドプロシージャとの違い

    ```text
    ・Oracle DBのPL/SQL(言語)
    　SQL ServerのTransact-SQL(言語)
    　MySQL/MariaDBのstored-procedures/functions(機能)
      (procedureは戻り値が無いことを意味する)
      には、手続き型の構文が含まれる
    
    ストアドプロシージャ/ストアドファンクション
      ・コンパイルされた形式でDBに保存される
      ・手続き型の構文(条件分岐・ループ)も使用可能
    ```

    ### 動的SQL実行

    ```text
    ・SQL文は実行時に動的に構築される
    ・ただし「DBには保存されない」
      ⇒その場限り(このセッション限り)の実行に向いている
      ⇒ストアドを使うほど大がかりな作業をしたくない場合など

    ・MySQL/Mariadbの場合、
      prepare,execute,deallocate等が使える

    プリペアドステートメントの利点

      ・同じクエリを何度も実行する際、解析と実行計画を
      　生成する為のオーバーヘッドが少なくなる

      ・ユーザからの入力をパラメータとして渡せるので、
        SQLi(sql-injection)を防ぎやすくなる

      ・コードの再利用がなくなるので保守性が増す

      ・パラメータの方が明示的に指定されるので
        型の不一致によるエラーが少なくなる
      
      ・動的なクエリを作成するために他のプログラミング言語
        の力を借りる必要がなくなる
    
    set @<変数名> =
      ・mysql内で変数として扱える
      ・setなのでセッション中は保存される
    
    prepare <query_name> from <文字列orユーザ変数>
      ・SQLステートメントを準備し、名前を割り当てる
      ・名前を使うことで、ステートメントを参照可能
      ・?を使用でき、後から値を挿入できる(SQLi対策)
    
    execute <query_name> [using @var_name]
      ・SQLステートメントを実行する
      ・プレスホルダに値を入れたい場合はusing句を使える
        ただし、文字列は指定できない
        ユーザ変数のみが指定可能
    
    deallocate prepare <query_name>
      ・SQLステートメントを削除する
    ```

    ### 動的SQLの例

    ```sql
    set @qry = "
      select
        customer_id,
        first_name,
        last_name
      from
        customer
    ";
    -- Query OK, 0 rows affected (0.001 sec)

    prepare dynsql1 from @qry;
    -- Query OK, 0 rows affected (0.000 sec)
    -- Statement prepared

    -- 何度でも実行可能
    execute dynsql1;

    /*
    +-------------+-------------+--------------+
    | customer_id | first_name  | last_name    |
    +-------------+-------------+--------------+
    |         375 | AARON       | SELBY        |
    |         367 | ADAM        | GOOCH        |
    ...skipping...
    599 rows in set (0.001 sec)
    */

    deallocate prepare dynsql1;
    -- Query OK, 0 rows affected (0.000 sec)

    -- 無効にしたので、もう使えない
    execute dynsql1;
    -- Unknown prepared statement handler (dynsql1) 
    -- given to EXECUTE
    ```

    ### プレスホルダを含む場合

    ```sql
    set @qry = "
      select
        customer_id,
        first_name,
        last_name
      from
        customer
      where
        customer_id = ?
    ";

    prepare dynsql2 from @qry;

    set @custid = 9;
    set @custid2 = 10;

    -- placeholderに9を指定
    execute dynsql2 using @custid;

    /*
    +-------------+------------+-----------+
    | customer_id | first_name | last_name |
    +-------------+------------+-----------+
    |           9 | MARGARET   | MOORE     |
    +-------------+------------+-----------+
    1 row in set (0.001 sec)
    */

    execute dynsql2 using @custid2;

    /*
    +-------------+------------+-----------+
    | customer_id | first_name | last_name |
    +-------------+------------+-----------+
    |          10 | DOROTHY    | TAYLOR    |
    +-------------+------------+-----------+
    1 row in set (0.000 sec)
    */

    deallocate prepare dynsql2;
    ```

    !!! info
        ### 変数を破棄したい場合

        ```sql
        -- たとえば上記の例でいうと
        -- @custidや@qryを破棄したいと考えたくなる
        -- そんな時はNULLをセットする

        set @qry = null;
        set @custid = null;
        set @custid2 = null;

        -- もともと存在しない変数をselectで表示した場合
        -- 値はNULLなのでこれで問題ない

        select
          @qry,
          @custid,
          @custid2,
          @nekoneko
        ;

        /*
        +------+---------+----------+-----------+
        | @qry | @custid | @custid2 | @nekoneko |
        +------+---------+----------+-----------+
        | NULL |    NULL |     NULL | NULL      |
        +------+---------+----------+-----------+
        1 row in set (0.000 sec)
        */
        ```

    ### 動的クエリを用いてスクリプトを生成する

    ```text
    以下補足

    select ... into <val1, val2>
      ・単一行を返すクエリの各カラムを各変数に格納する
        ex)
        select 1, 2 into @var1, @var2;
        select @var1, @var2;
        +-------+-------+
        | @var1 | @var2 |
        +-------+-------+
        |     1 |     2 |
        +-------+-------+
    
    concat_ws(sep, "str1", "str2)
      区切り文字をstringの間に適用する

    max(文字列カラム)
      ・照合順序を基準に大きいものが返される
        (文字列長とは関係がない)
      ・今回の場合、1つを除きNULLなので、NULL以外の値が返る

    ```

    ```sql
    -- カラムが9個までしか対応していない
    select
      concat(
        "SELECT ",
        concat_ws(
          ",",
          cols.col1,
          cols.col2,
          cols.col3,
          cols.col4,
          cols.col5,
          cols.col6,
          cols.col7,
          cols.col8,
          cols.col9
        ),
        " FROM customer WHERE customer_id = ?"
      )
    into @qry
    from
      (
        select
          case when ordinal_position = 1
                then column_name else null end col1,
          case when ordinal_position = 2
                then column_name else null end col2,
          case when ordinal_position = 3
                then column_name else null end col3,
          case when ordinal_position = 4
                then column_name else null end col4,
          case when ordinal_position = 5
                then column_name else null end col5,
          case when ordinal_position = 6
                then column_name else null end col6,
          case when ordinal_position = 7
                then column_name else null end col7,
          case when ordinal_position = 8
                then column_name else null end col8,
          case when ordinal_position = 9
                then column_name else null end col9
        from information_schema.columns
        where
          table_schema = "sakila"
          and
          table_name = "customer"
      ) cols
    ;

    -- 変数に代入した定義を閲覧
    select @qry;
    /*
    +-----------------------------------------------------------+
    | @qry                                                      |
    +-----------------------------------------------------------+
    | SELECT                                                    |
    | customer_id,store_id,first_name,last_name,email,          |
    | address_id,active,create_date,last_update FROM customer   |
    | WHERE customer_id = ?                                     |
    +-----------------------------------------------------------+
    1 row in set (0.000 sec)
    */

    prepare dynsql3 from @qry;

    set @custid = 45;

    -- 定義とプレスホルダを利用して実行
    execute dynsql3 using @custid \G

    /*
    *********** 1. row *********
    customer_id: 45
      store_id: 1
    first_name: JANET
      last_name: PHILLIPS
          email: JANET.PHILLIPS@sakilacustomer.org
    address_id: 49
        active: 1
    create_date: 2006-02-14 22:04:36
    last_update: 2006-02-15 04:57:20
    1 row in set (0.000 sec)
    */

    deallocate prepare dynsql3;
    ```

## Q122 ストアドのメリットデメリットについて知っていますか?

??? success

    ### ストアド概要

    ```text
    ・コンパイルされた形式でDBに保存される
    ・手続き型の構文(条件分岐・ループ)も使用可能
    ```

    ### 使いどころ

    ```text
    ・手続き型の構文の構文を使いたい
    ・けれど、パフォーマンスを考えると、
      他のプログラミング言語を使いたくはない
    ```

    ### メリット

    ```text
    ・複数のテーブルに対する更新や、大量のデータに対する
      連続更新について考える
    
    ・通常のプログラム言語の場合
      ⇒何度もアプリからDBにアクセスする必要がある
      ⇒APサーバとDBサーバに物理的な距離がある場合、
        ネットワークの負荷も加わってしまう
    
    ・ストアドの場合
      ⇒DBMS内で完結する処理なので、ネットワークや
        DB接続時の負荷はアプリから呼び出す際の1回で済む
    ```

    ### デメリット

    ```text
    ・DBMSによって仕様が大きく異なるので(ANSIに準拠して
      いない)、システムのDBを移行する場合は、コードを
      書き直す必要がある

    ※ただし、複雑な概念はないので、
      フレームワークやプログラミング言語間の移行に比べて
      負担は控え目
    ```

## Q123 ストアドの種類について知っていますか?

??? success

    ### ストアドの種類について(function)

    ```text
    ・functionなので引数を受け取り、戻り値を返す
    ・SELECT文の中で呼び出せる
    ・Transaction処理はできない

    組み込み関数を思い出す
      ・concatやreplace等はMySQL/Mariadbの組み込み関数だが
      　自作することも可能
      ・自作関数の中で、更新系のSQLも実行可能
    
    ```

    ### ストアドの種類について(procedure)

    ```text
    ・procedureなので引数を受けとるが、戻り値はない
      (ただし引数でOUT, INOUTの引数は受け取れる)
    ・CALLを使って呼び出す
    ・SELECT文の中で呼び出す事はできない
    ・Transaction制御が可能

    OUTとINOUT
      ・OUTパラメータはprocedureから呼び出し元に値を渡す
      ・INOUTパラメータに対して加えた変更は呼び出し元で
        閲覧可能。（初期値は呼び出し元で設定する）
      
      ※stored-procedureが未処理の例外で終了すると、
        OUTパラメータやINOUTパラメータに対して加えた変更は
        呼び出し元に渡されなくなる
    ```

    ### ストアドの種類について(Trigger)

    ```text
    ・特定のテーブルが更新された時に自動的に呼び出される
    ・Transaction制御はできない
    ・Triggerの中でProcedureを呼び出すことも可能

    ・意図的に呼び出すことはできず、insert/update/deleteが
      引き金になる
    ・Triggerを定義する際には、以下を選ぶ
      ・更新対象のテーブル
      ・更新の種類(insert/update/delete)
      ・対象になるのは更新前(old)か更新後(new)か

    ・引数や戻り値を返すことはない
    ```

## Q124 ストアドの基本的な例(function, procedure)について知っていますか?

??? success

    ### 注意点

    ```text
    ・基礎的な例だけを紹介する
    ```

    ### 構文関連の知識

    ```text
    delimiter:
      ・区切り文字を変更する。
      ・デフォルトは;
      ・//に変えることで、procedure/function内で使われる
      　;という区切り文字をmysqlに解釈させずに、
        サーバに渡すことが可能。
    
    [not] deterministic
      ・同じ入力パラメータに対して常に同じ結果を返すか否か
      ・デフォルトはnot deterministic
      ・非決定的なルーチンを、deterministicとすると
        optimizerが正しくない実行計画を選択する可能性あり
    
    begin ... end
      ・delimiterで区切り文字を変更していた場合
      　このキーワードで複数のステートメントを囲む事ができる
    
    ステートメント
      ・1つのクエリのこと
    ```

    ### 作成したストアドの一覧を見る

    ```sql
    -- created等の列を表示すると、作成したばかりのものを
    -- 特定できる
    select
      routine_name name,
      routine_type type,
      created
    from
      information_schema.routines
    order by 3 desc
    limit 3
    ;

    /*
    +-------------------+-----------+---------------------+      
    | name              | type      | created             |      
    +-------------------+-----------+---------------------+      
    | getPrice          | FUNCTION  | 2024-04-02 18:24:04 |      
    | film_in_stock     | PROCEDURE | 2024-02-18 03:22:35 |      
    | film_not_in_stock | PROCEDURE | 2024-02-18 03:22:35 |      
    +-------------------+-----------+---------------------+      
    3 rows in set (0.002 sec)
    */
    ```

    ### ストアドの削除方法

    ```sql
    -- drop {procedure | function} <routine_name>
    ```

    !!! info
        ### ストアドの変更

        ```text
        ・alter {procedure | function}はストアドの
          特性を変更するが、パラメータや本体を変更する
          事はできない。
        
        ・本体などを変更したい場合は
          drop -> createの流れを踏む必要がある
        ```

    ### 例(Procedure)

    ```sql

    /*
    -- procedureの定義

    delimiter //
    create function procedure_name (param type) 
    begin
      body
    end
    //

    delimiter ;

    -- 作成したprocedureを呼ぶ
    call procedure_name(param);
    */


    -- 返り値がなくとも変数は使えるので
    delimiter //
    create procedure val_double (p1 int)
    begin
      set @x = 0 + p1 * 2;
    end
    // --区切り文字を変えたので

    delimiter ; -- 区切り文字を戻す

    call val_double(12);

    select @x;

    /*
    +------+
    | @x   |
    +------+
    |   24 |
    +------+
    1 row in set (0.000 sec)
    */
    ```
    
    ### 例(Function)

    ```sql
    
    /*
    -- functionの定義
    -- bodyの中でreturnをし、戻り値を返す

    delimiter //
    create function fn_name (param type) returns type
    begin
      body
    end
    //

    delimiter ;
    */

    delimiter //

    create function absolute(num double) returns double
    deterministic
    begin
      if num > 0 then
        return num;
      else
        return num * -1;
      end if;
    end
    //

    delimiter ; -- 区切り文字を戻す

    -- selectの中で呼び出す
    select 
      absolute(100),
      absolute(-100)
    ;

    /*
    +---------------+----------------+
    | absolute(100) | absolute(-100) |
    +---------------+----------------+
    |           100 |            100 |
    +---------------+----------------+
    1 row in set (0.002 sec)
    */
    ```

    ### 単数ステートメントの場合

    ```sql
    -- delimiterは必要ない
    -- if not existsは存在していなかったら

    create function 
      if not exists
      hello(s char(20))
      returns char(50)
    deterministic
      return concat("Hello ", s, " !")
    ;

    select hello("Toraneko");

    /*
    +-------------------+
    | hello("Toraneko") |
    +-------------------+
    | Hello Toraneko !  |
    +-------------------+
    1 row in set (0.001 sec)
    */
    ```


    ### 便利構文の例(declare:ローカル変数)

    ```sql
    -- declare: 変数定義(宣言)
    -- declare <変数名> <型> [default <デフォルト値>]
    -- declareした後は通常の変数のようにset可能
    -- defalut値が無い場合、初期値はnull
    -- 2つ一気に宣言することも可能

    /*
    ローカルスコープのスコープはBEGIN, ENDブロック
    ネストしている場合、内側からも参照可能
    テーブルカラムと同じ名前を付けると混同が起きる
    */

    delimiter //
    create procedure sp1 (x int)
    begin
      declare xname varchar(5) default "bob";
      declare newname varchar(5);
      declare xid int;

      -- 以前説明したとおり
      select xname, x*2 into newname, xid;
      select newname, xid;
    end
    //
    delimiter ;

    call sp1(10);

    /*
    +---------+------+
    | newname | xid  |
    +---------+------+
    | bob     |   20 |
    +---------+------+
    1 row in set (0.021 sec)
    */
    ```

    ### 便利構文の例(CASEステートメント)

    ```sql
    /*
    ・ストアドの場合、end caseで締める

    ・thenに対してselectを書くこともできる

    ・条件に対して空の処理を実行させたい場合、
      begin ... end;だけを書く
    */
    delimiter //

    create procedure 
      if not exists p()
      begin
        declare v int default 2;

        case v
          when 2 then select v;
          when 3 then select 0;
          else
            begin
            end;
        end case;
      end;
    //
    delimiter ;

    -- 空の処理が実行される
    call p();
    -- Query OK, 0 rows affected (0.001 sec)

    drop procedure p;
    ```

    ### 便利構文(IFステートメント)

    ```sql
    -- 基本的な条件分岐を実装
    -- if, elseif, else , end ifはプログラマならお馴染み
    -- もちろん入れ子にすることも可能

    delimiter //
    create function SimpleCompare(n int, m int)
      returns varchar(20)

      begin
        declare ope varchar(20);

        if n > m then
          set ope = ">";
        elseif n = m then
          set ope = "=";
        else
          set ope = "<";
        end if;

        set ope = concat(n, " ", ope, " ", m);
        return ope;
      end //
    
    delimiter ;

    select 
      SimpleCompare(11, 20) compare1,
      SimpleCompare(13, 13) compare2,
      SimpleCompare(-11,-123) compare3;

    /*
    +----------+----------+------------+
    | compare1 | compare2 | compare3   |
    +----------+----------+------------+
    | 11 < 20  | 13 = 13  | -11 > -123 |
    +----------+----------+------------+
    1 row in set (0.001 sec)
    */
    ```

    ### 便利構文(Loopステートメント)

    ```text
    ラベル
      ・begin ... endブロックや
      　後述するloop, repeat, whileステートメントには
        ラベルを付けることができる
      ・end_labelはオプションで、作成する場合、
        begin_labelと同じ値にする。begin_labelは必須
    
    ラベルの例
      label1 : BEGIN
      END label1
    
    LEAVEステートメント
      ・特定のラベルを持つフロー制御構造分を終了させる
      ・もっとも外側のブロック内でLEAVEステートメントを
        使うと、プログラムが終了する
    ```

    ```sql
    -- loop ... end loopで単純なループ構文を実装
    -- LEAVEステートメントに達すると処理が終了
    -- つまり、LEAVEがないと無限ループする
    -- LOOP内で早期RETURNすることもできる
    -- 早期RETURNはBEGIN END内のどの位置でも可能

    delimiter //
    create procedure doiterate (p1 int)
    begin
      label1: LOOP
        set p1 = p1 + 1;
        if p1 < 10 then
          iterate label1; /*iterateについては後述*/
        end if;

        leave label1;
      end loop label1;

      set @x = p1;
    end
    //

    delimiter ;

    call doiterate(3);

    select @x;

    /*
    +------+
    | @x   |
    +------+
    |   10 |
    +------+
    1 row in set (0.000 sec)
    */

    ```

    ```sql
    delimiter //

    create procedure nest(p1 int, p2 int)
    begin
      set @x = 0;
      label1: loop
        set @y = 0;
        label2: loop
          if @y > p2 then
            leave label2;
          else
            select @x, @y;
            set @y = @y + 1;
          end if;
        end loop;

        set @x = @x + 1;
        if @x > p1 then
          leave label1;
        else
          begin
          end;
        end if;
      end loop;
    end
    //

    delimiter ;

    call nest(1, 2);

    /*
    +------+------+
    | @x   | @y   |
    +------+------+
    |    0 |    0 |
    +------+------+
    1 row in set (0.000 sec)

    +------+------+
    | @x   | @y   |
    +------+------+
    |    0 |    1 |
    +------+------+
    1 row in set (0.002 sec)

    +------+------+
    | @x   | @y   |
    +------+------+
    |    0 |    2 |
    +------+------+
    1 row in set (0.004 sec)

    +------+------+
    | @x   | @y   |
    +------+------+
    |    1 |    0 |
    +------+------+
    1 row in set (0.006 sec)

    +------+------+
    | @x   | @y   |
    +------+------+
    |    1 |    1 |
    +------+------+
    1 row in set (0.008 sec)

    +------+------+
    | @x   | @y   |
    +------+------+
    |    1 |    2 |
    +------+------+
    1 row in set (0.009 sec)
    */
    ```

    ### 便利な構文(REPEATステートメント)

    ```sql
    /*
    repeat
      statement_list
    until search_condition
    end repeat

    search_conditionがtrueになるまで繰り返す
    */

    delimiter //

    create procedure dorepeat(p1 int)
    begin
      declare cnt int default 0;
      set @x = 0;
      repeat
        set cnt = cnt + 1;
        set @x = @x + cnt;
        until
          cnt >= p1
      end repeat;
    end
    //

    delimiter ;

    call dorepeat(10);

    select @x;

    /*
    +------+
    | @x   |
    +------+
    |   55 |
    +------+
    1 row in set (0.000 sec)
    */

    drop procedure dorepeat;
    ```

    ### 便利構文(WHILE)

    ```sql
    /*
    条件がtrueの間、繰り返す

    while <条件> do
      statement_list
    end while

    */
    delimiter //
    create procedure dowhile()
    begin
      declare v1 int default 0;

      while v1 < 3 do
        select v1;
        set v1 = v1 + 1;
      end while;
    end
    //

    delimiter ;

    call dowhile();

    drop procedure dowhile;

    /*
    +------+ 
    | v1   |
    +------+
    |    0 |
    +------+
    1 row in set (0.001 sec)

    +------+
    | v1   |
    +------+
    |    1 |
    +------+
    1 row in set (0.005 sec)

    +------+
    | v1   |
    +------+
    |    2 |
    +------+
    1 row in set (0.007 sec)

    Query OK, 0 rows affected (0.010 sec)
    */
    ```

    ### 便利構文(ITERATEステートメント)

    ```sql
    -- LOOP, REPEAT, WHILEの中で
    -- ITERATE labelとすることでループを再び開始できる
    -- 下ではその特性を生かし入れ子を用いないループを書いた

    delimiter //
    create procedure doiterate()
    begin
      declare inner_cnt int default 0;
      declare outer_cnt int default 0;

      while inner_cnt < 2 do
        select inner_cnt, outer_cnt;
        set inner_cnt = inner_cnt + 1;

        if inner_cnt <> 2 then
          begin
          end;
        elseif outer_cnt = 0 then
          set inner_cnt = 0;
          set outer_cnt = outer_cnt + 1;
        else
          begin
          end;
        end if;
      end while;

    end //
    delimiter ;

    call doiterate();

    /*
    +-----------+-----------+
    | inner_cnt | outer_cnt |
    +-----------+-----------+
    |         0 |         0 |
    +-----------+-----------+
    1 row in set (0.001 sec)

    +-----------+-----------+
    | inner_cnt | outer_cnt |
    +-----------+-----------+
    |         1 |         0 |
    +-----------+-----------+
    1 row in set (0.003 sec)

    +-----------+-----------+
    | inner_cnt | outer_cnt |
    +-----------+-----------+
    |         0 |         1 |
    +-----------+-----------+
    1 row in set (0.005 sec)

    +-----------+-----------+
    | inner_cnt | outer_cnt |
    +-----------+-----------+
    |         1 |         1 |
    +-----------+-----------+
    1 row in set (0.007 sec)

    Query OK, 0 rows affected (0.009 sec)
    */
    ```

    ### not deterministicなfunction

    ```sql
    -- 実行した日付で返り値が異なる場合
    -- 引数が同じでも返り値が異なる場合があるので、
    -- notdeterministicを使う

    create temporary table tax (
      id int primary key auto_increment,
      value double precision,
      start_date date
    );

    insert into tax (value, start_date)
    values
    (0.05, "1997-04-01"),
    (0.08, "2014-04-01"),
    (0.1, "2019-04-01"),
    (0.2, "2030-01-01")
    ;

    delimiter //
    create function 
      if not exists 
      getPrice(price int, now_date date) returns int
    not deterministic
    begin
      declare tax double default 0.1;

      select value into tax
      from tax
      where
        start_date <= now_date
      order by
        start_date  desc
      limit 1
      ;

      return truncate(price + (price * tax), 0);
    end
    //
    
    delimiter ;

    -- 2024年の消費税
    select
      getPrice(100, current_date) as tax
    ;

    /*
    +------+
    | tax  |
    +------+
    |  110 |
    +------+
    1 row in set (0.000 sec)
    */

    -- 2034年の消費税(予想)
    select 
      getPrice(
        100, 
        date_add(current_date, interval 10 year)
      ) tax
    ;

    /*
    +------+
    | tax  |
    +------+
    |  120 |
    +------+
    1 row in set (0.001 sec)
    */
    ```

## Q125 カーソル&ハンドラについて知っていますか?

??? success
    ### カーソル

    ```text
    ・declare <cursor_name> cursor for select ...で定義
    ・openで宣言したカーソルの処理を開始
    ・closeでカーソルを閉じる
    ・fetch <cursor_name> into var_listで1行ずつデータ取得
      して、変数に格納
    ・最終行までfetchした後で、fetchするとnot foundという
      ハンドラ(SQL STATE: 02000)が得られるので、
      これを利用しループを抜ける
    
    ⇒行をスキップすることはできない
    ⇒読み取り専用でカーソル自体を更新することはできない
    ```

    ### ハンドラ

    ```text
    ・ストアドプログラムには、プログラム内で特定の条件が
      発生した時に呼び出されるハンドラを含めることが可能
    
    declare handler_type
      handler for <条件:condition_value> statement
    
    ・handler_typeには、continue, exitを指定可能
      continueならハンドラ実行後、元の場所に戻る
      exitならプログラムを終了する
    
    ・condition_valueにはハンドラをキャッチする条件を指定
      特定のSQLSTATE(5文字の文字列リテラル)をキャッチしたり
      SQLWARNING(01で始まるSQLSTATE)をキャッチしたり
      NOT FOUND(02で始まるSQLSTATE)をキャッチしたり
      SQLEXCEPTION(01,02以外で始まるSQLSTATE)をキャッチ
      mysql_error_code: 特定のエラーコードをキャッチしたり
    
    ⇒カンマ区切りで複数指定も可能
    
      declare <condition_name>
        condition for <条件:conditon_value>
      であらかじめ条件を定義しておいて、condition_name
      をhandler forの後に書くこともできる

      statement
        複数の場合、begin, endで囲う
    
    ```

    ### 簡単な例外ハンドラの例

    ```sql
    -- ERROR 1146(42S02)は
    -- DB上に該当するテーブルが見つからない時に出力される
    -- これをハンドリングする

    delimiter //
    create procedure notExistTable()
    begin
      declare done int default 0;
      declare exit
        handler for SQLSTATE "42S02"
        select "Table doesn't exist" Error
        ;
      -- ハンドラが発火する
      select * from hoge; 
    end;
    //

    delimiter ;

    call notExistTable();

    /*
    +---------------------+
    | Error               |
    +---------------------+
    | Table doesn't exist |
    +---------------------+
    1 row in set (0.012 sec)

    Query OK, 0 rows affected (0.023 sec)
    */
    ```

    ### 簡単なカーソルの例

    ```sql
    create temporary table t1 (
      name char(16),
      num int
    );

    create temporary table t2 (
      num int
    );

    create temporary table t3 (
      name char(16),
      num int
    );

    insert into t1
    values
    ("val1", 3),
    ("val2", 7),
    ("val3", 33)
    ;

    insert into t2
    values
    (10), (6)
    ;


    -- delimiterで変える場合、
    -- ;は区切り文字ではなくなっているので
    -- ;の後で --というタイプのコメントを書くとエラーが出る可能性がある
    -- これは--が行末までコメントアウトするため
    -- 別行に書いた方が安全(もしくは/**/)
    delimiter //
    create procedure curdemo()
    begin
      declare done int default false;
      declare a char(16);
      declare b, c int;

      /*
        fetchするときは、selectステートメントのカラム数が
        fetchステートメントで指定された変数の数に一致
        している必要がある(またここにintoを含めてはダメ)
      */
      declare cur1 cursor for 
        select name, num from t1;
      declare cur2 cursor for
        select num from t2;
      
      /*
        t1 or t2の最終行まで達したらnot found
        not foundの場合、t3に格納はせず、loopを抜ける
        handler for sqlstate "02000"でもいける
      */
      declare continue
      handler for not found
      set done = true;

      open cur1;
      open cur2;

      /*
        cur1.name -> a, cur1.num --> b
        cur2.num -> c
      */
      read_loop: LOOP
        fetch cur1 into a, b;
        fetch cur2 into c;
        if done then
          leave read_loop;
        end if;

        /*小さい方を格納*/
        if b < c then
          insert into t3 values (a,b);
        else
          insert into t3 values (a,c);
        end if;
      end loop;

      /*
      repeatを使う場合
      repeat
        fetch cur1 into a,b;
        fetch cur2 into c;
        if not done then
          if b < c then
            insert into t3 values (a,b);
          else
            insert into t3 values (a,c);
          end if;
        end if;
      until done 
      end repeat;
      */

      close cur1;
      close cur2;
    end;
    //

    delimiter ;

    call curdemo();

    select * from t3;
    /*
    +------+------+
    | name | num  |
    +------+------+
    | val1 |    3 |
    | val2 |    6 |
    +------+------+
    2 rows in set (0.000 sec)
    */
    ```

    !!! warning
        ### declareの順番

        ```text
        ・変数
        ・カーソル
        ・ハンドラ

        この順で宣言する
        ```

## Q126 トリガーについて知っていますか?

??? success
    ### 作り方と消し方

    ```text
    create 
      trigger <trigger_name>
      <trigger_time>
      <trigger_event>
      on <table_name> for each row
      [<trigger_order>]
      <trigger_body>
    
    drop trigger <trigger_name>
    ⇒テーブルをdropすると一緒に消える
    
    trigger_name: 
      ・トリガー名を記載
    trigger_time: 
      ・before or after
      ・トリガーのタイミングを記載
    trigger_event
      ・insert/update/delete
    trigger_order
      ・follows <他のトリガー>　⇒他のトリガーの後で実行
      ・precedes <他のトリガー> ⇒他のトリガーの前に実行
    trigger_body
      ・更新前のカラムにアクセスする場合は、old.column_name
      ・更新後のカラムにアクセスしたいなら、new.column_nam
      ・トリガからselectステートメントを実行することは
        出来ないので注意
    for each row
      ・更新行ごとに1回ずつtrigger_bodyが実行されることを
        意味する。
    ```

    ### トリガーの例

    ```sql
    create table account (
      acct_num int,
      amount decimal(10, 2)
    );

    -- 挿入後のテーブルのamountカラムの値を加算していく
    create 
      trigger ins_sum
      before
      insert
      on account
      for each row
      set @sum = @sum + new.amount
    ;

    set @sum = 0;
    insert into account
    values
    (1, 14.98),
    (2, 180.10),
    (3, -100.00)
    ;

    select @sum as "Total amount inserted";

    /*
    +-----------------------+
    | Total amount inserted |
    +-----------------------+
    |                 95.08 |
    +-----------------------+
    1 row in set (0.000 sec)
    */
    ```

    ### oldとnewについて

    ```text
    ・insertトリガ内ではnew.column_nameだけを使用可能
    ・deleteトリガ内ではold.column_nameだけを使用可能
    ・updateトリガ内では両方を使用可能

    ・oldは読み取り可能。変更は不可
    ・newは変更可能。ただしafterトリガの場合、既に行に
      変更が生じているため、beforeトリガを使う必要がある
    ```

    ### トリガの例(範囲処理)

    ```sql
    -- new&beforeを用いた挿入値の範囲チェック
    -- 一時テーブルに対してトリガを設定することはできない

    create table account (
      acct_num int,
      amount decimal(10, 2)
    );

    delimiter //
    create trigger update_check
    before update on account
    for each row
    begin
      if new.amount < 0 then
        set new.amount = 0;
      elseif new.amount > 100 then
        set new.amount = 100;
      end if;
    end;
    //

    delimiter ;

    insert into account
    values
    (1, 10),
    (2, 40),
    (3, 80);

    update account
    set amount =  -100
    where acct_num = 1;

    update account
    set amount = 110
    where acct_num = 2;

    update account
    set amount = 40
    where acct_num = 3;

    select * from account;

    -- 丸め込み処理が為されている
    /*
    +----------+--------+
    | acct_num | amount |
    +----------+--------+
    |        1 |   0.00 |
    |        2 | 100.00 |
    |        3 |  40.00 |
    +----------+--------+
    3 rows in set (0.000 sec)
    */
    ```

    ### トリガの例(変更履歴のチェック)

    ```sql
    create table if not exists users (
      id int primary key auto_increment,
      user_name varchar(10),
      email varchar(255)
    );

    insert into users
    (user_name, email)
    values
    ("AAA", "AAA@gmail.com"),
    ("BBB", "BBB@gmail.com"),
    ("CCC", "CCC@gmail.com")
    ;

    create table email_update_log (
      id int primary key auto_increment,
      user_id int,
      before_email varchar(255),
      after_email varchar(255),
      updated_at timestamp
    );

    delimiter //

    drop trigger if exists email_update_trigger //

    /*
      タイミングがafterなのでちゃんと更新が行われてから作動
      transaction制御してないので確認が必要
    */
    create trigger email_update_trigger
    after update on users
    for each row
    begin
      if old.email <> new.email then
        insert into email_update_log
        (user_id, before_email, after_email, updated_at)
        values
        (new.id, old.email, new.email, current_timestamp);
      end if;
    end
    //
    delimiter ;

    update users
      set email = "DDD@gmail.com"
      where user_name = "AAA"
    ;

    -- 同じ場合はログに載らない
    update users
      set email = "BBB@gmail.com"
      where user_name = "BBB"
    ;

    select * from email_update_log;

    /*
    +----+---------+---------------+---------------+---------------------+
    | id | user_id | before_email  | after_email   | updated_at          |
    +----+---------+---------------+---------------+---------------------+
    |  1 |       1 | AAA@gmail.com | DDD@gmail.com | 2024-04-03 15:31:30 |
    +----+---------+---------------+---------------+---------------------+
    1 row in set (0.001 sec)
    */
    ```

## Q127 get diagnosticsについて知っていますか?

??? success

    ### get diagnosticsについて
    ```text
    ・直前に実行されたステートメントに関する情報取得を行う
    
    ・function,procedure,triggerの中で使用する
    ・通常ストアドの中のハンドラで使用される
    
    ・構文は以下

    get [current | stacked] diagnostics 
      statement_information_item_list 
      | 
      condition condition_number 
      condition_information_item_list

    current
      現在の診断領域から情報を取得する。デフォルト
    stacked
      現在のコンテキストが条件ハンドラである場合のみ使用可能
    
    診断領域について
      statement_information_item_list
        ・発生した条件の数や影響を受けた行数が含まれる
      conditon_information_item_list
        ・エラーコードやメッセージなどの条件情報が含まれる
      
      ⇒このうちどちらかしかdiagosticsには記述できない
      ⇒たとえば成功した場合、statementを取得
      ⇒失敗した場合conditionを取得という方法が考えられる
      ⇒その場合、ハンドラの中にconditionを書き、
        statementはハンドラ外で書く
    ```

    ### Diagnosticsの例

    ```sql
    -- beginの後に;を付けてはいけない
    -- mariadb10.7以降ならrow_numberが使える
    -- updateに成功したタイミングでまた試す
    -- (linuxコマンドに精通してからの方がいい)

    delimiter //
    create procedure do_insert(value int)
    begin
      declare code char(5) default '00000';
      declare msg text;
      declare errno int;
      declare nrows int;
      declare result text;

      declare continue handler for sqlexception
      begin
        get diagnostics condition 1
        code = returned_sqlstate,
        msg = message_text,
        errno = mysql_errno
        ;
      end;

      insert into t1(int_col) values (value);

      /*前文でエラーになっていなければifを通る*/
      if code = "00000" then
        get diagnostics nrows = row_count;
        set result = concat(
          "insert successed row count = ", 
          nrows
        );

        select result;
      else
        select code, errno, msg; 
      end if;
    end;
    //

    delimiter ;

    create table t1(
      int_col int
    );

    call do_insert(4);

    /*
    +--------------------------------+
    | result                         |
    +--------------------------------+
    | insert successed row count = 1 |
    +--------------------------------+
    1 row in set (0.009 sec)

    Query OK, 1 row affected (0.010 sec)
    */

    drop table t1;

    call do_insert(8);
    /*
    +-------+-------+---------------------------------+
    | code  | errno | msg                             |
    +-------+-------+---------------------------------+
    | 42S02 |  1146 | Table 'sakila.t1' doesn't exist |
    +-------+-------+---------------------------------+
    1 row in set (0.001 sec)

    Query OK, 0 rows affected (0.001 sec)
    */
    ```
## Q128 ProcedureとTransactionを組み合わせた例について知っていますか?

??? success
    ### procedureはトランザクション処理が可能

    ```text
    ・procedureの中でstart transactionを行い、
      トランザクション処理を開始できる

    ・rollback, commitを使用することで、
      複数ステートメントの原子性を担保できる
    ```

    ```sql
    create table t1 (
      value int
    );

    /*
    sqlstateは予約語なので、変数名には使わない
    */

    delimiter //
    create procedure t1_insert()
    begin
      declare code char(5) default "00000";
      declare errno int;
      declare msg text;

      declare exit handler for sqlexception, sqlwarning
      begin
        get diagnostics condition 1
        code = returned_sqlstate,
        errno = mysql_errno,
        msg = message_text
        ;
        select code, errno, msg;
        rollback;
      end;
      
      start transaction;

        insert into t1 values
        (3);

        /*型が違うのでここでエラーになる*/
        insert into t1 values
        ("neko");

        commit;
        select "Success!" as result;
      end;
      //

      delimiter ;

      call t1_insert();

    /*
    +-------+-------+---------------------------------------------------------------------------+
    | code  | errno | msg                                                                       |
    +-------+-------+---------------------------------------------------------------------------+
    | 22007 |  1366 | Incorrect integer value: 'neko' for column `sakila`.`t1`.`value` at row 1 |
    +-------+-------+---------------------------------------------------------------------------+
    */

    /*rollbackしたので*/
    select * from t1;
    -- Empty set (0.001 sec)
    ```