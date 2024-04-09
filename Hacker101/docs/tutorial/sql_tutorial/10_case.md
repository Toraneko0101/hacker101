# クイズ(10:条件)

## Q87 条件付きロジックを知っていますか?(フィルターとは異なる)

??? success
    ### 思い出す
    
    ```text
    ・with rollupを説明する中で、ifを使ったことがあった。
    ```

    ### 条件付きロジック
    
    ```text
    ・実行結果に応じて動作が変更できれば便利

    ・なぜ以下ではifではなく、caseを用いるのか?
    
    case
      ・SQL91の規格の一部
      ・select, insert, update, delete文で使える
    
    if
      ・標準SQLではない
      ・一部のDBでは機能しない
    ```

## Q88 case式について知っていますか?

??? success
    ### 検索case式

    ```text
    case
      when c1 then e1
      when c2 then e2
      ...
      when cn then en
      [else ed]
    end

    ・when句の条件がtrueならthenの後の結果を返す
      返す値はサブクエリでもいいし、どんな値でもいい
    ・どれもtrueと評価されなかった場合、elseが返されるが、
      これはオプションである
    ```

    ```sql
    -- 相関サブクエリを用い、
    -- customer.activeの列が1の顧客ごとにレンタル回数を取得
    -- 0である場合は、1回もレンタルしていない

    -- group byが要らないのが魅力
    select
      c.first_name,
      c.last_name,
      case
        when c.active = 0 then 0
        else
          (
            select count(*) 
            from rental r
            where r.customer_id = c.customer_id
          )
      end num_rentals
    from customer c
    ;

    /*
    +-------------+--------------+-------------+
    | first_name  | last_name    | num_rentals |
    +-------------+--------------+-------------+
    | MARY        | SMITH        |          32 |
    | PATRICIA    | JOHNSON      |          27 |
    | LINDA       | WILLIAMS     |          26 |
    | BARBARA     | JONES        |          22 |
    | ELIZABETH   | BROWN        |          38 |
    | JENNIFER    | DAVIS        |          28 |
    599 rows in set (0.004 sec)
    */
    ```

    ### 単純case式

    ```text
    case v0
      when v1 then e1
      when v2 then e2
      ...
      when vN then eN
      [else ed]
    end

    ・v0とv1,...vNで値を比較する。
    ・一致しなければedを返す
    ```

    ### 検索case式との違い

    ```text
    ・単純case式は、範囲条件、不等号条件、
      and/or/notを用いた複合条件を追加できない
    
    ・柔軟性に欠けるので、単純なロジック以外は
      検索case式を使おう！
    ```

    ```sql
    -- 単純case
    select  
      category.category_id,
      case category.name
        when "Children" then "All Ages"
        when "Family" then "All Ages"
        when "Sports" then "All Ages"
        when "Animation" then "All Ages"
        when "Horror" then "Adult"
        when "Music" then "Teens"
        when "Games" then "Teens"
        else "Other"
      end name
    from category;


    -- 検索case
    select
      c.category_id,
      case
        when 
          c.name in 
          (
            "Children", 
            "Family", 
            "Sports", 
            "Animation"
          )
        then "All Ages"

        when c.name = "Horror" then "Adult"
        when c.name in ("Music", "Games") then "Teens"
        else "Others"
      end name
    from category c
    ;

    -- 結果は同一
    /*
    +-------------+----------+
    | category_id | name     |
    +-------------+----------+
    |           1 | Other    |
    |           2 | All Ages |
    |           3 | All Ages |
    |           4 | Other    |
    |           5 | Other    |
    |           6 | Other    |
    |           7 | Other    |
    |           8 | All Ages |
    |           9 | Other    |
    |          10 | Teens    |
    |          11 | Adult    |
    |          12 | Teens    |
    |          13 | Other    |
    |          14 | Other    |
    |          15 | All Ages |
    |          16 | Other    |
    +-------------+----------+
    16 rows in set (0.000 sec)
    */
    ```


## Q89 case式の具体的な例について知っていますか?

??? success

    ### 1. 結果セットの変換

    ```text
    たとえば
    +--------------+-------------+
    | rental_month | num_rentals |
    +--------------+-------------+
    | May          | 1156        |
    | June         | 2311        |
    | July         | 6709        |
    +--------------+-------------+
    3 rows in set (0.02 sec)

    上記の出力を
    +-------------+--------------+--------------+
    | May_rentals | June_rentals | July_rentals |
    +-------------+--------------+--------------+
    | 1156        | 2311         | 6709         |
    +-------------+--------------+--------------+
    1 row in set (0.01 sec)

    と変換したくなったとする
    ```

    ```sql
    -- monthnameは時間データを月名に変換する
    /*
    select monthname("2009-04-12");
    +-------------------------+
    | monthname("2009-04-12") |
    +-------------------------+
    | April                   |
    +-------------------------+
    1 row in set (0.008 sec)
    */

    -- 変換前
    select
      monthname(rental_date) rental_month,
      count(*) num_rentals
    from
      rental
    where
      rental_date between "2005-05-01" and "2005-08-01"
    group by monthname(rental_date)
    ;

    -- 変換後
    -- カラムに対して、合致する行を1にして、集計する
    select
      sum(
        case
          when monthname(rental_date) = "May" then 1
          else 0
        end
      ) May_rentals,
      
      sum(
        case
          when monthname(rental_date) = "June" then 1
          else 0
        end
      ) June_rentals,

      sum(
        case
          when monthname(rental_date) = "July" then 1
          else 0
        end
      ) July_rentals

      from rental
      where
        rental_date between "2005-05-01" and "2005-08-01"
      ;

    /*
    +-------------+--------------+--------------+
    | May_rentals | June_rentals | July_rentals |
    +-------------+--------------+--------------+
    |        1156 |         2311 |         6709 |
    +-------------+--------------+--------------+
    1 row in set (0.006 sec)
    */
    ```

    !!! warning
        - 値の個数が少ないのでうまくいっているだけ
        - 100個あったら崩壊する

    ### 補足(sql serverならpivotが使える！)

    ```sql
    -- https://onecompiler.com/sqlserver
    select @@version;

    /*
    Microsoft SQL Server 2022 (RTM-CU12) (KB5033663) - 16.0.4115.5 (X64) 
      Mar  4 2024 08:56:10 
      Copyright (C) 2022 Microsoft Corporation
      Developer Edition (64-bit) on Linux (Ubuntu 22.04.4 LTS) <X64>                                                                                                      

    (1 rows affected)    
    */

    create table sale(
      item varchar(255),
      money int
    )

    insert into sale
    values
    ("apple", 100),
    ("peach", 200),
    ("apple", 140),
    ("orange", 200),
    ("peach", 420)
    ;

    select
      pv.apple,
      pv.peach,
      pv.orange
    from
      sale
    pivot
      (
        sum(money)
        for item
        in (apple, peach, orange)
      ) as pv
    ;

    /*
    apple       peach       orange     
    ----------- ----------- -----------
            240         620         200

    (1 rows affected)
    */
    ```

    !!! info
        - mariadb/mysqlに、pivotはない
        - ストアドプロシージャを使って、無理やり変換する例がweb上に転がっていた
        - ~~諦めてpandas使えば?~~

    ### 2. 存在を確認したい場合

    ```sql
    -- first_nameがSAまたはSUで始まる俳優が
    -- レーティングがG, PGの映画に出演したことがあるかどうか

    -- 本数は関係なのでY/Nで変換する
    select 
      a.first_name, 
      a.last_name,
      case
        when exists
          ( 
            -- 相関サブクエリが使える(actorごとに)
            select 1 
            from film_actor fa
            inner join film f
            on fa.film_id = f.film_id
            where
              fa.actor_id = a.actor_id
              and
              f.rating = "G"
          ) then "Y"
        else "N"
      end g_actor,

      case
        when exists
          (
            select 1
            from film_actor fa
            inner join film f
            on fa.film_id = f.film_id
            where
              fa.actor_id = a.actor_id
              and
              f.rating = "PG"
          ) then "Y"
        else "N"
      end pg_actor
    
    from actor a
    where
      a.first_name regexp "^S[UA]"
    ;

    /*
    +------------+-----------+---------+----------+
    | first_name | last_name | g_actor | pg_actor |
    +------------+-----------+---------+----------+
    | SANDRA     | KILMER    | Y       | Y        |
    | SANDRA     | PECK      | Y       | Y        |
    | SUSAN      | DAVIS     | Y       | Y        |
    | SUSAN      | DAVIS     | Y       | Y        |
    | SALMA      | NOLTE     | Y       | N        |
    +------------+-----------+---------+----------+
    5 rows in set (0.006 sec)
    */
    ```

    ### 3. いち、に、さん、たくさん！

    ```sql
    -- 行の個数には関心があるが、それ以上なら細かく
    -- 数字にする必要がない場合

    select
      f.title,
      case
        (
          select count(*)
          from inventory i
          where
            i.film_id = f.film_id
        )
        when 0 then "在庫切れ!"
        when 1 then "売り切れ間近!"
        when 2 then "売り切れ間近!"
        when 3 then "人気！"
        when 4 then "人気！"
        else "通常"
      end film_availability
    from film f
    where f.title like "ALI%"
    ;

    /*
    +----------------+-------------------+
    | title          | film_availability |
    +----------------+-------------------+
    | ALI FOREVER    | 人気！            |
    | ALICE FANTASIA | 在庫切れ!         |
    | ALIEN CENTER   | 通常              |
    +----------------+-------------------+
    3 rows in set (0.001 sec)
    */

    ```

    ### 4. ゼロ除算エラーの対策

    ```sql
    -- なお、mysql/mariadbはnullになるだけ

    -- 各顧客の平均支払い額を求める。
    -- 1件もレンタルしていないと0で割りだすので対策

    select
      c.first_name,
      c.last_name,
      sum(p.amount) tot_payment_amt,
      count(p.amount) num_payments,
      sum(p.amount) /
        case 
          when count(p.amount) = 0 then 1
          else count(p.amount)
        end avg_payment
    from customer c
      left outer join payment p
      on c.customer_id = p.customer_id
    group by
      c.first_name, c.last_name
    ;

    /*
    +-------------+--------------+-----------------+--------------+-------------+
    | first_name  | last_name    | tot_payment_amt | num_payments | avg_payment |
    +-------------+--------------+-----------------+--------------+-------------+
    | AARON       | SELBY        |          110.76 |           24 |    4.615000 |
    | ADAM        | GOOCH        |          101.78 |           22 |    4.626364 |
    | ADRIAN      | CLARY        |           74.81 |           19 |    3.937368 |
    | AGNES       | BISHOP       |           98.77 |           23 |    4.294348 |
    ...skipping...
    +-------------+--------------+-----------------+--------------+-------------+
    599 rows in set (0.030 sec)
    */
    ```

    ### 5. 条件付きで更新する

    ```sql
    create temporary table A (
      num int,
      name varchar(10)
    );

    insert into A(num, name)
    select seq, "odd" from seq_0_to_5;

    update A
    set name = 
      case
        when num mod 2 = 0 then "even"
        else name
      end 
    ;

    select * from A;
    /*
    +------+------+
    | num  | name |
    +------+------+
    |    0 | even |
    |    1 | odd  |
    |    2 | even |
    |    3 | odd  |
    |    4 | even |
    |    5 | odd  |
    +------+------+
    6 rows in set (0.000 sec)
    */
    ```

    ### 6. nullを処理する

    ```sql
    -- nullをUnKnownに書き換える
    select
      c.first_name,
      c.last_name,
      case
        when a.address is null then "UnKnown"
        else a.address
      end address,

      case
        when ct.city is null then "UnKnown"
        else ct.city
      end city,

      case 
        when cn.country is null then "Unknown"
        else cn.country
      end country
    from customer c
      left outer join address a
      on c.address_id = a.address_id

      left outer join city ct
      on a.city_id = ct.city_id

      left outer join country cn
      on ct.country_id = cn.country_id
    ;

    /*
    +-------------+--------------+----------------------------------------+----------------------------+---------------------------------------+        
    | first_name  | last_name    | address                                | city                       | country                               |        
    +-------------+--------------+----------------------------------------+----------------------------+---------------------------------------+        
    | MARY        | SMITH        | 1913 Hanoi Way                         | Sasebo                     | Japan                                 |        
    | PATRICIA    | JOHNSON      | 1121 Loja Avenue                       | San Bernardino             | United States                         |        
    | LINDA       | WILLIAMS     | 692 Joliet Street                      | Athenai                    | Greece                                |  
    599 rows in set (0.004 sec) 
    */
    ```