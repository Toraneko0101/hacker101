# クイズ(2:基本)

## 方針

??? info
    - テーブルの設計方法の理解
    - DBに関する基礎知識の理解
    - データ型に関する理解
    - 基本的なSQLスキーマ文/データ文の理解
    - テーブルの情報表示や定義変更の理解
    - 便利な表示方法(pager,desc)や操作方法の理解

## Q13 mysql/mariadbユーティリティを使う方法を知っていますか?

??? success

    ### 以下の様に入力

    ```bash
    $ mysql -u <username> -p
    ```

## Q14 データベース一覧を表示する方法と、操作したいデータベースを作成/指定する方法を知っていますか?

??? success
    ### show databases;
    - データベース一覧を表示

    ### create database <yourdb>;
    - Databaseをまだ作成していない場合

    ### use <yourdb>;
    - 指定したデータベースを使用する

    ### drop database <yourdb>;
    - 指定した名前のデータベースを削除する

## Q15 MariaDB/MySQLの組み込み関数であるnow関数を用いて、現在の日付と時刻を取得することはできますか?

??? success
    ### SELECT now();

    ```bash
    MariaDB [(none)]> SELECT now();
    +---------------------+
    | now()               |
    +---------------------+
    | 2024-02-18 23:54:07 |
    +---------------------+
    1 row in set (0.003 sec)
    ```

## Q16 データベースサーバによっては、クエリを実行するとき、1つ以上のfrom句がないと失敗するが、その対処法を知っていますか?

??? success
    ### Oracleデータベースが有名
    - 関数を呼び出すだけでいい場合、`dual`テーブルが使われる

    ### MySQL/MariaDB
    - `Oracle`データベースとの互換性目的でこのテーブルは存在する。
    - つまり、now関数は以下の様にも書ける

    ```bash
    MariaDB [(none)]> SELECT now() FROM dual;
    +---------------------+
    | now()               |
    +---------------------+
    | 2024-02-18 23:55:54 |
    +---------------------+
    1 row in set (0.000 sec)
    ```

## Q17 mysql/mariaDBユーティリティを終了して、Unixシェルに制御を戻したいとき、exit;やquit;が使えることを知っていますか?

??? success
    ### どちらでもいい
    - 末尾セミコロンも任意

    ```bash
    MariaDB [(none)]> exit;
    Bye
    ```

    ```bash
    MariaDB [(none)]> quit;
    Bye
    ```

## Q18 MariaDBの文字データ型について知っていますか?

??? success
    ### 固定長または可変長
    - 固定長なら末尾はスペースで埋まり、常に同じバイト数消費
    - 可変長なら必ずしも同じバイト数を指定しない


    ### char/varchar
    - 文字列型の列を定義する場合は、その列の格納できる文字列の最大長を指定する

    ```sql
    char(20) /*固定長: 最大255バイト*/
    varchar(20) /*可変長: 最大65535バイト*/
    ```

    !!! warning
        - 基本的に、`char/varchar`の扱い方はどのデータベースでも変わらない
        - 例外として`Oracle`の場合、可変長の文字を格納するとき`varchar2`型を使う

    !!! warning
        - `varchar`は文字列の長さを格納するために2バイト使うので、実質`65532`バイトまでしか使えない
        - utf8文字は1文字で3バイト使うことに留意する
        - 末尾のスペースは削除されない

## Q19 SQLで扱える文字セットについて知っていますか?

??? success
    ### 文字セットとは?
    - 日本語のように文字を1バイトで表現できない場合、マルチバイト文字を使うことになる。
    - これに対応した文字セットをマルチバイト文字セットという

    ### MySQL/MariaDBでの文字セットの表示

    ??? info

        ```bash
        MariaDB [(none)]> show character set;
        +----------+-----------------------------+---------------------+--------+
        | Charset  | Description                 | Default collation   | Maxlen |
        +----------+-----------------------------+---------------------+--------+
        | big5     | Big5 Traditional Chinese    | big5_chinese_ci     |      2 |
        | dec8     | DEC West European           | dec8_swedish_ci     |      1 |
        | cp850    | DOS West European           | cp850_general_ci    |      1 |
        | hp8      | HP West European            | hp8_english_ci      |      1 |
        | koi8r    | KOI8-R Relcom Russian       | koi8r_general_ci    |      1 |
        | latin1   | cp1252 West European        | latin1_swedish_ci   |      1 |
        | latin2   | ISO 8859-2 Central European | latin2_general_ci   |      1 |
        | swe7     | 7bit Swedish                | swe7_swedish_ci     |      1 |
        | ascii    | US ASCII                    | ascii_general_ci    |      1 |
        | ujis     | EUC-JP Japanese             | ujis_japanese_ci    |      3 |
        | sjis     | Shift-JIS Japanese          | sjis_japanese_ci    |      2 |
        | hebrew   | ISO 8859-8 Hebrew           | hebrew_general_ci   |      1 |
        | tis620   | TIS620 Thai                 | tis620_thai_ci      |      1 |
        | euckr    | EUC-KR Korean               | euckr_korean_ci     |      2 |
        | koi8u    | KOI8-U Ukrainian            | koi8u_general_ci    |      1 |
        | gb2312   | GB2312 Simplified Chinese   | gb2312_chinese_ci   |      2 |
        | greek    | ISO 8859-7 Greek            | greek_general_ci    |      1 |
        | cp1250   | Windows Central European    | cp1250_general_ci   |      1 |
        | gbk      | GBK Simplified Chinese      | gbk_chinese_ci      |      2 |
        | latin5   | ISO 8859-9 Turkish          | latin5_turkish_ci   |      1 |
        | armscii8 | ARMSCII-8 Armenian          | armscii8_general_ci |      1 |
        | utf8mb3  | UTF-8 Unicode               | utf8mb3_general_ci  |      3 |
        | ucs2     | UCS-2 Unicode               | ucs2_general_ci     |      2 |
        | cp866    | DOS Russian                 | cp866_general_ci    |      1 |
        | keybcs2  | DOS Kamenicky Czech-Slovak  | keybcs2_general_ci  |      1 |
        | macce    | Mac Central European        | macce_general_ci    |      1 |
        | macroman | Mac West European           | macroman_general_ci |      1 |
        | cp852    | DOS Central European        | cp852_general_ci    |      1 |
        | latin7   | ISO 8859-13 Baltic          | latin7_general_ci   |      1 |
        | utf8mb4  | UTF-8 Unicode               | utf8mb4_general_ci  |      4 |
        | cp1251   | Windows Cyrillic            | cp1251_general_ci   |      1 |
        | utf16    | UTF-16 Unicode              | utf16_general_ci    |      4 |
        | utf16le  | UTF-16LE Unicode            | utf16le_general_ci  |      4 |
        ows Japanese   | cp932_japanese_ci   |      2 |
        | eucjpms  | UJIS for Windows Japanese   | eucjpms_japanese_ci |      3 |
        +----------+-----------------------------+---------------------+--------+
        40 rows in set (0.012 sec)
        ```

    ### 説明
    - `Maxlen`が2以上の場合、マルチバイト文字セット
    - 現在のデフォルト文字セットは`utf8mb4`

    ### デフォルトの文字セット等を確認したい場合

    ```sql
    show variables like 'char%';

    /*
    +--------------------------+----------------------------+    
    | Variable_name            | Value                      |    
    +--------------------------+----------------------------+    
    | character_set_client     | utf8mb3                    |    
    | character_set_connection | utf8mb3                    |    
    | character_set_database   | utf8mb4                    |    
    | character_set_filesystem | binary                     |    
    | character_set_results    | utf8mb3                    |    
    | character_set_server     | utf8mb4                    |    
    | character_set_system     | utf8mb3                    |    
    | character_sets_dir       | /usr/share/mysql/charsets/ |    
    +--------------------------+----------------------------+ 
        1 row in set (0.010 sec)
    */
    ```
    - 説明

    ```
    character_set_client
      クライアントとの通信に使用される文字セット
    character_set_connection
      クライアントとサーバー間の接続に使用される文字セット
    character_set_database
      データベースのデフォルトの文字セット
    character_set_filesystem
      ファイルシステムで使用される文字セット
    character_set_results
      クエリの結果セットの文字セット
    character_set_server
      サーバーのデフォルトの文字セット
    character_set_system
      システムの文字セット
    character_sets_dif
      MySQLで使用可能な文字セットの場所

    ※変更したい場合
    ・MariaDBの設定ファイルを変更する
    ```

    ### 特定のデータベースのデフォルトの文字セットを確認

    ```sql
    select schema_name, default_character_set_name
    from information_schema.schemata
    where schema_name="sakila";

    /*
    +-------------+----------------------------+
    | schema_name | default_character_set_name |
    +-------------+----------------------------+
    | sakila      | utf8mb4                    |
    +-------------+----------------------------+
    1 row in set (0.007 sec)
    */
    ```

    ### 列ごとに別の文字セットを選択したい場合

    ```sql
    varchar(20) character set latin1 /*latin1の文字セットを使う*/
    ```

    ### データベース作成時に文字セットを設定する

    ```sql
    create database hoge character set latin1 /*hogeはdb名*/
    ```

## Q20 varchar型を超えたサイズのデータを格納したい場合、テキスト型が使えることを知っていますか?

??? success
    ### テキスト型一覧

    | テキスト型 | 最大バイト数  |
    | ---------- | ------------- |
    | tinytext   | 255           |
    | text       | 65,535        |
    | mediumtext | 16,777,215    |
    | longtext   | 4,294,967,295 |

    ### テキスト型の注意点
    - テキスト型に格納したデータが、型の最大サイズを超える場合はデータが切り詰められる
    - 末尾のスペースは削除されない
    - ソートやグループ化に使う場合、最初の1024バイトだけが使われる。(varcharとの差異)ただこれは変更可能

    ### 使用ケース
    - ドキュメント等で、`mediumtext`や`longtext`を使う
    - 詳細なログを扱う上で、上記の型を使う
    - `textarea`に入力されたユーザの要望を格納する際に、上記の型を使う
    - ブログ記事における、コメント本文を格納するために上記の型を使う
    - ウェブサイトのコンテンツを管理するために上記の型を使う

    !!! warning
        - テキスト型に関しては、SQLごとに異なる
        - たとえば`SQL Server`は`text`型しか存在しない
        - `DB2`と`Oracle`では`clob`が用いられる
        - 補足として`SQL Server`は`varchar(max)`と出来、こうすることで、`2GB`まで格納可能らしい

## Q21 MariaDBの整数型について知っていますか?

??? success
    ### 整数型(1,2,3,4,8バイト)
    | 整数型    | signedの範囲     | unsignedの範囲 |
    | --------- | ---------------- | -------------- |
    | tinyint   | -128 ~ 127       | 0 ~ 255        |
    | smallint  | -32768 ~ 32767   | 0 ~ 65535      |
    | mediumint | -2^23 ~ 2^23 - 1 | 0 ~ 2^24 - 1   |
    | int       | -2^31 ~ 2^31 -1  | 0 ~ 2^32  - 1  |
    | bigint    | -2^63 ~ 2^63 - 1 | 0 ~ 2^64 - 1   |

    ### bool,boolean
    - `tinyint(1)`の同義語
    - 値0は`false`。ゼロ以外の値は`true`とみなされる

    ### signed, unsigned
    - `unsigned`を付けると、0以上しか格納されなくなる

    ### zerofill
    - `zerofill`を付けると、0パディングが行われる

    ### INT(3)について
    - `()`は表示幅を意味している
    - INT型の上限まで値を格納できるが、実際には3桁分しか表示されないということ
    - そのため`tinyint(1)`の同義語である`bool`に0,1以外の値を格納することもできる。ただtrue/falseの判定は前述のとおり、行われる


    ### 例

    ```sql
    create table ints (a bool, b tinyint zerofill, c int(3) zerofill, d int zerofill , e int unsigned);
    insert into ints values (false, 81, 120, 120, 21);
    select * from ints;

    /*
    c,dを見比べると、桁でパディングが行われている
    +------+------+------+------------+------+
    | a    | b    | c    | d          | e    |
    +------+------+------+------------+------+
    |   0 |   081 |  120 | 0000000120 |   21 |
    +------+------+------+------------+------+
    1 row in set (0.001 sec)
    */

    desc ints; --boolがtinyint(1)に変換されていることが確認できる
    /*
    +-------+---------------------------+------+-----+---------+-------+
    | Field | Type                      | Null | Key | Default | Extra |
    +-------+---------------------------+------+-----+---------+-------+
    | a     | tinyint(1)                | YES  |     | NULL    |       |
    | b     | tinyint(4)                | YES  |     | NULL    |       |
    | c     | int(3) unsigned zerofill  | YES  |     | NULL    |       |
    | d     | int(10) unsigned zerofill | YES  |     | NULL    |       |
    | e     | int(10) unsigned          | YES  |     | NULL    |       |
    +-------+---------------------------+------+-----+---------+-------+
    5 rows in set (0.001 sec)
    */


    ```

## Q22 Mariadbの小数点型について知っていますか?

??? success
    ### 浮動小数点

    | 型          | 数値の範囲                                                                                                      |
    | ----------- | --------------------------------------------------------------------------------------------------------------- |
    | float(p,s)  | `–3.402823466E+38 〜 –1.175494351E-38`<br>`1.175494351E-38 〜 3.402823466E+38`                                  |
    | double(p,s) | `–1.7976931348623157E+308 〜 –2.2250738585072014E-308`<br> `2.2250738585072014E-308 〜 1.7976931348623157E+308` |
    |             |                                                                                                                 |

    ### (p,s)
    - `p`:精度(小数点の左右の桁数の合計)
    - `s`:位取り(小数点以下の桁数)
    - 必ずしも`(p,s)`を指定する必要はなく、この精度を超えると丸めが起きる

    ### 使用例

    ```sql
    create table floats
    (a float, b float(4,2), c double unsigned);

    insert into floats
    values 
    (-3.1415, -3.1415, 1.618033988),
    (3.1415, 3.1415, 1.618033988),
    (27.44, -12.44, 1.618033988);

    select * from floats;
    /*
    bが丸められていることが確認できる。
    3.14になったのは、小数点以下の桁数を2にしたから
    +---------+-------+-------------+
    | a       | b     | c           |
    +---------+-------+-------------+
    | -3.1415 | -3.14 | 1.618033988 |
    |  3.1415 |  3.14 | 1.618033988 |
    |   27.44 |-12.44 | 1.618033988 |
    +---------+-------+-------------+
    1 row in set (0.001 sec)
    */
    ```

## Q23 Mariadbの時間データ型について知っていますか?

??? success

    ### 時間データ型

    | 型        | フォーマット        | 有効値                                                  |
    | --------- | ------------------- | ------------------------------------------------------- |
    | date      | YYYY-MM-DD          | 1000-01-01 ~ 9999-12-31                                 |
    | datetime  | YYYY-MM-DD HH:MI:SS | 1000-01-01 00:00:00.000000 ~ 9999-12-31 23:59:59.999999 |
    | timestamp | YYYY-MM-DD HH:MI:SS | 1970-01-01 00:00:00.000000 ~ 2106-02-07 06:28:15.999999 |
    | year      | YYYY                | 2155                                                    |
    | time      | HHH:MI:SS           | -838:59:59:000000 ~ 838:59:59:000000                    |

    ### フォーマットについて

    | 構成要素 | 定義     | 範囲        |
    | -------- | -------- | ----------- |
    | YYYY     | 年       | 1000 ~ 9999 |
    | MM       | 月       | 01 ~ 12     |
    | DD       | 日       | 01 ~ 31     |
    | HH       | 時       | 00 ~ 23     |
    | HHH      | 経過時間 | -838 ~ 838  |
    | MI       | 分       | 00 ~ 59     |
    | SS       | 秒       | 00 ~ 59     |

    !!! info
        ### 年について
        - 日付の範囲はデータベースサーバーによって異なる
        - `BC`に対応しているものもある(`Oracleやpostgre`)
        - 歴史的な日付を格納する際は注意する(というより今のところ、歴史的な年表をDBに作成するなどの意図では`postgre`を使った方がいいかもしれない)

        ### 13月について
        - たとえば、エチオピア暦は、13月が存在する。しかも13月は5日か6日しか存在しない
        - 2017年1月の`Oracle database 12c`の仕様書には、これをサポートしたという記述がある 


    ### マイクロミリ秒について
    - `datetime`, `time`, `timestamp`はマイクロ秒の精度を持つ
    - `datetime(2)`等とすることで、精度を制限できる

    ### 注意点
    - `timestamp`型の上限は、年々増えている。おそらく気にする必要はない
    - それよりもタイムゾーンに影響を受ける方が問題
    - `year`も上限に達する前に増えるか、それともそのころにはSQL自体がなくなっているだろう

    ### 参考(time型の上限が838である原因の考察)
    - [stackoverflow:why-is-mysqls-maximum-time-limit-8385959](https://stackoverflow.com/questions/39259910/why-is-mysqls-maximum-time-limit-8385959)

    ### 使用例

    ```sql
    create table times
    (a date, b datetime, c year, d time(2));

    insert into times
    values
    ('2024-02-19', 
    '2024-02-19 10:55:34.000000', 
    '2024', 
    '-21:12:00.98')

    select * from times;
    /*
    //小数点以下が0なら表示は省略される。
    //ただし別行の値が0でないならそれに合わせられる模様
    +------------+---------------------+------+--------------+
    | a          | b                   | c    | d            |   
    +------------+---------------------+------+--------------+   
    | 2024-02-19 | 2024-02-19 10:55:34 | 2024 | -21:12:00.98 |   
    +------------+---------------------+------+--------------+   
    1 rows in set (0.001 sec)
    */
    ```

    ### その他の使いどころ
    - 誕生日の管理なら`date`
    - 注文管理なら`datetime`
    - カラムの変更を追跡するなら`timestamp`。行が追加/変更された時に日時が自動的に挿入されるため
    - 年のデータだけが必要なら`yeaer`
    - ジョブの完了に必要な時間の長さを保持したいなら`time`。不要な日付の状態は要らないので

## Q24 テーブルを作成しようと考えてから、正規化を行うまでの流れが分かりますか?

??? success

    !!! warning
        - 以下はあくまで一例で、ベストプラクティスではない

    ### 1. 設計
    - どのような情報が含まれていると効果的か考え、列挙する
    - 列挙したら、列の名前とデータ型を決める

    ### `person`テーブルの例

    | 列             | 型           | 有効値   |
    | -------------- | ------------ | -------- |
    | name           | varchar(40)  |          |
    | eye_color      | char(2)      | BL,BR,GR |
    | birth_date     | date         |          |
    | address        | varchar(100) |          |
    | favorite_foods | varchar(200) |          |

    ### 2. 正規化する
    重複する列や複合列が、設計に含まれないようにしたい

    - `name`は姓名から成る複合オブジェクトなので分割
    - 一意性を保つ列がないので追加する
    - `address`も郵便番号、国、都道府県、市町村、番地に分割可能
    - `favorite_foods`にはいくつかの食べ物が含まれるので、別テーブルに記載して、外部キーを用いて結合する

    ### personテーブル
    | 列          | 型                 | 有効値   |
    | ----------- | ------------------ | -------- |
    | person_id   | smallint(unsigned) |          |
    | first_name  | varchar(20)        |          |
    | last_name   | varchar(20)        |          |
    | eye_color   | char(2)            | BL,BR,GR |
    | birth_date  | date               |          |
    | street      | varchar(30)        |          |
    | city        | varchar(20)        |          |
    | state       | varchar(20)        |          |
    | country     | varchar(20)        |          |
    | postal_code | varchar(20)        |          |

    ### favorite_foodsテーブル
    - `person_id`が3つの`food`を好きなら、3行書く

    | 列        | 型                 |
    | --------- | ------------------ |
    | person_id | smallint(unsigned) |
    | food      | varchar(20)        |

    ### どこまで分割するか
    - たとえば`address`を細分化したのは正しかったのか
    - この是非はデータの取り扱い方によって異なる
    - 分割すれば部分的な更新が容易になる
    - が、その一方、データを取り扱う際の複雑さが増し、検索やクエリのパフォーマンスに影響が出る
    - たとえば、ユーザに`street`,`city`,`country`,`postal_code`を独立に入力させ、プロフィールを更新できるようにするなら、おそらく列は分割した方がいい

## Q25 SQLスキーマ文の作り方が分かりますか?

??? success
    ### 3. 正規化が終わったら、実際にテーブルを作成していく

    ### `CREATE TABLE <yourtable> (col1, col2,...);`
    - 以下はテーブル作成の例

    ```sql
    CREATE TABLE person
    (
      person_id smallint unsigned,
      fname varchar(20),
      lname varchar(20),
      eye_color char(2) CHECK(eye_color IN ('BR', 'BL', 'GR')),
      birth_date date,
      street varchar(30),
      city varchar(20),
      state varchar(20),
      country varchar(20),
      postal_code varchar(20),
      CONSTRAINT pk_person PRIMARY KEY (person_id)
    );

    CREATE TABLE favorite_foods
    (
      person_id smallint unsigned,
      food varchar(20),
      -- 複合主キー
      CONSTRAINT pk_favorite_food PRIMARY KEY (person_id, food),
      -- `person`テーブルの`person_id`列に、挿入しようとしている`person_id`列の値が存在しない場合、操作は失敗する
      CONSTRAINT fk_fav_food_person_id FOREIGN KEY (person_id)
      REFERENCES person (person_id)
    );
    ```

    ### 説明
    - `CREATE TABLE`:テーブルを作成する際に用いるクエリ
    - `CONSTRAINT`: 制約。
    - `PRIMARY KEY`: 主キー制約
    - `CHECK`:検査制約
    - `FOREIGN KEY (column) REFERENCES outer_table (outer_column)`: 外部キー制約

    ### DROP TABLE `<your_table>`
    - 指定したテーブルを削除する。（削除したい場合）

## Q26 制約について知っていますか?

??? success

    ### 主キー制約
    - 一意性を保証する
    - 重複とNULLを禁止する
    - 1つのテーブルに1つまで

    ### CHECK制約
    - 条件を指定し、条件を満たさないデータを禁止する
    - 後天的に付け加えることは`MariaDB`ではサポートされていない

    !!! info
        - Q25のCHECK制約は以下の様に書き直せる

        ```sql
        eye_color ENUM('BR', 'BR', 'GR'),
        ```

    ### NOT NULL制約
    - NULL値を禁止する

    !!! info
        - `null`は適用外や不明、空という意味で使われる

    ### UNIQUE KEY制約
    - 重複したデータを禁止する
    - 複数列に設定可能
    - NULLはOK

    ### FOREIGN KEY制約
    - 他のテーブルの列を参照し、その列にないデータを禁止する

    ### DEFAULT(制約ではない)
    - デフォルト値を設定
    ### AUTO INCREMENT(制約ではない)
    - 自動インクリメント機能を追加。
    - 挿入しないまたはNULLを挿入するとSQLサーバ側が、自動で利用可能な値を挿入する。
    - マルチユーザ環境でも、うまく動作する

## Q27 describeコマンドを実行するとテーブル構造が確認できることを知っていますか?

??? success
    ### describe/desc

    ```sql
    desc favorite_foods;
    /*
    +-----------+----------------------+------+-----+---------+-------+
    | Field     | Type                 | Null | Key | Default | Extra |
    +-----------+----------------------+------+-----+---------+-------+
    | person_id | smallint(5) unsigned | NO   | PRI | NULL    |       |
    | food      | varchar(20)          | NO   | PRI | NULL    |       |
    +-----------+----------------------+------+-----+---------+-------+
    2 rows in set (0.001 sec)]
    */
    ```

## Q28 テーブルの定義を変更する方法を知っていますか?(alter table)

??? success
    ### alter table

    - 特定のテーブルの特定の列の型を変更する

    ```sql
    ALTER TABLE <table_name>
    MODIFY COLUMN <column_name> <new_data_type>;
    ```

    ### 使用例
    - 自動インクリメントを有効にする

    ```sql
    ALTER TABLE person
    MODIFY person_id SMALLINT UNSIGNED AUTO_INCREMENT;

    desc person;
    /*
    +-------------+----------------------+------+-----+---------+----------------+
    | Field       | Type                 | Null | Key | Default | Extra          |
    +-------------+----------------------+------+-----+---------+----------------+
    | person_id   | smallint(5) unsigned | NO   | PRI | NULL    | auto_increment |
    | fname       | varchar(20)          | YES  |     | NULL    |                |
    | lname       | varchar(20)          | YES  |     | NULL    |                |
    | eye_color   | char(2)              | YES  |     | NULL    |                |
    | birth_date  | date                 | YES  |     | NULL    |                |
    | street      | varchar(30)          | YES  |     | NULL    |                |
    | city        | varchar(20)          | YES  |     | NULL    |                |
    | state       | varchar(20)          | YES  |     | NULL    |                |
    | country     | varchar(20)          | YES  |     | NULL    |                |
    | postal_code | varchar(20)          | YES  |     | NULL    |                |
    +-------------+----------------------+------+-----+---------+----------------+
    10 rows in set (0.001 sec)
    */
    ```

    ### 参照整合性違反について
    - たとえば以下はエラーとなる

    ```sql
    ALTER TABLE favorite_foods
    MODIFY person_id SMALLINT unsigned AUTO_INCREMENT;
    ```

    - これは、他のテーブルで外部キーとして参照されている主キーの変更が、参照整合性に違反するため


## Q29 データを挿入/更新/削除し、該当データを閲覧する方法を知っていますか?

??? success
    ### `insert into <table> (col1,col2,...) values (data1, data2, ...)`
    - データを挿入するときに使う
    - 列の個数と値の個数は一致する必要がある
    - `table`の後の`()`がない場合、テーブルのカラム数と`Value`句で指定する値の数は同じでなければならない
    - ただし、`null`が許容されている場合、足りない場合は`null`が挿入される。多い場合はエラーとなる
    - フォーマットを満たす限り、文字列は自動的に日付に変換される

    ```sql
    INSERT INTO person
    (person_id, fname, lname, eye_color, birth_date)
    VALUES
    (null, "William", "Turner", "BR", "1999-01-01");

    INSERT INTO person
    (person_id, fname, lname, eye_color, birth_date,
    street, city, state, country, postal_code)
    VALUES
    (null, "Susan", "Smith", "BL", "1998-11-13",
    "4th St.", "Arima", "Kanagawa", "JP", "216");

    -- 複数を同時に挿入することも可能
    INSERT INTO favorite_foods (person_id, food) 
    VALUES
    (1, "pizza"),
    (1, "cookies"),
    (1, "gummy");
    ```

    ### `select col1, col2, ... from <table>`
    - `table`内の指定したカラムのデータを閲覧する
    - `where`句で条件を指定可能
    - `order by <column>`句でカラム順にソート
    - その他については後程述べる

    ```sql
    SELECT person_id, fname, lname, birth_date
    FROM person
    WHERE person_id = 1;

    /*
    +-----------+---------+--------+------------+
    | person_id | fname   | lname  | birth_date |
    +-----------+---------+--------+------------+
    |         1 | William | Turner | 1999-01-01 |
    +-----------+---------+--------+------------+
    1 row in set (0.002 sec)
    */

    SELECT food
    FROM favorite_foods
    WHERE person_id = 1
    ORDER BY food;

    /*
    +---------+
    | food    |
    +---------+
    | cookies |
    | gummy   |
    | pizza   |
    +---------+
    3 rows in set (0.003 sec)
    */
    ```

    ### `update <table> set col1 = "val1", col2 = "val2" ... where coln = valn`
    - 後からデータを更新可能
    - `where`句を指定しないとすべての行が変更されるので注意

    ```sql
    UPDATE person
    SET 
    street = '6th Honmati St',
    city = "Nagamori",
    state = "Gifu",
    country = "JP",
    postal_code = "080" --末尾カンマは要らないので注意
    WHERE person_id = 1;

    /*
    Query OK, 1 row affected (0.008 sec)
    Rows matched: 1  Changed: 1  Warnings: 0
    */
    ```

    ### `delete from <table> where col1 = val1`
    - `where`句の条件に一致した行を削除する

    ```sql
    DELETE FROM person
    WHERE person_id = 2;

    /*
    Query OK, 1 row affected (0.008 sec)
    */
    ```

## Q30 データ挿入等の際に陥りやすい誤りとその際に表示されるエラーメッセージについて知識はありますか？

??? success

    ### 一意制約を無視する

    ```sql
    insert into person
    (person_id)
    values
    (1);

    /*
    ERROR 1062 (23000): Duplicate entry '1' for key 'PRIMARY'
    */
    ```

    ### 外部キー制約を無視する
    - 親である`person`テーブルの`person_id`に存在しない値を挿入しようとしたため

    ```sql
    insert into favorite_foods (person_id, food)
    values (999, "milk");

    /*
    ERROR 1452 (23000): 
    Cannot add or update a child row: 
    a foreign key constraint fails 
    (`mydb`.`favorite_foods`, 
    CONSTRAINT `fk_fav_food_person_id` 
    FOREIGN KEY (`person_id`) 
    REFERENCES `person` (`person_id`))
    */
    ```

    !!! info
        - `Engine`が`InnoDB`でない場合、外部キー制約は適用されない

    ### ENUM値にない値を入れた場合

    ```sql
    update person set eye_color = "ZZ" where person_id = 1;
    /*
    ERROR 1265 (01000): Data truncated for column 'eye_color' at row 1
    */
    ```

    ### フォーマット形式に一致しない日付を挿入した場合

    ```sql
    update person
    set birth_date = "NOV-21-1971"
    where person_id = 1;
    
    /*
    ERROR 1292 (22007): Incorrect date value: 'NOV-21-1971' for column `mydb`.`person`.`birth_date` at row 1   
    */ 
    ```

    !!! info
        ### str_to_date関数
        - 上記のものを使うと、フォーマット文字列を指定できる

        ```sql
        update person
        set birth_date = str_to_date("NOV-18-2011", "%b-%d-%Y")
        WHERE person_id = 1;

        /*
        Query OK, 1 row affected (0.009 sec)
        Rows matched: 1  Changed: 1  Warnings: 0
        */
        ```

        ### 変換用フォーマット文字列一覧

        | 略語 | 説明            |
        | ---- | --------------- |
        | %a   | 簡略曜日名(Sun) |
        | %b   | 簡略月名(Jan)   |
        | %c   | 月を表す数字    |
        | %d   | 日を表す数字    |
        | %f   | マイクロ秒      |
        | %H   | 24時間の時      |
        | %h   | 12時間の時      |
        | %i   | 分              |
        | %j   | 通年日(001~366) |
        | %M   | 月名(January)   |
        | %m   | 月を表す数字    |
        | %p   | AM or PM        |
        | %s   | 秒              |
        | %W   | 曜日名(Sunday)  |
        | %w   | 曜日を表す数字  |
        | %Y   | 4桁年           |

## Q31 テーブルに関する詳細な情報を取得するクエリを知っていますか?

??? success
    ### show full tables
    - `BASE TABLE`か`VIEW`、`SYSTEM VIEW`を返す
    - `BASE TABLE`:通常のテーブル
    - `VIEW`:1つ以上のテーブルからデータを取得するための仮想テーブル
    - `SYSTEM VIEW`: データベースのメタデータなどを提供する特殊なビュー

    ```sql
    show full tables;

    /*
    +----------------+------------+
    | Tables_in_mydb | Table_type |
    +----------------+------------+
    | favorite_foods | BASE TABLE |
    | floats         | BASE TABLE |
    | ints           | BASE TABLE |
    | person         | BASE TABLE |
    | times          | BASE TABLE |
    +----------------+------------+
    5 rows in set (0.003 sec)
    */

    ```

    ### `show create table <table>`
    - 指定したテーブルと、同じテーブル構造を作成するための`CREATE TABLE`文を表示する
    - 現在のテーブルの構造も表示できる
    - `desc`等ではCHECK例外などは表示されないのでより詳細であるといえる

    ```sql
    show create table person;

    /*
    +--------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
    | Table  | Create Table                                                                                                                                                                                                                                                                                                                                                                                                                                     
                                                                                                              |
    +--------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
    | person | CREATE TABLE `person` (
      `person_id` smallint(5) unsigned NOT NULL AUTO_INCREMENT,
      `fname` varchar(20) DEFAULT NULL,
      `lname` varchar(20) DEFAULT NULL,
      `eye_color` enum('BR','BL','GR') DEFAULT NULL,
      `birth_date` date DEFAULT NULL,
      `street` varchar(30) DEFAULT NULL,
      `city` varchar(20) DEFAULT NULL,
      `state` varchar(20) DEFAULT NULL,
      `country` varchar(20) DEFAULT NULL,
      `postal_code` varchar(20) DEFAULT NULL,
      PRIMARY KEY (`person_id`)
    ) ENGINE=InnoDB AUTO_INCREMENT=3 DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_general_ci |
    +--------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
    1 row in set (0.001 sec)
    */
    ```

    ### show table status
    - テーブルに現在格納されているデータ数や最終更新時間などの詳細情報を取得する

    ```sql
    show table status

    /*
    +----------------+--------+---------+------------+------+----------------+-------------+-----------------+--------------+-----------+----------------+---------------------+---------------------+------------+--------------------+----------+----------------+---------+------------------+-----------+
    | Name           | Engine | Version | Row_format | Rows | Avg_row_length | Data_length | Max_data_length | Index_length | Data_free | Auto_increment | Create_time         | Update_time         | Check_time | Collation          | Checksum | Create_options | Comment | Max_index_length | Temporary |
    +----------------+--------+---------+------------+------+----------------+-------------+-----------------+--------------+-----------+----------------+---------------------+---------------------+------------+--------------------+----------+----------------+---------+------------------+-----------+
    | favorite_foods | InnoDB |      10 | Dynamic    |    3 |           5461 |       16384 |               0 |            0 |         0 |           NULL | 2024-02-19 06:39:09 | 2024-02-19 07:06:56 | NULL       | utf8mb4_general_ci |     NULL |                |         |                0 | N         |
    | floats         | InnoDB |      10 | Dynamic    |    4 |           4096 |       16384 |               0 |            0 |         0 |           NULL | 2024-02-19 04:21:38 | 2024-02-19 04:31:20 | NULL       | utf8mb4_general_ci |     NULL |                |         |                0 | N         |
    | ints           | InnoDB |      10 | Dynamic    |    2 |           8192 |       16384 |               0 |            0 |         0 |           NULL | 2024-02-19 04:13:47 | NULL                | NULL       | utf8mb4_general_ci |     NULL |                |         |                0 | N         |
    | person         | InnoDB |      10 | Dynamic    |    1 |          16384 |       16384 |               0 |            0 |         0 |              3 | 2024-02-19 07:45:22 | NULL                | NULL       | utf8mb4_general_ci |     NULL |                |         |                0 | N         |
    | times          | InnoDB |      10 | Dynamic    |    2 |           8192 |       16384 |               0 |            0 |         0 |           NULL | 2024-02-19 04:59:09 | 2024-02-19 04:59:21 | NULL       | utf8mb4_general_ci |     NULL |                |         |                0 | N         |
    +----------------+--------+---------+------------+------+----------------+-------------+-----------------+--------------+-----------+----------------+---------------------+---------------------+------------+--------------------+----------+----------------+---------+------------------+-----------+

    */

    -- 対象のテーブルを絞り込みたい場合
    show table status where name = "favorite_foods"\G
    /*
    *************************** 1. row ***************************
                Name: favorite_foods
              Engine: InnoDB
            Version: 10
          Row_format: Dynamic
                Rows: 3
      Avg_row_length: 5461
        Data_length: 16384
    Max_data_length: 0
        Index_length: 0
          Data_free: 0
      Auto_increment: NULL
        Create_time: 2024-02-19 06:39:09
        Update_time: 2024-02-19 07:06:56
          Check_time: NULL
          Collation: utf8mb4_general_ci
            Checksum: NULL
      Create_options:
            Comment:
    Max_index_length: 0
          Temporary: N
    1 row in set (0.001 sec)
    */
    ```

    !!! warning
        - ストレージエンジンとして`InnoDB`を使用している場合、最終更新日やデータの数は正確でない可能性がある
        - 最終更新日は、テーブルのメタデータの更新時間である可能性がある
        - データの数については、信頼性は高いが推定値を出力する

    ### information_schema.tables
    - 一般的に`show table status`より詳細

    ```sql
    select * from information_schema.tables 
    where table_schema="mydb" and table_name="favorite_foods";
    /*
    +---------------+--------------+----------------+------------+--------+---------+------------+------------+----------------+-------------+-----------------+--------------+-----------+----------------+---------------------+---------------------+------------+--------------------+----------+----------------+---------------+------------------+-----------+
    | TABLE_CATALOG | TABLE_SCHEMA | TABLE_NAME     | TABLE_TYPE | ENGINE | VERSION | ROW_FORMAT | TABLE_ROWS | AVG_ROW_LENGTH | DATA_LENGTH | MAX_DATA_LENGTH | INDEX_LENGTH | DATA_FREE | AUTO_INCREMENT | CREATE_TIME         | UPDATE_TIME         | CHECK_TIME | TABLE_COLLATION    | CHECKSUM | CREATE_OPTIONS | TABLE_COMMENT | MAX_INDEX_LENGTH | TEMPORARY |
    +---------------+--------------+----------------+------------+--------+---------+------------+------------+----------------+-------------+-----------------+--------------+-----------+----------------+---------------------+---------------------+------------+--------------------+----------+----------------+---------------+------------------+-----------+
    | def           | mydb         | favorite_foods | BASE TABLE | InnoDB |      10 | Dynamic    |          3 |           5461 |       16384 |               0 |            0 |         0 |           NULL | 2024-02-19 06:39:09 | 2024-02-19 07:06:56 | NULL       | utf8mb4_general_ci |     NULL |                
    |               |                0 | N         |
    +---------------+--------------+----------------+------------+--------+---------+------------+------------+----------------+-------------+-----------------+--------------+-----------+----------------+---------------------+---------------------+------------+--------------------+----------+----------------+---------------+------------------+-----------+
    1 row in set (0.004 sec)
    */
    ```

## Q32 \Gオプションを用いると結果を縦に表示できることを知っていますか?

??? success
    ### \G
    - 横に長い時、見やすくなる

    ```sql
    select * from information_schema.tables 
    where table_schema="mydb" 
    and table_name="favorite_foods"\G;
    /*
    *************************** 1. row ***************************
          TABLE_CATALOG: def
            TABLE_SCHEMA: mydb
              TABLE_NAME: favorite_foods
              TABLE_TYPE: BASE TABLE
                  ENGINE: InnoDB
                VERSION: 10
              ROW_FORMAT: Dynamic
              TABLE_ROWS: 3
          AVG_ROW_LENGTH: 5461
            DATA_LENGTH: 16384
        MAX_DATA_LENGTH: 0
            INDEX_LENGTH: 0
              DATA_FREE: 0
          AUTO_INCREMENT: NULL
            CREATE_TIME: 2024-02-19 06:39:09
            UPDATE_TIME: 2024-02-19 07:06:56
              CHECK_TIME: NULL
        TABLE_COLLATION: utf8mb4_general_ci
                CHECKSUM: NULL
          CREATE_OPTIONS:
          TABLE_COMMENT:
        MAX_INDEX_LENGTH: 0
              TEMPORARY: N
        1 row in set (0.002 sec)
    */
    ```

## Q33 pagerコマンドを用いると、出力を見やすく整形(もしくは別ファイルに出力など)できることを知っていますか?

??? success
    ### pager less -SFX
    - `pager`コマンドは長い結果をページ単位で表示する
    - (あたかも、結果がパイプでlessコマンドに渡されたかのようになる)
    - 水平スクロールが有効になる
    - `-S`:画面幅より長い行を折り返さない
    - `-n`:行番号を表示しない
    - `-i`:検索時に大文字小文字を区別しない
    - `-F`:1画面でファイル全体が表示できる場合はlessを自動終了
    - `X`:端末に`termcap`初期化文字、非初期化文字を送らない（これを指定しないと、`less`コマンドの表示、終了時に画面がクリアされる）
    - 終了したい場合は、`q`を押す

    ```sql
    pager less -SFX
    -- PAGER set to 'less -SFX'

    show table status where name ="favorite_foods";
    /*
    +----------------+--------+---------+------------+------+--->
    | Name           | Engine | Version | Row_format | Rows | Av>
    +----------------+--------+---------+------------+------+--->
    | favorite_foods | InnoDB |      10 | Dynamic    |    3 |   >
    +----------------+--------+---------+------------+------+--->
    (END)
    */
    ```

    ### pager cat > /your/file/path
    - 標準出力が、別ファイルにリダイレクトされる

    ```sql
    pager cat > /mnt/c/file_test/result.txt
    -- PAGER set to 'cat > /mnt/c/file_test/result.txt'

    show table status where name ="favorite_foods";
    -- 1 row in set (0.001 sec)
    -- 出力は表示されずに指定したファイルにリダイレクトされる
    --

    ```

    ### pager grep `<search_keyword>`
    - 一致した行のみを表示する

    ```sql
    pager grep "INNODB_FT";
    -- PAGER set to 'grep "INNODB_FT"'

    show tables from information_schema;

    -- フィルタリングしているだけなので、行数は変わらない
    /*
    | INNODB_FT_CONFIG                      |
    | INNODB_FT_DELETED                     |
    | INNODB_FT_INDEX_TABLE                 |
    | INNODB_FT_INDEX_CACHE                 |
    | INNODB_FT_BEING_DELETED               |
    | INNODB_FT_DEFAULT_STOPWORD            |
    79 rows in set (0.001 sec)
    */

    ```

    ### 複数コマンドを指定することも可能

    ```sql
    pager grep "INNODB_FT" | less -SFX
    -- PAGER set to 'grep "INNODB_" | less -SFX'

    show tables from information_schema;

    /*
    | INNODB_FT_CONFIG                      |
    | INNODB_FT_DELETED                     |
    | INNODB_FT_INDEX_TABLE                 |
    | INNODB_FT_INDEX_CACHE                 |
    | INNODB_FT_BEING_DELETED               |
    | INNODB_FT_DEFAULT_STOPWORD            |
    79 rows in set (0.001 sec)
    */
    ```

    ### pager wc
    - 行数,単語数,バイト数

    ```sql
    pager wc
    -- PAGER set to 'wc'

    show tables from information_schema;
    /*
     83     243    3486
     79 rows in set (0.001 sec)
    */
    ```

    ### pager tee -a /your/file/path
    - `tee`コマンドを使って標準出力とファイル出力を同時に行う
    - `a`オプションは追記

    ```sql
    pager tee -a output.txt | less -SFX
    -- PAGER set to 'tee -a output.txt | less -SFX'

    show tables from information_schema;

    -- 標準出力も行われている
    /*
    +---------------------------------------+
    | Tables_in_information_schema          |
    +---------------------------------------+
    | ALL_PLUGINS                           |
    | APPLICABLE_ROLES                      |
    | CHARACTER_SETS                        | 
    | ...                                   | 
    */
    ``` 

    ### デフォルトに戻す

    ```sql
    nopager;
    -- PAGER set to stdout
    -- 標準出力の戻している
    ```

    ### 起動時に指定する場合

    ```bash
    $ mysql --pager="less -SFX"
    ```

    ### cat > /dev/null
    - 行数と実行時間だけを知りたい場合

    ```sql
    -- 結果はブラックホールに投げる
    pager cat > /dev/null
    -- PAGER set to 'cat > /dev/null'

    show tables from information_schema;
    -- 79 rows in set (0.001 sec)
    ```

## Q34 そもそもオプションの指定方法について理解していますか?

??? success

    ### 通常

    ```bash
    $ mysql -u root #(ショートオプション 空白文字 値)
    $ mysql -uroot #(ショートオプション 値)
    $ mysql --user root #(ロングオプション 空白 値)
    $ mysql --user=root #(ロングオプション イコール 値)
    ```
    
    ### パスワードは例外

    ```bash
    # ショートオプション 値
    # ロングオプション イコール 値
    # の形式しか取れない
    # もちろんクォートはなくてもよい
    $ mysql -u root -p"hoge"
    $ mysql -u root --password="hoge"
    ```

    ### 呼び出し時にDBも指定可能

    ```bash
    $ mysql -u root -p"hoge" <your_db> 
    # MariaDB [your_db]> 
    ```

## Q35 クエリの取り消しを行う時は\c, クエリの中断を行う時はKILLが使えることを知っていますか?

??? success
    ### \c
    - クエリを実行せずに、新しいクエリの入力を開始する

    ```sql
    MariaDB [(none)]> SELECT * FROM typo --間違えた!
        -> \c
    MariaDB [(none)]>
    ```

    ### 実行中のクエリの場合はKILLコマンド
    - 重いクエリを意図せず実行してしまったときにも役立つ

    ```sql
    show processlist\G; --実行中クエリを確認可能

    /*
    *************************** 1. row ***************************
          Id: 49
        User: <YourUserName>
        Host: localhost
          db: NULL
    Command: Query
        Time: 0
      State: starting
        Info: show processlist
    Progress: 0.000
    1 row in set (0.000 sec)
    */

    KILL 49;
    ```

## Q36 データをXMLとして取得したい場合、--xmlオプションが指定できることを知っていますか?

??? success

    ### 呼び出し時にオプション指定

    ```bash
    $ mysql -u <user_name> -p"<your_pass>" --xml <your_db>
    # MariaDB [your_db]> 
    ```

    ```sql
    select * from favorite_foods;

    /*
    <?xml version="1.0"?>

    <resultset statement="select * from favorite_foods;" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
      <row>
            <field name="person_id">1</field>
            <field name="food">cookies</field>
      </row>

      <row>
            <field name="person_id">1</field>
            <field name="food">gummy</field>
      </row>

      <row>
            <field name="person_id">1</field>
            <field name="food">pizza</field>
      </row>
    </resultset>
    3 rows in set (0.003 sec)
    */
    ```

## コラム1(先頭カンマ vs 末尾カンマ)

??? success
    ### 気持ち悪い!

    ```text
    ・先頭カンマは気持ち悪い。当初はそう感じた

    ・実は今もそう感じている

    ・しかし、下記の例から見ても、先頭カンマの方が
      ミスが少なくなることは事実だろう（この事実は
      統計的にも示されている）

    ・先頭カンマは、カラムの削除/追加、カンマの見落としを
      避けるという点で優れている

    ・気持ち悪いと感じるのは、それだけカンマに注目できている
      事を意味するのかもしれない。

    ・実際、末尾カンマを書いていたころは、gitのdiffが2行に
      なることにイライラしていたが、それがなくなった
      
    ・ただし、カラム -> , より、スペース->カンマ->カラムの
      方が時間がかかり面倒な気がする。（特に練習している時は

    ・結果として、見落としが（比較的）許されない開発環境では
      先頭カンマを用い、SQLの勉強中や、あまり注意力を必要と
      しない場合は、簡単のため末尾カンマを使った方がいい
      という結論に達した

    ・手続き型言語のように、最後の項の末尾にカンマを
      置ければいいのにと感じている

    ・このチュートリアルでは末尾カンマを用いることにした
    ・（理由は、前述のとおり）
    ・決して自分の（愚かな）美的センスのためではない()
    ```

    ```sql

    select
      f.film_id
      , f.title
      , f.description
      -- , f.release_year
      -- , f.language_id
      -- , f.original_language_id
      , f.rental_duration
      , f.rental_rate
      , f.length
      -- , f.replacement_cost
      -- , f.ration
      -- , f.special_features
      , f.last_update
      , a.first_name
      , a.last_name
    from film f
    inner join film_actor fa
      on f.film_id = fa.film_id
    inner join actor a
      on fa.actor_id = a.actor_id
    ;
    ```