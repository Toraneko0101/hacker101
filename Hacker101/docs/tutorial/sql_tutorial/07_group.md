# クイズ(7:集計)

## Q66 グループ化について知っていますか?

??? success
    ### グループ化

    ```text
    グループ化のメリット
      ・データをグループ化して集計する事で
        データベースに格納した時よりも、
        大きな単位でデータを扱えるようになる
    
    集計関数
      ・各グループの行を数えたり、総和を取ったり、
        複数の値に集まりに対して1つの値を返す関数

    注意点
      ・group by句の評価はwhere句より後ろなので、
        グループ化後の行から不要なデータを取り除きたい場合は
        having句を使う必要がある
      
      ・データベース内のデータに適用するフィルタはwhere句に
        指定し、グループ化されたデータに適用するフィルタは
        having句に指定する

        e.g. 
          select 
            fa.actor_id,
            f.rating, 
            count(*)
          from film_actor fa
            inner join film f
            on fa.film_id = f.film_id
          where f.rating in ("G", "PG")
          group by fa.actor_id, f.rating
          having count(*) >= 10
          ;
    ```

    ### グループ化の例

    ```sql
    select customer_id, count(*)
    from rental
    group by customer_id
    --これをwhereに書くことはできない。
    --whereの時点ではまだグループ化されていないため
    having count(*) >= 40 
    order by 2 desc;

    /*
    +-------------+----------+
    | customer_id | count(*) |
    +-------------+----------+
    |         148 |       46 |
    |         526 |       45 |
    |         144 |       42 |
    |         236 |       42 |
    |          75 |       41 |
    |         197 |       40 |
    |         469 |       40 |
    +-------------+----------+
    7 rows in set (0.006 sec)
    */
    ```

## Q67 集計関数の例について知っていますか?

??? success
    ### 主要な一覧

    ```text
    max(): 最大値
    min(): 最小値
    avg(): 平均値
    sum(): 合計値
    count():　集合内の値の個数
    count(*):　グループ内の行数
    ```

    ### 使用例

    ```sql
    -- group byを使っていないのに、集計関数が使えている
    -- 点については次のクイズで説明する
    select 
      max(amount) max_amt,
      min(amount) min_amt,
      avg(amount) avg_amt,
      sum(amount) tot_amt,
      count(*) num_payments
    from payment;

    /*
    +---------+---------+----------+----------+--------------+
    | max_amt | min_amt | avg_amt  | tot_amt  | num_payments |   
    +---------+---------+----------+----------+--------------+   
    |   11.99 |    0.00 | 4.201356 | 67406.56 |        16044 |   
    +---------+---------+----------+----------+--------------+   
    1 row in set (0.021 sec)
    */
    ```

## Q68 暗黙的なグループについて知っていますか?

??? success
    ### 暗黙的なグループ

    ```text
    暗黙的なグループ
      ・テーブルのすべての行からなるグループ
      ・group by句がないにもかかわらず、集計関数を
      　用いている場合、テーブル全てから集計している
    
    明示的なグループ
      ・group by句で指定されたカラムなどによって、指定された
        グループ
    
    group by句で集計関数を適用するグループを指定すると、
    集計関数はグループ化されたデータに対して、集計する
    ```

    ### 明示的なグループの例

    ```sql
    select 
      customer_id,
      max(amount) max_amt,
      min(amount) min_amt,
      avg(amount) avg_amt,
      sum(amount) tot_amt,
      count(*) num_payments
    from payment
    group by customer_id
    having customer_id <= 5;

    /*
    +-------------+---------+---------+----------+---------+--------------+
    | customer_id | max_amt | min_amt | avg_amt  | tot_amt | num_payments |
    +-------------+---------+---------+----------+---------+--------------+
    |           1 |    9.99 |    0.99 | 3.708750 |  118.68 |           32 |
    |           2 |   10.99 |    0.99 | 4.767778 |  128.73 |           27 |
    |           3 |   10.99 |    0.99 | 5.220769 |  135.74 |           26 |
    |           4 |    8.99 |    0.99 | 3.717273 |   81.78 |           22 |
    |           5 |    9.99 |    0.99 | 3.805789 |  144.62 |           38 |
    +-------------+---------+---------+----------+---------+--------------+
    5 rows in set (0.001 sec)
    */
    ```

## Q69 count関数を使って、グループのメンバーの総数を割り出す方法を知っていますか?

??? success
    ### 重複を取り除きたい場合

    ```sql
    -- distinctキーワードが使える
    select 
      count(customer_id) num_rows,
      count(distinct customer_id) num_customers
    from payment;

    /*
    +----------+---------------+
    | num_rows | num_customers |
    +----------+---------------+
    |    16044 |           599 |
    +----------+---------------+
    1 row in set (0.006 sec)
    */
    ```

## Q70 集計関数の引数として式を使うこともできることを知っていますか?

??? success
    ### 以下の通り

    ```sql
    -- ハイフンは特殊文字なのでquoteで囲っている
    -- 通常はアンダースコアを用いた方がいい

    select
      max(datediff(return_date, rental_date)) "loan-period"
    from rental;

    /*
    +-------------+
    | loan-period |
    +-------------+
    |          10 |
    +-------------+
    1 row in set (0.005 sec)
    */
    ```

## Q71 集計関数がnullをどのように扱うか知っていますか?

??? success
    ### 通常は無視されるが......

    ```sql
    create temporary table number_tbl(
      val smallint
    );

    insert into number_tbl
    values
    (1),
    (3),
    (5),
    (null);

    select
      count(*) num_rows,
      count(val) num_vals,
      sum(val) total,
      max(val) max_val,
      avg(val) avg_val
    from number_tbl;

    -- count(*)だけは例外
    -- count(*)は行の個数を数える
    -- count(val)はval列に含まれている値の個数を数える
    /*
    +----------+----------+-------+---------+---------+
    | num_rows | num_vals | total | max_val | avg_val |
    +----------+----------+-------+---------+---------+
    |        4 |        3 |     9 |       5 |  3.0000 |
    +----------+----------+-------+---------+---------+
    1 row in set (0.001 sec)
    */
    ```

## Q72 複数列や式によるグループ化について理解していますか?

??? success
    ### 複数列

    ```sql
    -- 俳優とratingの組み合わせごとに集計する
    select
      fa.actor_id,
      f.rating,
      count(*)
    from film_actor fa
      inner join film f
      on fa.film_id = f.film_id
    group by fa.actor_id, f.rating
    having count(*) >= 15
    order by 1, 2;

    /*
    +----------+--------+----------+
    | actor_id | rating | count(*) |
    +----------+--------+----------+
    |      107 | NC-17  |       16 |
    |      111 | PG     |       15 |
    +----------+--------+----------+
    2 rows in set (0.006 sec)
    */
    ```

    ### 式

    ```sql
    -- rentalテーブルのrental_dateカラムから
    -- 年だけを抜き出してグループ化している
    select 
      extract(year from rental_date) year,
      count(*) how_many
    from rental
    group by
      extract(year from rental_date)
    ;

    /*
    +------+----------+
    | year | how_many |
    +------+----------+
    | 2005 |    15862 |
    | 2006 |      182 |
    +------+----------+
    2 rows in set (0.008 sec)
    */
    ```

## Q73 グループ化時に、総計を含めて表示する方法を知っていますか?(with rollup)

??? success
    ### 使いどころ

    ```text
    ・俳優とレーティングの組み合わせごとの映画の本数を取得
      するとき、各俳優が出演している映画の本数も同時に
      表示したくなったとする
    
    with rollup
      ・グループ化時に指定する
      ・総計を出力
      ・総計の行において、グループ化に使用した列の表記は
        NULLとなる（groupingで変更可能）
      ・すべての行に対してもNULLが表示される
    
    grouping(カラム)
      ・総計の列である場合、true(1)を返す
      ・直接表示する代わりに、ifを用いて置換可能
      ・mysql限定(mariadbでは使えない: 2024/03現在)
    ```

    ### 総計の例

    ```sql
    select
      fa.actor_id,
      f.rating,
      count(*)
    from film_actor fa
      inner join film f
      on fa.film_id = f.film_id
    group by 
      fa.actor_id asc, 
      f.rating with rollup 
    -- order by 1, 2 -- (mysql限定)
    ;

    /*
    +----------+--------+----------+
    | actor_id | rating | count(*) |
    +----------+--------+----------+
    |        1 | G      |        4 |
    |        1 | PG     |        6 |
    |        1 | PG-13  |        1 |
    |        1 | R      |        3 |
    |        1 | NC-17  |        5 |
    |        1 | NULL   |       19 |
    |        2 | G      |        7 |
    ...............................
    |      200 | NC-17  |        4 |
    |      200 | NULL   |       20 |
    |     NULL | NULL   |     5462 |
    +----------+--------+----------+
    1197 rows in set (0.004 sec)
    */
    ```

    !!! warning
        ### mysql8.3の場合
        - only_full_group_byモードが有効になっている場合、order byとrollupを併用することはできない

        ### mariadbの場合
        - order byとrollupは併用できない。

        ### 共通点
        - asc, descをgroup byの列に適用し、表示順を変えることは可能

    ### 総計の例(grouping適用)

    ```sql
    select
      fa.actor_id,
      IF(grouping(f.rating), "All rating", f.rating),
      count(*)
    from film_actor fa
      inner join film f
      on fa.film_id = f.film_id
    group by 
      fa.actor_id asc, 
      f.rating with rollup 
    ;
    ```

    ### mariadbの場合の置き換え

    ```text
    if
      if(条件, trueなら, falseなら)

    coalesce
      nullでない最初の引数を返す
    
    問題点
      ・本当にNULLである場合と、
        groupingによってNULLが追加された場合の区別ができない
        ということ
    ```

    ```sql
    select
      -- if(fa.actor_id is null, "All actor", fa.actor_id) actor_id,
      coalesce(fa.actor_id, "All actor"),
      -- if(f.rating is null, "All rating", f.rating) rating,
      coalesce(f.rating, "All rating"),
      count(*)
    from film_actor fa
      inner join film f
      on fa.film_id = f.film_id
    group by
      fa.actor_id asc,
      f.rating with rollup

    /*
    +-----------+------------+----------+
    | actor_id  | rating     | count(*) |
    +-----------+------------+----------+
    | 1         | G          |        4 |
    | 1         | PG         |        6 |
    | 1         | PG-13      |        1 |
    | 1         | R          |        3 |
    | 1         | NC-17      |        5 |
    | 1         | All rating |       19 |
    ...skipping...
    | 200       | G          |        5 |
    | 200       | PG         |        3 |
    | 200       | PG-13      |        2 |
    | 200       | R          |        6 |
    | 200       | NC-17      |        4 |
    | 200       | All rating |       20 |
    | All actor | All rating |     5462 |
    +-----------+------------+----------+
    1197 rows in set (0.005 sec)
    */
    ```