# クイズ(6:データ操作)

## Q59 データの操作/変換/操作に用いるコマンドが標準の規格に含まれていないことを知っていますか?

??? success
    ### 組み込み関数

    ```text
    ・データの生成、変換、操作のすべてが、
    　標準規格に含まれているわけではない
    ・代わりに組み込み関数を用いる
    ```

## Q60 文字列データを生成/挿入する方法を知っていますか?(モード, char, quote, ascii)

??? success
    ### 説明のため、以下のテーブルを用いる

    ```sql
    create temporary table string_tbl
    (
      char_fld char(30),
      vchar_fld varchar(30),
      text_fld text
    );
    ```

    ### 文字列を挿入する

    ```sql
    -- 文字列を引用符で囲むのが基本
    insert into string_tbl (char_fld, vchar_fld, text_fld)
    values 
    (
      "This is char data", 
      "This is varchar data",
      "This is text data"
    );
    -- Query OK, 1 row affected (0.006 sec)
    ```

    ### 挿入時の注意点

    ```sql
    -- 文字列の長さが、最大サイズを超えると例外が送出される
    update string_tbl
    set vchar_fld = "This is a piece of extremely long varchar data";
    -- ERROR 1406 (22001): 
    -- Data too long for column 'vchar_fld' at row 1
    ```

    ### strictモード

    ```text
    ・現在の、Mysql/mariadbのデフォルトのふるまいは
      「strict」モード
    ・問題が起きた場合は例外を送出する

    ・一方古いバージョンでは、文字列を切り捨てて警告を
      生成していた
    ・これは「ANSI」モードと呼ばれている
    ```

    ### モードを更新する

    ```sql
    -- 現在のモードを見る

    select @@session.sql_mode;
    /*
    +-------------------------------------------------------------------------------------------+
    | @@session.sql_mode                                                                        |
    +-------------------------------------------------------------------------------------------+
    | STRICT_TRANS_TABLES,ERROR_FOR_DIVISION_BY_ZERO,NO_AUTO_CREATE_USER,NO_ENGINE_SUBSTITUTION |
    +-------------------------------------------------------------------------------------------+
    1 row in set (0.001 sec)
    */

    -- ansiモードに変更(sessionのモードだけが変わるので再起動で戻る)
    set sql_mode = "ansi"

    -- 再度モード確認
    /*
    +-------------------------------------------------------------+
    | @@session.sql_mode                                          |
    +-------------------------------------------------------------+
    | REAL_AS_FLOAT,PIPES_AS_CONCAT,ANSI_QUOTES,IGNORE_SPACE,ANSI |
    +-------------------------------------------------------------+
    1 row in set (0.000 sec)
    */

    -- ansiモードで、最大長を超える文字列を挿入
    update string_tbl 
    set vchar_fld = 'This is a piece of extremeley long varchar data';
    -- Query OK, 1 row affected, 1 warning (0.009 sec)
    -- Rows matched: 1  Changed: 1  Warnings: 1

    -- 警告が表示されているので、警告を見る
    show warnings;
    /*
    +---------+------+------------------------------------------------+
    | Level   | Code | Message                                        |
    +---------+------+------------------------------------------------+
    | Warning | 1265 | Data truncated for column 'vchar_fld' at row 1 |
    +---------+------+------------------------------------------------+
    1 row in set (0.000 sec)
    */

    -- 切り捨てられた挿入が為されているか確認
    select vchar_fld from string_tbl;
    /*
    +--------------------------------+
    | vchar_fld                      |
    +--------------------------------+
    | This is a piece of extremeley  |
    +--------------------------------+
    1 row in set (0.001 sec)
    */
    ```

    ### モードは変更するべきか

    ```text
    ・切り捨てだけのことを考えるならしないべき

    ・varchar型の場合、列の最大サイズに設定しても、
      データベースサーバは文字列を格納するのに十分な領域
      しか使わないので、無駄になる心配はない
    
    ・特に、varchar(255)以下の場合、データベースは新たな
      領域を確保しないので、一時テーブルなどを用いていない
      場合パフォーマンスに有意な差は出ない
      https://stackoverflow.com/questions/1962310/importance-of-varchar-length-in-mysql-table
    
    ・ただし、varcharの列を結合やインデックスに使用する場合は
    Indexサイズの増大が見られるため、注意する必要がある
    validationの観点から言えば、短いに越したことはない
    ```

    ### クォートを含む文字列を挿入する(escape)

    ```text
    ・文字列の終わりとして扱われないようにescapeする
    ・MySQL, MariaDB, Oracleではバックスラッシュを挿入
    ・また、シングルクォートの直前に、シングルクォートを追加　するとエスケープ扱いになる
    ・なお、ダブルクォートの中ではシングルが使え、
    　シングルの中ではダブルがエスケープなしで使える
    ```

    ```sql
    -- no escape
    update string_tbl
    set text_fld = "It's a string."

    -- single single
    update string_tbl
    set text_fld = 'It''s a string.'
    ;

    -- back-slash single
    update string_tbl
    set text_fld = 'It\'s a string.';

    select text_fld from string_tbl;
    /*
    +----------------+
    | text_fld       |
    +----------------+
    | It's a string. |
    +----------------+
    1 row in set (0.001 sec)
    */
    ```

    ### エスケープを表示する場合

    ```text
    ・取得する場合、通常文字列は表示されない
    ・表示した場合、組み込み関数quote()が使える
    ```

    ```sql
    -- なお、""の中に'を入れた場合も、以下の様に表示される
    select quote(text_fld)
    from string_tbl;
    /*
    +-------------------+
    | quote(text_fld)   |
    +-------------------+
    | 'It\'s a string.' |
    +-------------------+
    1 row in set (0.000 sec)
    */
    ```

    ### 特殊な文字を含む文字列を扱いたい場合

    ```text
    char
      ・ASCII文字セットの255文字から文字列を構築する
    ```

    !!! waring
        - 日本語のwindows環境では、コマンドシェルのコードページがデフォルトで932(shift-jis)になっているので、ドイツ語などを表示したい場合は、文字化けしないよう437に変更する必要がある

        ```text
        $ chcp 437
        ```

        - wslの場合は、以下の様にする

        ```text
        $ chcp.com
        現在のコード　ページ: 932

        $ chcp.com 65001
        Active code page: 65001

        なお、私の環境の場合、chcp.comは932で固定されて
        しまい、変わらなかった。解決法求ム
        ```

    ```sql
    -- char関数は、ASCII文字セットの255文字から文字列を構築
    select 
      "abcdefg", 
      char(97,98,99,100,101,102,103)
    ;
    /*
    +---------+--------------------------------+
    | abcdefg | char(97,98,99,100,101,102,103) |
    +---------+--------------------------------+
    | abcdefg | abcdefg                        |
    +---------+--------------------------------+
    1 row in set (0.002 sec)
    */
    ```

    ```sql
    -- concat関数を用いて連結する
    select concat("Let", char(39), "s");

    /*
    +------------------------------+
    | concat("Let", char(39), "s") |
    +------------------------------+
    | Let's                        |
    +------------------------------+
    1 row in set (0.000 sec)
    */
    ```

    ```sql
    -- 特定の文字がASCIIのどの文字に相当するのか
    -- 確かめたいとき

    SELECT ASCII('a');
    /*
    +------------+
    | ASCII('a') |
    +------------+
    |         97 |
    +------------+
    1 row in set (0.001 sec)
    */
    ```

## Q61 文字列を操作する方法を知っていますか?

??? info
    ### 一覧

    ```text
    length:     文字数把握
    position:   部分文字列のindex
    locate:     部分文字列のindex(検索位置の指定可能)
    like:       select文で条件文
    concat:     末尾追加、カラム連結
    insert:     挿入/置き換え
    substring:  部分文字列切り出し
    ```

??? success
    ### 使用するデータ
    - Q60で作成したテーブル

    ### length関数(文字数を把握する)

    ```sql
    -- length関数は、文字列に含まれる文字の個数を返す

    select 
      length(char_fld) char_length,
      length(vchar_fld) varchar_length,
      length(text_fld) text_length
    from string_tbl;

    /*
    +-------------+----------------+-------------+
    | char_length | varchar_length | text_length |
    +-------------+----------------+-------------+
    |          17 |             20 |          17 |
    +-------------+----------------+-------------+
    1 row in set (0.001 sec)
    */
    ```

    !!! warning
        - char型の列の末尾はスペースで埋められる
        - しかし、`MySQL/MariaDB`サーバは、`char`型の列からデータを取り出すとき、末尾のスペースを取り除く


    ### position(部分文字列の位置を把握する)

    ```sql
    -- 初めに見つかった部分文字列のindexを返す
    select position("varchar" in vchar_fld)
    from string_tbl;

    /*
    +----------------------------------+
    | position("varchar" in vchar_fld) |
    +----------------------------------+
    |                                9 |
    +----------------------------------+
    1 row in set (0.002 sec)
    */

    select position("not found" in vchar_fld)
    from string_tbl;
    /*
    +------------------------------------+
    | position("not found" in vchar_fld) |
    +------------------------------------+
    |                                  0 |
    +------------------------------------+
    1 row in set (0.000 sec)
    */
    ```

    !!! warning
        - 手続き型のよくある言語と異なり、indexが1から始まっていることに注意
        - つまり、SQLの場合1文字目で見つかれば出力は1。見つからない場合が0となる

    ### locate(検索の開始位置を指定する)

    ```sql
    -- isは6文字目にあるので、見つからない
    -- なお、出発点を変えても、indexは変わらない
    -- たとえば6文字目から検索を開始した場合、今回の出力は6
    select locate("is", vchar_fld, 7)
    from string_tbl;

    /*
    +----------------------------+
    | locate("is", vchar_fld, 7) |
    +----------------------------+
    |                          0 |
    +----------------------------+
    1 row in set (0.000 sec)
    */
    ```

    ### strcmp(ソート順を返す)

    ```sql
    -- 1つ目の文字列 < 2つ目の文字列なら -1
    -- 1つ目の文字列 = 2つ目の文字列なら  0
    -- 1つ目の文字列 > 2つ目の文字列なら  1
    -- 通常の照合順序では大文字小文字を区別しないことに注意
    select 
      strcmp("abcd", "xyz") abcd_xyz,
      strcmp("abcd", "B") abcd_b,
      strcmp("xyz", "12345") xyz_12345;

    /*
    +----------+--------+-----------+
    | abcd_xyz | abcd_b | xyz_12345 |
    +----------+--------+-----------+
    |       -1 |     -1 |         1 |
    +----------+--------+-----------+
    1 row in set (0.000 sec)
    */
    ```

    ### select文でLIKE, REGEXPを使う

    ```sql
    -- 条件に合致していればtrue, 誤っていればfalseとなる
    select name, name LIKE "%Y" ends_in_y
    from category;

    /*
    +-------------+-----------+
    | name        | ends_in_y |
    +-------------+-----------+
    | Action      |         0 |
    | Animation   |         0 |
    | Children    |         0 |
    | Classics    |         0 |
    | Comedy      |         1 |
    | Documentary |         1 |
    | Drama       |         0 |
    | Family      |         1 |
    | Foreign     |         0 |
    | Games       |         0 |
    | Horror      |         0 |
    | Music       |         0 |
    | New         |         0 |
    | Sci-Fi      |         0 |
    | Sports      |         0 |
    | Travel      |         0 |
    +-------------+-----------+
    16 rows in set (0.023 sec)
    */

    select name, name regexp "[insy]$" ends_in_insy
    from category

    /*
    +-------------+--------------+
    | name        | ends_in_insy |
    +-------------+--------------+
    | Action      |            1 |
    | Animation   |            1 |
    | Children    |            1 |
    | Classics    |            1 |
    | Comedy      |            1 |
    | Documentary |            1 |
    | Drama       |            0 |
    | Family      |            1 |
    | Foreign     |            1 |
    | Games       |            1 |
    | Horror      |            0 |
    | Music       |            0 |
    | New         |            0 |
    | Sci-Fi      |            1 |
    | Sports      |            1 |
    | Travel      |            0 |
    +-------------+--------------+
    16 rows in set (0.029 sec)
    */
    ```

    ### concat(末尾に語句を追加する)

    ```sql
    select text_fld from string_tbl;

    /*
    +-------------------+
    | text_fld          |
    +-------------------+
    | This is text data |
    +-------------------+
    1 row in set (0.000 sec)
    */

    update string_tbl
    set text_fld = concat(text_fld, ", please");
    -- Query OK, 1 row affected (0.002 sec)
    -- Rows matched: 1  Changed: 1  Warnings: 0

    select text_fld from string_tbl;

    /*
    +---------------------------+
    | text_fld                  |
    +---------------------------+
    | This is text data, please |
    +---------------------------+
    1 row in set (0.000 sec)
    */
    ```

    ### concat(連結して文章生成)

    ```sql
    -- 引数に入れた数値型や日付型は文字列型に変換される
    select 
    concat(
      first_name, 
      " ", 
      last_name,
      "has been a customer since ",
      date(create_date)
      ) cust_narrative
    from customer
    limit 5;

    /*
    +------------------------------------------------------+     
    | cust_narrative                                       |     
    +------------------------------------------------------+     
    | MARY SMITHhas been a customer since 2006-02-14       |     
    | PATRICIA JOHNSONhas been a customer since 2006-02-14 |     
    | LINDA WILLIAMShas been a customer since 2006-02-14   |     
    | BARBARA JONEShas been a customer since 2006-02-14    |     
    | ELIZABETH BROWNhas been a customer since 2006-02-14  |     
    +------------------------------------------------------+     
    5 rows in set (0.001 sec)
    */
    ```

    ### insert(末尾以外の追加/置き換え)

    ```sql
    /*
    insert(
      元の文字列, 
      置き換えの開始位置, 
      置き換える文字の個数, 
      新しい文字列
    )
    */

    -- 9文字目から置き換える文字は0なので、=挿入
    select insert("goodbye world", 9, 0, "cruel ") string;

    /*
    +---------------------+
    | string              |
    +---------------------+
    | goodbye cruel world |
    +---------------------+
    1 row in set (0.006 sec)
    */

    -- 置き換え(繰り返すがindexは1始まり)
    select insert("goodbye world", 1, 7, "hello") string;

    /*
    +-------------+
    | string      |
    +-------------+
    | hello world |
    +-------------+
    1 row in set (0.000 sec)
    */
    ```

    ### substring(部分文字列を取り出す)

    ```sql
    -- position, locateは部分文字列の位置を返す
    -- substringは実際に取り出す
    -- substrと書いてもいい

    select substring("goodbye cruel world", 9, 5) string;

    /*
    +--------+
    | string |
    +--------+
    | cruel  |
    +--------+
    1 row in set (0.000 sec)
    */

    -- マイナスなら末尾からの検索になる
    -- 第三引数が無いなら末尾まで
    select substr("barbar", -3, 3);
    /*
    +-------------------------+
    | substr("barbar", -3, 3) |
    +-------------------------+
    | bar                     |
    +-------------------------+
    1 row in set (0.000 sec)
    */

    select substr("neko", 1);
    /*
    +-------------------+
    | substr("neko", 1) |
    +-------------------+
    | neko              |
    +-------------------+
    1 row in set (0.000 sec)
    */
    ```

## Q62 数値データを操作する方法を知っていますか?

??? success
    ### 一覧

    ```text
    ・算術演算(e.g. div(), pow(), sqrt(), mod(), 
    greatest(), least(), sin(), cos(), tan(),
    radians(), pi(), -, conv(), rand())
    ・精度(floor(), ceil(), round(), truncate())
    ・符号(abs(), sign())
    ```
    ### 入力するだけ(基本)

    ```sql
    select (37 * 59) / (60 - (12 * 6)) ans;

    /*
    +-----------+
    | ans       |
    +-----------+
    | -181.9167 |
    +-----------+
    1 row in set (0.001 sec)
    */
    ```

    ### 問題点

    ```text
    ・数値型の列の最大サイズを超えると、数値が丸められる
    ```

    ### 算術演算用の関数について

    ```sql
    -- [-]：引数の符号を変更
    select - category_id from category limit 5;

    /*
    +---------------+
    | - category_id |
    +---------------+
    |            -1 |
    |            -2 |
    |            -3 |
    |            -4 |
    |            -5 |
    +---------------+
    5 rows in set (0.000 sec)
    */

    -- 整数除算
    select 10 div 3;
    /*
    +----------+
    | 10 div 3 |
    +----------+
    |        3 |
    +----------+
    1 row in set (0.001 sec)
    */

    -- 余り
    -- 実数でもいいらしい。すごい
    select mod(10.2, 4);

    /*
    +--------------+
    | mod(10.2, 4) |
    +--------------+
    |          2.2 |
    +--------------+
    1 row in set (0.001 sec)
    */

    -- 累乗
    select pow(2,8);

    /*
    +----------+
    | pow(2,8) |
    +----------+
    |      256 |
    +----------+
    1 row in set (0.000 sec)
    */

    -- 平方根
    select sqrt(2);
    /*
    +--------------------+
    | sqrt(2)            |
    +--------------------+
    | 1.4142135623730951 |
    +--------------------+
    1 row in set (0.000 sec)
    */

    -- 最大値(文字列でも可能。nullが1つでも含まれていると
    -- 返り値はnullになる)
    select greatest(3,6,5);
    /*
    +-----------------+
    | greatest(3,6,5) |
    +-----------------+
    |               6 |
    +-----------------+
    1 row in set (0.000 sec)
    */

    -- 最小値
    select least(3,6,5);
    /*
    +--------------+
    | least(3,6,5) |
    +--------------+
    |            3 |
    +--------------+
    1 row in set (0.000 sec)
    */

    -- 自然対数 e^xを求める
    select exp(1);

    /*
    +-------------------+
    | exp(1)            |
    +-------------------+
    | 2.718281828459045 |
    +-------------------+
    1 row in set (0.000 sec)
    */

    -- sin, cos, tan, piなど
    -- asin, acos, atan等も可能
    -- radians, degreesで相互変換も可能
    select
      sin(pi() / 2),
      cos(0),
      tan(pi() / 4)
    ;

    /*
    +---------------+--------+--------------------+
    | sin(pi() / 2) | cos(0) | tan(pi() / 4)      |
    +---------------+--------+--------------------+
    |             1 |      1 | 0.9999999999999999 |
    +---------------+--------+--------------------+
    1 row in set (0.000 sec)
    */

    select round(sin(radians(30)), 2) sin30;

    /*
    +-------+
    | sin30 |
    +-------+
    |  0.50 |
    +-------+
    1 row in set (0.001 sec)
    */

    -- 自然対数
    select ln(2) loge2;
    /*
    +--------------------+
    | loge2              |
    +--------------------+
    | 0.6931471805599453 |
    +--------------------+
    1 row in set (0.000 sec)
    */

    -- 基数変換
    select conv("a", 16, 2);
    /*
    +------------------+
    | conv("a", 16, 2) |
    +------------------+
    | 1010             |
    +------------------+
    1 row in set (0.001 sec)
    */

    -- 疑似乱数(0 <= v < 1)
    select rand();
    /*
    +--------------------+
    | rand()             |
    +--------------------+
    | 0.7692554488491783 |
    +--------------------+
    1 row in set (0.001 sec)
    */
    ```

    ### 精度を制御する

    ```text
    ・最大の精度は型で決まる
    ・ceil, floor, round, truncateを使って表示する
      精度を制限可能
    ```

    ```sql
    -- 整数への切り上げ、切り捨て
    select ceil(72.445), floor(72.455);

    /*
    +--------------+---------------+
    | ceil(72.445) | floor(72.455) |
    +--------------+---------------+
    |           73 |            72 |
    +--------------+---------------+
    1 row in set (0.005 sec)
    */

    -- round(実数, 精度): 四捨五入
    select round(72.0909, 3);

    /*
    +-------------------+
    | round(72.0909, 3) |
    +-------------------+
    |            72.091 |
    +-------------------+
    1 row in set (0.000 sec)
    */

    -- truncate(実数, 精度): 小数点以下を削除
    -- 負の値の場合も、同じように小数点以下を削除する
    select truncate(9.99, 1);
    /*
    +-------------------+
    | truncate(9.99, 1) |
    +-------------------+
    |               9.9 |
    +-------------------+
    1 row in set (0.000 sec)
    */

    -- 精度に対して負の値を指定する
    -- 整数の位を丸める
    select 
      round(178, -2),
      round(178, -1),
      truncate(178, -2)
    ;

    /*
    +----------------+----------------+-------------------+      
    | round(178, -2) | round(178, -1) | truncate(178, -2) |      
    +----------------+----------------+-------------------+      
    |            200 |            180 |               100 |      
    +----------------+----------------+-------------------+      
    1 row in set (0.000 sec)
    */
    ```

    ### 符号付きのデータを扱う

    ```text
    ・abs：絶対値
    ・sign: 負なら-1, 正なら1, 0なら0を返す
    ```

    ```sql
    create temporary table account(
      account_id int,
      acct_type varchar(30),
      balance double(10,5)
    );

    insert into account
    (account_id, acct_type, balance)
    values
    (123, "MONEY MARKET", 785.22),
    (456, "SAVINGS", 0.00),
    (789, "CHECKING", -324.22)
    ;

    select
      account_id, sign(balance), abs(round(balance, 2))
    from account;

    /*
    +------------+---------------+------------------------+      
    | account_id | sign(balance) | abs(round(balance, 2)) |      
    +------------+---------------+------------------------+      
    |        123 |             1 |                 785.22 |      
    |        456 |             0 |                   0.00 |      
    |        789 |            -1 |                 324.22 |      
    +------------+---------------+------------------------+      
    3 rows in set (0.001 sec)
    */
    ```

    ### seq(連番を作成する)

    ```sql
    -- 連番を作成できる
    -- seq_start_to_end
    select * from seq_1_to_3;

    /*
    +-----+
    | seq |
    +-----+
    |   1 |
    |   2 |
    |   3 |
    +-----+
    3 rows in set (0.001 sec)
    */

    -- 3000の倍数を考える
    select *
    from seq_1_to_10000
    where 
      seq mod 3000 = 0;

    /*
    +------+
    | seq  |
    +------+
    | 3000 |
    | 6000 |
    | 9000 |
    +------+
    3 rows in set (0.002 sec)
    */
    ```

## Q63 タイムゾーンを変更する方法を知っていますか?

??? success
    ### 現在時(UTC)やタイムゾーンの情報を取得

    ```text
    協定世界時（Coordinaetd Universal Time：UTC）
      ・高精度な200個の原子時計の平均
      ・UTCタイムスタンプを取得するための関数は
        utc_timestamp
    
    グリニッジ標準時(Grennwith Mean Time: GMT)
      ・イギリスのグリニッジの時刻のこと
      ・他のタイムゾーンはすべてGMTからの時差で表す
    
    タイムゾーンについて
      ・ほとんどのデータサーバはデフォルトで設置場所の
        タイムゾーンを用いる
      ・たとえば、証券取引情報ならUTCを使うのが望ましい
      ・小売店の取引情報なら、朝夜の売れ行きを観察する
        ためにローカルゾーンがいいかもしれない
    
    @@global.time_zone
      グローバルタイムゾーン。固定
    
    @@session.time_zone
      セッションタイムゾーン。ログインするユーザごとに異なる
    ```

    ```sql
    -- utcタイムスタンプの取得
    select utc_timestamp;
    /*
    +---------------------+
    | utc_timestamp       |
    +---------------------+
    | 2024-03-10 06:29:33 |
    +---------------------+
    1 row in set (0.007 sec)
    */

    -- timezoneを表示する
    -- SYSTEMは、このデータベースがインストールされている
    -- サーバのタイムゾーン設定を使うことを意味する
    select @@global.time_zone, @@session.time_zone;

    /*
    +--------------------+---------------------+
    | @@global.time_zone | @@session.time_zone |
    +--------------------+---------------------+
    | SYSTEM             | SYSTEM              |
    +--------------------+---------------------+
    1 row in set (0.000 sec)
    */
    ```

    ### timezoneを変更する

    ```text
    変更するにはtimezoneテーブルを読み込む必要がある
      ・インストール時にタイムゾーン用のテーブルは作られるが
        読み込まれてはいないため
      
      $ mysql_tzinfo_to_sql /usr/share/zoneinfo | \
        mysql -u<username> -p mysql

      ⇒これで、mysql.time_zoneテーブルにタイムゾーンが
        読み込まれるので、ログインして確認
    ```

    ```sql
    select * from mysql.time_zone limit 5;
    -- emptyでなければOK
    /*
    +--------------+------------------+
    | Time_zone_id | Use_leap_seconds |
    +--------------+------------------+
    |            1 | N                |
    |            2 | N                |
    |            3 | N                |
    |            4 | N                |
    |            5 | N                |
    +--------------+------------------+
    5 rows in set (0.005 sec)
    */

    -- 現在のタイムゾーンは以下で得られる
    -- 今回の場合、SYSTEMがJSTなので、
    -- globalでもsessionでも
    -- 日本標準時が使われていることが分かる
    show variables like "%time_zone%";
    /*
    +------------------+--------+
    | Variable_name    | Value  |
    +------------------+--------+
    | system_time_zone | JST    | 
    | time_zone        | SYSTEM |
    +------------------+--------+
    2 rows in set (0.001 sec)
    */

    -- ここで現在時を見る
    select current_time();

    /*
    +----------------+
    | current_time() |
    +----------------+
    | 15:51:57       |
    +----------------+
    1 row in set (0.000 sec)    
    */

    -- sessionタイムゾーンを変更する
    set time_zone = "Europe/Zurich"

    -- もう一度、現在時を見る
    select current_time();
    /*
    +----------------+
    | current_time() |
    +----------------+
    | 07:52:24       |
    +----------------+
    1 row in set (0.000 sec)
    */

    -- sessionタイムゾーンも変更されている
    select @@session.time_zone;
    /*
    +---------------------+
    | @@session.time_zone |
    +---------------------+
    | Europe/Zurich       |
    +---------------------+
    1 row in set (0.000 sec)
    */
    ```

## Q64 時間データを生成/操作する方法を知っていますか?

??? success
    ### 方法一覧

    ```text
    ・既存のdate, datetime, time型の列データをコピー
    ・組み込み関数で作成
    ・文字列表現で作成
    ```

    ### date, datetime, time等のフォーマットについて

    ```text
    ・クイズ2を参照のこと
    ・ただし、mysql/mariadbはセパレータにかなり寛容で、
      2019/09/17 15:30:30や
      2019,09,17,15,30,00
      20190917153000
      等も許される
    ```

    ### datetime型が渡されることが予期されている場合

    ```text
    ・データサーバは文字列を自動的にパースする
    ```

    ### datetime型が渡されることをサーバが予期していない場合

    ```sql
    -- CAST関数を使って、変換する
    -- ただし、フォーマットを守る必要がある

    select cast("2024-03-10 15:30:00" as datetime) d;
    /*
    +---------------------+
    | d                   |
    +---------------------+
    | 2024-03-10 15:30:00 |
    +---------------------+
    1 row in set (0.005 sec)
    */

    -- time型やdate型でも同じ
    select 
      cast("2019-09-17" as date) date_filed,
      cast("108:12:35" as time) time_filed;

    /*
    +------------+------------+
    | date_filed | time_filed |
    +------------+------------+
    | 2019-09-17 | 108:12:35  |
    +------------+------------+
    1 row in set (0.001 sec)
    */
    ```

    ### 文字列のフォーマットを指定する

    ```text
    str_to_date(文字列, フォーマット)
      ・第二引数でフォーマットを定義することで、
        フォーマット文字列の内容に応じて
        datetime, date, timeが返される
    
    ※詳細についてはQ30を参照のこと
    ```

    ```sql
    -- フォーマットが不正な場合はnullとなる
    select 
      str_to_date(
        "March-10-2011 23:24:35",
        "%M-%d-%Y %H:%i:%s"
      ) as d;
    /*
    +---------------------+
    | d                   |
    +---------------------+
    | 2011-03-10 23:24:35 |
    +---------------------+
    1 row in set (0.000 sec)
    */
    ```

    ### 現在日時を知りたい場合

    ```sql
    -- current_date(): 日時
    -- current_time(): 時間
    -- current_timestamp(): 日時 + 時間を返す

    select
      current_date() date,
      current_time() time,
      current_timestamp() date_time
    ;

    /*
    +------------+----------+---------------------+
    | date       | time     | date_time           |
    +------------+----------+---------------------+
    | 2024-03-10 | 16:21:09 | 2024-03-10 16:21:09 |
    +------------+----------+---------------------+
    1 row in set (0.001 sec)
    */
    ```

    ### date_add, date_sub(時間を足し引きする)

    ```text
    date_add
      指定した日付に期間を足す
    
    date_sub
      指定した日付から期間を引く
    
    interval
      上記の関数の第二引数に指定
      interval 5 dayの場合、5日間を意味する
      year, month, day, hour, minute, secondがある

      minute_second
        コロンと区切られた分数と秒数
      hour_second
        コロンと区切られた時：分：秒
      year_month
        ハイフンで区切られた年数と月数を利用することもできる
    ```

    ```sql
    -- 足し算
    select
      date_add(current_date(), interval 1 month) d;

    /*
    +------------+
    | d          |
    +------------+
    | 2024-04-10 |
    +------------+
    1 row in set (0.000 sec)
    */

    -- 引き算
    select
      date_sub(current_time(), interval 1 hour) d;
  
    /*
    +----------+
    | d        |
    +----------+
    | 15:26:44 |
    +----------+
    1 row in set (0.000 sec)
    */

    -- hour_second
    -- たとえば特定のカラムから1時間25分10秒引く場合

    select 
      date_sub(
        current_time(), 
        interval "1:25:10" hour_second
      ) d;

    /*
    +----------+
    | d        |
    +----------+
    | 15:03:45 |
    +----------+
    1 row in set (0.000 sec)
    */
    ```

    ### last_day(月末の日付を突き止めたい場合)

    ```sql
    -- last_day("日付")を使う
    -- その日付が含まれる月の最終日が得られる
    select last_day("2024-02-01");

    /*
    +------------------------+
    | last_day("2024-02-01") |
    +------------------------+
    | 2024-02-29             |
    +------------------------+
    1 row in set (0.000 sec)
    */
    ```

    ### dayname(特定の日付の曜日を割り出す場合)

    ```sql
    select dayname("2024-03-10");

    /*
    +-----------------------+
    | dayname("2024-03-10") |
    +-----------------------+
    | Sunday                |
    +-----------------------+
    1 row in set (0.019 sec)
    */
    ```

    ### extract(時刻データから特定の情報だけ取り出す場合)

    ```sql
    select 
      extract(year from current_timestamp()) y,
      extract(month from current_timestamp()) m,
      extract(day from current_timestamp()) d,
      extract(hour from current_timestamp()) h,
      extract(minute from current_timestamp()) min,
      extract(second from current_timestamp()) s
    ;

    /*
    +------+------+------+------+------+------+
    | y    | m    | d    | h    | min  | s    |
    +------+------+------+------+------+------+
    | 2024 |    3 |   10 |   16 |   39 |    2 |
    +------+------+------+------+------+------+
    1 row in set (0.000 sec)
    */
    ```

    ### datediff(2つの日付データの期間を取得したい場合)

    ```sql
    -- 前から後を引くことに注意
    -- 時刻については無視される
    select
      datediff("2025/03/10", "2024/03/10") y;

    /*
    +------+
    | y    |
    +------+
    |  365 |
    +------+
    1 row in set (0.000 sec)
    */
    ```

## Q65 型変換を行う方法を知っていますか(cast)

??? success
    ### cast関数

    ```text
    ・cast(値or式 as 変換先の型)
    ・文字列を数値に変換する際、
      たとえば999AA11の場合、999となる。
      errorにはならないが警告が発せられる
      A123のような、1文字目から数値でない場合0となる
    ・時刻型に変換する際、デフォルトの
      フォーマットでないならstr_to_dateを代わりに利用する
    ```

    ```sql
    select cast("123AA12" as unsigned integer) as uint;

    /*
    +------+
    | uint |
    +------+
    |  123 |
    +------+
    1 row in set, 1 warning (0.000 sec)
    */

    show warnings;
    -- 完全に数値型にはできなかったので以下のエラーが出る
    /*
    +---------+------+----------------------------------------------+
    | Level   | Code | Message                                      |
    +---------+------+----------------------------------------------+
    | Warning | 1292 | Truncated incorrect INTEGER value: '123AA12' |
    +---------+------+----------------------------------------------+
    1 row in set (0.000 sec)
    */
    ```