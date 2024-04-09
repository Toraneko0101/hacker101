# クイズ(3:○○句)

## 方針
??? info
    - Q38からは`sakila`データベースを用いていく

## Q37 クエリがSQLサーバによってどのように解釈されるか知っていますか?

??? success
    ### サーバ接続時
    - SQLサーバは、ユーザ名とパスワード(別のコンピュータ上で実行するならホスト名も)を要求する
    - 正しいことが確認されると、データベース接続が作成される
    - この接続は、接続をリクエストしたアプリ側で解放されるか、サーバが接続を閉じるまで維持される

    !!! info
        - mysql/mariadbユーティリティの場合は、`quit`を打ち込むか、サーバをシャットダウンするときまで

    ### 接続にはIDが割り振られる

    ```bash
    #Welcome to the MariaDB monitor.  Commands end with ; or \g.
    #Your MariaDB connection id is 53
    ```

    ### IDの用途
    - 不正なクエリが延々と実行されている場合、この接続IDを用いてKILLする

    ```sql
    -- Q35参照(本来は接続IDではなくクエリをKILLする)
    show processlist;
    KILL 53;
    ```

    ### クエリを入力すると

    ```
    1. クエリはサーバに送信される
    2. クエリは、ユーザのパーミッションをチェックする(実行許可、データに対するアクセス許可)
    3. テストに合格すると、文はクエリオプティマイザ(query optimizer)に渡される
    4. クエリオプティマイザは、テーブルの結合順序や利用可能なindexを調べ、サーバがクエリを実行するために使う実行プランを選択する
    5. 実行プランに従って、クエリを実行し、呼び出し元に結果セットを返す
    6. メッセージが表示される
    ```

    ### 実行プランについて
    - `EXPLAIN`を用いると、クエリがどのように実行されるのか確認可能
    - 実行プランのさらなる分析、クエリヒントを使ったオプティマイザの制御、サーバのスタートアップパラメータについて`MySQL`で確認したい場合は、`High Performance MySQL 4th Edition`を読むこと。(英語)

    ```sql
    explain select * from favorite_foods;
    -- type:スキャン方法(ALLやindexの場合、検索対象が1件でもすべての行から探していることになるので注意が必要)
    --  index:インデックス全体をスキャン。ALL:インデックスが全く利用されていない
    -- possible_keys: optimizerがテーブルのアクセスに利用可能だと判断したキー
    -- key: 実際にoptimizerによって選択されたキー
    -- Extra:optimizerがどのようなことを考えたのか
    /*

    +------+-------------+----------------+-------+---------------+---------+---------+------+------+-------------+
    | id   | select_type | table          | type  | possible_keys | key     | key_len | ref  | rows | Extra       |
    +------+-------------+----------------+-------+---------------+---------+---------+------+------+-------------+
    |    1 | SIMPLE      | favorite_foods | index | NULL          | PRIMARY | 84      | NULL | 3    | Using index |
    +------+-------------+----------------+-------+---------------+---------+---------+------+------+-------------+
    1 row in set (0.000 sec)
    */

    explain select * from favorite_foods where food = "cookies";
    -- ExtraにUsing Whereが表示されている
    /*
    +------+-------------+----------------+-------+---------------+---------+---------+------+------+--------------------------+
    | id   | select_type | table          | type  | possible_keys | key     | key_len | ref  | rows | Extra                    |
    +------+-------------+----------------+-------+---------------+---------+---------+------+------+--------------------------+
    |    1 | SIMPLE      | favorite_foods | index | NULL          | PRIMARY | 84      | NULL | 3    | Using where; Using index |
    +------+-------------+----------------+-------+---------------+---------+---------+------+------+--------------------------+
    1 row in set (0.001 sec)
    */
    ```

## Q38 select文を構成する句(節)について知っていますか?

??? success
    ### 一覧
    - 以下はすべてANSI規格に含まれている

    | 句の名前 | 目的                                 | 評価順 |
    | -------- | ------------------------------------ | ------ |
    | select   | クエリの結果セットに含める列を決める | 5      |
    | from     | データを取得するテーブルと結合方法   | 1      |
    | where    | 不要なデータを取り除く               | 2      |
    | group by | 共通な列の値に基づき行をグループ化   | 3      |
    | having   | 不要なグループを取り除く             | 4      |
    | order by | 結果セットの行を並び替え             | 6      |

    ```text
    ・LIMIT句は標準の規格に含まれていない（最後に評価）
    ・JOIN句を考える場合は、FROM句の直後で評価される
    ```


## Q39 select句で式や組み込み関数、関数呼び出しができることを知っていますか?(あと重複を取る方法も)

??? success
    ### select句は最後に評価する
    - 他の句で操作を行い、最終的な候補の中から、結果セットに表示する列を決めるから

    ### `*`
    全ての列を結果セットに含める
    - 言葉にすると、fromに指定したテーブルの行と列をすべて表示せよ

    ### `*`を使わない場合
    - 指定した列だけが表示される

    ```sql
    select language_id, name, last_update from 
    language;
    /*
    +-------------+----------+---------------------+
    | language_id | name     | last_update         |
    +-------------+----------+---------------------+
    |           1 | English  | 2006-02-15 05:02:19 |
    |           2 | Italian  | 2006-02-15 05:02:19 |
    |           3 | Japanese | 2006-02-15 05:02:19 |
    |           4 | Mandarin | 2006-02-15 05:02:19 |
    |           5 | French   | 2006-02-15 05:02:19 |
    |           6 | German   | 2006-02-15 05:02:19 |
    +-------------+----------+---------------------+
    6 rows in set (0.003 sec)    
    */
    ```

    ### SELECTで組み込み関数を使う
    - `upper()`は大文字にする
    - `lower()`は小文字にする

    ```sql
    select language_id, upper(name), lower(name) 
    from language;
    /*
    +-------------+-------------+-------------+
    | language_id | upper(name) | lower(name) |
    +-------------+-------------+-------------+
    |           1 | ENGLISH     | english     |
    |           2 | ITALIAN     | italian     |
    |           3 | JAPANESE    | japanese    |
    |           4 | MANDARIN    | mandarin    |
    |           5 | FRENCH      | french      |
    |           6 | GERMAN      | german      |
    +-------------+-------------+-------------+
    6 rows in set (0.001 sec)
    */
    ```

    ### SELECT句で式を使う

    ```sql
    select language_id, language_id * 3.1415927 
    from language;
    /*
    +-------------+-------------------------+
    | language_id | language_id * 3.1415927 |
    +-------------+-------------------------+
    |           1 |               3.1415927 |
    |           2 |               6.2831854 |
    |           3 |               9.4247781 |
    |           4 |              12.5663708 |
    |           5 |              15.7079635 |
    |           6 |              18.8495562 |
    +-------------+-------------------------+
    6 rows in set (0.001 sec)
    */
    ```

    ### select句で数字や文字列などのリテラルを使う

    ```sql
    select language_id, "COMMON", 12 
    from language;
    /*
    +-------------+--------+----+
    | language_id | COMMON | 12 |
    +-------------+--------+----+
    |           1 | COMMON | 12 |
    |           2 | COMMON | 12 |
    |           3 | COMMON | 12 |
    |           4 | COMMON | 12 |
    |           5 | COMMON | 12 |
    |           6 | COMMON | 12 |
    +-------------+--------+----+
    6 rows in set (0.000 sec)
    */
    ```

    ### 別名を付ける
    - `<your_column_name> AS <new_name>`
    - `組み込み関数 or 式 or リテラル AS <new_name>`も可能
    - ASはなくてもいい(可読性 or 冗長性)

    ```sql
    select 
    language_id,
    "COMMON" AS language_usage,
    language_id * 3.14 AS lang_pi,
    upper(name) language_upper_name
    from language; 

    /*
    +-------------+----------------+---------+---------------------+
    | language_id | language_usage | lang_pi | language_upper_name |
    +-------------+----------------+---------+---------------------+
    |           1 | COMMON         |    3.14 | ENGLISH             |
    |           2 | COMMON         |    6.28 | ITALIAN             |
    |           3 | COMMON         |    9.42 | JAPANESE            |
    |           4 | COMMON         |   12.56 | MANDARIN            |
    |           5 | COMMON         |   15.70 | FRENCH              |
    |           6 | COMMON         |   18.84 | GERMAN              |
    +-------------+----------------+---------+---------------------+
    6 rows in set (0.000 sec)
    */
    ```

    ### fromが要らないなら取る
    - `version()`:versionを表示する組み込み関数
    - `user()`:userを表示する組み込み関数
    - `database()`:接続中のDBを表示する組み込み関数

    ```sql
    select version(), user(), database();
    /*
    +----------------------------------+-----------------------+------------+
    | version()                        | user()                | database() |
    +----------------------------------+-----------------------+------------+
    | 10.6.16-MariaDB-0ubuntu0.22.04.1 | <your_name>@localhost | sakila     |
    +----------------------------------+-----------------------+------------+
    1 row in set (0.000 sec)
    */
    ```

    ### 重複を取り除く
    - `SELECT`キーワードの直後に`DISTINCT`

    ```sql
    select actor_id 
    from film_actor 
    order by actor_id 
    limit 10;
    /*
    +----------+
    | actor_id |
    +----------+
    |        1 |
    |        1 |
    |        1 |
    |        1 |
    |        1 |
    |        1 |
    |        1 |
    |        1 |
    |        1 |
    |        1 |
    +----------+
    10 rows in set (0.000 sec)
    */

    select DISTINCT actor_id 
    from film_actor 
    order by actor_id 
    limit 10;
    /*
    +----------+
    | actor_id |
    +----------+
    |        1 |
    |        2 |
    |        3 |
    |        4 |
    |        5 |
    |        6 |
    |        7 |
    |        8 |
    |        9 |
    |       10 |
    +----------+
    10 rows in set (0.002 sec)
    */
    ```

    !!! warning
        - `sec`に着目すると、後者の方が時間を要していることが分かる
        - 重複していない結果セットを生成するには、データの並び替えのための時間がかかる
        - そのため安易に`distinct`を使わず、扱っているデータを理解することも大事

    !!! info
        - 実は普段のクエリにおいて暗黙的に`all`というキーワードが指定されている
        - これは、重複するデータを削除しないことを意味する

        ```sql
        select all actor_id
        from film_actor
        order by actor_id
        limit 5;

        /*
        +----------+
        | actor_id |
        +----------+
        |        1 |
        |        1 |
        |        1 |
        |        1 |
        |        1 |
        +----------+
        5 rows in set (0.001 sec)
        */
        ```

## Q40 from句の役割や、一時、派生、仮想テーブルについて知っていますか?

??? success
    ### from句の定義
    - from句はクエリに使うテーブルを定義する
    - テーブル同士をリンクする方法も定義する

    ### そもそもテーブルは4種類ある
    - 永続テーブル: `create table`で作成されたテーブル
    - 派生テーブル: サブクエリが返して、メモリ内で保持される行セット。実際のテーブルのように使用される
    - 一時テーブル: `create temporary table`で作成した一時的なテーブル。セッション終了時に自動削除
    - 仮想テーブル: `create view`文を使って作成したテーブル

    ### サブクエリ
    - 別のクエリの中に含まれるクエリのこと。丸括弧で囲まれる

    ```
    SELECT句のサブクエリ
      サブクエリの結果を表示する
    FROM句のサブクエリ
      派生テーブルを作成する
    WHERE句のサブクエリ
      条件式の一部として他のクエリの結果を使用可能
    ```

    ### SELECT句のサブクエリの例
    - `price`の平均値をサブクエリ内で求めている

    ```sql
    select 
    FID , price, (select AVG(price) from film_list) avg 
    from film_list 
    order by price desc 
    limit 10;

    /*
    +-----+-------+----------+
    | FID | price | avg      |
    +-----+-------+----------+
    |   2 |  4.99 | 2.980000 |
    |   7 |  4.99 | 2.980000 |
    |   8 |  4.99 | 2.980000 |
    |  10 |  4.99 | 2.980000 |
    |  13 |  4.99 | 2.980000 |
    |  20 |  4.99 | 2.980000 |
    |  21 |  4.99 | 2.980000 |
    |  28 |  4.99 | 2.980000 |
    10 rows in set (0.044 sec)
    */
    ```

    ### WHERE句のサブクエリの例
    - `price`が平均以上のものの中から、小さい順に表示

    ```sql
    select FID, price
    from film_list
    where price > (SELECT AVG(price) from film_list)
    order by price asc
    limit 10;

    /*
    +-----+-------+
    | FID | price |
    +-----+-------+
    |   3 |  2.99 |
    |   4 |  2.99 |
    |   5 |  2.99 |
    |   6 |  2.99 |
    |   9 |  2.99 |
    |  15 |  2.99 |
    |  16 |  2.99 |
    |  22 |  2.99 |
    |  24 |  2.99 |
    |  25 |  2.99 |
    +-----+-------+
    10 rows in set (0.042 sec)
    */
    ```

    ### 派生テーブル(FROM句のサブクエリの例)
    - クエリの他の句から参照できるテーブル
    - from句に指定した他のテーブルと合わせて処理可能
    - 下の例でいうと、`cust`のデータはクエリ完了までメモリ内で保持され、クエリ完了時に削除される

    ```sql
    SELECT 
    concat(cust.last_name, ",", cust.first_name) full_name
    FROM
    (
      SELECT first_name, last_name , email
      FROM customer
      WHERE first_name = "JESSIE"
    ) AS cust; -- ASは省略可能

    /*
    +--------------+
    | full_name    |
    +--------------+
    | BANKS,JESSIE |
    | MILAM,JESSIE |
    +--------------+
    2 rows in set (0.025 sec)
    */
    ```

    ### 一時テーブル
    - セッション終了時に消えるテーブル

    ```sql
    -- 一時テーブルを作成
    create temporary table actors_j
    (
      actor_id smallint(5),
      first_name varchar(45),
      last_name varchar(45)
    );

    -- 結果クエリを一時テーブルに挿入する
    insert into actors_j
      select actor_id, first_name, last_name
      from actor
      where last_name like "J%";
    
    select * from actors_j;

    /*
    +----------+------------+-----------+
    | actor_id | first_name | last_name |
    +----------+------------+-----------+
    |      119 | WARREN     | JACKMAN   |
    |      131 | JANE       | JACKMAN   |
    |        8 | MATTHEW    | JOHANSSON |
    |       64 | RAY        | JOHANSSON |
    |      146 | ALBERT     | JOHANSSON |
    |       82 | WOODY      | JOLIE     |
    |       43 | KIRK       | JOVOVICH  |
    +----------+------------+-----------+
    7 rows in set (0.000 sec)
    */
    ```

    !!! info
        - `Oracle database`の場合は例外で、セッション終了時に一時テーブルの定義を保存する

    ### VIEW(仮想テーブル)
    - ビューは、結果クエリを定義するクエリで構成される
    - 具体的にはエイリアスとしてクエリを書く
    - ビューに対してクエリを実行すると、クエリとビューの定義をマージした最終的に実行されるクエリが作成される。

    ```sql
    create view cust_vw AS
    select customer_id, first_name, last_name, active
    from customer;

    -- viewに対してクエリ実行(今回は段階的に絞り込んでいるようなもの)
    select first_name, last_name
    from cust_vw
    where active = 0;

    /*
    +------------+-----------+
    | first_name | last_name |
    +------------+-----------+
    | SANDRA     | MARTIN    |
    | JUDITH     | COX       |
    | SHEILA     | WELLS     |
    | ERICA      | MATTHEWS  |
    | HEIDI      | LARSON    |
    | PENNY      | NEAL      |
    | KENNETH    | GOODEN    |
    | HARRY      | ARCE      |
    | NATHAN     | RUNYON    |
    | THEODORE   | CULP      |
    | MAURICE    | CRAWLEY   |
    | BEN        | EASTER    |
    | CHRISTIAN  | JUNG      |
    | JIMMIE     | EGGLESTON |
    | TERRANCE   | ROUSH     |
    +------------+-----------+
    15 rows in set (0.002 sec)
    */
    ```

    !!! info
        ### VIEWを作成する理由
        - ユーザがアクセスできるデータを制御したい場合
        - 条件を含むクエリをビューとして定義し、メンテナンス性を向上させる

    ### テーブルをリンクする場合
    - `ON`の後ろに、結合条件を書き、複数のテーブルを結合する。
    - 結合の方法については後記

    ```sql
    select 
    customer.first_name, 
    customer.last_name,
    time(rental.rental_date) rental_time
    from customer
      INNER JOIN rental
      ON customer.customer_id = rental.customer_id
    where date(rental.rental_date) = "2005-06-14"

    /*
    +------------+-----------+-------------+
    | first_name | last_name | rental_time |
    +------------+-----------+-------------+
    | CATHERINE  | CAMPBELL  | 23:17:03    |
    | JOYCE      | EDWARDS   | 23:16:26    |
    | AMBER      | DIXON     | 23:42:56    |
    | JEANETTE   | GREENE    | 23:54:46    |
    | MINNIE     | ROMERO    | 23:00:34    |
    | GWENDOLYN  | MAY       | 23:16:27    |
    | SONIA      | GREGORY   | 23:50:11    |
    | MIRIAM     | MCKINNEY  | 23:07:08    |
    | CHARLES    | KOWALSKI  | 23:54:34    |
    | DANIEL     | CABRAL    | 23:09:38    |
    | MATTHEW    | MAHAN     | 23:25:58    |
    | JEFFERY    | PINSON    | 22:53:33    |
    | HERMAN     | DEVORE    | 23:35:09    |
    | ELMER      | NOE       | 22:55:13    |
    | TERRANCE   | ROUSH     | 23:12:46    |
    | TERRENCE   | GUNDERSON | 23:47:35    |
    +------------+-----------+-------------+
    16 rows in set (0.031 sec)
    */
    ```

    ### テーブルエイリアスについて
    - テーブルにも`AS`が使えるので、これを用いてコンパクトにするのが普通
    - もちろん、半角スペースの後ろにエイリアスを書いてもよい

    ```sql
    select 
    c.first_name, 
    c.last_name,
    time(r.rental_date) rental_time --日付の情報は要らないので
    from customer AS c
      INNER JOIN rental r
      ON c.customer_id = r.customer_id
    where date(r.rental_date) = "2005-06-14";
    ```

## Q41 where句を用いて、結果セットから不要な行を取り除くことができますか?

??? success
    ### where
    - 条件に当てはまる行のみを抽出する

    ### フィルタ条件
    - `and`,`or`,`not`演算子を用いて、フィルタ可能
    - 複数種類の演算子を使いたい場合、`AND`は`OR`より優先されるが、分かりにくいので丸括弧で囲むようにする

    ### 使用例

    ```sql
    select title, rating, rental_duration
    from film
    where 
    (
      (rating = 'G' AND rental_duration >= 7)
      OR
      (rating = 'PG-13' AND rental_duration < 4)
    ) 
    AND
    (title LIKE 'C%');

    -- ratingがGで貸出期間が7日超か
    -- ratingがPG-13で貸出期間が4日未満の映画のうち
    -- 題名がCで始まるものを抽出している
    /*
    +---------------------+--------+-----------------+
    | title               | rating | rental_duration |
    +---------------------+--------+-----------------+
    | CASPER DRAGONFLY    | PG-13  |               3 |
    | CATCH AMISTAD       | G      |               7 |
    | CITIZEN SHREK       | G      |               7 |
    | COLDBLOODED DARLING | G      |               7 |
    | CONFUSED CANDLES    | PG-13  |               3 |
    | CONTROL ANTHEM      | G      |               7 |
    | CORE SUIT           | PG-13  |               3 |
    | CRUELTY UNFORGIVEN  | G      |               7 |
    +---------------------+--------+-----------------+
    8 rows in set (0.008 sec)
    */
    ```

## Q42 group by句と、having句を使って、データをグループ化することができますか?

??? success
    ### group by
    - 指定した列の値に基づき、同値のデータをグループ化できる
    - `group by`を行った場合、`SELECT`句で指定できるのは、基本的に集計関数、リテラル、`group by`で指定したカラムとなる
    - （グループ化に使用していないカラムを指定すると、どの値を表示していいのかわからないので）

    !!! warning
        - `SELECT`句が最後に評価されることを思い出す
        - そのためそれより先に評価される`GROUP BY`でSELECTで指定するカラムのエイリアスを使うことはできない
        - `FROM`句よりは後に用いているので、テーブルのエイリアスは使える

    ### having
    - グループ化したデータをフィルタリングする

    ### なぜwhereではだめなのか
    - `group by`句は`where`句の後で評価されるので
    - `where`はレコードレベルでのフィルタリング
    - `having`はグループレベルでのフィルタリング
    
    ### 使用例

    ```sql
    SELECT c.first_name, c.last_name, count(*)
    FROM customer c
      INNER JOIN rental r
      ON c.customer_id = r.customer_id
    GROUP BY c.first_name, c.last_name
    HAVING count(*) >= 40;

    /*
    +------------+-----------+----------+
    | first_name | last_name | count(*) |
    +------------+-----------+----------+
    | CLARA      | SHAW      |       42 |
    | ELEANOR    | HUNT      |       46 |
    | KARL       | SEAL      |       45 |
    | MARCIA     | DEAN      |       42 |
    | SUE        | PETERS    |       40 |
    | TAMMY      | SANDERS   |       41 |
    | WESLEY     | BULL      |       40 |
    +------------+-----------+----------+
    7 rows in set (0.014 sec)
    */

    ```

## Q43 order by句を使って、結果セットを並び替えることができますか?

??? success
    ### order by col1, col2...
    - 前に書いたカラムほど、優先される
    - 同値の場合、二番目以降のカラムが用いられる

    ### desc, asc
    - `desc`:降順
    - `asc`:昇順
    - 暗黙的に`asc`なので指定する必要はないが、明示した方がコードの意思を明確にできる

    ### limit
    - データを並べ替えた後、最初のX行以外をすべて削除する

    ### 使用例

    !!! warning
        - `order by`は`select`より後に評価されるので、エイリアスを用いることができる

    ```sql
    -- firstnameをアルファベット順に
    -- 同じ場合、lastnameで比較するが、これは逆順に

    select 
      c.first_name as fn, 
      c.last_name as ln,
      time(r.rental_date) rental_time
    from customer c
      inner join rental r
      on c.customer_id = r.customer_id
    where date(r.rental_date) = "2005-06-14"
    order by 
      fn asc,
      ln desc,
      time(r.rental_date) asc
    limit 10;

    /*
    +-----------+----------+-------------+
    | fn        | ln       | rental_time |
    +-----------+----------+-------------+
    | AMBER     | DIXON    | 23:42:56    |
    | CATHERINE | CAMPBELL | 23:17:03    |
    | CHARLES   | KOWALSKI | 23:54:34    |
    | DANIEL    | CABRAL   | 23:09:38    |
    | ELMER     | NOE      | 22:55:13    |
    | JEFFERY   | PINSON   | 22:53:33    |
    | JOYCE     | EDWARDS  | 23:16:26    |
    +-----------+----------+-------------+
    10 rows in set (0.037 sec)
    */
    ```

    ### 数値のプレースホルダーも使える
    - 位置でソートすることも可能

    !!! warning
        - あまり使わない方がいい
        - 誤った並び替えが生じたとき、メンテナンスが困難になるため

    ```sql
    select
      c.first_name, 
      c.last_name,
      time(r.rental_date) rental_time
    from customer c
      inner join rental r
      on c.customer_id = r.customer_id
    where date(r.rental_date) = "2005-06-14"
    order by 3 asc;

    /*
    +------------+-----------+-------------+
    | first_name | last_name | rental_time |
    +------------+-----------+-------------+
    | JEFFERY    | PINSON    | 22:53:33    |
    | ELMER      | NOE       | 22:55:13    |
    | MINNIE     | ROMERO    | 23:00:34    |
    | MIRIAM     | MCKINNEY  | 23:07:08    |
    | DANIEL     | CABRAL    | 23:09:38    |
    | TERRANCE   | ROUSH     | 23:12:46    |
    | JOYCE      | EDWARDS   | 23:16:26    |
    | GWENDOLYN  | MAY       | 23:16:27    |
    | CATHERINE  | CAMPBELL  | 23:17:03    |
    | MATTHEW    | MAHAN     | 23:25:58    |
    | HERMAN     | DEVORE    | 23:35:09    |
    | AMBER      | DIXON     | 23:42:56    |
    | TERRENCE   | GUNDERSON | 23:47:35    |
    | SONIA      | GREGORY   | 23:50:11    |
    | CHARLES    | KOWALSKI  | 23:54:34    |
    | JEANETTE   | GREENE    | 23:54:46    |
    +------------+-----------+-------------+
    16 rows in set (0.017 sec)
    */
    ```

## Q44 where句に指定できるフィルターについて知っていますか?(例えばlike, in, betweenや、`<>`等)

??? success
    ### 等号条件(`=`)
    - 等しければ`true`

    ### 不等号条件(`<>` or `!=`)

    ```sql
    select c.email
    from customer c
      inner join rental r
      on c.customer_id = r.customer_id
    where date(r.rental_date) <> "2005-06-14"; 
    -- where date(r.rental_date) != "2005-06-14"; でも同じ

    /*
    +------------------------------------------+
    | email                                    |
    +------------------------------------------+
    | MARY.SMITH@sakilacustomer.org            |
    | MARY.SMITH@sakilacustomer.org            |
    | MARY.SMITH@sakilacustomer.org            |
    | ...                                      |
    16028 rows in set (0.015 sec)
    */
    ```
  
    ### データを変更するときに、よく使われる

    ```sql
    -- レンタル日が2005 or 2006年以外の行を削除する
    -- ※1件もヒットしないので、実際には削除されないが

    delete from rental
    where 
    year(rental_date) <> 2005 
    AND
    year(rental_date) <> 2006;
    ```

    ### 範囲条件：片側(`<`, `<=`, `>`, `>=`)

    ```sql
    select
    customer_id, rental_date
    from rental
    where rental_date <= "2005-05-25";

    /*
    +-------------+---------------------+
    | customer_id | rental_date         |
    +-------------+---------------------+
    |         130 | 2005-05-24 22:53:30 |
    |         459 | 2005-05-24 22:54:33 |
    |         408 | 2005-05-24 23:03:39 |
    |         333 | 2005-05-24 23:04:41 |
    |         222 | 2005-05-24 23:05:21 |
    |         549 | 2005-05-24 23:08:07 |
    |         269 | 2005-05-24 23:11:53 |
    |         239 | 2005-05-24 23:31:46 |
    +-------------+---------------------+
    8 rows in set (0.001 sec)
    */
    ```

    !!! warning
        ### 2005-05-25のデータがヒットしない理由は?
        - 時刻を指定しないと、デフォルトで午前0時になるため
        - `2005-05-25 23:59:59`とすると件数が変わることが確認できる


    ### 範囲条件:両側(`カラム名 between 下限 and 上限`)
    - 片側を2つ使ってもいいけれど......

    !!! warning
        ### 落とし穴
        - 上限 and 下限とすると1つもヒットしない
        - これは、データベースサーバが内部では`<=`と`>=`演算子を用いて、1つの条件から2つの条件を生成するため

        ### 落とし穴2
        - `<=`と`>=`が生成されることに注意する
        - `Between '2005-06-14' and '2005-06-15'`の場合、範囲は、6/14の0:00から、6/15の0:00までとなる

        ### 落とし穴3
        - 文字列の場合にも注意。
        - 文字セット内の文字の順序（照合順序）に依存する
        - 通常の場合、`between "AB" and "AC"`なら、`ACA`は含まれない

    ```sql
    select customer_id, rental_date
    from rental
    where rental_date 
    between '2005-06-14 23:50:00' and '2005-06-15 01:00:00';

    /*
    +-------------+---------------------+
    | customer_id | rental_date         |
    +-------------+---------------------+
    |         284 | 2005-06-14 23:50:11 |
    |         306 | 2005-06-14 23:54:34 |
    |         191 | 2005-06-14 23:54:46 |
    |          95 | 2005-06-15 00:12:51 |
    |         197 | 2005-06-15 00:15:15 |
    |         512 | 2005-06-15 00:28:37 |
    |         210 | 2005-06-15 00:33:04 |
    |         279 | 2005-06-15 00:36:40 |
    |         119 | 2005-06-15 00:36:50 |
    |         432 | 2005-06-15 00:39:01 |
    |         546 | 2005-06-15 00:42:17 |
    |         196 | 2005-06-15 00:45:21 |
    |         329 | 2005-06-15 00:49:19 |
    |         295 | 2005-06-15 00:49:36 |
    |           1 | 2005-06-15 00:54:12 |
    |         368 | 2005-06-15 00:56:45 |
    |         334 | 2005-06-15 00:58:50 |
    +-------------+---------------------+
    17 rows in set (0.001 sec)
    */
    ```

    !!!info
        ### ソートされている理由
        - データベースが最適なアクセス経路を探す過程で、ソートされることがある
        - 必ずソートしてほしいなら、やはり`order by`を書くべき

    ### メンバーシップ条件(IN)
    - 値の有限集合を使いたい場合、`in`演算子が便利

    ```sql
    -- ratingの値が、PG or Gの値を列挙する
    select title, rating
    from film
    where rating IN ("G", "PG");

    /*
    +---------------------------+--------+
    | title                     | rating |
    +---------------------------+--------+
    | ACADEMY DINOSAUR          | PG     |
    | ACE GOLDFINGER            | G      |
    | AFFAIR PREJUDICE          | G      |
    | AFRICAN EGG               | G      |    
    ......................................
    | BRIDE INTRIGUE            | G      |
    | BRINGING HYSTERICAL       | PG     |
    372 rows in set (0.002 sec)
    */
    ```

    ### Where句のサブクエリを用いることもできる
    - 段階的に判断するときに便利

    ```
    サブクエリに対してIN演算子を適用する場合、
    サブクエリは1つのカラムを返す必要がある

    2つ以上を返した場合
    ERROR 1241 (21000): Operand should contain 1 column(s) 
    ```

    ```sql
    -- タイトルにHOLYという文字列が含まれている
    -- 映画のレーティングを取り出す

    /*
    select rating from film where title like "%HOLY%";
    +--------+
    | rating |
    +--------+
    | PG     |
    | R      |
    +--------+
    */
    -- このレーティングに属する映画を列挙する

    select title, rating
    from film
    where rating in (
      select rating from film where title like "%HOLY%"
    );

    /*
    +-------------------------+--------+
    | title                   | rating |
    +-------------------------+--------+
    | ACADEMY DINOSAUR        | PG     |
    | AGENT TRUMAN            | PG     |
    | AIRPORT POLLOCK         | R      |
    | ALASKA PHANTOM          | PG     |
    | ALI FOREVER             | PG     |
    | ALONE TRIP              | R      |
    ...................................
    389 rows in set (0.002 sec)
    */
    ```

    ### not in演算子について
    - 除外したいときに使う

    ```sql
    -- PG-13, R, NC-17, PGに分類される映画を除外
    select title, rating
    from film
    where rating not in ("PG-13", "R", "NC-17", "PG")

    /*
    +---------------------------+--------+
    | title                     | rating |
    +---------------------------+--------+
    | ACE GOLDFINGER            | G      |
    | AFFAIR PREJUDICE          | G      |
    .....................................
    | CONTROL ANTHEM            | G      |
    178 rows in set (0.003 sec)
    */
    ```

    ### カラムの特定の文字とマッチングさせる

    ```text
    left()という組み込み関数が使える
    ```

    ```sql
    -- 名前の1文字目がQであるカラムを取得
    select last_name, first_name
    from customer
    where left(last_name, 1) = "Q";

    /*
    +-------------+------------+
    | last_name   | first_name |
    +-------------+------------+
    | QUALLS      | STEPHEN    |
    | QUINTANILLA | ROGER      |
    | QUIGLEY     | TROY       |
    +-------------+------------+
    3 rows in set (0.003 sec)
    */
    ```

    ### ワイルドカードを使う(_ or %)

    ```text
    ・特定の部分文字列で始まる/終わる文字列
    ・途中に部分文字列が含まれている文字列
    ・特定のフォーマットを持つ文字列

    ⇒これらは、
    _ : 1文字を意味する
    % : 任意の個数の文字(0個でもいい)
    と
    like演算子を用いて表すことができる
    ```

    ```sql
    -- 2文字目がA, 4文字目がTで最後の文字がSであるlast_name
    select last_name, first_name
    from customer
    where last_name like "_A_T%S";

    /*
    +-----------+------------+
    | last_name | first_name |
    +-----------+------------+
    | MATTHEWS  | ERICA      |
    | WALTERS   | CASSANDRA  |
    | WATTS     | SHELLY     |
    +-----------+------------+
    3 rows in set (0.001 sec)
    */
    ```

    ```sql
    -- 複雑な場合は、or等を用いる

    -- last_nameがQまたはYで始まるカラム
    select last_name, first_name
    from customer
    where 
    last_name like "Q%"
    or
    last_name like "Y%";

    /*
    +-------------+------------+
    | last_name   | first_name |
    +-------------+------------+
    | QUALLS      | STEPHEN    |
    | QUIGLEY     | TROY       |
    | QUINTANILLA | ROGER      |
    | YANEZ       | LUIS       |
    | YEE         | MARVIN     |
    | YOUNG       | CYNTHIA    |
    +-------------+------------+
    6 rows in set (0.002 sec)
    */
    ```

    ### 正規表現を使う

    ```text
    like演算子の代わりに、regexp演算子を使う

    ※
    Oracle Databaseの場合は、regexp_like演算子を使う
    SQL Serverでは、正規表現もlike演算子で賄うことが可能
    ```

    ```sql
    -- last_nameがQで始まり、AまたはYで終わるもの

    select last_name, first_name
    from customer
    where 
    last_name regexp "^Q.*[AY]$"

    /*
    +-------------+------------+
    | last_name   | first_name |
    +-------------+------------+
    | QUINTANILLA | ROGER      |
    | QUIGLEY     | TROY       |
    +-------------+------------+
    2 rows in set (0.011 sec)
    */
    ```

    ### 大文字小文字の区別

    ```text
    ・mysqlでデフォルトの文字セットを用いている場合
    　where句で大文字小文字は区別されない

    ・これは、文字列が「照合順序」に基づいて比較されるため
    ・照合順序は、文字コードによる比較とは異なる
    　たとえば、B > a

    ・文字コードで比較したい場合は、binaryレベルの比較を行う
    ```

    ```sql
    -- binaryレベルの比較
    select *
    from customer 
    where binary first_name = "mary";
    -- Empty set (0.002 sec)

    select *
    from customer 
    where binary first_name = "MARY";
    -- 1 row in set (0.001 sec)
    ```

## Q45 nullについて理解していますか?

??? success
    ### nullのマッチ

    ```text
    NULLは、=ではマッチできないことに注意
      そのため、not betweenや<>でもマッチされない

    列に値が代入されているか調べたい場合
      is not null演算子を使う
    列がnullであるか調べたい場合
      is null演算子を使う
    ```

    ```sql
    -- たとえば、以下では返却されていない場合、return_date
    -- がnullになっている。
    --　そのためnullである物を列挙して未返却の映画を調べる
    select rental_id, customer_id, return_date
    from rental
    where return_date is null;

    /*
    +-----------+-------------+-------------+
    | rental_id | customer_id | return_date |
    +-----------+-------------+-------------+
    |     11496 |         155 | NULL        |
    |     11541 |         335 | NULL        |
    ........................................
    |     14516 |         457 | NULL        |
    183 rows in set (0.010 sec)
    */
    ```

    ### 注意点

    ```
    not between "2005-05-01" and "2005-09-01"
    というフィルタでは、NULLが設定されているカラムを
    取得することはできない

    nullを取得したい場合は、
    is nullと明示的に指定する必要がある
    ```

    ```sql
    select rental_id , customer_id, return_date
    from rental
    where return_date is null
    or return_date not between "2005-05-01" and "2005-09-01";
    /*
    +-----------+-------------+---------------------+
    | rental_id | customer_id | return_date         |
    +-----------+-------------+---------------------+
    |     11496 |         155 | NULL                |
    |     11541 |         335 | NULL                |
    |     11563 |          83 | NULL                |
    .................................................
    |     16033 |         226 | 2005-09-01 02:36:15 |
    |     16037 |          45 | 2005-09-01 02:48:04 |
    |     16040 |         195 | 2005-09-02 02:19:33 |
    +-----------+-------------+---------------------+
    245 rows in set (0.013 sec)
    */
    ```
