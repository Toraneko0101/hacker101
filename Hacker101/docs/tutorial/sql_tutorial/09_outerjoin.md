# クイズ(9:外部結合)

## Q82 外部結合について知っていますか?

??? success
    ### 内部結合との違い

    ```text
    ・内部結合(inner join)は、結合条件が一致するものを抽出
      していた
    ```

    ```sql
    -- 本来filmテーブルには1000行あるが......
    -- inventoryテーブルに1件もないfilmがあるため、
    -- 以下の様になっている。
    select 
      f.film_id,
      f.title,
      count(*) num_copies
    from
      film f
    inner join
      inventory i
    on
      f.film_id = i.film_id
    group by
      f.film_id, 
      f.title
    ;

    /*
    +---------+-----------------------------+------------+       
    | film_id | title                       | num_copies |       
    +---------+-----------------------------+------------+       
    |       1 | ACADEMY DINOSAUR            |          8 |       
    |       2 | ACE GOLDFINGER              |          3 |       
    |       3 | ADAPTATION HOLES            |          4 |       
    |       4 | AFFAIR PREJUDICE            |          7 |       
    |       5 | AFRICAN EGG                 |          3 |       
    |       6 | AGENT TRUMAN                |          6 |       
    958 rows in set (0.009 sec)
    */
    ```

    ### 外部結合の場合

    ```text
    ・outer joinキーワードを用いる
    ```

    ```sql
    -- inventoryテーブルに行が含まれているかに関係なく
    -- (filmテーブルを基準に)行を返したい場合

    select
      f.film_id,
      f.title,
      count(i.inventory_id) num_copies
    from
      film f
    left outer join
      inventory i
    on
      f.film_id = i.film_id
    group by
      f.film_id, f.title
    ;

    /*
    +---------+-----------------------------+------------+       
    | film_id | title                       | num_copies |       
    +---------+-----------------------------+------------+       
    |       1 | ACADEMY DINOSAUR            |          8 |       
    |       2 | ACE GOLDFINGER              |          3 |       
    |       3 | ADAPTATION HOLES            |          4 |       
    |       4 | AFFAIR PREJUDICE            |          7 |       
    |       5 | AFRICAN EGG                 |          3 |       
    |       6 | AGENT TRUMAN                |          6 |       
    1000 rows in set (0.008 sec)
    */
    ```

    ### group化を使わず見比べてみる

    ```sql
    -- inner join(結合条件を満たしていないので以下)
    select 
      f.film_id, f.title, i.inventory_id
    from film f
    inner join inventory i
    on f.film_id = i.film_id
    where
      f.film_id = 14
    ;
    -- Empty set (0.001 sec)

    -- outer join
    -- 1件も結合条件が満たされないと、nullを返す
    select 
      f.film_id, f.title, i.inventory_id
    from film f
    left outer join inventory i
    on f.film_id = i.film_id
    where
      f.film_id = 14
    ;

    /*
    +---------+----------------+--------------+
    | film_id | title          | inventory_id |
    +---------+----------------+--------------+
    |      14 | ALICE FANTASIA |         NULL |
    +---------+----------------+--------------+
    1 row in set (0.000 sec)
    */
    ```

## Q83 左外部結合と右外部結合の違いを知っていますか?

??? success
    ### left outer join

    ```text
    ・結合の左側のテーブルによって、
      結果セットの行の個数が決まる
    ```

    ### right outer join

    ```text
    ・結合の右側のテーブルによって
      結果セットの行の個数が決まる
    ```

    ### 違いの例

    ```sql
    create temporary table hoge (
      num int
    );

    -- 連番の作成方法についてはクイズ(6:数値データを操作する
    -- 方法を知っていますか?)を参照のこと

    insert into hoge (num)
      select seq 
      from seq_1_to_10
      where seq <> 4;

    create temporary table fuga (
      num int
    );

    insert into fuga(num)
      select seq 
      from seq_1_to_10
      where (seq mod 2) = 0
    ; 

    -- hogeは4以外
    -- fugaは2の倍数

    -- left outer join
    -- 右だけに存在するものは気にされない
    select
      *
    from 
      hoge
    left outer join 
      fuga
    on
      hoge.num = fuga.num
    order by 1
    ;

    /*
    +------+------+
    | num  | num  |
    +------+------+
    |    1 | NULL |
    |    2 |    2 |
    |    3 | NULL |
    |    5 | NULL |
    |    6 |    6 |
    |    7 | NULL |
    |    8 |    8 |
    |    9 | NULL |
    |   10 |   10 |
    +------+------+
    9 rows in set (0.000 sec)
    */

    -- right outer join
    -- 左だけに存在するものは気にされない

    select
      *
    from 
      hoge
    right outer join 
      fuga
    on
      hoge.num = fuga.num
    order by 1
    ;

    /*
    +------+------+
    | num  | num  |
    +------+------+
    | NULL |    4 |
    |    2 |    2 |
    |    6 |    6 |
    |    8 |    8 |
    |   10 |   10 |
    +------+------+
    5 rows in set (0.001 sec)
    */
    ```

    ### 右外部結合について

    ```text
    ・右外部結合は、サポートしていないDBサーバも存在する
    ```

    ### 補足(省略記法)

    ```text
    ・outerキーワードは書かないこともできるが(A left join B)
      常に明示した方が分かりやすい
    ```

## Q84 3つ以上のテーブルを外部結合する方法を知っていますか?

??? success
    ### 内部結合の時とやり口は同じ

    ```sql
    select
      f.film_id,
      f.title,
      i.inventory_id,
      r.rental_date
    from
      film f
    left outer join
      inventory i
    on
      f.film_id = i.film_id
    left outer join
      rental r
    on
      i.inventory_id = r.inventory_id
    where
      f.film_id between 13 and 14
    ;

    /*
    +---------+----------------+--------------+---------------------+
    | film_id | title          | inventory_id | rental_date         |
    +---------+----------------+--------------+---------------------+
    |      13 | ALI FOREVER    |           67 | 2005-07-31 18:11:17 |
    |      13 | ALI FOREVER    |           67 | 2005-08-22 21:59:29 |
    |      13 | ALI FOREVER    |           68 | 2005-07-28 15:26:20 |
    |      13 | ALI FOREVER    |           68 | 2005-08-23 05:02:31 |
    |      13 | ALI FOREVER    |           69 | 2005-08-01 23:36:10 |
    |      13 | ALI FOREVER    |           69 | 2005-08-22 02:12:44 |
    |      13 | ALI FOREVER    |           70 | 2005-07-12 10:51:09 |
    |      13 | ALI FOREVER    |           70 | 2005-07-29 01:29:51 |
    |      13 | ALI FOREVER    |           70 | 2006-02-14 15:16:03 |
    |      14 | ALICE FANTASIA |         NULL | NULL                |
    +---------+----------------+--------------+---------------------+
    10 rows in set (0.001 sec)
    */
    ```

## Q85 クロス結合を明示的に作成する方法を知っていますか?

??? success
    ### cross joinの例

    ```sql
    select 
      c.name category_name,
      l.name language_name
    from category c
    cross join language l
    ;

    /*
    | category_name | language_name |
    +---------------+---------------+
    | Action        | English       |
    | Action        | Italian       |
    | Action        | Japanese      |
    | Action        | Mandarin      |
    | Action        | French        |
    ...skipping...
    | Sports        | German        |
    | Travel        | English       |
    | Travel        | Italian       |
    | Travel        | Japanese      |
    | Travel        | Mandarin      |
    | Travel        | French        |
    | Travel        | German        |
    +---------------+---------------+
    96 rows in set (0.001 sec)
    */
    ```

    ### 使いどころはあるのか

    ```text
    ・サブクエリを用いて、テーブルを人工的に作成する際、
      union allを用いて1行ずつ連結することがあった

    ・しかし、これは100行以上のような、大規模なテーブルを
      作成したい場合にはうまく行かない
    
    ・そこで各位について集合を生成し、cross joinを用いて
    　作成してみる
    ```

    ```sql
    select
      date_add(
        "2020-01-01",
        interval(
          ones.num + tens.num + hundreds.num
        ) day
      ) dt
    from
      (
        select 0 num union all
        select 1 num union all
        select 2 num union all
        select 3 num union all
        select 4 num union all
        select 5 num union all
        select 6 num union all
        select 7 num union all
        select 8 num union all
        select 9 num
      ) ones
      cross join
      (
        select 0 num union all
        select 10 num union all
        select 20 num union all
        select 30 num union all
        select 40 num union all
        select 50 num union all
        select 60 num union all
        select 70 num union all
        select 80 num union all
        select 90 num
      ) tens
      cross join
      (
        select 0 num union all
        select 100 num union all
        select 200 num union all
        select 300 num
      ) hundreds
    
    where date_add(
      "2020-01-01",
      interval(ones.num + tens.num + hundreds.num) day
    ) < "2021-01-01"
    order by 1
    ;

    /*
    +------------+
    | dt         |
    +------------+
    | 2020-01-01 |
    | 2020-01-02 |
    | 2020-01-03 |
    | 2020-01-04 |
    | 2020-01-05 |
    ...skipping...
    | 2020-12-25 |
    | 2020-12-26 |
    | 2020-12-27 |
    | 2020-12-28 |
    | 2020-12-29 |
    | 2020-12-30 |
    | 2020-12-31 |
    +------------+
    366 rows in set (0.015 sec)
    */
    ```

    ### なお

    ```sql
    -- 0 ~ 365なのは、閏年の対策。whereで絞っている
    select
      date_add(
        "2020-01-01",
        interval(seq) day
      ) dt
    from
      seq_0_to_365
    where
      date_add(
        "2020-01-01",
        interval(seq) day
      ) < "2021-01-01"
    ;

    /*
    +------------+
    | dt         |
    +------------+
    | 2020-01-01 |
    | 2020-01-02 |
    | 2020-01-03 |
    | 2020-01-04 |
    | 2020-01-05 |
    ...skipping...
    | 2020-12-25 |
    | 2020-12-26 |
    | 2020-12-27 |
    | 2020-12-28 |
    | 2020-12-29 |
    | 2020-12-30 |
    | 2020-12-31 |
    +------------+
    366 rows in set (0.004 sec)
    */
    ```

    ### whereを書くのが面倒な場合は、こっち

    ```sql
    -- 再帰CTE再登場！
    with recursive date_ranges as (
      select "2020-01-01" dt
      union all
      select dt + interval 1 day from date_ranges
      where dt + interval 1 day <= "2020-12-31"
    )

    select dt from date_ranges;,

    /*
    +------------+
    | dt         |
    +------------+
    | 2020-01-01 |
    | 2020-01-02 |
    | 2020-01-03 |
    | 2020-01-04 |
    | 2020-01-05 |
    | 2020-01-06 |
    ...skipping...
    | 2020-12-24 |
    | 2020-12-25 |
    | 2020-12-26 |
    | 2020-12-27 |
    | 2020-12-28 |
    | 2020-12-29 |
    | 2020-12-30 |
    | 2020-12-31 |
    +------------+
    366 rows in set (0.005 sec)
    */
    ```

    ### 上記例の使い道

    ```sql
    --2005年のすべての日付とその日のレンタル件数の報告
    --更に上位10個だけ表示

    select
      days.dt,
      count(r.rental_id) num_rentals
    from rental r
    right outer join
    (
      select
      date_add(
        "2005-01-01",
        interval(seq) day
      ) dt
      from
        seq_0_to_365
      where
        date_add(
          "2005-01-01",
          interval(seq) day
        ) < "2006-01-01"
    ) days
    on days.dt = date(r.rental_date)
    group by days.dt
    order by 2 desc
    limit 10
    ;

    /*
    +------------+-------------+
    | dt         | num_rentals |
    +------------+-------------+
    | 2005-07-31 |         679 |
    | 2005-08-01 |         671 |
    | 2005-08-21 |         659 |
    | 2005-07-27 |         649 |
    | 2005-08-02 |         643 |
    | 2005-07-29 |         641 |
    | 2005-07-30 |         634 |
    | 2005-08-19 |         628 |
    | 2005-08-22 |         626 |
    | 2005-08-20 |         624 |
    +------------+-------------+
    10 rows in set (0.999 sec)
    */
    ```

## Q86 自然結合について知っていますか?

??? success
    ### natural join

    ```text
    ・結合条件として、同じ名前のカラムを指定する場合に使える
    ・明示的に結合した方がいい(まだusingの方がまし)
    ```

    ```sql
    -- 同じ名前のカラム全てを結合条件に指定するので、
    -- 以下の様に片方のテーブルを明示する必要があったりする

    -- 本来なら、last_updateとcustomer_idを結合するが
    -- 片方のテーブルの列を制限しているので以下の様になる
    
    select
      cust.first_name,
      cust.last_name,
      date(r.rental_date)
    from
    (
      select
        customer_id,
        first_name,
        last_name
      from
        customer
    ) cust
    natural join
      rental r;

    /*
    +-------------+--------------+---------------------+
    | first_name  | last_name    | date(r.rental_date) |
    +-------------+--------------+---------------------+
    | MARY        | SMITH        | 2005-05-25          |
    | MARY        | SMITH        | 2005-05-28          |
    | MARY        | SMITH        | 2005-06-15          |
    | MARY        | SMITH        | 2005-06-15          |
    | MARY        | SMITH        | 2005-06-15          |
    | MARY        | SMITH        | 2005-06-16          |
    16044 rows in set (0.047 sec)
    */
    ```

