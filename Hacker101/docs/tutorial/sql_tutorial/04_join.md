# クイズ(4:結合)

## Q46 結合の概要と、一般的でない結合(デカルト積)について知っていますか?

??? success

    ### 結合について

    ```text
    ・殆どのクエリでは、2つ以上のテーブルが必要になる
    ・そのため外部キーでテーブルを結合して、両方のテーブル列
      をクエリの結果セットに追加できるようにする
    
    ※外部キー制約
    ・外部キー制約とは、参照する側のテーブル（子テーブル）が
      親テーブルを参照するとき、親テーブルにない値を
      挿入しないよう、子テーブルに設定するものを指す
    ・これはテーブルを結合する際、必要とは限らない
    ```

    ### デカルト積について

    ```sql
    -- 単にjoinキーワードを設定する。onも使わない
    -- ※明示的にデカルト積を行いたい場合はcross joinをする事

    select c.first_name, c.last_name, a.address
    from customer c join address a;

    --361197 rows in set (0.342 sec)
    ```

    ```text
    ここで、
    customerテーブルは599行であり
    addressテーブルが603行であることが確認できる。
    599 * 603 = 361197

    ・つまるところ、すべての組み合わせを適用している(直積)

    ・デカルト積という言葉が、データベースの文脈で使われる時
    　全ての行の組み合わせであることを意味している
    ```

## Q47 内部結合について知っていますか?

??? success
    ### 内部結合

    ```text
    ・顧客ごとに1行のデータが返されるように結合したい
    ・そのため、2つのテーブルのリンクとなる列を
    　onキーワードに追加する

    [内部結合]
      ・一方のリンク列に存在する値が、もう片方の列には存在
        しない場合、その値を含んでいる行の結合は失敗し、
        結果セットに含まれなくなる
      
      ・下の例でいうと、a.address_idに999が存在し、
        c.address_idに999が存在しない場合、a.address_idが
        999の列は含まれない
    
    [外部結合]
      ・どちらか一方に存在する行が全て結果セットに追加
        されるようにしたい場合に使用する
    ```

    ```sql
    -- inner join 内部結合を指す
    -- 明示しなかった場合、デフォルトで内部結合が実行される
    -- ただし、後から見るときのことを考えるべき
    select c.first_name, c.last_name, a.address
    from customer c 
    inner join address a
      on c.address_id = a.address_id;

      -- 599 rows in set (0.002 sec)
    ```

    ### usingについて

    ```text
    結合に使う列の名前が同じである場合は、
    onキーワードの場合に使うことができる
    ```

    ```sql
    select c.first_name, c.last_name, a.address
    from customer c 
    inner join address a
      using(address_id);
    ```

## Q48 ANSIの結合構文と、SQL92について知っていますか?

??? success

    ### 代表的な標準規格

    ```text
    ・代表的なトピックについて以下でまとめた
    ```

    ### 1986年

    ```text
    ・統一標準規格が発表された
    ・データ操作言語の仕様策定

    ・それまでは、ベンダーにより独自の拡張が為されていたが、
    　標準規格では、独自拡張の互換性も保証された
    ```

    ### 1989年

    ```text
    データ定義言語の仕様策定
    制約や整合性機能も追加
    ```

    ### 1992年

    ```text
    ・データ型が拡張された
    ・JOIN句でテーブルを結合!!!!!!!!
    ・一時テーブルの追加
    ```

    ### 1996年

    ```text
    ストアドプロシージャの標準化
    ```

    ### 1999年

    ```text
    ・正規表現
    ・WITH
    ・再帰クエリ
    ・UNION
    ・制御構文のサポート
    ```

    ### 2003年

    ```text
    ・ウィンドウ関数
    ```

    ### 2016年

    ```text
    ・JSONデータ型の導入
    ```

    ### 2023年

    ```text
    ・グラフDBMSとRDBMSの差異を軽減
    ```

    ### 92年以前の結合構文

    ```text
    ・JOINを用いず、where句で結合条件を指定していた
    ・古い結合構文も大抵のサーバはサポートしている
    ```

    ```sql
    -- 92年以前
    -- 各テーブルをカンマで区切り、結合条件はwhere
    select c.first_name, c.last_name, a.address
    from customer c, address a
    where c.address_id = a.address_id;

    -- 599 rows in set (0.005 sec)
    ```

    ### 92年以前の結合構文の問題点

    ```text
    ・結合条件とフィルタ条件が同じ句にあるのでわかりにくい
    ・結合条件ごとにonがないので、
    　3つ以上のテーブルを結合すると書き忘れる可能性がある
    ・データベースサーバー間での移植ができない可能性がある
    ```

    ```sql
    -- フィルタ条件と混じるとわかりにくくなる

    -- 古い方
    select c.first_name, c.last_name, a.address
    from customer c, address a
    where c.address_id = a.address_id
    and a.postal_code in (52137, 52138);

    -- 新しい方
    select c.first_name, c.last_name, a.address
    from customer c inner join address a
    on c.address_id = a.address_id
    where a.postal_code in (52137, 52138);

    /*
    +------------+-----------+------------------------+
    | first_name | last_name | address                |
    +------------+-----------+------------------------+
    | JAMES      | GANNON    | 1635 Kuwana Boulevard  |
    | FREDDIE    | DUGGAN    | 1103 Quilmes Boulevard |
    +------------+-----------+------------------------+
    2 rows in set (0.005 sec)
    */
    ```

## Q49 3つ以上のテーブルを結合するときの方法を知っていますか?

??? success
    ### 例

    ```text
    customer, address, city　テーブルを用いて、
    顧客の名前と、都市名を取得する
    ```

    ```sql
    desc customer;
    /*
    +-------------+----------------------+------+-----+---------------------+-------------------------------+
    | Field       | Type                 | Null | Key | Default             | Extra                         |
    +-------------+----------------------+------+-----+---------------------+-------------------------------+
    | customer_id | smallint(5) unsigned | NO   | PRI | NULL                | auto_increment                |
    | store_id    | tinyint(3) unsigned  | NO   | MUL | NULL                |                               |
    | first_name  | varchar(45)          | NO   |     | NULL                |                               |
    | last_name   | varchar(45)          | NO   | MUL | NULL                |                               |
    | email       | varchar(50)          | YES  |     | NULL                |                               |
    | address_id  | smallint(5) unsigned | NO   | MUL | NULL                |                               |
    | active      | tinyint(1)           | NO   |     | 1                   |                               |
    | create_date | datetime             | NO   |     | NULL                |                               |
    | last_update | timestamp            | NO   |     | current_timestamp() | on update current_timestamp() |
    +-------------+----------------------+------+-----+---------------------+-------------------------------+
    */
    desc address;
    /*
    +-------------+----------------------+------+-----+---------------------+-------------------------------+
    | Field       | Type                 | Null | Key | Default             | Extra                         |
    +-------------+----------------------+------+-----+---------------------+-------------------------------+
    | address_id  | smallint(5) unsigned | NO   | PRI | NULL                | auto_increment                |
    | address     | varchar(50)          | NO   |     | NULL                |                               |
    | address2    | varchar(50)          | YES  |     | NULL                |                               |
    | district    | varchar(20)          | NO   |     | NULL                |                               |
    | city_id     | smallint(5) unsigned | NO   | MUL | NULL                |                               |
    | postal_code | varchar(10)          | YES  |     | NULL                |                               |
    | phone       | varchar(20)          | NO   |     | NULL                |                               |
    | last_update | timestamp            | NO   |     | current_timestamp() | on update current_timestamp() |
    +-------------+----------------------+------+-----+---------------------+-------------------------------+
    */

    desc city
    /*
    +-------------+----------------------+------+-----+---------------------+-------------------------------+
    | Field       | Type                 | Null | Key | Default             | Extra                         |
    +-------------+----------------------+------+-----+---------------------+-------------------------------+
    | city_id     | smallint(5) unsigned | NO   | PRI | NULL                | auto_increment                |
    | city        | varchar(50)          | NO   |     | NULL                |                               |
    | country_id  | smallint(5) unsigned | NO   | MUL | NULL                |                               |
    | last_update | timestamp            | NO   |     | current_timestamp() | on update current_timestamp() |
    +-------------+----------------------+------+-----+---------------------+-------------------------------+
    */

    -- customer.address_idとaddress.address_id
    -- address.city_idとcity.city_idをリンクさせることで、
    -- customer.last_name, customer.first_name, city.city_nameを表示できそう
    ```

    ```sql
    -- join...onで1つずつ連結していく
    select c.last_name, c.first_name, ct.city
    from customer c
      inner join address a
      on c.address_id = a.address_id
      inner join city ct
      on a.city_id = ct.city_id

    /*
    +--------------+-------------+----------------------------+  
    | last_name    | first_name  | city                       |  
    +--------------+-------------+----------------------------+  
    | SMITH        | MARY        | Sasebo                     |  
    | JOHNSON      | PATRICIA    | San Bernardino             |  
    ...........................................................
    599 rows in set (0.009 sec)
    */
    ```

    ### 結合する順序について

    ```text
    そもそもクエリを実行される順序は
      ・SQLは非手続き型言語なので、実行はOptimizerに任される
      ・つまり、指定するテーブルの順番を変えても、Optimizer
        は、最善の方法を自分で決定する
      ・そのためfrom句に指定するテーブルの順序について
        深く考える必要はない
      
    本当に？
      ・最初に選択されるテーブルを駆動表(diving table)という
      ・現在のoptimizerはかなり賢く、ある程度複雑なクエリでも
        常に最小コストでクエリを実行してくれる
      ・しかし、稀にoptimizerが誤った実行計画を選択する場合
      　がある。最適な実行計画が分かっているのならば、mysqlの
        場合、straight_joinというキーワードを指定し、
        指定した順番でテーブルを結合できる
    
    STRAIGHT_JOIN
      指定すると、左側のテーブルが常に右側のテーブルより
      先に読み取られる
    ```

    !!! warning
        - 殆どの場合、optimizerの実行計画は正しいことに注意する。常に、`straight_join`を使うべきではない
        - 特に現在最適であったとしても、手動で結合する場合は、時間の経過とともに最適でなくなる可能性がある点に注意する事

    ### straight_joinを使ってみる

    ```sql
    -- 3通り試してみたが、今回は誤差の範囲内だった
    select straight_join c.first_name, c.last_name, ct.city
    from city ct
      inner join address a
      on a.city_id = ct.city_id
      inner join customer c
      on a.address_id = c.address_id;
    ```

## Q50 サブクエリと結合する方法を知っていますか?

??? success
    ### サブクエリはテーブルとして扱える(派生テーブル)

    ```text
    ・サブクエリ内のfrom句で定義されたエイリアスを
    　サブクエリの外で使おうとすると、クエリは失敗する

    ・サブクエリ内のSELECT文にa.address, c.addressという
    　風にテーブル名以外が同名のカラムが存在する場合、
      サブクエリ内のSELECT文でエイリアスを定義する

    ```

    ```sql
    -- 復習

    /*
    addressとcityテーブルを内部結合し、
    更に、カリフォルニア地区だけに絞り、
    その結果セットをaddrと命名
    customerテーブルと結合している

    */
    select 
      c.first_name, c.last_name, 
      addr.address, addr.city
    from customer c
      inner join
      (
        select a.address_id, a.address, ct.city
        from address a
          inner join city ct
          on a.city_id = ct.city_id
        where a.district = "California"
      ) addr
      on c.address_id = addr.address_id;

    /*
    +------------+-----------+------------------------+----------------+
    | first_name | last_name | address                | city           |
    +------------+-----------+------------------------+----------------+
    | PATRICIA   | JOHNSON   | 1121 Loja Avenue       | San Bernardino |
    | BETTY      | WHITE     | 770 Bydgoszcz Avenue   | Citrus Heights |
    | ALICE      | STEWART   | 1135 Izumisano Parkway | Fontana        |
    | ROSA       | REYNOLDS  | 793 Cam Ranh Avenue    | Lancaster      |
    | RENEE      | LANE      | 533 al-Ayn Boulevard   | Compton        |
    | KRISTIN    | JOHNSTON  | 226 Brest Manor        | Sunnyvale      |
    | CASSANDRA  | WALTERS   | 920 Kumbakonam Loop    | Salinas        |
    | JACOB      | LANCE     | 1866 al-Qatif Avenue   | El Monte       |
    | RENE       | MCALISTER | 1895 Zhezqazghan Drive | Garden Grove   |
    +------------+-----------+------------------------+----------------+
    9 rows in set (0.002 sec)
    */
    ```

## Q51 同じテーブルを2回以上結合できることを知っていますか?

??? success

    ### 概要

    ```text
    結合するたびに、異なるエイリアスを当てて、
    データベースサーバが区別できるようにする
    ```

    ### 例

    ```text
    ・特定の人物が共演している映画だけを取得したい
    ・filmテーブルにはfilm_idとtitleがある
    ・film_actorテーブルにはfilm_idとactor_idがある
    ・actorテーブルにはactor_id,first_name,last_nameがある
    ```

    ```sql
    -- 共演は関係ない
    -- Cate McQueenとCuba Birchのいずれかが出演した映画を取得する場合
    
    select f.title
    from film f
      inner join film_actor fa
      on f.film_id = fa.film_id
      inner join actor a
      on fa.actor_id = a.actor_id
    
    where 
      (
        (a.first_name = "CATE" and a.last_name = "MCQUEEN")
        or
        (a.first_name = "CUBA" and a.last_name = "BIRCH")
      );
    ```

    ```sql
    -- 共演した映画を取得する場合

    /*
    a1, a2を結合すると同一フィルムに出演している
    a1, a2もfilmテーブルに対して結合を行うので、
    俳優の組み合わせ分だけ行が取得できる。
    組み合わせをフィルタリングしたいので、2回結合して
    エイリアスを設定する必要があった

    +-------+------+-------+   
    | a1    |title | a2    |   
    +-------+------+-------+
    |actor1 |film1 |actor1 |
    |actor1 |film1 |actor2 |
    |actor2 |film1 |actor1 |
    |actor2 |film1 |actor2 |
    |actor1 |film2 |actor1 |
    |actor1 |film2 |actor3 |
    |actor3 |film2 |actor1 |
    |actor3 |film2 |actor3 |

    */
    ```
 
    ```sql
    select 
     f.title
    from film f
      inner join film_actor fa1
      on f.film_id = fa1.film_id
      inner join actor a1
      on fa1.actor_id = a1.actor_id

      inner join film_actor fa2
      on f.film_id = fa2.film_id
      inner join actor a2
      on fa2.actor_id = a2.actor_id
    
    where (
      (a1.first_name = "CATE" and a1.last_name = "MCQUEEN")
      and
      (a2.first_name = "CUBA" and a2.last_name = "BIRCH")
    );

    /*
    +------------------+
    | title            |
    +------------------+
    | BLOOD ARGONAUTS  |
    | TOWERS HURRICANE |
    +------------------+
    2 rows in set (0.009 sec)
    */
    ```

    ### 補足(組み合わせについて)

    ```sql
    -- このうちwhereのフィルタ条件に当てはまるものを
    -- 結果セットとしている

    select 
     f.title, 
     concat(a1.last_name, " " , a1.first_name) as actor_1,
     concat(a2.last_name, " " , a2.first_name) as actor_2
    from film f
      inner join film_actor fa1
      on f.film_id = fa1.film_id
      inner join actor a1
      on fa1.actor_id = a1.actor_id

      inner join film_actor fa2
      on f.film_id = fa2.film_id
      inner join actor a2
      on fa2.actor_id = a2.actor_id
      
      limit 10;

    /*
    +------------------+------------------+------------------+   
    | title            | actor_1          | actor_2          |   
    +------------------+------------------+------------------+   
    | ACADEMY DINOSAUR | GUINESS PENELOPE | GUINESS PENELOPE |   
    | ACADEMY DINOSAUR | GUINESS PENELOPE | GABLE CHRISTIAN  |   
    | ACADEMY DINOSAUR | GUINESS PENELOPE | TRACY LUCILLE    |   
    | ACADEMY DINOSAUR | GUINESS PENELOPE | PECK SANDRA      |   
    | ACADEMY DINOSAUR | GUINESS PENELOPE | CAGE JOHNNY      |   
    | ACADEMY DINOSAUR | GUINESS PENELOPE | TEMPLE MENA      |   
    | ACADEMY DINOSAUR | GUINESS PENELOPE | NOLTE WARREN     |   
    | ACADEMY DINOSAUR | GUINESS PENELOPE | KILMER OPRAH     |   
    | ACADEMY DINOSAUR | GUINESS PENELOPE | DUKAKIS ROCK     |   
    10 rows in set (0.001 sec)
    */
    ```


## Q52 自己結合(self-join)が可能なことを知っていますか?

??? success
    ### 自己結合

    ```text
    自己参照外部キー
      ・同じテーブル内の主キーを参照している列
    
    ※同じテーブル名が出てくるので、エイリアスは必要
    ```

    ### 例

    ```text
    ・filmテーブルに、前編のtitle_idが格納されたカラム
      prequel_film_idがあるとする
    
    ・この時、前編を持つ映画のタイトルと、前編のタイトルを
      取得したい
    ```

    ```sql
    select f.title, f_prnt.title prequel
    from film f
      inner join film f_prnt
      on f_prnt.film_id = f.film_id
    
    where f.prequel_film_id is not null;
    ```