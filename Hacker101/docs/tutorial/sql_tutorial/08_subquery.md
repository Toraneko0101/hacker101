# クイズ(8:サブクエリ)

## Q74 サブクエリについて知っていますか?

??? success
    ### サブクエリ

    ```text
    サブクエリ
      ・丸括弧で囲まれ、別スコープで実行されるクエリ
      （丸括弧で囲まれている＝サブクエリではない。
        算術演算を考えればわかると思うけど）
      ・通常は外側の文（句）よりも先に実行される
      ・外側のクエリの実行後、サブクエリから返された
        データはすべて削除される(文スコープ)
      ・情報を1つのクエリで取得できるという利点がある
    
    文スコープ
      ・SQL文の実行が終了した後、結果セットのために
        確保されたメモリがすべて解放されることを意味する

    ```

    ### サブクエリの例

    ```sql
    -- customer_idが最大の列を返す
    select customer_id, first_name, last_name
    from customer
    where
      customer_id = (
        select max(customer_id) from customer
      )
    ;

    /*
    +-------------+------------+-----------+
    | customer_id | first_name | last_name |
    +-------------+------------+-----------+
    |         599 | AUSTIN     | CINTRON   |
    +-------------+------------+-----------+
    1 row in set (0.147 sec)
    */
    ```

    ### サブクエリと比較

    ```text
    ・上の例の場合、結果セットは1行1列なので、比較可能
    
    ・※結果セットが2行以上になる場合、等しくはならない
    ⇒オペランドの片方は1つの式となるため、2行以上（式の
      集合）とは比較しても同じにはならない
    ```

## Q75 サブクエリの種類について知っていますか?

??? success
    ### 非相関サブクエリ

    ```text
    非相関サブクエリ
      ・通常はこっち
      ・単体でも実行可能
      ・外側の文を参照しない
      ・特定の句の先頭で実行される
      （たとえばwhere句にサブクエリがあったら、実行順序は
        from句 -> サブクエリ -> where句
    ```

    ### スカラーサブクエリ

    ```text
    ・1行1列の結果セットを返す
    ・演算子比較の左右の項で使うことができる
    ```

    ### 複数行を返すサブクエリ

    ```text
    ・1つの値が値の集合の中に含まれているかを調べることは可能
    ```

    ```sql
    -- (in演算子)カナダとメキシコのすべての都市を取得する
    select
      city_id, city
    from city
    where 
      country_id in (
        select country_id
        from country
        where country in ("Canada", "Mexico")
      )
    ;

    /*
    +---------+----------------------------+
    | city_id | city                       |
    +---------+----------------------------+
    |     179 | Gatineau                   |
    |     196 | Halifax                    |
    |     300 | Lethbridge                 |
    |     313 | London                     |
    ...skipping...
    |     541 | Torreón                    |
    |     556 | Uruapan                    |
    |     563 | Valle de Santiago          |
    |     595 | Zapopan                    |
    +---------+----------------------------+
    37 rows in set (0.001 sec)
    */
    ```

    ```sql
    -- <> all === not in
    -- レンタル料金が0ドルの顧客のIDを返し、
    -- それぞれのIDと比較し、すべて異なっていれば、true


    -- <> all
    select
      first_name, last_name
    from customer
    where customer_id <> ALL (
      SELECT customer_id
      from payment
      where amount = 0
    );

    -- not inの場合
    select
      first_name, last_name
    from customer
    where customer_id not in (
      select customer_id
      from payment
      where amount = 0
    );

    -- 576 rows in set (0.003 sec)
    ```

    !!! warning
        - `not in`や `<> all`を使って比較を行う場合、サブクエリの中にnullが含まれると、nullと比較したため、常にfalseと評価される事に注意

    ```sql
    -- all

    -- 北米のどの顧客のレンタル回数より、
    -- レンタル回数が多い顧客を返す
    select customer_id, count(*)
    from rental
    group by customer_id
    having count(*) > ALL(
      select count(*)
      from rental r
        inner join customer c
        on r.customer_id = c.customer_id
        inner join address a
        on c.address_id = a.address_id
        inner join city ct
        on a.city_id = ct.city_id
        inner join country co
        on ct.country_id = co.country_id
      where 
        co.country in ("United States", "Mexico", "Canada")
      group by r.customer_id
    );

    /*
    +-------------+----------+
    | customer_id | count(*) |
    +-------------+----------+
    |         148 |       46 |
    +-------------+----------+
    1 row in set (0.021 sec)

    */
    ```

    ```sql
    -- any

    -- 支払総額が、ボリビアの顧客全員、パラグアイの顧客全員
    -- またはチリの顧客全員の支払う総額より多い顧客を見つける

    select 
      customer_id, sum(amount)
    from payment
    group by customer_id
    having sum(amount) > any (
      select sum(p.amount)
      from payment p
        inner join customer c
        on p.customer_id = c.customer_id
        inner join address a
        on c.address_id = a.address_id
        inner join city ct
        on a.city_id = ct.city_id
        inner join country co
        on ct.country_id = co.country_id
      where
        co.country in ("Bolivia", "Paraguay", "Chile")
      group by co.country
    );

    /*
    +-------------+-------------+
    | customer_id | sum(amount) |
    +-------------+-------------+
    |         137 |      194.61 |
    |         144 |      195.58 |
    |         148 |      216.54 |
    |         178 |      194.61 |
    |         459 |      186.62 |
    |         526 |      221.55 |
    +-------------+-------------+
    6 rows in set (0.016 sec)
    */
    ```

    ### 複数列を返すサブクエリ

    ```text
    ・単一列のサブクエリを2つ返し、AND条件で比較することは
    ・複数列のサブクエリと比較する事と同じ
    ```

    ```sql
    -- Monroeという名前の俳優とPGの映画のすべての組み合わせの
    -- うち、film_actorテーブルで見つかる行を返す
    select fa.actor_id, fa.film_id
    from film_actor fa
    where 
      (fa.actor_id, fa.film_id) in (
        select a.actor_id, f.film_id
        from actor a
          cross join film f
          where a.last_name = "MONROE" and f.rating = "PG"
      )
    ;

    /*
    +----------+---------+
    | actor_id | film_id |
    +----------+---------+
    |      120 |      63 |
    |      120 |     144 |
    |      120 |     414 |
    |      120 |     590 |
    |      120 |     715 |
    |      120 |     894 |
    |      178 |     164 |
    |      178 |     194 |
    |      178 |     273 |
    |      178 |     311 |
    |      178 |     983 |
    +----------+---------+
    11 rows in set (0.001 sec)
    */
    ```

    ### 相関サブクエリ

    ```text
    相関サブクエリ
      ・外側の文の列を1つ以上参照するサブクエリ
      ・外側の文に依存しているため、外側のクエリの各行
      　（結果セットに含まれる可能性のある候補行）に対して
      　サブクエリが評価される。結果、サブクエリの実行回数は 
        外側のクエリの行数となる
      
      ・そのため外側のクエリが大量の行を返す場合は
        パフォーマンスが問題となる

    ```

    ### 相関サブクエリの例

    ```sql
    -- 等号条件

    -- 相関サブクエリを用いて、顧客ごとにレンタルの回数を
    -- 数えた後、レンタル回数が40回の顧客を取得する

    -- 顧客は599人いるので、サブクエリは599回実行される
    select
      c.first_name, c.last_name
    from
      customer c
    where 40 = (
      select count(*)
      from rental r
      -- ここで外側のクエリを参照
      where r.customer_id = c.customer_id
    );

    /*
    +------------+-----------+
    | first_name | last_name |
    +------------+-----------+
    | SUE        | PETERS    |
    | WESLEY     | BULL      |
    +------------+-----------+
    2 rows in set (0.004 sec)
    */
    ```

    ```sql
    -- 範囲条件

    -- これも599回サブクエリは実行される
    select
      c.first_name, c.last_name
    from
      customer c
    where
      (
        -- 特定のcustomer_idのものだけをpaymentテーブルから
        -- 選んで、暗黙的なグループの集合関数を取っている
        -- 条件を満たせば、該当のc.customer_idを含む行が
        -- 表示されることになる
        select sum(p.amount)
        from payment p
        where p.customer_id = c.customer_id
      ) Between 180 and 240;

    /*
    +------------+-----------+
    | first_name | last_name |
    +------------+-----------+
    | RHONDA     | KENNEDY   |
    | CLARA      | SHAW      |
    | ELEANOR    | HUNT      |
    | MARION     | SNYDER    |
    | TOMMY      | COLLAZO   |
    | KARL       | SEAL      |
    +------------+-----------+
    6 rows in set (0.018 sec)
    */
    ```

## Q76 exists演算子と相関サブクエリの組み合わせが頻出することを知っていますか?

??? success
    ### exists演算子

    ```text
    ・結果セットが存在すればtrue
    ・anyとの違いは、片方のオペランドと比較しないこと

    exists
      サブクエリ内の結果が空でなければtrue
    any
      サブクエリの結果を外部のクエリの値と比較して
      1つ以上一致していればtrue
    all
      サブクエリの値を外部のクエリの値と比較して
      すべて一致していればtrue
    ```

    ```sql
    --2005/5/25より前に映画を少なくとも1本レンタルしていた
    --顧客を返す

    select
      c.first_name, c.last_name
    from
      customer c
    where exists (
      -- 顧客ごとにサブクエリを実行している
      -- 顧客は複数件借りているが、そのうちrental_dateの
      -- 条件に合うものだけを結果として出力
      -- そのうえで、exists条件を満たすか考える
      select 1 from rental r
      where 
        r.customer_id = c.customer_id
        and 
        date(r.rental_date) < "2005-05-25"
    );

    /*
    +------------+-------------+
    | first_name | last_name   |
    +------------+-------------+
    | CHARLOTTE  | HUNTER      |
    | DELORES    | HANSEN      |
    | MINNIE     | ROMERO      |
    | CASSANDRA  | WALTERS     |
    | ANDREW     | PURDY       |
    | MANUEL     | MURRELL     |
    | TOMMY      | COLLAZO     |
    | NELSON     | CHRISTENSON |
    +------------+-------------+
    8 rows in set (0.004 sec)
    */
    ```

    ### select 1について

    ```text
    ・existsは結果が空が否かだけ見ているので、返される行の
      中身は関係ない。
    ・しかし、select 1 または、select *を指定するのが慣例
    ```

    ### not exists

    ```sql
    -- サブクエリの結果セットが空かどうかチェック

    -- R指定の映画に一本も出演していない俳優名を返す
    select
      a.first_name, a.last_name
    from
      actor a
    where not exists (
      -- 俳優ごとにサブクエリを呼ぶ
      select 1
      from film_actor fa
        inner join film f
        on f.film_id = fa.film_id
      where
        fa.actor_id = a.actor_id
        and
        f.rating = "R"
    );

    /*
    +------------+-----------+
    | first_name | last_name |
    +------------+-----------+
    | JANE       | JACKMAN   |
    +------------+-----------+
    1 row in set (0.004 sec)
    */
    ```

## Q77 サブクエリがselect文以外でも使えることを知っていますか?

??? success
    ### 相関サブクエリ & update

    ```sql
    -- customerテーブルのlast_updateカラムの値を
    -- 顧客の最終レンタル日に更新する
    -- (不完全な例であることに注意。実行しないこと)
    update 
      customer c
    set
      c.last_update = (
        --customerの各行に対して(where句がないので)
        --last_updateカラムの値を最終レンタル日に更新 
        select 
          max(r.rental_date)
        from
          rental r
        where
          r.customer_id = c.customer_id
      )
    ;

    -- 上記の問題点は、exists条件を考えていないので
    -- 1回も映画をレンタルしていない顧客がいた場合
    -- 相関サブクエリでnullが返され、nullが設定される可能性
    -- があること

    -- よって、where句を設定する
    update
      customer c
    set
      c.last_update = (
        select
          max(r.rental_date)
        from
          rental r
        where
          r.customer_id = c.customer_id
      )
    where 
      exists(
        select 1 
        from rental r
        where
          r.customer_id = c.customer_id
      )
    ;
    ```

    ### 相関サブクエリ & delete

    ```sql
    -- 過去100年間レンタル記録がない行を削除

    delete c from customer c
    where
      36500 < ALL (
        select
          datediff(
            now(), r.rental_date
          ) days_since_last_rental
        from
          rental r
        where
          r.customer_id = c.customer_id
      )
    ;
    ```

    ### delete句とaliasについて

    ```text
    ・mysql/mariadbではエイリアスを定義する場合、
      deleteとfromの間に別名を書き直す必要がある

    ・書かなかった場合(delete from customer as c)
    You have an error in your SQL syntax; 
    check the manual that corresponds to your MariaDB 
    server version for the right syntax to use near 'as c

    ・書いた場合
    Query OK, 0 rows affected (0.008 sec)
    ```

## Q78 from句における、サブクエリの使いどころについて把握していますか?

??? success
    ### 基本的な例

    ```sql
    -- paymentテーブルを顧客ごとにグループ化して、
    -- それをcustomerテーブルと結合している

    -- 総支払額が200を超えている顧客名を出力
    select
      c.first_name,
      c.last_name,
      pymnt.num_rentals,
      pymnt.tot_payments
    from customer c
      inner join (
        select
          customer_id,
          count(*) num_rentals,
          sum(amount) tot_payments
        from payment
        group by customer_id
      ) pymnt
      on c.customer_id = pymnt.customer_id
      where pymnt.tot_payments >= 200;

    /*
    +------------+-----------+-------------+--------------+      
    | first_name | last_name | num_rentals | tot_payments |      
    +------------+-----------+-------------+--------------+      
    | ELEANOR    | HUNT      |          46 |       216.54 |      
    | KARL       | SEAL      |          45 |       221.55 |      
    +------------+-----------+-------------+--------------+      
    2 rows in set (0.006 sec)
    */
    ```

    ### from句におけるサブクエリの利点

    ```text
    ・実際にあるテーブルに関係なく、必要に応じてデータを整形
      し、利用できる
    ・そして、その結果を他のテーブルやサブクエリに結合できる

    ・サブクエリを使うことで、本当に新しいデータを格納したい
      場合のみ、テーブルをDBに追加でき、その他の場合は、
      サブクエリを使うという行動がとれる。
    ```

    ### データを人工的に生成する

    ```sql
    -- サブクエリに設定するクエリ
    -- これを別のクエリのfrom句に追加する
    select
      "Small Fry" name,
      0 low_limit,
      74.99 high_limit
    union all
    select
      "Average Joes" name,
      75 low_limit,
      149.99 high_limit
    union all
    select 
      "Heavy Hitters" name,
      150 low_limit,
      9999999.99 high_limit
    ;
    /*
    +---------------+-----------+------------+
    | name          | low_limit | high_limit |
    +---------------+-----------+------------+
    | Small Fry     |         0 |      74.99 |
    | Average Joes  |        75 |     149.99 |
    | Heavy Hitters |       150 | 9999999.99 |
    +---------------+-----------+------------+
    3 rows in set (0.003 sec)
    */

    -- 顧客IDごとに支払額をgroup化し、
    -- 支払額によって、どのpymnt_grpsの行と
    -- 内部結合するか選択
    -- そして、pymnt_grps.nameでグループ化し
    -- グループ名とグループに属する顧客数を出力

    -- ※結合条件にbetweenを使っていることに注意

    select
      pymnt_grps.name,
      count(*) num_customers
    from 
      (
        select
          customer_id,
          count(*) num_rentals,
          sum(amount) tot_payments
        from
          payment
        group by
          customer_id
      ) pymnt
      inner join
      (
        select
          "Small Fry" name,
          0 low_limit,
          74.99 high_limit
        union all
        select
          "Average Joes" name,
          75 low_limit,
          149.99 high_limit
        union all
        select 
          "Heavy Hitters" name,
          150 low_limit,
          9999999.99 high_limit
      ) pymnt_grps
      on pymnt.tot_payments
        between 
        pymnt_grps.low_limit
        and
        pymnt_grps.high_limit
    group by
      pymnt_grps.name
    ;

    /*
    +---------------+---------------+
    | name          | num_customers |
    +---------------+---------------+
    | Average Joes  |           515 |
    | Heavy Hitters |            46 |
    | Small Fry     |            38 |
    +---------------+---------------+
    3 rows in set (0.024 sec)
    */
    ```

    ### タスク思考のサブクエリ

    ```sql
    -- 各顧客の名前、住所、支払総額、レンタル回数を示したい

    -- サブクエリを使わない場合
    -- 表示したいがために、group byの列が増えている
    select
      c.first_name,
      c.last_name,
      ct.city,
      sum(p.amount) tot_payments,
      count(*) tot_rentals
    from payment p
      inner join customer c
      on p.customer_id = c.customer_id
      inner join address a
      on c.address_id = a.address_id
      inner join city ct
      on a.city_id = ct.city_id
    group by c.first_name, c.last_name, ct.city
    ;
    -- 599 rows in set (0.037 sec)

    -- 本来、group化に使うのはcustomer_idで十分
    -- よって、グループ化をサブクエリで行い
    -- その結果セットに、他の3つのテーブルを結合する

    select
      c.first_name,
      c.last_name,
      ct.city,
      pymnt.tot_payments,
      pymnt.tot_rentals
    from
      (
        select
          customer_id,
          count(*) tot_rentals,
          sum(amount) tot_payments
        from payment
        group by customer_id
      ) pymnt
      inner join customer c
      on pymnt.customer_id = c.customer_id
      inner join address a
      on c.address_id = a.address_id
      inner join city ct
      on a.city_id = ct.city_id
    ;

    -- 599 rows in set (0.009 sec)
    --グループ化を単列で行っているため、クエリは高速化する
    ```

    ### 補足(lateral)

    ```text
    ・from句内で相関サブクエリを使う場合、
      lateralキーワードを用いることができる。(工事中)
    ```

    ```sql
    -- mysqlであることに注意！
    -- mariadbではv11までlateralはサポートされない見込み
    -- 現在(10.6.16)

    -- https://onecompiler.com/mysql
    -- なお、以下の例なら普通に違う方法を使った方がいい
    select version();
    create temporary table hoge(
          id int
    );

    create temporary table fuga(
      num int
    );

    insert into hoge(id)
    values
    (1),(3),(5),(7),(9);

    insert into fuga(num) 
    values
    (0),(0),(1),(1),(1),
    (2),(2),(2),(3),(3);




    SELECT hoge.id, f.fnum
    FROM hoge
    left join LATERAL (
      SELECT fuga.num AS fnum
      FROM fuga
      WHERE fuga.num = hoge.id
      and
      hoge.id <> 1
    ) f on true;

    /*
    +-----------+
    | version() |
    +-----------+
    | 8.0.27    |
    +-----------+
    +------+------+
    | id   | fnum |
    +------+------+
    |    1 | NULL |
    |    3 |    3 |
    |    3 |    3 |
    |    5 | NULL |
    |    7 | NULL |
    |    9 | NULL |
    +------+------+
    */
    ```

## Q79 共通テーブル式について知っていますか?

??? success
    ### 共通テーブル

    ```text
    共通テーブル(common table expression: CTE)
      ・CTEは名前付きのサブクエリ
      ・with句を使ってクエリの前に配置する
      ・with句には複数のCTEをカンマ区切りで指定でき、
        先に定義された他のCTEを参照することもできる
    ```

    ### 共通テーブルの例

    ```sql

    -- 後から使いたい列のみを、selectで排出するようにする
    -- たとえば、actors_s_pgのs.actor_idと、f.titleは
    -- どこからも参照されていないので、本来必要ない

    with
    -- lastnameがsで始まるactorテーブルの行
      actors_s as
      (
        select
          actor_id,
          first_name,
          last_name
        from actor
        where
          last_name LIKE "S%"
      ),
      -- lastnameがSであり、ratingがPGである映画
      actors_s_pg AS
      (
        select
          s.actor_id,
          s.first_name,
          s.last_name,
          f.film_id,
          f.title
        from actors_s s
          inner join film_actor fa
          on s.actor_id = fa.actor_id
          inner join film f
          on f.film_id = fa.film_id
        where f.rating = "PG"
      ),
      -- ratingがPGで、lastnameがSで始まる俳優が出演している
      -- 映画のレンタル収益の総額
      actors_s_pg_revenue as
      (
        select
          spg.first_name,
          spg.last_name,
          p.amount
        from actors_s_pg spg
          inner join inventory i
          on i.film_id = spg.film_id
          inner join rental r
          on i.inventory_id = r.inventory_id
          inner join payment p
          on r.rental_id = p.rental_id
      )
    select 
      spg_rev.first_name,
      spg_rev.last_name,
      sum(spg_rev.amount) tot_revenue
    from actors_s_pg_revenue spg_rev
    group by
      spg_rev.first_name, spg_rev.last_name
    order by 3 desc;


    /*
    +------------+-------------+-------------+
    | first_name | last_name   | tot_revenue |
    +------------+-------------+-------------+
    | NICK       | STALLONE    |      692.21 |
    | JEFF       | SILVERSTONE |      652.35 |
    | DAN        | STREEP      |      509.02 |
    | GROUCHO    | SINATRA     |      457.97 |
    | SISSY      | SOBIESKI    |      379.03 |
    | JAYNE      | SILVERSTONE |      372.18 |
    | CAMERON    | STREEP      |      361.00 |
    | JOHN       | SUVARI      |      296.36 |
    | JOE        | SWANK       |      177.52 |
    +------------+-------------+-------------+
    9 rows in set (0.076 sec)
    */
    ```

    ### 共通テーブルの使い道

    ```text
    ・クエリの結果をスコープ内で複数回参照したい場合
    ・一時的なテーブルをより簡単に作成したい場合
    ・長いSQLクエリを幾つかの部品に分解し、
    　読みやすくしたい場合

    一時テーブル
      ・セッションが終了するまで保持される
    
    CTE
      ・クエリの実行が終了すると自動的に削除される
    
    ```

## Q80 再帰クエリについて知っていますか?（ナイーブツリーと、代替案についても）

??? success
    ### CTEの本当の利点

    ```text
    ・可読性があがったり、再利用ができるのも
    　確かに嬉しいけれど...

    ・再帰CTEを使うと、階層データやツリー構造などの
      再帰的なデータ構造を扱えるようになる

    ・CTEは複数回参照するときに最適だったので、
      再帰はその典型的な例。再帰処理の定義を1か所にまとめ
      られるため、コードの重複が避けられ、メンテナンス性も
      向上する
    ```

    ### 例として以下の木構造を考える

    ```text
    foo - bar
      └ buz - fuga - piyo
          └ hoge
    +------+-----------+------+
    | id   | parent_id | name |
    +------+-----------+------+
    |    1 |      NULL | foo  |
    |    2 |         1 | bar  |
    |    3 |         1 | baz  |
    |    4 |         3 | hoge |
    |    5 |         3 | fuga |
    |    6 |         5 | piyo |
    +------+-----------+------+
    6 rows in set (0.001 sec)
    ```
    
    ### with recursiveの記法

    ```text
    ・再帰CTEを書くときに使う
    ・asはwith recursive 仮想テーブル名の後のasは必須

    with recursive
      仮想テーブル名 as
      (
        仮想テーブルの先頭行になるレコード
        union all
        再帰項
      )

      メインクエリ（この中で仮想テーブルを呼び出す）
    ```
    ### 再帰クエリを使って最下層のデータのみを取得

    ```sql
    -- 今回使用する一時テーブル
    create temporary table A(
      id int,
      parent_id int,
      name varchar(30)
    );


    insert into A
    values
    (1, null, "foo"),
    (2, 1, "bar"),
    (3, 1, "baz"),
    (4, 3, "hoge"),
    (5, 3, "fuga"),
    (6, 5, "piyo")
    ;
    ```

    ```sql

    -- 再帰ステップごとに WITH RECURSIVEで
    -- 定義したテーブルに新しいデータが追加される
    -- 下記の例では、幅優先探索のような動きをする

    with recursive RecursiveCTE as (
      -- 初期クエリ(rootを取得)
      select id, parent_id, name, 1 as level
      from A
      where parent_id is null

      union all

      -- 再帰ステップ
      -- with recursiveに指定したテーブル名を用いると
      -- 前のステップの結果を利用できる
      -- 今回は前のステップのIDと、今回のステップのparent_id
      -- が一致するものを取得している
      select t.id, t.parent_id, t.name, rc.level + 1
      from A t
      inner join RecursiveCTE rc
      on t.parent_id = rc.id
    )
    -- 最下層のデータを取得

    select id, parent_id, name, level
    from RecursiveCTE
    where not exists (
      -- 再帰の結果作られたtableのidが
      -- 元テーブルに親IDとして存在していないかチェック
      select 1
      from A t2
      where t2.parent_id = RecursiveCTE.id
    )

    order by level, id;

    /*
    +------+-----------+------+-------+
    | id   | parent_id | name | level |
    +------+-----------+------+-------+
    |    2 |         1 | bar  |     2 |
    |    4 |         3 | hoge |     3 |
    |    6 |         5 | piyo |     4 |
    +------+-----------+------+-------+
    3 rows in set (0.007 sec)
    */
    ```

    ### 再帰CTEを用いて、特定のIDとその子孫を削除

    !!! warning
        - `mariadb`では再帰CTEと、`delete`や`update`, `insert`文を併用できない(2024年3月現在)
        - `https://jira.mariadb.org/browse/MDEV-18511`

    ```sql
    -- 実行未確認

    with recursive DeleteNodes as (
      -- 初期クエリ 
      select id
      from A
      where id = 3

      union all 

      -- 再帰クエリ(子孫を再帰的に取得)
      select t.id
      from A t
      inner join DeleteNodes dn
      on t.parent_id =  dn.id
    )

    delete from A
    where id in (
      select id from DeleteNodes
    );

    select * from A;
    ```

    ```sql
    -- mariadbにおける現状の対策
    -- 一時テーブルを作成する場合

    -- 削除する行を再帰的に取得し、これを一時テーブルに格納
    -- それから反映している。
    create temporary table DeleteNodes
    (
      id int
    );

    insert into DeleteNodes
    with recursive cte as (
      select T.id
      from A as T
      where T.id = 3

      union all

      select T.id
      from A as T
      inner join cte
      on cte.id = T.parent_id

    )

    select id from cte;

    select * from DeleteNodes;

    /*
    +------+
    | id   |
    +------+
    |    3 |
    |    4 |
    |    5 |
    |    6 |
    +------+
    4 rows in set (0.000 sec)
    */

    delete from A
    where id in (select id from DeleteNodes);

    select * from A;

    /*
    +------+-----------+------+
    | id   | parent_id | name |
    +------+-----------+------+
    |    1 |      NULL | foo  |
    |    2 |         1 | bar  |
    +------+-----------+------+
    2 rows in set (0.000 sec)
    */

    -- 一時テーブルの削除(クリーンアップ)
    drop temporary table if exists DeleteNodes;
    ```

    ### ナイーブツリーについて

    ```text
    ナイーブツリー
      ・上記の様に、ツリー構造を表現するときに
      　親IDだけを覚えている、いわゆる隣接リストをさす
      ・ツリー全体などを取得したい場合に、
      　テーブルを幾つも自己結合する必要があるので、
      　アンチパターンとされている
      ・ただし、枝分かれが多く、階層がごく浅い木に関しては
        必ずしもアンチパターンではないと思われる
      ・再帰CTEによる削除ができない場合は、
      　上記で上げたように少し面倒
    
    経路列挙モデル
      ・親子関係をそれぞれのノードにパスとして持たせるモデル
      ・パスを更新する操作が面倒
    
    閉包テーブルモデル
      ・別テーブルでノード同士の関係性を定義する
      ・親子関係だけでなく、すべての祖先と子孫の組み合わせを
        個別のレコードとして格納するので、データ量が非常に
        増加する危険性がある
      ・ただし、データの追加、削除、更新は容易で、整合性も
        維持しやすい
    
    入れ子集合モデル
      ・idのほかに、left,rightのような左右の境界を
      　意味するカラムを持つ

      ・たとえば以下のデータを考える
      | id  | left | right |
      | --- | ---- | ----- |
      | 1   | 3    | 7     |
      | 2   | 1    | 9     |
      | 3   | 4    | 6     |

      ・この時、親子関係は
        2 -> 1 -> 3となる
      
      ・先祖になる条件は、特定のノードのleftよりleftが小さく
        特定のノードのrightよりrightが大きいこと
        （つまり、外側から囲んでいる）
      ・子孫になる条件は、特定のノードのleftよりleftが大きく
      　特定のノードのrightよりrightが小さいこと
        （つまり間に収まる）
      
      ・この性質上、子孫、先祖の一括取得ができると
      　いう利点がある

      ・ただし、データの更新、削除時にはleft,rightの再計算
      　が必要となり、また、データが複雑になる
      　という欠点もある
    ```

## Q81 select句でサブクエリを使用する際の注意点について知っていますか?

??? success
    ###　注意点

    ```text
    select句(というよりfrom句とwhere句以外)
    でサブクエリを使った場合
      ・サブクエリの結果セットは1行1列でなければならない
      ・つまり、スカラーサブクエリしか使えない

    ```

    ```sql
    -- 下記のように一気に返すことはできない
    select 
      (
        select c.first_name, c.last_name 
        from customer c
        where c.customer_id = p.customer_id
      )

    ```

    ```sql
    -- inner joinで結合するのではなく
    -- 相関サブクエリをselect句で使っている

    -- select句ではスカラーサブクエリしか使えないので、
    -- 3回アクセスして1行*1列のデータを3つ返している。
    select
    (
      select c.first_name
      from customer c
      where c.customer_id = p.customer_id
    ) 
    first_name,

    (
      select c.last_name
      from customer c
      where c.customer_id = p.customer_id
    )
    last_name,

    (
      select ct.city
      from customer c
        inner join address a
        on c.address_id = a.address_id
        inner join city ct
        on a.city_id = ct.city_id
      where c.customer_id = p.customer_id
    )
    city,

    sum(p.amount) tot_payments,
    count(*) tot_rentals
  
    from payment p
    group by p.customer_id
    ;

    /*
    +-------------+--------------+----------------------------+--------------+-------------+
    | first_name  | last_name    | city                       | tot_payments | tot_rentals |
    +-------------+--------------+----------------------------+--------------+-------------+
    | MARY        | SMITH        | Sasebo                     |       118.68 |          32 |
    | PATRICIA    | JOHNSON      | San Bernardino             |       128.73 |          27 |
    | LINDA       | WILLIAMS     | Athenai                    |       135.74 |          26 |
    | BARBARA     | JONES        | Myingyan                   |        81.78 |          22 |
    | ELIZABETH   | BROWN        | Nantou                     |       144.62 |          38 |
    | JENNIFER    | DAVIS        | Laredo                     |        93.72 |          28 |
    ...skipping...
    | AUSTIN      | CINTRON      | Tieli                      |        83.81 |          19 |
    +-------------+--------------+----------------------------+--------------+-------------+
    599 rows in set (0.022 sec)
    */
    ```

    ### 補足(insert文で非相関サブクエリ)

    ```sql
    -- 1つのクエリで値を追加できている。
    insert into
      film_actor (actor_id, film_id, last_update)
    values(
      (
        select actor_id 
        from actor
        where 
          first_name = "JENNIFER" and last_name = "DAVIS"
      ),

      (
        select film_id
        from film
        where title = "ACE GOLDFINGER"
      ),
      now()
    );
    ```


    ### 補足(order by句でサブクエリ)

    ```sql
    -- 演じた映画の数順にsort
    select 
      a.actor_id,
      a.first_name,
      a.last_name
    from
      actor a
    order by
      (
        select count(*)
        from film_actor fa
        where fa.actor_id = a.actor_id
      ) desc
    limit 10
    ;

    /*
    +----------+------------+-----------+
    | actor_id | first_name | last_name |
    +----------+------------+-----------+
    |      107 | GINA       | DEGENERES |
    |      102 | WALTER     | TORN      |
    |      198 | MARY       | KEITEL    |
    |      181 | MATTHEW    | CARREY    |
    |       23 | SANDRA     | KILMER    |
    |       81 | SCARLETT   | DAMON     |
    |       13 | UMA        | WOOD      |
    |      106 | GROUCHO    | DUNST     |
    |      158 | VIVIEN     | BASINGER  |
    |       37 | VAL        | BOLGER    |
    +----------+------------+-----------+
    10 rows in set (0.011 sec)
    */
    ```

