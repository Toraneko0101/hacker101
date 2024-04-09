# クイズ(13:ビュー)

## Q113 Viewについて知っていますか?

??? success
    ### Viewの概要

    ```text
    上手く設計されたアプリ
      ・実装の詳細を明かさず、エンドユーザに機能を提供
      ・設計が変更されてもユーザに影響が及ばないようにする
    
    Viewとは
      ・データを取得するメカニズム

      ・テーブルとは異なり、データストレージを持たない
      ⇒ディスク領域を圧迫しない

      ・クエリに名前を付け、格納することで作成可能
        (create viewを実行しても、DBはビューの定義を格納
        するだけで実行はしない。あとから利用できるようにする)

      ・他ユーザはViewを用いてテーブルで直接クエリを
        実行するのと同じようにデータにアクセス可能
        (from句にviewを指定するだけ)
    ```

    ### Viewの例

    ```text
    1 マーケティング部門が、広報活動のためにメールアドレスに
      アクセスしなければならないケースを考える
      

    2 ただし、会社の個人情報保護の規定に従い、このデータは
      機密扱いになっている
      (つまり個人を特定するような情報は入手させたくない)
    
    3 そのため直接customerテーブルにアクセスさせることは
      避けたい
    
    4 そこで、customer_vwというビューを定義し、
      顧客データにアクセスしたい場合はこのビューを使わせる
    
    5 必要な情報だけcustomer_vwに格納することで
      元のテーブルを変えずに公開が可能
    ```

    ```sql
    /*
    ・viewの列名はテーブルの列の名前と違っていても構わない
    ・viewの列1つごとにselectの列を1つ対応させる
    ・下記ではemailの最初の2文字と最後の4文字だけを公開した
    */
    create view customer_vw
    (
      customer_id,
      first_name,
      last_name,
      email
    ) as
    select
      customer_id,
      first_name,
      last_name,
      concat(substr(email, 1, 2), "*****", substr(email, -4)) email
    from
      customer
    ;

    select first_name, last_name, email
    from customer_vw
    limit 5
    ;

    /*
    +------------+-----------+-------------+
    | first_name | last_name | email       |
    +------------+-----------+-------------+
    | MARY       | SMITH     | MA*****.org |
    | PATRICIA   | JOHNSON   | PA*****.org |
    | LINDA      | WILLIAMS  | LI*****.org |
    | BARBARA    | JONES     | BA*****.org |
    | ELIZABETH  | BROWN     | EL*****.org |
    +------------+-----------+-------------+
    5 rows in set (0.001 sec)
    */
    ```

    ### viewを削除したいとき

    ```sql
    -- tableの代わりにview
    drop view customer_vw;
    ```

    ### select文に関しては元のテーブルのように扱える

    ```sql
    select
      first_name,
      count(*),
      min(last_name) min,
      max(last_name) max
    from
      customer_vw
    where
      first_name like "J%"
    group by 
      first_name
    having
      count(*) > 1
    order by
      1
    ;

    /*
    +------------+----------+-------+-------+
    | first_name | count(*) | min   | max   |
    +------------+----------+-------+-------+
    | JAMIE      |        2 | RICE  | WAUGH |
    | JESSIE     |        2 | BANKS | MILAM |
    +------------+----------+-------+-------+
    2 rows in set (0.001 sec)
    */

    -- 他のviewや他のテーブルとの結合も可能

    select
      cv.first_name, cv.last_name, p.amount
    from
      customer_vw cv
      inner join
        payment p
      on cv.customer_id = p.customer_id
    where
      p.amount >= 11
    ;

    /*
    +------------+-----------+--------+
    | first_name | last_name | amount |
    +------------+-----------+--------+
    | ALMA       | AUSTIN    |  11.99 |
    | KAREN      | JACKSON   |  11.99 |
    | KENT       | ARSENAULT |  11.99 |
    | NICHOLAS   | BARFIELD  |  11.99 |
    | RICHARD    | MCCRARY   |  11.99 |
    | ROSEMARY   | SCHMIDT   |  11.99 |
    | TANYA      | GILBERT   |  11.99 |
    | TERRANCE   | ROUSH     |  11.99 |
    | VANESSA    | SIMS      |  11.99 |
    | VICTORIA   | GIBSON    |  11.99 |
    +------------+-----------+--------+
    10 rows in set (0.015 sec)
    */
    ```

## Q114 Viewを使う理由について知っていますか?

??? success
    ### データセキュリティ

    ```text
    ・テーブルには、ID番号やクレジットカードの番号など
      機密情報を格納する列が含まれる
    
    ・これらのデータをすべてのユーザに公開するのは
      個人情報保護や法律の観点から問題である
      (社内であってもNeed to Knowの観点から問題になる)
    
    機密情報を守るために
      ・テーブルを非公開にする
      ・機密情報を含んだ列は省略、または難読化した上で
        必要情報をviewで公開する
      ・アクセスできる行を制限することも可能

      ※Oracle DBの場合、VPD(Virtual Private Database)
        を用いることで、Viewなしで行と列にポリシーをかける
        事ができる。(ポリシーを適用すると、予め設定しておいた
        where句の文が動的に追加される)
    ```

    ```sql
    /*
    activeでない状態の顧客の行を取り除く
    ⇒メールを送信する際、activeな顧客だけを対象にする等
    */

    create view active_customer_vw
    (
      customer_id,
      first_name,
      last_name,
      email
    ) as
    select
      customer_id,
      first_name,
      last_name,
      concat(substr(email,1,2), "*****", substr(email, -4)) email
    from
      customer
    where
      active = 1
    ;
    -- 584 rows in set (0.003 sec)
    -- 15行が省略されている
    ```

    ### データ集計

    ```text
    ・レポート等を作成する際には、集計データが必要になる

    ・データが事前に集計されているように見せたい場合、
      Viewがよい手段となる
    
    ・事前に集計したViewを作っておけば、アプリ開発者は
    　毎回集計用のクエリを記述することなく、
    　アプリ開発に集中できる

    ・また、ユーザに提供するビューは同じにしたまま、
      内部のクエリを書き直すことができるので
      パフォーマンスの観点からも優れている
    ```
    ```sql
    /*
    sakilaDBを使っている場合デフォルトで作成済み
    映画のカテゴリーと売上の集計用のview
    */

    -- 全てのカラムを採用するときは、()は要らない
    create view sales_by_film_category
    as
    select
      c.name category,
      sum(p.amount) total_sales
    from
      payment p
      inner join rental r
      on p.rental_id = r.rental_id

      inner join inventory i
      on r.inventory_id = f.film_id

      inner join film f
      on i.film_id = f.film_id

      inner join film_category fc
      on f.film_id = fc.film_id

      inner join category c
      on fc.category_id = c.category_id

    group by c.name
    order by total_sales desc
    ;

    select *
    from sales_by_film_category
    where category = "sports"
    ;
    /*
    +----------+-------------+
    | category | total_sales |
    +----------+-------------+
    | Sports   |     5314.21 |
    +----------+-------------+
    1 row in set (0.007 sec)
    */
    ```

    ### 複雑さの隠蔽

    ```text
    ・もっとも一般的な理由の1つ

    ・簡潔なviewを提供することで、ユーザは複雑なクエリ
    　を書かずに済む(データ集計の項と同じ)

    ・またパフォーマンスの観点からも優れている
      （以下の例を参照）
    ```

    ```sql
    /*
    スカラーサブクエリは、通常、
    実際に参照されるときに実行される

    これは、view定義に含まれていても
    ユーザが該当列を使用しない場合、実行されない事を意味する
    ⇒毎回再計算されるわけではないためCPU使用量も下がる
    ⇒再利用性も増す
    */
    create view film_stats
    as
    select
      f.film_id, f.title, f.description, f.rating,
      (
        select 
          c.name
        from
          category c
          inner join film_category fc
          on c.category_id = fc.category_id
          where 
            fc.film_id = f.film_id
      ) category_name,
      
      (
        select 
          count(*)
        from
          film_actor fa
          where 
            fa.film_id = f.film_id
      ) num_actors,

      (
        select
          count(*)
        from
          inventory i
        where
          i.film_id = f.film_id
      ) inventory_cnt,

      (
        select count(*)
        from inventory i
          inner join rental r
          on i.inventory_id = r.inventory_id
        where
          i.film_id = f.film_id
      ) num_rentals
    from film f
    ;

    -- inventory_cnt等のサブクエリは実行されない
    select 
      film_id, title, rating, num_rentals
    from
      film_stats
    ;
    ```

    ### 分割されたデータの統合

    ```text
    ・設計時には、パフォーマンスの観点から
      大きなテーブルを分割することがある
    
    例）
      paymentというテーブルを
      過去6か月分のデータが収納されたpayment_currentと
      それ以前のデータが収納されたpayment_historicに分割

      全てのデータを確認したい場合は
      paymentという結合されたviewから確認する

      ⇒データを収納する際は、payment_currentだけが使われ
        るため大量の過去データを気にする必要がない
        (テーブルの総行数が増えると、indexの更新にかかる
        時間も増大する)
      
      ⇒検索をかける場合は、viewを経由することで
        分割前と同様に扱える
    ```

    ```sql
    -- 実行しないこと
    create view payment
    (
      payment_id,
      customer_id,
      staff_id,
      rental_id,
      amount,
      payment_date,
      last_udpate
    ) as
    select
      payment_id,
      customer_id,
      staff_id,
      rental_id,
      amount,
      payment_date,
      last_update
    from
      payment_historic
    union all
    select
      payment_id,
      customer_id,
      staff_id,
      rental_id,
      amount,
      payment_date,
      last_update
    from
      payment_current
    ;    

    select * from payment
    limit 5
    ;
    ```

## Q115 (コラム)JOINの計算量について知っていますか?

??? success

    ### MySQL/Mariadbの場合

    ```text
    ・MySQLはNested Loop結合アルゴリズム（の発展版）を採用
      している

    Nested Loop
      1 外側のテーブルから行を1つずつ読み取っていく
      2 各行を内側（結合先）のテーブルに渡す
      3 内側の行を1つずつ読み取り、渡された外側の行と比較
      4 これを繰り返す
    
    ⇒最悪の場合計算量は各テーブルの行数の積に比例する
    ⇒つまり、CROSS JOINで出力される行数だけ参照される

    つまり、多重for文のようになる
    for row in table1 {
      for row in table2 {
        for row in table3 {
          ...
        }
      }
    }

    ⇒ただ、結合する際にはindexがあることが一般的である
    例えば2つのテーブルがあるとして、
    t1の結合タイプが、rangeで、
    t2の結合タイプがrefであった場合、ループは以下の様になる

    for row in t1 matching range {
      for row in t2 matching reference key{

      }
    }

    ・つまり、t1はrangeに属する行しかスキャンされず
    　外側のループから渡された行と合致する内側の行は
    　indexをもとに見つけ出される

    Block Nested Loop結合アルゴリズム
      ・外側のループで読み取られた行のバッファリング
        を利用して、内側のループの際は、外側の複数行と
        一度に比較する
      ⇒内側のテーブルを読み取る回数が大幅に減少する
    ```

## Q116 更新可能なViewについて知っていますか?

??? success

    ### VIEWと更新/挿入について

    ```text
    ・ユーザにVIEWを通じてdataの変更を行わせたい場合を考える

    ・データを取得するときはVIEWを介し、変更するときは
      元テーブルを操作させるのは変な話だ
    
    ・MySQL等のDBサーバは特定の制約に従うことを前提に、
      VIEWをつかってデータ変更を許している
    
    MySQL/Mariadbの更新&挿入条件
      ・max, count等の集計関数を使っていない
      ・VIEW定義時にhaving, group byを使っていない
      ・VIEW定義時にunion, union allを使っていない
      ・select, from句にサブクエリが存在しない
      ・where句のサブクエリが相関サブクエリでない
      ・テーブルまたは更新可能なVIEWがfrom句に1つ以上含まれる
      ・テーブルまたはVIEWが複数ある場合、
        FROM句では内部結合のみを使っている
      ・複数テーブルを一度に更新しようとしていない
    
    挿入する場合
      ・元テーブルの列が複数回現れてはならない
      ・VIEW定義にデフォルト値を持たないすべての列が必要
        ※デフォルト値が無くてもNULLが許容されていればヨシ!
      ・VIEWの列はすべて単純な列でなければならない
        （つまり、column + 25や、lower(colimn)や、
          column1 / column2や、リテラルが1つでもあると不可）

    更新する場合
      ・更新したい列が、単純な列としてVIEWに存在すれば可能
    ```

    ### VIEWからUPDATE

    ```sql
    /*
    ・from句のbase-tableは1つのみ(結合していない)
    ・union, union all, distinctは使っていない
    ・サブクエリは含まれない
    ・集計関数を使っていない
    ・group by , havingも使っていない

    ⇒更新可能
    */
    create view customer_vw
    (
      customer_id,
      first_name,
      last_name,
      email
    ) as
    select
      customer_id,
      first_name,
      last_name,
      concat(substr(email, 1, 2), "*****", substr(email, -4)) email
    from 
      customer
    ;

    -- 実際に更新してみる
    select
      customer_id, first_name, last_name
    from customer
    where customer_id = 1
    ;

    /*
    +-------------+------------+-----------+
    | customer_id | first_name | last_name |
    +-------------+------------+-----------+
    |           1 | MARY       | SMITH     |
    +-------------+------------+-----------+
    1 row in set (0.001 sec)
    */

    update customer_vw
    set 
      last_name = "SMITH-ALLEN"
    where
      customer_id = 1
    ;
    -- Query OK, 1 row affected (0.007 sec)
    -- Rows matched: 1  Changed: 1  Warnings: 0

    -- 元テーブルの値が更新されている
    select
      customer_id, first_name, last_name
    from customer
    where customer_id = 1
    ;

    /*
    +-------------+------------+-------------+
    | customer_id | first_name | last_name   |
    +-------------+------------+-------------+
    |           1 | MARY       | SMITH-ALLEN |
    +-------------+------------+-------------+
    1 row in set (0.000 sec)
    */

    -- 戻す
    update customer_vw 
    set
      last_name = "SMITH"
    where
      customer_id = 1
    ;


    -- 更新できない例(emailは単純な列ではない)
    update customer_vw
    set
      email = "hogehoge@gmail.com"
    where
      customer_id = 1
    ;
    -- ERROR 1348 (HY000): Column 'email' is not updatable
    ```

    ### VIEWからINSERT

    ```sql
    create table parent (
      id int primary key auto_increment,
      last_name varchar(30),
      not_view varchar(1) not null
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
    (last_name, not_view)
    values
    ("琴葉", "a"), ("結月", "a"), ("紲星", "a")
    ;

    insert into children
    (parent_id, first_name)
    values
    (1, "茜"),
    (1, "葵"),
    (2, "ゆかり"),
    (3, "あかり")
    ;
    
    create view fullname_vw 
    as 
    select 
      c.parent_id parent_id,
      p.last_name last_name,
      c.first_name first_name
    from
      parent p
      inner join children c
      on p.id = c.parent_id
    ;

    -- VIEWのすべての列が単純な列で
    -- VIEW内にデフォルト値を持たない or NULLを許容する
    -- 行を除くすべての列が含まれている
    insert into fullname_vw
    (parent_id, first_name)
    values
    (2, "弟")
    ;
    -- Query OK, 1 row affected (0.003 sec)

    select * from fullname_vw;
    /*
    +-----------+-----------+------------+
    | parent_id | last_name | first_name |
    +-----------+-----------+------------+
    |         1 | 琴葉      | 茜         |
    |         1 | 琴葉      | 葵         |
    |         2 | 結月      | ゆかり     |
    |         3 | 紲星      | あかり     |
    |         2 | 結月      | 弟         |
    +-----------+-----------+------------+
    5 rows in set (0.001 sec)
    */

    select *
    from children;

    /*
    +----+-----------+------------+
    | id | parent_id | first_name |
    +----+-----------+------------+
    |  1 |         1 | 茜         |
    |  2 |         1 | 葵         |
    |  3 |         2 | ゆかり     |
    |  4 |         3 | あかり     |
    |  5 |         2 | 弟         |
    +----+-----------+------------+
    5 rows in set (0.001 sec)
    */

    -- NULLが許容されないにもかかわらず、
    -- VIEWに該当のカラムがないため、挿入動作ができない
    insert into fullname_vw
    (last_name)
    values
    ("京町")
    ;
    -- Field of view 'sakila.fullname_vw' 
    -- underlying table doesn't have a default value
    ```

    ### 更新可能なVIEWかどうか調べる

    ```sql
    select
      table_name,
      is_updatable
    from
      information_schema.views
    where
      table_name = "customer_vw"
    ;

    -- Yesとでても必ずしも挿入可能とは限らないことに注意
    -- あくまでも特定の列を更新できるかというフラグ
    /*
    +-------------+--------------+
    | table_name  | is_updatable |
    +-------------+--------------+
    | customer_vw | YES          |
    +-------------+--------------+
    1 row in set (0.002 sec)
    */
    ```

    ### 複雑なVIEWに対しての更新とその是非

    ```text
    ・そもそもVIEWを介して、元テーブルを更新/挿入
      させることはいい習慣なのか
    ⇒あまり良い習慣ではないと思われる

    ・一度に複数のベーステーブルをupdate出来ないのが主な理由
    ```

    ```sql
    /*
    例えば以下の場合、
    VIEWを介してアクセスしているユーザからは
    どのカラムがどのベーステーブルのものかはわからない

    しかし、first_nameとlast_nameは実際に、
    別テーブルのものであるため、一度にupdateしようとすると
    エラーが発生する

    ⇒複雑な構成を隠蔽するためにVIEWを使用したのに、
      元テーブルの構成を知らなければUPDATEできない。
      これは、VIEWの利点に反している

    */


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
    
    create view fullname_vw 
    as 
    select 
      c.parent_id parent_id,
      p.last_name last_name,
      c.first_name first_name
    from
      parent p
      inner join children c
      on p.id = c.parent_id
    ;

    -- 2つ以上のbase tableに対して一度にinsertしようと
    -- しているためエラー
    insert into fullname_vw
    (parent_id, last_name, first_name)
    values
    (4, "京町", "せいか")
    ;
    -- Can not modify more than one base table 
    -- through a join view 'sakila.fullname_vw'
    ```

    !!! info
        ### WITH CHECK OPTION

    ```sql
    /*
    これを用いると条件に合わない更新/挿入クエリを排除できる。
    しかし、条件に合わない行も表示できなくなるため
    これを用いて更新/挿入不可で検索可能なVIEWを作ることは
    できない

     ⇒そもそも特定のユーザのUPDATE権限を剥奪すれば？
     ⇒権限を剥奪せず、禁止したい場合、
      concat(column,"")や
      num_column + 0
      のように、式にしてしまう方法もある
    */
    /*
    WITH CHECK OPTIONがある場合、
      ・DBサーバはVIEWのWHERE句をチェックする
      ・ルールに反すると、insert/updateが失敗する
    */
    create table t1 (
      a int
    );

    create view v1 
    as
    select *
    from t1
    where a < 2
    with check option;

    -- ルールに反した場合
    insert into v1
    values (3);
    -- CHECK OPTION failed `sakila`.`v1`

    -- 反しない場合
    insert into v1
    values (1);
    -- Query OK, 1 row affected (0.021 sec)
    ```

    !!! info
        ### instead-of trigger

        ```text
        ・OracleDBを拡張したPL/SQLや、SQL Server等で使える
          Transact-SQL(いずれもSQLを拡張し、通常の手続き型
          プログラミング言語のように扱えるようにしたもの)を
          用いると、VIEWに対するinsert/update/delete文を
          独自にカスタムできる
        
        ・上記を使う場合、VIEWに対するinsert/update/delete
          は許容される
        ```