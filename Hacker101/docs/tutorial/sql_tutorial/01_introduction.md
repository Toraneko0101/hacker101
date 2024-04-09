# クイズ(1:導入)

## Q0 前置き

??? info
    ### 概要
    - このチュートリアルでは`SQL`について扱います

    ### 環境
    - `DB`として`MariaDB`
    - 環境として`Linux`を用います

    ### 注意書き
    - `Windows`や`mac`等を用いている場合
    - `MariaDB`以外の`DB`を用いている場合は読み替えてください
    - 特段注意がない場合、`MariaDB`での挙動について説明しています

    !!! warning
        ### MySQLとMariaDBについて
        - `MariaDB`がおそらく将来も`OSS`であり、
        - 新機能の追加に重点を当てており、
        - またパフォーマンスも上回っていると思われる
        - ことから、今回はこちらを採用しました
        - `MySQL`でもほぼ同等の操作が行えます

## Q1 DBのインストール方法が分かりますか?

??? success

    ### 1. MariaDBの存在を確認する

    - 存在していた場合は、Q1スキップ

    ```bash
    $ mysql --version
    mysql  Ver 15.1 Distrib 10.6.16-MariaDB, for debian-linux-gnu (x86_64) using  EditLine wrapper
    ```

    - 存在していなかった場合は、2に進む

    ```bash
    $ mysql --version
    -bash: /usr/bin/mysql: そのようなファイルやディレクトリはありません
    ```

    ### 2. MariaDBをインストール

    ```bash
    # パッケージリストを更新して、最新のパッケージ情報を入手
    $ sudo apt update
    $ sudo apt upgrade

    # MariaDBサーバーのインストール。
    $ sudo apt install mariadb-server

    # Mariadbサービスの起動と自動起動の設定
    $ sudo systemctl start mariadb
    $ sudo systemctl enable mariadb
    ```

    !!! warning
        ### エラーに対処する

        ```bash
        # インストール時に何らかの要因でデータディレクトリが壊れている場合
        $ sudo mysql --initialize --user=mysql -datadir=/var/lib/mysql

        # --initialize: データベースシステムを再初期化
        # --user=mysql MySQLサーバがデータベースを操作する際に使用するユーザ
        # --ldata=/var/lib/mysql: Mysqlのデータファイルが格納されるディレクトリを指定
        # (--datadir=/var/lib/mysqlとしてもよい)

        # こちらは古い書き方
        $ sudo mysql_install_db --user=mysql --ldata=/var/lib/mysql
        
        ```
    
    ### 3. MariaDBのセキュリティ設定を行う
    - rootパスワードの変更
    - 匿名ユーザの削除
    - テストデータベースの削除
    - 等を行う

    ```bash
    $ sudo mysql_secure_installation

    #まだrootのパスワードを設定していないのでEnterキーを押す
    If you've just installed MariaDB, and haven't set the root password yet, you should just press enter here.
    

    # unix認証に切り替えたい場合はy。今回は簡単のためnとした
    Switch to unix_socket authentication [Y/n] n
    ... skipping.

    # ルートのパスワードを設定する
    Change the root password? [Y/n] Y
    New password: <Your password>
    Re-enter new password: <Your password>
    Password updated successfully!
    Reloading privilege tables..
    ... Success!

    # 匿名ユーザを削除
    Remove anonymous users? [Y/n] Y
    ... Success!


    # リモートからrootログインは許可したくないのでY
    Disallow root login remotely? [Y/n] Y
    ... Success!


    # testデータベースを削除する
    Remove test database and access to it? [Y/n] Y
    - Dropping test database...
    ... Success!
    - Removing privileges on test database...
    ... Success!


    # 上記の変更を今すぐ適用するか
    Reload privilege tables now? [Y/n] Y
    ... Success!

    Cleaning up...

    All done!  If you've completed all of the above steps, your MariaDB
    installation should now be secure.
    ```

    ### 4. ユーザを作成する

    - ルートでログイン

    ```bash
    # rootは普段使いしたくないので
    $ mysql -u root -p
    MariaDB >
    ```

    ```sql
    -- 新しいユーザを作成する
    CREATE USER 'newuser'@'localhost' IDENTIFIED BY 'password';
    -- すべてのDBにアクセスするための権限を付与
    GRANT ALL PRIVILEGES ON *.* TO 'newuser'@'localhost';
    -- 変更を有効にする
    FLUSH PRIVILEGES;
    -- MariaDB > exit でログアウト
    ```

    - 作成したユーザでログイン

    ```bash
    $ mysql -u 'newuser'@'localhost' -p
    MariaDB >
    ```

    !!! warning
        - ユーザを削除したい場合

        ```sql
        -- $ sudo mysql -u root -p
        DROP USER 'username'@'localhost';
        FLUSH PRIVILEGES;
        ```

## Q2. Sample Databaseについて知っていますか?

??? success
    ### Sakila Sample Database
    - `MySQL`が提供するサンプルデータベース
    - サンプルデータベースを使うことで、自分でデータベースを作成することなく、様々なSQL文をテスト可能

    ### インストール方法

    ```bash
    wget "https://downloads.mysql.com/docs/sakila-db.zip";
    ```

    - MariaDBにログイン後、以下のコマンド実行

    ```bash
    MariaDB > source sakila-db/sakila-schema.sql
    MariaDB > source sakila-db/sakila-data.sql;
    ```

    ### インストールできているか確かめる

    ```bash
    MariaDB [none]> use sakila
    Database changed
    MariaDB [sakila]> show tables;
    +----------------------------+
    | Tables_in_sakila           |
    +----------------------------+
    | actor                      |
    | actor_info                 |
    | address                    |
    | category                   |
    | city                       |
    | country                    |
    | customer                   |
    | customer_list              |
    | film                       |
    | film_actor                 |
    | film_category              |
    | film_list                  |
    | film_text                  |
    | inventory                  |
    | language                   |
    | nicer_but_slower_film_list |
    | payment                    |
    | rental                     |
    | sales_by_film_category     |
    | sales_by_store             |
    | staff                      |
    | staff_list                 |
    | store                      |
    +----------------------------+
    23 rows in set (0.000 sec)
    ```

## Q2.5 MySQLとの差異(見つけたものだけ)

??? success
    ### MariaDB Enterpriseの場合
    - 整数値は2つ分、予約される

## Q3 データベースという言葉の意味を知っていますか?

??? success
    ### database(データベース)
    - 関連する情報を集めたもの

    ### データベースシステム
    - データを電子的に保存するもの

    ### データベースシステムの利点
    - データを素早く取得可能
    - データにインデックスを付けることが可能

## Q4 初期のデータベースシステムがデータを磁気テープで管理していたことを知っていますか?

??? success
    ### データが必要になると
    - 技術者が磁気テープをテープリーダーに取り付けていた
    - 必要なくなればまた外す
    - 書き込む際も磁気テープへ

## Q5 リレーショナルデータベースが登場する前の、データベース界隈について知っていますか?

??? success
    ### 階層型データベースシステム
    - データはツリー構造としてあらわされる
    - ツリーを辿って目的のデータを見つけ出す
    - ツリーの各ノードには、親を1つ持つものと、持たないもの、子を1つ以上持つ者と持たないものがあったので、シングルペアレント階層と呼ばれた

    ### ネットワークデータベースシステム
    - レコードを公開する
    - レコード間の関係を定義するリンクを設定する
    - 後は目的のデータまでレコードを辿っていく
    - レコードには複数の場所からアクセスできるので、マルチペアレント階層と呼ばれた

    ### コラム
    !!! info
        - 階層型データベースなどは今でも存在する
        - たとえば`Microsoft`の`Active Directory`(組織内のユーザ、コンピュータ、グループなどのリソースを階層的な構造で管理する)は階層型
        - 大企業や公官庁のメインフレームの中にも残っているかもしれない

## Q6 リレーショナルデータベースと、それにまつわる用語について知っていますか?

??? success
    ### 概要
    - データをテーブルの集合としてあらわしたもの
    - データを用いて、テーブル内のレコードとリンクする

    ### エンティティ
    - データの対象物

    ### テーブル
    - 格納された行の集合
    - ストレージに格納された場合は永続的
    - メモリに格納された場合は非永続的

    ### 結果セット
    - 大抵はSQLクエリの結果
    - 非永続テーブルの別名

    ### 主キー
    - テーブル内の行を一意に識別するための情報
    - 2つ以上の列で構成される場合、複合キーと呼ばれる
    - 自然キー： データ内に既に存在する一意な識別子
    - 人工キー：一意性を確保すべく人工的に導入されるキー
    
    ### 外部キー
    - 別のテーブルを辿るための情報

    ### 結合
    - 外部キーを用いて2つ以上のテーブルを1つにすること

    ### 正規化
    - データベースの設計を見直し、データの冗長性を排すこと
    - 整合性が高まる

## Q7 SQLについて知っていますか?

??? success
    ### SQL
    - データを操作するための言語

    ### クエリ
    - クエリとはデータを操作する際の一連の命令を意味する
    - クエリの結果はテーブルとして出力され、それは結果セットと呼ばれる
    - 結果セットを新しい入力としたり、データベースに格納したりも可能

## Q8 SQL言語が3つに分類できることを知っていますか?

??? success
    ### DDL: Data Definition Language
    - データベースの物理的な構造を定義するための言語
    - たとえばDBやTable構造の作成/変更/削除
    - データを一括で削除するTRUNCATEもここに含まれる

    ```sql
    -- 例 : corporationテーブルの作成
    CREATE TABLE corporation
      (coro_id SMALLINT,
       name VARCHAR(30),
        CONSTRAINT pk_corporation PRIMARY KEY (corp_id)
      )
    ```

    ### DML: Data Manipulation Language
    - データベース内のデータを操作する為の言語
    - たとえばデータの挿入/更新/削除/選択
    - スキーマが存在していない場合、データは操作できない

    ```sql
    -- 例 行を挿入する
    INSERT INTO corporation (corp_id, name)
    VALUES (27, 'hogehoge corporation');
    ```

    ```sql
    -- 例 データを取得する
    SELECT name 
    FROM corporation
    WHERE corp_id = 27;
    /*
    +------------------------+
    | name                   |
    +------------------------+
    | hogehoge corporation   |
    +------------------------+
    1 row in set (0.00 sec)
    */
    ```

    ### DCL: Data Control Language
    - トランザクションや、データへのアクセス権を制御するための言語
    - ※場合によっては、トランザクションを制御するための言語を、TCL(Transaction Control Language)と分けて考える

    ### データベースに関するデータ
    - SQLスキーマ文を使って作成されたデータベース要素は、データベースディクショナリという特別なテーブルに格納される
    - データベースに関するデータを __メタデータ__と呼ぶ
    - データベースディクショナリも`select`で調べることができ、データ構造等を確認できる。

## Q9 SQLが非手続き型言語であることと、Optimizerの基本的な挙動を理解していますか?

??? success
    ### 手続き型言語
    - アルゴリズムや処理手順を明示的に記述。
    - たとえばC言語や`Java`
    - コードは上から下へ実行され、条件分岐などの処理構造あり

    ### 非手続き型言語
    - SQLは「どのようにしてデータを得るべきか」というような処理手順を指定しない
    - 「データがどうなるべきか」を記述する
    - この点で、手続き型言語とは異なる
    
    ### 内部ではどのように実行され、結果が生成されるのか
    - `Optimizer`と呼ばれるデータベースエンジンのコンポーネントが処理を行う
    - オプティマイザはSQL文を調べ、テーブルの構成や利用可能なインデックスをもとに、効率のよい実行パスを判断する
    - この処理の中身はユーザ側が指定することはできない
    - 代わりに入力と出力をSQL文で指定する

    !!! warning
        ### オプティマイザヒント
        - 実は、特定のインデックスの仕様を提案するなどといったオプティマイザヒントを使うことで、オプティマイザの判断を間接的に制御可能
        - しかしほとんどのユーザはこうした調整を行わない

## Q10 SQLが特定のプログラミング言語と統合することで、威力を発揮することを理解していますか?

??? success
    ### 非手続き型言語なので
    - 自由なアプリケーションをSQLだけで作ることはできない

    ### SQL統合ツールキット
    - SQLを特定の言語から利用する

    | 言語   | ツールキット         |
    | ------ | -------------------- |
    | Java   | JDBC                 |
    | C#     | ADO.NET              |
    | Ruby   | Ruby DBI             |
    | Python | Python DB            |
    | Go     | Package dababase/sql |

    ### 単に対話形式で実行したいだけなら?
    - どのデータベースもコマンドラインツールを提供している

    ### GUIで結果を見たい場合は
    - グラフィカルツールを検索して使う

## Q11 1 row in set (0.00 sec)のような出力結果の使いどころが分かりますか?

??? success
    ### 予想外の挙動がないか確認する
    - たとえば`select`文によって返された行
    - `insert`文によって返された行
    - `update`文によって返された行
    - `delete`文によって返された行
    - が上記に対応するため、すべての行を誤って削除していないか等の確認が可能

## Q12 ANSI SQL規格について知っていますか? またSQLが異なるデータベース管理システムでも動作する意味も

??? success
    ### ANSI SQL規格
    - SQL言語の標準化されたバージョン
    - SQL言語の基本的な機能を定義する
    - DBMS(データベース管理システム)は、最新のANSI SQL規格に従おうとするため、基本的なSQLはどのDBMSでも動作する

    ### たとえば
    - `Oracle Database`でも`SQL Server`でも`MySQL`でも`PostgreSQL`でも`SELECT`文は同様に動作する

