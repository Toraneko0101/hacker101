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

    ・範囲には、連続しているが、重複しないものを指定する
    　そのために、values less than演算子が使われる
    
    ・less thanなので、
    　2020を指定すれば、< 2020になる
    　2020年第1週までのデータを格納したいなら202002と指定する

    ・次第に増加していく必要があるので、
      1つ目で < 2018とし、2つ目で < 2017とする事はできない
      順番を間違えると、以下のようなエラーが出る
      ERROR 1493 (HY000): 
        VALUES LESS THAN value must be strictly 
        increasing for each partition
    
    ・MAXVALUESは明示的に指定されている最大値を超える
      全ての値を受け入れる
      ⇒つまり、範囲(RANGE)の場合、最後に指定する

    ```

    ```sql

    /*
    partition by <分割方法> 

    ⇒rangeの場合、
      整数値, または返り値が整数値になる物以外を指定できない
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

??? success
    ### リスト分割

    ```text
    ・partition-keyが列挙値の場合は、リスト分割が優れている
    ・MAXVALUEのような受け皿は定義できない
    ```

    ```text
    [listとlist columnの違い]
    partition by list
      ・values inに整数値しか指定できない
    
    partition by list column
      ・values inに整数値以外も指定可能
    ```

    ```sql
    create table sales
    (
      sale_id int not null,
      cust_id int not null,
      store_id int not null,
      sale_date date not null,
      geo_region_cd varchar(6) not null,
      amount decimal(9,2)
    )
    partition by list columns (geo_region_cd)
    (
      partition northamerica values in (
        "US_NE", "US_SE", "US_MW", "US_NW",
        "US_SW", "CAN", "MEX"
      ),
      partition europe values in ("EUR_E", "EUR_W"),
      partition asia values in ("CHN", "JPN", "IND")
    );
    ```

    ### パーティション外の値を指定した場合

    ```sql
    insert into sales
    values
    (1,1,1, "2001-01-01", "US_NE", 100),
    (2,3,4, "2001-01-01", "CAN", 200),
    (3,5,6, "2001-01-01", "KOR", 300)
    ;
    -- ERROR 1526 (HY000): 
    -- Table has no partition for value from column_list
    ```

    ### IGNOREを使って挿入した場合

    ```sql
    -- 一致しないパーティショニングカラム値が含まれる行は
    --　挿入されず、エラーは報告されない

    insert ignore into sales
    values
    (1,1,1, "2001-01-01", "US_NE", 100),
    (2,3,4, "2001-01-01", "CAN", 200),
    (3,5,6, "2001-01-01", "KOR", 300)
    ;

    -- Query OK, 2 rows affected, 1 warning (0.003 sec)
    -- Records: 3  Duplicates: 1  Warnings: 1

    show warnings;

    /*
    +---------+------+---------------------------------------------------+
    | Level   | Code | Message                                           |
    +---------+------+---------------------------------------------------+
    | Warning | 1526 | Table has no partition for value from column_list |
    +---------+------+---------------------------------------------------+
    */

    select sale_id, geo_region_cd from sales;
    /*
    +---------+---------------+
    | sale_id | geo_region_cd |
    +---------+---------------+
    |       1 | US_NE         |
    |       2 | CAN           |
    +---------+---------------+
    2 rows in set (0.001 sec)
    */

    alter table sales
      reorganize partition asia into (
        partition asia values in ("CHN", "JPN", "IND", "KOR")
      )
    ;

    insert into sales
    values
    (3,5,6, "2001-01-01", "KOR", 300)
    ;
    -- Query OK, 1 row affected (0.003 sec)
    ```

## Q141 RANGE COLUMNSによって知っていますか?

??? success

    ### RANGEとの差異

    ```text
    ・range columnsは式を受け入れない
    ・range columnsは複数のカラムのリストを受け入れ
      タプル単位で比較する
    ・rabnge columnsには整数以外も指定可能
    
    ```

    ### タプル単位での比較

    ```sql
    select 
      row(5,10) < row(5, 11),
      row(5, 11) < row(5, 11)
    ;

    /*
    +------------------------+-------------------------+
    | row(5,10) < row(5, 11) | row(5, 11) < row(5, 11) |
    +------------------------+-------------------------+
    |                      1 |                       0 |
    +------------------------+-------------------------+
    1 row in set (0.000 sec)
    */
    ```

    ```sql
    /*
    RANGEの項でも触れたが、指定する順番を間違えやすいので注意
    ⇒条件は、次第に増加していく必要がある
    */
    create table rc1 (
      a int,
      b varchar(5)
    )
    partition by range columns (a, b) (
      partition 0a values less than (0, "b"),
      partition 0b values less than (0, "c"),
      partition 1a values less than (1, "b"),
      partition 9z values less than (maxvalue, maxvalue)
    );

    insert into rc1 values
    (0, "a"),
    (0, "bz"),
    (1, "azfds"),
    (4, "a")
    ;

    select 
      concat("rows in 0a = ", count(*)) partition_count
    from rc1 partition (0a) partition_count union all
    select 
      concat("rows in 0b = ", count(*))
    from rc1 partition (0b) partition_count union all
    select 
      concat("rows in 1a = ", count(*))
    from rc1 partition (1a) partition_count union all
    select 
      concat("rows in 9z = ", count(*))
    from rc1 partition (9z) partition_count
    ;

    /*
    +-----------------+
    | partition_count |
    +-----------------+
    | rows in 0a = 1  |
    | rows in 0b = 1  |
    | rows in 1a = 1  |
    | rows in 9z = 1  |
    +-----------------+
    4 rows in set (0.001 sec)
    */
    ```

    !!! warning 
        ### MAXVALUEの使い方

        ```text
        ・複数のpartition定義の最初のカラムの制限値として、
          MAXVALUEを使うことはできない

          たとえば、以下のようなケースはダメ
          partition 0a values less than (MAXVALUE, "b")
          partition 0a values less than (MAXVALUE, MAXVALUE)
        ```

    ### よくあるRANGE COLUMNSの例(時間型)

    ```sql
    create table e1 (
      employee_id int,
      employed_date date
    )

    partition by range columns (employed_date) (
      partition p0 values less than ("1970-01-01"),
      partition p1 values less than ("1980-01-01"),
      partition p2 values less than ("1990-01-01"),
      partition p3 values less than ("2000-01-01"),
      partition p4 values less than ("2010-01-01"),
      partition p5 values less than ("2020-01-01"),
      partition p6 values less than (maxvalue)
    );

    insert into e1 values
    (1, "1969-01-01"),
    (2, "1989-01-01"),
    (3, "1998-01-01"),
    (4, "2012-01-01"),
    (5, "2023-01-01"),
    (6, now())
    ;

    select
      concat("rows in p0 = ", count(*)) partition_count
    from e1 partition (p0) union all
    select
      concat("rows in p1 = ", count(*)) partition_count
    from e1 partition (p1) union all
    select
      concat("rows in p2 = ", count(*)) partition_count
    from e1 partition (p2) union all
    select
      concat("rows in p3 = ", count(*)) partition_count
    from e1 partition (p3) union all
    select
      concat("rows in p4 = ", count(*)) partition_count
    from e1 partition (p4) union all
    select
      concat("rows in p5 = ", count(*)) partition_count
    from e1 partition (p5) union all
    select
      concat("rows in p6 = ", count(*)) partition_count
    from e1 partition (p6)
    ;

    /*
    +-----------------+
    | partition_count |
    +-----------------+
    | rows in p0 = 1  |
    | rows in p1 = 0  |
    | rows in p2 = 1  |
    | rows in p3 = 1  |
    | rows in p4 = 0  |
    | rows in p5 = 1  |
    | rows in p6 = 2  |
    +-----------------+
    7 rows in set (0.003 sec)
    */
    ```

## Q142 ハッシュ分割について知っていますか?

??? success
    ### ハッシュ分割

    ```text
    ・一連のパーティションに行を均等に分割する?
      ⇒均等と言っているのに、パーティションキーが必要な理由
        は以下でわかる。

    ・パーティションキーとして選択する列が、
      範囲分割やリスト分割に適していない場合に使用
      ⇒たとえば、列の種類が数種類に定まっている場合は
        リスト分割で済む。
      ⇒列の種類が多い方がいい（理由は後述）
    
    ・呼称の利用は、DBサーバが列の値に
      ハッシュ関数を適用するところから
    ```

    ### ハッシュ分割の例

    ```sql
    /*
    partition句を省いてみた
    以下の場合、パーティション名はp0, p1, ...と割り振られる

    なお、partitionを省略し、partitions 4の部分も省くと、
    パーディション数はデフォルトの1になる
    */
    create table t1 (
      col1 int,
      con2 char(5),
      col3 date
    )
    partition by hash (year(col3))
    partitions 3
    ;

    insert into t1 values
    (1, "aaaaa", "2005-01-01"),
    (1, "aaaab", "2006-01-01"),
    (1, "aaaac", "2007-01-01"),
    (1, "aaaad", "2005-01-01"),
     (1, "aaaaa", "2005-01-02"),
    (1, "aaaae", "2005-01-01")
    ;

    select concat("rows in p0 = ", count(*)) p_count
    from t1 partition (p0) union all
    select concat("rows in p1 = ", count(*)) p_count
    from t1 partition (p1) union all
    select concat("rows in p2 = ", count(*)) p_count
    from t1 partition (p2)
    ;

    /*
    +----------------+
    | p_count        |
    +----------------+
    | rows in p0 = 1 |
    | rows in p1 = 4 |
    | rows in p2 = 1 |
    +----------------+
    3 rows in set (0.001 sec)
    */

    ```

    ### 列の種類が少ないと役に立たないわけ

    ```text
    ・前項のp_countを見ると、1,4,1と等しく割り振られている
      わけではないことが分かる
    
    ・それはこのような計算が行われているからである
    ```

    ```sql
    -- year(col) == 2005なら

    select mod(year("2005-01-01"), 4) ans;

    -- 1なのでp1に格納される
    /*
    +------+
    | ans  |
    +------+
    |    1 |
    +------+
    1 row in set (0.000 sec)
    */
    ```

## Q143 複合分割について知っていますか?

??? success
    ### 異なる種類の分割方式を使う

    ```text
    ・1つ目の分割方式でパーティションを定義し、
      2つ目の分割方式でサブパーティションを定義する
    ```

    ### 制限

    ```text
    ・サブパーション化できるのは、RANGE/LISTで
      パーティション化されたテーブルだけ
      ⇒RANGE COLUMNS等も可能
    
    ・サブパーティショニングにはHASH/KEYが使用できる
    ```

    ### サブパーティショニングの例

    ```sql
    /*
    partition(3) * subpartition(2) = テーブルは6分割される
    ※以下の例では、サブパーティションのSUBPARTION句を
      省いている
      ⇒その場合、サブパーティション名は
        パーティション名 + SP0等になる
        (サブパーティション名もテーブル内で重複しては
        いけないので一意になるようにしている)
    */
    create table ts(id int, purchased date)
      partition by range (year(purchased))
      subpartition by hash(to_days(purchased))
      subpartitions 2 
      (
        partition p0 values less than (1990),
        partition p1 values less than (2000),
        partition p2 values less than maxvalue
      )
      ;

      select
        partition_name,
        subpartition_name
      from information_schema.partitions 
      where table_name = "ts"
      order by
        partition_ordinal_position,
        subpartition_ordinal_position
      ;

      /*
    +----------------+-------------------+
    | partition_name | subpartition_name |
    +----------------+-------------------+
    | p0             | p0sp0             |
    | p0             | p0sp1             |
    | p1             | p1sp0             |
    | p1             | p1sp1             |
    | p2             | p2sp0             |
    | p2             | p2sp1             |
    +----------------+-------------------+
    6 rows in set (0.001 sec)
      */


    /*冗長な例*/
    create table ts (id int , purchased date)
    partition by range (year(purchased))
    subpartition by hash (to_days(purchased))
    -- subpartitions 2
    (
      partition p0 values less than (1990) (
        subpartition s0,
        subpartition s1
      ),
      partition p1 values less than (maxvalue) (
        subpartition s2,
        subpartition s3
      )
    )
    ;

    ```

    ### 1つのサブパーティションからデータを取得する

    ```sql
    create table ts(id int, purchased date)
      partition by range (year(purchased))
      subpartition by hash(to_days(purchased))
      subpartitions 2 
      (
        partition p0 values less than (1990),
        partition p1 values less than (2000),
        partition p2 values less than maxvalue
      )
      ;

    insert into ts values
    (1, "1989-01-01"),
    (2, "1989-01-14"),
    (3, "1989-01-27"),
    (4, "1989-02-11"),
    (5, "1989-03-14"),
    (6, "1989-04-17"),
    (7, "2001-05-04")
    ;

    select * from ts partition (p0sp0);
    /*
    +------+------------+
    | id   | purchased  |
    +------+------------+
    |    1 | 1989-01-01 |
    |    3 | 1989-01-27 |
    |    5 | 1989-03-14 |
    |    6 | 1989-04-17 |
    +------+------------+
    4 rows in set (0.000 sec)
    */

    ```

## Q144 その他の分割法について知っていますか?

??? success
    ### 線形ハッシュ

    ```text
    [hashとの差異]
    ・partitionの追加/削除/merge/分割の速度が上がる
    ・均等に配分される可能性は低い
    ```

    ```sql
    create table t1 (
      id int not null,
      hired date not null default "1970-01-01"
    )
    partition by linear hash( year(hired))
    partitions 4;
    ```

    ### KEY

    ```text
    ・パーティショニングには、カラム名のみが指定可能
    ・カラム名を加工した式は不可
    ・ただし、指定できる列は整数値やNULL値に限定されない
    ```

    ```sql
    /*
    カラム名を指定しない場合、テーブルの主キーが使われる
    ⇒今回の場合、id
    */
    create table k1 (
      id int not null primary key,
      name varchar(20)
    )
    partition by key()
    partition 2;

    /*
    主キーがない && 一意キーがある場合
    ⇒一意キーが使われる(NOT NULLでないと失敗する)
    */
    create table k2 (
      id int not null,
      name varchar(20),
      unique key(id) not null
    )
    partition by key()
    partitions 2;
    ```

    !!! info
        ### 線形キーを用いることもできる

        ```sql
        create table tk (
          col1 int not null,
          col2 char(5),
          col3 date
        )
        partition by linear key (col1)
        partitions 3
        ;
        ```

## Q145 パーティショニングでNULLを用いたときの挙動を知っていますか?

??? success
    ### NULLの扱い

    ```text
    基本
      ・パーティショニング実装の際、NULLはNULLでない値より
        小さい値とみなされる

    RANGE
      ・一番初めのパーティションに挿入される
    
    LIST
      ・明示的にリストにNULLを指定する必要がある
    
    HASH&KEY
      ・今のところ不明
      ・公式には戻り値が0扱いになると書いてあったが、
        partitions 3にした例ではそうでないように思える
    ```

    ```sql
    -- hash && null

    create table h1 (
      id int
    )
    partition by hash(id)
    partitions 3;

    -- 2,1,1になるはず
    insert into h1 values
    (0), (null), (1), (2);

    select * 
    from h1 partition (p2);

    /*
    +------+
    | id   |
    +------+
    | NULL |
    |    2 |
    +------+
    2 rows in set (0.001 sec)
    */
    ```

## Q146 パーティションを削除/追加/データ削除を行う方法を知っていますか?

??? success
    ### パーティションの削除(RANGE)

    ```sql
    -- パーティションの内のデータも消える

    create table tr (id int, purchased date)
      partition by range columns (purchased)
      (
        partition p0 values less than ("1990-01-01"),
        partition p1 values less than ("1995-01-01"),
        partition p2 values less than ("2000-01-01"),
        partition p3 values less than (maxvalue)
      );
    
    insert into tr values
    (1, "1989-12-31"),
    (2, "1999-12-31"),
    (3, "1993-12-31"),
    (4, "2004-12-31")
    ;

    --　削除
    alter table tr drop partition p2;
    -- Query OK, 0 rows affected (0.042 sec)
    -- Records: 0  Duplicates: 0  Warnings: 0
    
    select * from tr;
    -- 行が削除されても報告されていないことが分かる
    /*
    +------+------------+
    | id   | purchased  |
    +------+------------+
    |    1 | 1989-12-31 |
    |    3 | 1993-12-31 |
    |    4 | 2004-12-31 |
    +------+------------+
    3 rows in set (0.001 sec)
    */

    -- dataを失いたくないなら
    alter table tr reorganize partition p3 into (
      partition p1_1 values less than ("2005-01-01"),
      partition p1_2 values less than (maxvalue)
    ) ;

    -- 実際にはp1_1, p1_2ではなく、p3とp4にしたかった
    alter table tr reorganize partition p1_1 into (
      partition p3 values less than ("2005-01-01")
    );

    alter table tr reorganize partition p1_2 into (
      partition p4 values less than (maxvalue)
    );

    select * from tr partition (p3);
    /*
    +------+------------+
    | id   | purchased  |
    +------+------------+
    |    4 | 2004-12-31 |
    +------+------------+
    1 row in set (0.001 sec)
    */
    ```

    ### パーティションの追加(RANGE)

    ```sql
    create table k1 (
      id int,
      name varchar(10)
    );
    -- 無い状態で追加
    alter table k1 
    partition by range columns (name) (
      partition p0 values less than ("a"),
      partition p1 values less than ("b"),
      partition p2 values less than ("c")
    );

    insert into k1 values
    (1, "8"),
    (2, "abc"),
    (3, "bneko")
    ;

    -- 範囲の末尾に追加するときはaddが使える
    -- 末尾以外はreorganize partition ... intoがよさそう
    alter table k1
    add partition (
      partition p3 values less than maxvalue
    );
    ```

    ### パーティションをマージする(RANGEの隣接するものを)

    ```sql
    alter table k1
    reorganize partition p0, p1, p2 into (
      partition p0 values less than ("c")
    );

    select * 
    from k1 partition (p0);

    /*
    +------+-------+
    | id   | name  |
    +------+-------+
    |    1 | 8     |
    |    2 | abc   |
    |    3 | bneko |
    +------+-------+
    3 rows in set (0.001 sec)
    */
    ```

    ### パーティションの削減/増加(HASH)

    ```sql
    create table h1 (
      id int
    )

    partition by hash(id)
    partitions 10;

    -- 削減(合体) 4つ分減らす
    -- hash, key, linear (hash/key)で機能する
    alter table h1 coalesce partition 4;

    -- 追加(4つ分増やす)
    alter table h1 add partition partitions 4;
    ```

    ### パーティション自体を削除

    ```sql
    alter table h1 remove partitioning;
    ```

## Q147 パーティションをテーブルと交換する方法を知っていますか?

??? success

    ### 補足(create table ... like)

    ```sql
    -- 元のテーブルに定義されているカラム属性やインデックス
    -- を引き継いだ空のテーブルを作成できる

    create table e (
      id int not null,
      fname varchar(20),
      lname varchar(20)
    )
    partition by range (id) (
      partition p0 values less than (50),
      partition p1 values less than (100),
      partition p2 values less than (150),
      partition p3 values less than (maxvalue)
    );

    insert into e values
    (1000, "NekoMoti", "Taro"),
    (320, "Nekogasira", "Zyuzaburo"),
    (24, "Nekota", "Nekokiti"),
    (2005, "Nekozyarasi", "Musyanosuke")
    ;


    -- コピーを作成
    create table e2 like e;

    -- コピーからパーティションを削除
    alter table e2 remove partitioning;

    ```

    ### パーティションとテーブルを交換

    ```sql
    select * from e partition(p0);
    /*
    +----+--------+----------+
    | id | fname  | lname    |
    +----+--------+----------+
    | 24 | Nekota | Nekokiti |
    +----+--------+----------+
    1 row in set (0.000 sec)
    */


    alter table e
    exchange partition p0
    with table e2;
    -- Query OK, 0 rows affected (0.024 sec)

    /*中身を入れ替えている*/
    select * from e partition(p0);
    -- Empty set (0.000 sec)

    select * from e2;
    /*
    +----+--------+----------+
    | id | fname  | lname    |
    +----+--------+----------+
    | 24 | Nekota | Nekokiti |
    +----+--------+----------+
    1 row in set (0.001 sec)
    */
    ```

## Q148 パーティションプルーニングについて知っていますか?

??? success
    ### パーティショニングの大きな利点

    ```text
    ・パーティション分割されたテーブルがあるとする

    ・ここで、パーティションキー列に基づく条件を含む
      クエリを実行する

    ・するとDBサーバはクエリに関連するデータを含んでいない
      パーティションを考慮の対象から外す
    
    ⇒検索を制限することで、すべてのパーティションをスキャン
      するより、検索時間を減らすことができる
      これを、「プルーニング」という
    
    ・プルーニングは、等号や、比較、INやBetweenなどで
      使うことができる
    ```

    ```sql
    --プルーニングの例

    /*
    例ではRangeを用いているが、Listでも可能
    等号の場合、HashやKeyでもプルーニングを使える

    */

    create table t1 (
      fname varchar(50) not null,
      lname varchar(50) not null,
      region_code int unsigned not null,
      dob date not null
    )

    partition by range(region_code) (
      partition p0 values less than (64),
      partition p1 values less than (128),
      partition p2 values less than (192),
      partition p3 values less than maxvalue
    );

    insert into t1 
    values
    ("Abraham", "Lincoln", 111, "1809-02-12"),
    ("Buster", "Keaton", 111, "1895-10-04"),
    ("Robert", "Nelson", 156, "1794-08-08"),
    ("Charles", "Chaplin", 112, "1889-04-16")
    ;

    -- partionkeyであるregion_codeを含んでいる
    -- そのため、p2だけを探せている
    select fname, lname, region_code, dob
    from t1
    where region_code > 150 and region_code < 160
    ;

    /*
    +--------+--------+-------------+------------+
    | fname  | lname  | region_code | dob        |
    +--------+--------+-------------+------------+
    | Robert | Nelson |         156 | 1794-08-08 |
    +--------+--------+-------------+------------+
    1 row in set (0.001 sec)
    */
    ```

    ### その他の利点

    ```text
    ・作成日時などでテーブルを分けることで、
      一定以上経過したデータを簡単に削除できる
    ```

## Q149 クラスタリングについて知っていますか?

??? success
    ###　問題はストレージだけではない

    ```text
    ・パーティショニングを駆使することで、1つのRDBに
      多くのデータを格納できる
    
    [残る問題点]
    ・数千、数万の同時ユーザに対処する
    ・数万件ものレポートを作成する必要がある
    ⇒1台のサーバでは不十分
    ```

    ### クラスタリング

    ```text
    ・複数のサーバを1つのDBとして機能させる

    [以下のことが可能]
    ・クラスタ内の全てのサーバが
      全てのディスクにアクセス可能
    
    ・特定のサーバのcacheに、
      クラスタ内の他のサーバからアクセス可能
    
    ・障害が発生した場合、クラスタ内の
      別のDBサーバにフェイルオーバーする

    [クラスタリングに使われるクラウドサービス]
    ・現在は、AWSやGoogle Cloudなどの
      クラウドの機能を用いることが多い

    ・オートスケーリング機能があるので、
      サーバのリソースを無駄にすることも少なくなった

    ・たとえばAWSのAuroraの場合、登録処理は
      プライマリDBインスタンスを用い、
      Selectなどの情報参照等なら
      Auroraレプリカを用いて、負荷を分散するなどの
      処理も容易である
    
    ・AuroraはMySQLに対応しているので、
      簡単に試すことができるだろう
    （つい最近v1を切り捨てて、常に0.5キャパシティ使う
    ようになったため名称と実体がつり合っていない
    Aurora Serverlessなんてものもあるみたいだしね）
    ```

## Q150 シャーディングについて知っていますか?

??? success
    ### シャーディング

    ```text
    パーティショニング
      ⇒個々のテーブルを分割する
    
    シャーディング(sharding)
      ⇒データを複数のDB（シャード）に分割する
      ⇒ただし、パーティショニングより複雑
    ```

    ### シャーディング概要

    ```text
    ・シャーディングキーを選択する
      ⇒接続先のDBを決めるために使用
    
    ・個々の行は1つのシャードに割り当てられる

    ・スキーマを変更したい場合、
      変更をすべてのシャードに反映させなければならない
    
    ・複数のシャードに格納されているデータにアクセスしたい時
      複数のDBに対してクエリを実行したり
      複数のDBにわたってトランザクションを実装する
    ```

    ```text
    [メリット]
      ・シャード単位で小さなデータセットを並行処理できる
        ⇒応答速度の改善
      
      ・DBのスケールの問題に対応できる

      ・シャーディングでは同じ情報のコピーは作成されないが
        レプリケーションと組み合わせることで
        高可用性を実現可能
    
    [デメリット]
    ・運用が難しい。複雑
    ・マシンが増えればコストもかかる
    ・メンテナンスコストも忘れてはならない
    ```

## Q151 ビッグデータという概念について知っていますか?

??? success
    ### ビッグデータ概要

    ```text
    ・厚生労働省の文書も参考にすること
    ```