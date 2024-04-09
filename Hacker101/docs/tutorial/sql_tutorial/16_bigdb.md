# (クイズ16:大規模DB)

## Q137 パーティショニングについて知っていますか?

??? success
    ### 年々肥大化するDB

    ```text
    ・RDBの初期は、ハードディスクの容量もせいぜい数MB
    
    ・今はTB, PB, EB単位になりつつある

    ・クラウドのストレージに至っては、金を払えば
      実質無制限ともいえる
    ```

    ### DBが大きくなりすぎるとどうなるか

    ```text
    [以下のタスクが時間を要し、イライラしだす]

    ・フルテーブルスキャン
    ・Indexの作成と再構築
    ・データのアーカイブと削除
    ・Table/Indexの統計データの作成
    ・Tableの再配置
    ・DBのBackUp

    [時間を要するとどうなるか]
    ・管理の時間は有限なので、管理しなくなる
    ```

    ### パーティショニングとは

    ```text
    ・大きなテーブルを分割すること
    ・最初に分割(パーティショニング)しておくのが簡単

    ・管理タスクは個々のパーティションで行ったり、
      同時に行ったりする
    
    ・タスクによってはパーティションをスキップ可能
    ```

    ### パーティショニング概要

    ```text
    [概要]
    ・全てのDBサーバにはTable/Indexを分割する機能がある

    ・Tableを分割すると、2つ以上の
      テーブルパーティションが生成される
    
    ・それぞれのパーティションの定義は同じ

    ・分割しているので、重なっているデータ部分はない

    [分割の例]
    ・売上データを月ごとに分割する
    ・売上データを、地域ごとに分割する

    [分割した後のテーブル]
    ・仮想概念になる
    ・データを実際に含んでいるのはパーティション
    ・Indexもパーティション内のデータに基づき作成される

    ★ユーザ側はテーブルが分割されていることを意識しないまま
      使い続けることが可能
    ```

    ### 同じでなければならないものと違っていてもいいもの

    ```text
    [同じ]
    ・各パーティションのスキーマ定義（テーブルの列や型）は
      同じである必要がある
    
    [違う]
    ・各パーティションが、物理的に異なるストレージの
      別の表領域に格納されていてもいい
    
    ・各パーティションを、異なる圧縮方式で圧縮してもいい

    ・一部のパーティションで、パーティションごとの専用の
      Indexであるローカルインデックスを削除可能
    
    ・一部のパーティションで、テーブルの統計データを凍結
      することが可能
    
    ・よりアクセスするパーティションをメモリに保管し
      アクセスしないデータをフラッシュストレージに格納する
      ことで、効率的にメモリを使うことが可能
    ```

    !!! warning
        ### パーティショニング不可能なStorage Engine

        ```text
        ・InnoDB等の基本的なEngineはパーティショニングを
          サポートしているが、していないものもある
        ```

## Q138 テーブルやインデックスの分割手法について知っていますか?

??? success
    ### 水平分割

    ```text
    ・行全体を1つのパーティションに割り当てる

    ・パーティションキーを（多くの場合単一の）列に割り当て
      この列にパーティション関数を適用することで、
      各行を割り当てるパーティションを決定する
    ```

    ### 垂直分割

    ```text
    ・行内の列を異なるパーティションに配置する
    
    ・全ての列が必要でない場合、
      頻繁に使用する列に対して応答速度を上げたい場合に使用
    ```

    ### グローバル/ローカルインデックス

    ```text
    ・分割したテーブルにインデックスが含まれている場合
      ⇒インデックスをそのまま使う（グローバルインデックス）

      ⇒インデックスを分割して、パーティションごとに
        専用のIndexを割り当てる（ローカルインデックス）
    
    [ローカルインデックスの使いどころ]

    ・検索が各パーティション内で完結する場合
      ⇒他のパーティションは必要ないので
        ローカルインデックスがあるといい
      
    ・更にクエリでパーティションキーの値を指定する場合
      （一般的にパーティションキーが
      　ローカルインデックスになるので）

    [グローバルインデックスの使いどころ]

    ・複数パーティションを縦断する検索を行う場合
      ⇒グローバルインデックスは、すべてのパーティション
      　にまたがるインデックスなので全てのパーティション
      　を調べる手間が省ける
    
    ・検索は単一パーティション内で完結するが、
    　クエリでパーティションキーの値を使用しない場合
      ⇒パーティションキーによるインデックス付けが行えないので
        グローバルインデックスを使用する
    ```

    ### 例

    ```sql
    -- tableをsale_date列で分割し、
    -- geo_region_cd列がglobal indexとする

    -- 更に1つのパーティション内でクエリは実行されるとする

    /*
    パーティションキーが使われていないので、
    global indexが使える
    */
    select
      sum(amount)
    from
      sales
    where
      geo_region_cd = "US"
    ;

    /*
    パーティションキーが使われているので
    local indexが使える
    */
    select
      sum(amount)
    from
      sales
    where 
      sale_date 
      between "2007-01-01" and "2008-01-01"
    ;

    ```

## Q139 範囲分割を知っていますか?

??? success
    ### 範囲分割

    ```text
    ・もっとも一般的な分割手法
    ・日付の範囲でテーブルを分割するのが一般的
    ```

    ```sql

    /*
    範囲は、連続しているが、重複しないものでなければならない
    そのために、values less than演算子が使われる

    MAXVALUESは明示的に指定されている最大値を超える
    全ての値を受け入れる

    less thanなので、
    2020を指定すれば、< 2020になる
    2020年第1週までのデータを格納したいなら202002と指定する
    */
    create table sales
    (
      sale_id int not null,
      cust_id int not null,
      store_id int not null,
      sale_date date not null,
      amount decimal(9,2)
    )
    partition by range (yearweek(sale_date))
    (
      partition s1 values less than (202002),
      partition s2 values less than (202003),
      partition s3 values less than (202004),
      partition s4 values less than (202005),
      partition s5 values less than (202006),
      partition s999 values less than (maxvalue)
    );

    -- Query OK, 0 rows affected (0.039 sec)
    ```

    ### 分割したテーブルのメタデータを見る

    ```sql
    /*
    paritition_method:
      パーティションのタイプ
    partition_expression
      パーティション化関数の式
      HASH(c1 + c2)なら、c1 + c2が表示される
    partition_description
      rangeの場合、values less than句
      lustの場合、values in句
    table_rows
      ・パーティション内のテーブルの行数(推測)
    partition_ordinal_position
      ・パーティションとして定義された順番
    */
    select
      partition_name name,
      partition_method method,
      partition_expression,
      partition_description "詳細",
      table_rows row
    from
      information_schema.partitions
    where
      table_name = "sales"
    order by
      partition_ordinal_position
    ;

    /*
    +------+--------+-------------------------+----------+-----+ 
    | name | method | partition_expression    | 詳細     | row | 
    +------+--------+-------------------------+----------+-----+ 
    | s1   | RANGE  | yearweek(`sale_date`,0) | 202002   |   0 | 
    | s2   | RANGE  | yearweek(`sale_date`,0) | 202003   |   0 | 
    | s3   | RANGE  | yearweek(`sale_date`,0) | 202004   |   0 | 
    | s4   | RANGE  | yearweek(`sale_date`,0) | 202005   |   0 | 
    | s5   | RANGE  | yearweek(`sale_date`,0) | 202006   |   0 | 
    | s999 | RANGE  | yearweek(`sale_date`,0) | MAXVALUE |   0 | 
    +------+--------+-------------------------+----------+-----+ 
    6 rows in set (0.001 sec)
    */
    ```

    ### ALTER TABLEを使って特定のパーティションを更に分割

    ```sql
    alter table sales reorganize partition s999 into
    (
      partition s6 values less than (202007),
      partition s7 values less than (202008),
      partition s999 values less than (MAXVALUE)
    )
    ;
    -- Query OK, 0 rows affected (0.113 sec)
    -- Records: 0  Duplicates: 0  Warnings: 0
    ```

    ```sql
    -- 再度定義を見る
    select
      partition_name name,
      partition_method method,
      partition_description "<"
    from
      information_schema.partitions
    where
      table_name = "sales"
    order by
      partition_ordinal_position
    ;

    /*
    +------+--------+----------+
    | name | method | <        |
    +------+--------+----------+
    | s1   | RANGE  | 202002   |
    | s2   | RANGE  | 202003   |
    | s3   | RANGE  | 202004   |
    | s4   | RANGE  | 202005   |
    | s5   | RANGE  | 202006   |
    | s6   | RANGE  | 202007   |
    | s7   | RANGE  | 202008   |
    | s999 | RANGE  | MAXVALUE |
    +------+--------+----------+
    8 rows in set (0.001 sec)
    */
    ```

    ### 行を追加して確認してみる

    ```sql
    -- 追加する際は、Tableを指定するだけでいい

    insert into sales
    values
    (1, 1, 1, "2020-01-01", 5211.12),
    (2, 3, 4, "2020-01-05", 2765.34),
    (3, 5, 6, "2020-01-12", 2765.34)
    ;

    -- 行数を見る
    /*
    01-01は2019の最終週, 01-05~11は202001
    ⇒つまり、どちらもS1に収まる

    from句の後でpartition (partition_name)を指定すると、
    特定のパーティションの値だけを取得できる
    */
    select 
      concat("rows in S1 = ", count(*)) partition_rowcount
    from sales partition (s1) union all
    select
      concat("rows in S2 = ", count(*))
    from sales partition (s2) union all
    select
      concat("rows in S3 = ", count(*))
    from sales partition (s3) union all
    select
      concat("rows in S4 = ", count(*))
    from sales partition (s4) union all
    select
      concat("rows in S5 = ", count(*))
    from sales partition (s5) union all
    select
      concat("rows in S6 = ", count(*))
    from sales partition (s6) union all
    select
      concat("rows in S7 = ", count(*))
    from sales partition (s7) union all
    select
      concat("rows in S999 = ", count(*))
    from sales partition (s999)
    ;

    /*
    +--------------------+
    | partition_rowcount |
    +--------------------+
    | rows in S1 = 2     |
    | rows in S2 = 1     |
    | rows in S3 = 0     |
    | rows in S4 = 0     |
    | rows in S5 = 0     |
    | rows in S6 = 0     |
    | rows in S7 = 0     |
    | rows in S999 = 0   |
    +--------------------+
    8 rows in set (0.002 sec)
    */

    select * from sales partition(s1);
    /*
    +---------+---------+----------+------------+---------+      
    | sale_id | cust_id | store_id | sale_date  | amount  |      
    +---------+---------+----------+------------+---------+      
    |       1 |       1 |        1 | 2020-01-01 | 5211.12 |      
    |       2 |       3 |        4 | 2020-01-05 | 2765.34 |      
    +---------+---------+----------+------------+---------+      
    2 rows in set (0.001 sec)
    */
    ```

## Q140 リスト分割について知っていますか?