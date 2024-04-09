# クイズ(15:解析関数)

## Q129 解析関数について知っていますか?

??? success
    ### 解析関数概要

    ```text
    ・データ解析を、SQL内で行うために使用する

    ・結果セット全体に変更を加えることなく、
      データを解析関数で使うために分割できるのが特徴
    
    ・集約関数との違いは1行に集約されないこと
    ```

    ### 集約関数との違いを説明する例

    ```sql
    -- 基本的な関数
    /*quarterはどの四半期に属するか*/
    select 
      quarter(payment_date) quarter,
      monthname(payment_date) month_nm,
      sum(amount) monthly_sales
    from
      payment
    where
      year(payment_date) = 2005
    group by
      quarter(payment_date), monthname(payment_date)
    order by
      FIELD(monthname(payment_date), 'January', 'February', 'March', 'April', 'May', 'June', 'July', 'August', 'September', 'October', 'November', 'December')
    ;

    /*
    +---------+----------+---------------+
    | quarter | month_nm | monthly_sales |
    +---------+----------+---------------+
    |       2 | May      |       4823.44 |
    |       2 | June     |       9629.89 |
    |       3 | July     |      28368.91 |
    |       3 | August   |      24070.14 |
    +---------+----------+---------------+
    4 rows in set (0.027 sec)
    */

    -- 四半期ごとの支払総額を調べる場合を考える
    -- group byを使ってしまうまとまってしまうので、こうする
    -- (overとpartition byについては後述)

    select
      quarter(payment_date) quarter,
      monthname(payment_date) month_nm,
      sum(amount) monthly_sales,
      max(sum(amount))
        over() max_overall_sales,
      max(sum(amount))
        over(partition by quarter(payment_date)) max_qrtr_sales
    from payment
    where year(payment_date) = 2005
    group by 
      quarter(payment_date), /*ここをコメントアウトしてもエラーが出なかった場合は、Q131を先に読むこと*/
      monthname(payment_date)
    ;

    /*
    +---------+----------+---------------+-------------------+----------------+
    | quarter | month_nm | monthly_sales | max_overall_sales | max_qrtr_sales |
    +---------+----------+---------------+-------------------+----------------+
    |       2 | June     |       9629.89 |          28368.91 |        9629.89 |
    |       2 | May      |       4823.44 |          28368.91 |        9629.89 |
    |       3 | August   |      24070.14 |          28368.91 |       28368.91 |
    |       3 | July     |      28368.91 |          28368.91 |       28368.91 |
    +---------+----------+---------------+-------------------+----------------+
    4 rows in set (0.013 sec)
    */
    ```

    ### overとpartition by

    ```text
    window関数
      ・集計のような操作を実行するが、
        結果クエリを1行にまとめないようにするための関数

      ・基本的に集計関数と同じ関数で、
        後ろにover()を付けることでwindow関数になる
      
      ・しかし、window関数としてしか存在しない関数もある

    over()
      結果セット全体がウィンドウに含まれる
      window関数を使うという合図
    
    over(partition by <任意の条件>)
      ・どの範囲でグループを作るか指定する
    
    avg(payment) over(partition by quarter(payment_date))
      ・自分が属するquarterの中のpaymentのavgを指定する

    [構文]

    window_func 
      over([partition by句][order by句][frame 句])
    ```

    ### 補足(FIELD)

    ```text
    ・特殊な並び順に対応するための関数
    ・第1引数が第2引数以降の何番目に存在するかを教える
    ・order byに指定することで、並び順を指定可能
    ・順序が低い順から並べられる
    ```

    ```sql
    select field("neko", "inu", "neko", "nezumi") num;

    /*
    +-----+
    | num |
    +-----+
    |   2 |
    +-----+
    1 row in set (0.000 sec)
    */

    select "kizi" animals
    union all
    select "inu"
    union all
    select "saru"
    union all
    select "momotaro"
    order by 
    field(animals, "saru", "inu", "kizi", "momotaro")
    ;

    /*
    +----------+
    | animals  |
    +----------+
    | saru     |
    | inu      |
    | kizi     |
    | momotaro |
    +----------+
    4 rows in set (0.001 sec)
    */
    ```

    ### (理解できない場合)もう少し小さな例で試す

    ```sql
    create temporary table food (
      store_id int,
      item varchar(20),
      price int
    );

    insert into food values
    (1, "bread", 180),
    (1, "rice", 150),
    (1, "noddle", 130)
    ;

    -- 集約関数
    select sum(price) from food
    ;

    /*
    +------------+
    | sum(price) |
    +------------+
    |        460 |
    +------------+
    1 row in set (0.000 sec)
    */

    -- window関数。集約関数と違い、他のカラムも書ける
    select
      store_id,
      item,
      price,
      sum(price) over()
    from food
    ;

    /*
    +----------+--------+-------+-------------------+
    | store_id | item   | price | sum(price) over() |
    +----------+--------+-------+-------------------+
    |        1 | noddle |   130 |               460 |
    |        1 | rice   |   150 |               460 |
    |        1 | bread  |   180 |               460 |
    +----------+--------+-------+-------------------+
    3 rows in set (0.001 sec)


    */

    -- Bで始まるか否かでグルーピング
    select
      store_id,
      item,
      price,
      sum(price) over(
        partition by(item like "b%")
      ) as initialB
    from food
    ;

    /*
    +----------+--------+-------+----------+
    | store_id | item   | price | initialB |
    +----------+--------+-------+----------+
    |        1 | noddle |   130 |      280 |
    |        1 | rice   |   150 |      280 |
    |        1 | bread  |   180 |      180 |
    +----------+--------+-------+----------+
    3 rows in set (0.001 sec)
    */
    ```

## Q130 解析関数のorder by句について知っていますか?

??? success 
    ### over句の中で、order byを使う

    ```text
    [補足]
      RANK
        ・window関数としてのみ使える関数

      解析関数のorder by
        ・window関数がpartitionの中で行を処理する順番を指定

      rank() over (order by <条件>)
        ・処理された順にランク付けしている

        ・ソートではないため、ソートを行いたい場合は
          別途order by句に記載する必要がある      
      
      
    ```

    ```sql
    -- 支払総額が最も多い月で並び変える
    select
      quarter(payment_date) quarter,
      monthname(payment_date) month_nm,
      sum(amount) monthly_sales,
      -- sum(amount)が大きい順に処理して
      -- 処理した順にランク付け
      rank() over (order by sum(amount) desc) sales_rank
    from 
      payment
    where
      year(payment_date) = 2005
    group by
      quarter(payment_date), 
      monthname(payment_date)
    -- 並び順は月順を採用する
    order by
      field(monthname(payment_date), 'January', 'February', 'March', 'April', 'May', 'June', 'July', 'August', 'September', 'October', 'November', 'December')
    ;

    /*
    +---------+----------+---------------+------------+
    | quarter | month_nm | monthly_sales | sales_rank |
    +---------+----------+---------------+------------+
    |       2 | May      |       4823.44 |          4 |
    |       2 | June     |       9629.89 |          3 |
    |       3 | July     |      28368.91 |          1 |
    |       3 | August   |      24070.14 |          2 |
    +---------+----------+---------------+------------+
    4 rows in set (0.013 sec)
    */
    ```

    ### partition byを用いて四半期ごとのランク付けを行う

    ```sql
    select
      quarter(payment_date) quarter,
      monthname(payment_date) month_nm,
      sum(amount) monthly_sales,
      rank() over(
        partition by quarter(payment_date)
        order by sum(amount) desc
      ) qtr_sales_rank
    from  
      payment
    where
      year(payment_date) = 2005
    group by
      quarter(payment_date),
      monthname(payment_date)
    order by
      field(monthname(payment_date), 'January', 'February', 'March', 'April', 'May', 'June', 'July', 'August', 'September', 'October', 'November', 'December')
    ;

    /*
    +---------+----------+---------------+----------------+
    | quarter | month_nm | monthly_sales | qtr_sales_rank |      
    +---------+----------+---------------+----------------+      
    |       2 | May      |       4823.44 |              2 |      
    |       2 | June     |       9629.89 |              1 |      
    |       3 | July     |      28368.91 |              1 |      
    |       3 | August   |      24070.14 |              2 |      
    +---------+----------+---------------+----------------+      
    4 rows in set (0.018 sec)
    */
    ```

## Q131 コラム(sql_mode)について知っていますか?

??? success

    ### そもそも集約したのにどうやって非集約列の値を使えと

    ```text
    ・SELECT句と、GROUP BYの非集約カラムがそろっていないのに
      エラーが起きないことがある
    
    ・その場合、SELECT句で表示されたカラムには
      適当な値が入っている（関数従属性が存在しない場合）
    
    関数従属性
      ・カラム1の値を決めた場合
        カラム2の値が一意に決まるとする
        ⇒その場合、関数従属性が存在する
        ⇒つまり、カラム1で集約してもカラム2の値を表示する
          のに何も問題はないのでエラーは起きない
    ```

    ### 上記のようなエラーが起こる理由

    ```sql
    select @@sql_mode;

    -- ONLY_FULL_GROUP_BYがない(本来は1行で表示される)

    /*
    STRICT_TRANS_TABLES,
    ERROR_FOR_DIVISION_BY_ZERO,
    NO_AUTO_CREATE_USER,
    NO_ENGINE_SUBSTITUTION
    */
    ```

    ### sql_modeについて(デフォルトでONになっているもの中心)

    ```text
    only_full_group_by
      ・GROUP BY句で指定されていない非集約カラムでも
        SELECT, HAVING, ORDER BYで使えてしまう
      ⇒関数従属性が存在する場合は指定なしでもエラーにならない
      ⇒適当な値が入っていると誤解を生むので、常にONにすべき
      ⇒MariaDB(v10.6)だとデフォルトでOFF

    --------------------------
    
    strict_trans_tables
      ・別名、厳密モード
      ・isnert/update時に、値がテーブルの指定に従っていない
        とSQLの実行を中止する
      
      ⇒これがOFFになっていると、
      　デフォルト値が無いカラムに値無しでinsertしたり
      　最大長を指定したカラムに対して、それを超える長さで
        挿入を行った場合でも、適当に切られて格納される
      ⇒エラーが出ないのは本意ではないので、常にONにすべき
    
    --------------------------

    error_for_division_by_zero
      ・0除算を含む、insert/updateがあった場合の設定
      ・0除算の結果はNULLになるのはON/OFF問わず
      ・ONにすると警告が表示される
      ・無効にすると警告が表示されない
      ⇒警告は出た方がいいので、常にONにすべき
    
      select 1/0;
      +------+
      | 1/0  |
      +------+
      | NULL |
      +------+
      1 row in set, 1 warning (0.000 sec)

      show warnings;
      +---------+------+---------------+
      | Level   | Code | Message       |
      +---------+------+---------------+
      | Warning | 1365 | Division by 0 |
      +---------+------+---------------+
      1 row in set (0.000 sec)
    
    --------------------------


    no_auto_create_user
      ・存在しないユーザに対して権限を付与しようとした際の
        挙動を制御する
      
      ・ONならエラーを吐く
      ・OFFならユーザ作成を行う(MySQL 8.0の場合、できない)
      ⇒いずれにせよ、ONの方が安全
    
        select user, host from mysql.user;
        +-------------+-----------+
        | User        | Host      |
        +-------------+-----------+
        | mariadb.sys | localhost |
        | mysql       | localhost |
        | <hoge>      | localhost |
        | root        | localhost |
        +-------------+-----------+
        4 rows in set (0.008 sec)

        GRANT ALL on *.* to hoge@localhost;
        ERROR 1133 (28000): 
          Can't find any matching row in the user table

    --------------------------

    no_engine_substitution
      ・create tableでengineを指定した時、
        指定engineが使えない場合の挙動を制御する
      ・ONならエラーを吐く
      ・OFFなら勝手にdefaultエンジンを使う(警告は出る)

      ⇒エンジンが勝手に変化するのは困るので常にON推奨

    -------------------------- 

    no_zero_in_dateとno_zero_dateについて
      ・将来的にstrictモードに取り込まれそう
    
    -------------------------- 

    それ以外のモードについて
      ・有用そうなものがあったら追記する
    ```
    ### sql_modeを一時的に変えたい場合(Q60でも触れた)

    ```sql
    -- ここに必要なモードをカンマ区切りで指定する
    -- 接続を閉じると、この設定は消失する
    set sql_mode = ""
    ```

    ### sql_modeを恒久的に変えたい場合

    ```text
    1 my.cnfの設定に記述する
    2 mysql/mariadbを再起動する

    1 my.cnfの場所について
      helpの中に書いてあるので、grepで抽出する
      ※複数のmy.cnfに異なることを書く場合、
       後から読み込まれた物が優先されるので注意すること

      $ mysql --help | grep my.cnf
      /etc/my.cnf /etc/mysql/my.cnf ~/.my.cnf

      見つけたら以下の様に記入
      (""の中に適用したいモードをカンマ区切りで記載)

      [mysqld]
      sql_mode="ONLY_FULL_GROUP_BY,STRICT_TRANS_TABLES,ERROR_FOR_DIVISION_BY_ZERO,NO_AUTO_CREATE_USER,NO_ENGINE_SUBSTITUTION"
    
    2 再起動しないと反映されないので
      (mysqlならmariadbの場所を書き換える)
      sudo systemctl stop mariadb
      sudo systemctl start mariadb
    
    3 起動して、select @@sql_mode;で確認
    ```

    ```sql
    -- 1行だと分かりづらいので\nや\tで整えている
    select 
      concat(
        "\n\t",
        replace(@@sql_mode, ",", "\n\t"),
        "\n"
      ) sql_mode_list
    ;

    /*
    +----------------------------------
    | sql_mode_list    
    +----------------------------------
    |
            ONLY_FULL_GROUP_BY
            STRICT_TRANS_TABLES
            ERROR_FOR_DIVISION_BY_ZERO
            NO_AUTO_CREATE_USER
            NO_ENGINE_SUBSTITUTION
    |
    +-----------------------------------
    */
    ```

## Q132 RANKを含む3つのランキング用関数について知っていますか?

??? success
    ### 3種の違い

    ```text
    row_number
      ・各行に対して一意な数字を返す
      ・tieの場合は任意の順位
    
    rank
      ・tieの場合は同じ順位を返す
      ・次の順位は飛ばす。1位, 1位, 3位
      ・一般的
    
    dense_tank
      ・tieの場合は同じ順位を返す
      ・次の順位は飛ばさない。1位, 1位, 2位 
    ```

    ### 例で確認する

    ```sql
    select
      customer_id,
      count(*) num_rentals,
      row_number() over(order by count(*) desc) row_number_rnk,
      rank() over(order by count(*) desc) rank_rnk,
      dense_rank() over(order by count(*) desc) dense_rank_rnk
    from rental
    group by customer_id
    order by num_rentals desc
    ;

    /*
    +-------------+-------------+----------------+----------+----------------+
    | customer_id | num_rentals | row_number_rnk | rank_rnk | dense_rank_rnk |
    +-------------+-------------+----------------+----------+----------------+
    |         148 |          46 |              1 |        1 |              1 |
    |         526 |          45 |              2 |        2 |              2 |
    |         144 |          42 |              3 |        3 |              3 |
    |         236 |          42 |              4 |        3 |              3 |
    |          75 |          41 |              5 |        5 |              4 |
    ...skipping...
    599 rows in set (0.011 sec)
    */
    ```

    ### 使用例1(partition byで月ごとの順位作成)

    ```text
    ・解析関数の評価順はWHERE句やHAVING句より後なので
      結果に基づいてフィルタリングを行いたい場合は
      入れ子にする
    
    ・SELECT文で解析関数を使用しているので、
      当たり前といえば当たり前だが
      （そういえば、SELECT文がHAVING句より
      　後ろで評価されるってのは記載があるのだろうか?
      　今まで当たり前の様に受け入れてきたけれど）
    
    ※一応、postgresqlの実行順序を入手した
      想定通りの順序ではある
    https://www.postgresql.org/docs/current/sql-select.html

    1 with
    2 from
    3 where
    4 group by
    5 having
    6 select
    7 distinct
    8 union/intersect/except
    9 order by
    10 limit 
    ```

    ```sql
    select
      customer_id,
      rental_month,
      num_rentals,
      rank_rnk ranking
    from(
      select
        customer_id,
        monthname(rental_date) rental_month,
        count(*) num_rentals,
        /*
          partition byやorder by句に指定できるのは
          groupbyに指定した非集約カラムと集約関数や式

          以下の場合、monthnameが等しいものがグループ単位で
          count(*)を基準にランク付けしている
        */
        rank()
          over(
            partition by monthname(rental_date) 
            order by count(*) desc
          )
        rank_rnk
      from rental
      where year(rental_date) = 2005
      group by customer_id, monthname(rental_date)
    ) cust_rankings
    where rank_rnk <= 1
    order by 
      field(rental_month, "May", "June", "July", "August"), 
      num_rentals desc, 
      rank_rnk,
      customer_id
    ;

    /*
    +-------------+--------------+-------------+---------+       
    | customer_id | rental_month | num_rentals | ranking |       
    +-------------+--------------+-------------+---------+       
    |         197 | May          |           8 |       1 |       
    |          31 | June         |          11 |       1 |       
    |         148 | July         |          22 |       1 |       
    |          15 | August       |          18 |       1 |       
    |         119 | August       |          18 |       1 |       
    |         148 | August       |          18 |       1 |       
    |         569 | August       |          18 |       1 |       
    +-------------+--------------+-------------+---------+       
    7 rows in set (0.012 sec)
    */
    ```

## Q133 最小値や最大値をover句と組み合わせることで、何ができるか把握していますか?

??? success
    ### 一見、冗長に見えるが

    ```text
    ・式の計算に使うことで効果を発揮する
    ```

    ```sql
    /*
    [現状の理解]
    window化された集計は疑似集計なので実際には
    複数の行を結合しない

    sum(sum(amount))の場合、内側のsum(amount)は
    実際の集計関数。外側がwindow関数

    sum(sum(amount))はウィンドウごとのsum(amount)の
    総計を意味する。今回の場合、ウィンドウがover()なので総計
    partition by等があるとまた変わってくる
    */
    select
      monthname(payment_date) payment_month,
      sum(amount) month_total,
      round(
        sum(amount) / sum(sum(amount)) over() * 100,
        2
      ) pct_of_total
    from
      payment
    where
      year(payment_date) = 2005
    group by
      monthname(payment_date)
    order by
      field(
        monthname(payment_date),
        "May", "June", "July", "August"
      )
    ;

    /*
    +---------------+-------------+--------------+
    | payment_month | month_total | pct_of_total |
    +---------------+-------------+--------------+
    | May           |     4823.44 |         7.21 |
    | June          |     9629.89 |        14.40 |
    | July          |    28368.91 |        42.41 |
    | August        |    24070.14 |        35.98 |
    +---------------+-------------+--------------+
    4 rows in set (0.026 sec)
    */
    ```   

    ### case式を用いて最大最小であることを表現する

    ```sql
    /*
    max(sum(amount))のような記法は集計関数ではできない
    ウィンドウ関数を用いているので各行について比較出来ている
    */
    select
      monthname(payment_date) payment_month,
      sum(amount) month_total,
      /*sum(amount)が最大/最小値と一致するか判断*/
      case sum(amount)
        when max(sum(amount)) over() then "Highest"
        when min(sum(amount)) over() then "Lowest"
        else "Middle"
      end descriptor
    from payment
    where year(payment_date) = 2005
    group by monthname(payment_date)
    order by
      field(
        monthname(payment_date),
        "May", "June", "July", "August"
      )
    ;

    /*
    +---------------+-------------+------------+
    | payment_month | month_total | descriptor |
    +---------------+-------------+------------+
    | May           |     4823.44 | Lowest     |
    | June          |     9629.89 | Middle     |
    | July          |    28368.91 | Highest    |
    | August        |    24070.14 | Middle     |
    +---------------+-------------+------------+
    4 rows in set (0.016 sec)
    */
    ```

## Q134 解析関数のフレーム句について知っていますか?

??? success
    ### 累積和の例

    ```text
    [補足]
    yearweek
      ・日付に対応する年/週を返す
      ・週の始まりが12月の場合、昨年の最終週が表示される

    rows unbounded preceding
      ・結果セットの先頭から現在の行までの
        データウィンドウを定義する
      
      ・sum(sum(amount))はウィンドウごとのsum(amount)の
        総計を意味する
      
      ・今回の場合、ウィンドウは
        結果セットの先頭から現在の行までの合計
    ```

    ```sql
    select
      yearweek(payment_date) payment_week,
      sum(amount) week_total,
      sum(sum(amount)) over (
        order by yearweek(payment_date)
        rows unbounded preceding
      ) rolling_sum
    from
      payment
    group by
      yearweek(payment_date)
    order by 1
    ;

    /*
    +--------------+------------+-------------+
    | payment_week | week_total | rolling_sum |
    +--------------+------------+-------------+
    |       200521 |    2846.19 |     2846.19 |
    |       200522 |    1977.25 |     4823.44 |
    |       200524 |    5603.43 |    10426.87 |
    |       200525 |    4026.46 |    14453.33 |
    |       200527 |    8490.83 |    22944.16 |
    |       200528 |    5982.64 |    28926.80 |
    |       200530 |   11027.23 |    39954.03 |
    |       200531 |    8412.07 |    48366.10 |
    |       200533 |   10619.11 |    58985.21 |
    |       200534 |    7907.17 |    66892.38 |
    |       200607 |     514.18 |    67406.56 |
    +--------------+------------+-------------+
    11 rows in set (0.010 sec)
    */
    ```

    ### 構文で理解する

    ```text
    rows
      開始行と終了行によってフレームを定義する
    
    range
      ・範囲内の行がフレーム
      ・行数ではなく、時間などの範囲を表したい時に頻出

    unbounded preceding
      最初のpartition行
    
    unbounded following
      最後のpartition行
    
    1 preceding
      ・rowsの場合、現在の行の前の1行。
      　数値を調整することで変化
      ・rangeの場合、現在の行から数値を引いた行
    
    1 following
      ・precedingの逆 
    
    current row
      ・現在行

    構文
    {rows | range} 
      {frame_start | frame_between}

    frame_start
      ・フレームの開始のみを指定する
      ・現在の行が終了地点になる
    
    frame_between
      BETWEEN frame_start and frame_end
      ⇒明示的に開始点と終了点を指定

    使い方
      ・開始行から現在の行までをフレームにする
      ⇒累積合計(敢えてbetweenにしてみた)
        rows between unbounded preceding and current row

      ・現在の行の両側、N行をフレームにする
      ⇒移動平均
        rows between 1 preceding and 1 following 
    ```

    ### 注意点

    ```text
    cums_dist()
    dense_rank()
    rank()
    row_number()
    lag()
    lead()
    ntile()
    percent_rank()

    これらの関数はframe句を無視する
    ```

    ### 上記を踏まえた上で移動平均を考える

    ```sql
    /*
    partitionが全体なので、
    単にyearweek(payment_week)の順に処理される

    先頭と末尾の場合は、それぞれ前と後の行がないので
    2行のavgがとられている
    */
    select
      yearweek(payment_date) payment_week,
      sum(amount) week_total,
      avg(sum(amount)) over (
        order by yearweek(payment_date)
        rows between 1 preceding and 1 following
      ) rolling_3wk_avg
    from payment
    group by yearweek(payment_date)
    order by 1
    ;

    /*
    +--------------+------------+-----------------+
    | payment_week | week_total | rolling_3wk_avg |
    +--------------+------------+-----------------+
    |       200521 |    2846.19 |     2411.720000 |
    |       200522 |    1977.25 |     3475.623333 |
    |       200524 |    5603.43 |     3869.046667 |
    |       200525 |    4026.46 |     6040.240000 |
    |       200527 |    8490.83 |     6166.643333 |
    |       200528 |    5982.64 |     8500.233333 |
    |       200530 |   11027.23 |     8473.980000 |
    |       200531 |    8412.07 |    10019.470000 |
    |       200533 |   10619.11 |     8979.450000 |
    |       200534 |    7907.17 |     6346.820000 |
    |       200607 |     514.18 |     4210.675000 |
    +--------------+------------+-----------------+
    11 rows in set (0.009 sec)
    */
    ```

    ### 行数ではなく、特定の範囲の平均を取りたい場合

    ```text
    ・RANGEが利用できる
    ・RANGEで数値または時間型を使用するには
      order byに含む必要がある
    ```

    !!! warning
        ### Mariadb(10.6)の場合
        ```text
        ・以下の構文はエラーになった
        ・理由はrangeタイプのフレームに対して
          date型をmariadbがサポートしていないため

        ・以下のURLを参照して、いつサポートされるか監視できる

        https://jira.mariadb.org/browse/MDEV-9727
        ```

    ```sql
    select
      date(payment_date),
      sum(amount),
      avg(sum(amount)) over (
        order by date(payment_date)
        range 
          between 
            interval 3 day preceding
            and
            interval 3 day following
      ) 7_day_avg
    from 
      payment
    where
      payment_date between "2005-07-01" and "2005-08-01"
    group by
      date(payment_date)
    order by 1
    ;
    ```

    ### MariaDB(10.6)におけるとりあえずの回避策

    ```sql
    -- mariadb対応版(数値型なら問題ないので)
    -- 数値型を利用するためにorder byに数値を含んでいる

    select
      date(payment_date),
      sum(amount),
      avg(sum(amount)) over (
        order by unix_timestamp(date(payment_date))
        range 
          between 
            259200 preceding
            and
            259200 following
      ) 7_day_avg
    from 
      payment
    where
      payment_date between "2005-07-01" and "2005-08-01"
    group by
      date(payment_date)
    order by 1
    ;

    /*
    +--------------------+-------------+-------------+
    | date(payment_date) | sum(amount) | 7_day_avg   |
    +--------------------+-------------+-------------+
    | 2005-07-05         |      128.73 | 1603.740000 |
    | 2005-07-06         |     2131.96 | 1698.166000 |
    | 2005-07-07         |     1943.39 | 1738.338333 |
    | 2005-07-08         |     2210.88 | 1766.917143 |
    | 2005-07-09         |     2075.87 | 2049.248571 |
    | 2005-07-10         |     1939.20 | 2035.463333 |
    | 2005-07-11         |     1938.39 | 2053.878000 |
    | 2005-07-12         |     2105.05 | 2014.627500 |
    | 2005-07-26         |      160.67 | 2046.642500 |
    | 2005-07-27         |     2726.51 | 2205.446000 |
    | 2005-07-28         |     2577.80 | 2315.906667 |
    | 2005-07-29         |     2721.59 | 2315.906667 |
    | 2005-07-30         |     2840.66 | 2746.954000 |
    | 2005-07-31         |     2868.21 | 2752.065000 |
    +--------------------+-------------+-------------+
    14 rows in set (0.005 sec)
    */
    ```

## Q135 前の行や後の行と、現在行を比較するためにlagとleadが使えることを知っていますか?

??? success
    ### 構文

    ```text
    lag(値, 何行前か)
    lead(値, 何行後か)

    ⇒第二引数を指定しない場合、1行前/後の値になる
    ⇒1行前/後が存在しない場合、NULLになる
    ```

    ### 1行前/後の値にアクセス

    ```sql
    select 
      yearweek(payment_date) payment_week,
      sum(amount) week_total,
      lag(sum(amount), 1) over (
        order by yearweek(payment_date)
      ) prev_wk_tot,
      lead(sum(amount), 1) over (
        order by yearweek(payment_date)
      ) next_wk_tot
    from payment
    group by
      yearweek(payment_date)
    order by 1
    ;

    /*
    +--------------+------------+-------------+-------------+
    | payment_week | week_total | prev_wk_tot | next_wk_tot |
    +--------------+------------+-------------+-------------+
    |       200521 |    2846.19 |        NULL |     1977.25 |    
    |       200522 |    1977.25 |     2846.19 |     5603.43 |    
    |       200524 |    5603.43 |     1977.25 |     4026.46 |    
    |       200525 |    4026.46 |     5603.43 |     8490.83 |    
    |       200527 |    8490.83 |     4026.46 |     5982.64 |    
    |       200528 |    5982.64 |     8490.83 |    11027.23 |    
    |       200530 |   11027.23 |     5982.64 |     8412.07 |    
    |       200531 |    8412.07 |    11027.23 |    10619.11 |    
    |       200533 |   10619.11 |     8412.07 |     7907.17 |    
    |       200534 |    7907.17 |    10619.11 |      514.18 |    
    |       200607 |     514.18 |     7907.17 |        NULL |    
    +--------------+------------+-------------+-------------+    
    11 rows in set (0.015 sec)
    */
    ```

    ### 前週との差をパーセンテージで表す

    ```sql
    /*
    前週とのdiff / 前週 * 100 = %
    小数第1位まで
    */
    select
      yearweek(payment_date) payment_week,
      sum(amount) week_total,
      round(
        /*前週とのdiff*/
        (
          sum(amount) - lag(sum(amount), 1) over (
            order by yearweek(payment_date)
          )
        )
        /
        /*前週の値*/
        lag(sum(amount), 1) over (
          order by yearweek(payment_date)
        ) * 100
      , 1
      ) pct_diff
    from payment
    group by yearweek(payment_date)
    order by 1
    ;

    /*
    +--------------+------------+----------+
    | payment_week | week_total | pct_diff |
    +--------------+------------+----------+
    |       200521 |    2846.19 |     NULL |
    |       200522 |    1977.25 |    -30.5 |
    |       200524 |    5603.43 |    183.4 |
    |       200525 |    4026.46 |    -28.1 |
    |       200527 |    8490.83 |    110.9 |
    |       200528 |    5982.64 |    -29.5 |
    |       200530 |   11027.23 |     84.3 |
    |       200531 |    8412.07 |    -23.7 |
    |       200533 |   10619.11 |     26.2 |
    |       200534 |    7907.17 |    -25.5 |
    |       200607 |     514.18 |    -93.5 |
    +--------------+------------+----------+
    11 rows in set (0.013 sec)
    */
    ```

    ### 黒三角にしてみたり

    ```sql

    select
      yearweek(payment_date) payment_week,
      sum(amount) week_total,
      replace(
      round(
        /*前週とのdiff*/
        (
          sum(amount) - lag(sum(amount), 1) over (
            order by yearweek(payment_date)
          )
        )
        /
        /*前週の値*/
        lag(sum(amount), 1) over (
          order by yearweek(payment_date)
        ) * 100
      , 1
      ), "-", "▲"
      ) pct_diff
    from payment
    group by yearweek(payment_date)
    order by 1
    ;

    /*
    +--------------+------------+----------+
    | payment_week | week_total | pct_diff |
    +--------------+------------+----------+
    |       200521 |    2846.19 | NULL     |
    |       200522 |    1977.25 | ▲30.5    |
    |       200524 |    5603.43 | 183.4    |
    |       200525 |    4026.46 | ▲28.1    |
    |       200527 |    8490.83 | 110.9    |
    |       200528 |    5982.64 | ▲29.5    |
    |       200530 |   11027.23 | 84.3     |
    |       200531 |    8412.07 | ▲23.7    |
    |       200533 |   10619.11 | 26.2     |
    |       200534 |    7907.17 | ▲25.5    |
    |       200607 |     514.18 | ▲93.5    |
    +--------------+------------+----------+
    11 rows in set (0.011 sec)
    */
    ```

## Q136 group_concatという区切り文字を用いて連結する集約関数について知っていますか?

??? success
    ### 解析関数ではないが、役に立つ

    ```sql

    -- 通常の場合
    select 
      f.title,
      count(*) /*特定の映画に出たactorの人数*/
    from actor a
      inner join film_actor fa
      on a.actor_id = fa.actor_id
      inner join film f
      on fa.film_id = f.film_id
    group by f.title
    ;

    /*
    +-----------------------------+----------+
    | title                       | count(*) |
    +-----------------------------+----------+
    | ACADEMY DINOSAUR            |       10 |
    | ACE GOLDFINGER              |        4 |
    | ADAPTATION HOLES            |        5 |
    | AFFAIR PREJUDICE            |        5 |
    997 rows in set (0.013 sec)
    */

    select 
      f.title,
      /*
        集約関数の中で、
        非集約カラムを使う場合、グループごとに集計が行われる

        下記では各グループ内のa.last_nameの値を連結している

      */
      group_concat(
        a.last_name order by a.last_name separator ", "
      ) actors
    from actor a
      inner join film_actor fa
      on a.actor_id = fa.actor_id
      inner join film f
      on fa.film_id = f.film_id
    group by f.title
    having count(*) = 13
    ;
    /*
    +-------------------+--------------------------------------------------------------------------------------------------------------------+
    | title             | actors                                                                                                             |
    +-------------------+--------------------------------------------------------------------------------------------------------------------+
    | BOONDOCK BALLROOM | BAILEY, BRIDGES, CHASE, COSTNER, DAVIS, GARLAND, HARRIS, JACKMAN, JOHANSSON, PALTROW, PECK, PESCI, WOOD            |
    | CHITTY LOCK       | BOLGER, DAVIS, DEGENERES, DUKAKIS, GARLAND, GARLAND, LOLLOBRIGIDA, MARX, MONROE, PFEIFFER, SOBIESKI, TEMPLE, TRACY |
    | CRAZY HOME        | BACALL, BASINGER, DREYFUSS, HARRIS, HAWKE, HOPE, JOLIE, MCDORMAND, MCKELLEN, NOLTE, SOBIESKI, TEMPLE, WILSON       |
    | DRACULA CRYSTAL   | CHAPLIN, CRAWFORD, DEGENERES, HOPKINS, HUNT, MCCONAUGHEY, TANDY, WAHLBERG, WEST, WILLIAMS, WILLIAMS, WILLIS, WRAY  |
    | MUMMY CREATURES   | ASTAIRE, BACALL, BAILEY, BLOOM, BRODY, CROWE, DEGENERES, DUKAKIS, HACKMAN, PECK, STREEP, TORN, VOIGHT              |
    | RANDOM GO         | AKROYD, BALE, CRUISE, DAVIS, DEGENERES, DEPP, DUKAKIS, HOPE, HUDSON, HUNT, KILMER, TEMPLE, TRACY                   |
    +-------------------+--------------------------------------------------------------------------------------------------------------------+
    6 rows in set (0.007 sec)
    */
    ```