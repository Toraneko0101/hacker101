# クイズ(5:集合)

## Q53 集合論の基本的な知識はありますか?

??? success

    ### 積集合(intersect)

    ```text
    ・2つの集合から共通部分を取り出す演算
    ・A & B
    ```

    ### 和集合(union)

    ```text
    ・2つの集合の、少なくとも一方に属する物からなる集合
    ・A | B
    ```

    ### 差集合(except)

    ```text
    ・ある集合から別の集合に属する要素を取り去った後の集合
    ・ A - B
    ```

## Q54 DBに対して集合演算子を用いる方法が分かりますか?

??? success
    ### DBにおける集合演算

    ```text
    ・組み合わせるデータ集合の列数は同一である必要がある
    ・対になる列のデータ型は同じである（または変換できる）
      必要性がある
    ```

    ### 集合演算を実践する

    ```sql
    -- 2つのselect文の間に集合演算子を配置する
    -- ※最初のselect文で指定された列名が結果セット全体で
    -- 共有されていることに注意する
    select 1 num1, "abc" str
    union
    select 9 num2, "xyz" str;

    /*
    +------+-----+
    | num1 | str |
    +------+-----+
    |    1 | abc |
    |    9 | xyz |
    +------+-----+
    2 rows in set (0.001 sec)
    */
    ```

    ### 複合クエリ

    ```text
    複合クエリ
      ・本来なら独立している複数のクエリを組み合わせる事から
      ・上記の例は複合クエリ
    ```

## Q55 union演算子について知っていますか?

??? success
    ### union/union all演算子

    ```text
    UNION
      ・和集合を結果クエリとして生成する
      ・ALLの場合は、結果クエリの重複を取り除かない
        よって、A union all Bの時、結果の行はA + Bになる

    ・SELECTは、暗黙的にallで、distinctを明示したが、
    ・UNIONは、暗黙的にdistinctで、allを明示する

    ・つまり、union演算子は組み合わせたデータ集合を並び替え
      重複を取り除く
    
    ・union allの場合、重複データをチェックしない
    ```

    ### union allの例

    ```sql
    -- customerとactorから
    -- first_nameがJ, last_nameがDで始まるデータを抽出
    select c.first_name, c.last_name
    from customer c
    where 
      c.first_name LIKE "J%" AND
      c.last_name LIKE "D%"
    union all
    select a.first_name, a.last_name
    from actor a
    where
      a.first_name LIKE "J%" AND
      a.last_name LIKE "D%"
    ;
    /*
    +------------+-----------+
    | first_name | last_name |
    +------------+-----------+
    | JENNIFER   | DAVIS     |
    | JENNIFER   | DAVIS     |
    | JUDY       | DEAN      |
    | JODIE      | DEGENERES |
    | JULIANNE   | DENCH     |
    +------------+-----------+
    5 rows in set (0.001 sec)
    */
    ```

    ### union演算子の例

    ```sql
    -- 重複を取る

    select c.first_name, c.last_name
    from customer c
    where 
      c.first_name LIKE "J%" AND
      c.last_name LIKE "D%"
    union
    select a.first_name, a.last_name
    from actor a
    where
      a.first_name LIKE "J%" AND
      a.last_name LIKE "D%"
    ;
    /*
    +------------+-----------+
    | first_name | last_name |
    +------------+-----------+
    | JENNIFER   | DAVIS     |
    | JUDY       | DEAN      |
    | JODIE      | DEGENERES |
    | JULIANNE   | DENCH     |
    +------------+-----------+
    4 rows in set (0.001 sec)
    */
    ```

## Q56 intersect演算子について知っていますか?

??? success

    ### intersect/intersect all

    ```text
    INTERSECT
      ・積集合を結果クエリとして抽出する
      ・ALLの場合は、結果クエリの重複を取り除かない
    ```

    ### intersect 

    ```sql
    select c.first_name, c.last_name
    from customer c
    where
      c.first_name LIKE "J%" and c.last_name LIKE "D%"
    intersect
    select a.first_name, a.last_name
    from actor a
    where 
      a.first_name LIKE "J%" and a.last_name LIKE "D%"
    ;

    /*
    +------------+-----------+
    | first_name | last_name |
    +------------+-----------+
    | JENNIFER   | DAVIS     |
    +------------+-----------+
    1 row in set (0.001 sec)
    */
    ```

    ### intersect all

    ```sql
    -- 一時テーブルを作って検証する

    create temporary table A
    (
      name varchar(10),
      value int
    );

    create temporary table B
    (
      name varchar(10),
      value int
    );

    insert into A
    values
    ("aaa", 111),
    ("aaa", 222),
    ("aaa", 222),
    ("bbb", 333),
    ("bbb", 333),
    ("bbb", 444),
    ("ccc", 111),
    ("ccc", 111);

    insert into B
    values
    ("aaa", 111),
    ("aaa", 333),
    ("bbb", 222),
    ("bbb", 333),
    ("bbb", 333),
    ("bbb", 555),
    ("ccc", 111),
    ("ddd", 111);    

    select * from A
    intersect
    select * from B;
    -- bbb, 333は2回分重複しているはずだが表示されていない

    /*
    +------+-------+
    | name | value |
    +------+-------+
    | aaa  |   111 |
    | bbb  |   333 |
    | ccc  |   111 |
    +------+-------+
    3 rows in set (0.001 sec)
    */

    select * from A
    intersect all
    select * from B

    /*
    +------+-------+
    | name | value |
    +------+-------+
    | aaa  |   111 |
    | bbb  |   333 |
    | bbb  |   333 |
    | ccc  |   111 |
    +------+-------+
    4 rows in set (0.001 sec)
    */
    ```

## Q57 except演算子について知っていますか?

??? success

    ### except/except all

    ```text
    except
      ・集合Aから重複データをすべて取り除く
    
    except all
      ・集合Bに含まれている重複データごとに
        集合Aから重複データを1つ取り除く
      ・結果クエリから重複を取り除いているわけではない
        事に注意
    ```

    ### exceptの例

    ```sql
    -- 先ほどの一時テーブルを利用する
    -- ccc 111はAに2つ、Bに1つあるが、
    -- distinctなので2つとも消えている
    select * from A
    except
    select * from B;

    /*
    +------+-------+
    | name | value |
    +------+-------+
    | aaa  |   222 |
    | bbb  |   444 |
    +------+-------+
    2 rows in set (0.005 sec)
    */
    ```

    ### except allの例

    ```sql
    select * from A
    except all
    select * from B;

    /*
    +------+-------+
    | name | value |
    +------+-------+
    | aaa  |   222 |
    | aaa  |   222 |
    | bbb  |   444 |
    | ccc  |   111 |
    +------+-------+
    4 rows in set (0.001 sec)
    */
    ```

## Q58 複合クエリを扱う時に、従わなければならないルールについて知っていますか?

??? success
    ### 結果の並べ替え

    ```text
    ORDER BY
      ・最後のクエリの後ろに追加する
      ・評価順番は集合演算の後
      ・最初のselect文で指定された列名が結果セット全体で
        共有されるため、列名は1つ目のクエリのものを用いる
        必要がある
      ・両方のクエリ列に同じ列エイリアスを割り当てて
        常にそれを用いるようにしておけば、誤解の心配はない
    ```

    ```sql
    select a.first_name fname, a.last_name lname
    from actor a
    where
      a.first_name LIKE "J%" and a.last_name LIKE "D%"
    union all
    select c.first_name, c.last_name
    from customer c
    where c.first_name LIKE "J%" and c.last_name LIKE "D%"
    order by lname, fname;

    /*
    +----------+-----------+
    | fname    | lname     |
    +----------+-----------+
    | JENNIFER | DAVIS     |
    | JENNIFER | DAVIS     |
    | JUDY     | DEAN      |
    | JODIE    | DEGENERES |
    | JULIANNE | DENCH     |
    +----------+-----------+
    5 rows in set (0.005 sec)
    */
    ```

    ### 優先順位

    ```text
    ・基本的に、3つ以上のクエリからなる複合クエリは
      上から順番に評価される
    ・intersect演算子は、他の集合演算子より優先される
    ・複数のクエリを丸括弧で囲むと、クエリを組み合わせる
      順序を指定可能
    ```

    ```sql
    -- 上から順番に評価
    select a.first_name, a.last_name
    from actor a
    where a.first_name like "J%" and a.last_name like "D%"
    union all
    select a.first_name, a.last_name
    from actor a
    where a.first_name LIKE "M%" and a.last_name like "T%"
    union
    select c.first_name, c.last_name
    from customer c
    where c.first_name LIKE "J%" and c.last_name LIKE "D%"
    ;

    /*
    +------------+-----------+
    | first_name | last_name |
    +------------+-----------+
    | JENNIFER   | DAVIS     |
    | JUDY       | DEAN      |
    | JODIE      | DEGENERES |
    | JULIANNE   | DENCH     |
    | MARY       | TANDY     |
    | MENA       | TEMPLE    |
    +------------+-----------+
    6 rows in set (0.002 sec)
    */

    -- 最後にunion allをしたので重複した
    select a.first_name, a.last_name
    from actor a
    where a.first_name like "J%" and a.last_name like "D%"
    union
    select a.first_name, a.last_name
    from actor a
    where a.first_name LIKE "M%" and a.last_name like "T%"
    union all
    select c.first_name, c.last_name
    from customer c
    where c.first_name LIKE "J%" and c.last_name LIKE "D%"
    ;

    /*
    +------------+-----------+
    | first_name | last_name |
    +------------+-----------+
    | JENNIFER   | DAVIS     |
    | JUDY       | DEAN      |
    | JODIE      | DEGENERES |
    | JULIANNE   | DENCH     |
    | MARY       | TANDY     |
    | MENA       | TEMPLE    |
    | JENNIFER   | DAVIS     |
    +------------+-----------+
    7 rows in set (0.001 sec)
    */

    -- 括弧を使ってみる
    -- union allから評価されたため、順序を気にする必要がない
    select a.first_name, a.last_name
    from actor a
    where a.first_name like "J%" and a.last_name like "D%"
    union
    (
      select a.first_name, a.last_name
      from actor a
      where 
        a.first_name LIKE "M%" and 
        a.last_name like "T%"
      union all
      select c.first_name, c.last_name
      from customer c
      where 
        c.first_name LIKE "J%" and 
        c.last_name LIKE "D%"
    )
    ;

    /*
    +------------+-----------+
    | first_name | last_name |
    +------------+-----------+
    | JENNIFER   | DAVIS     |
    | JUDY       | DEAN      |
    | JODIE      | DEGENERES |
    | JULIANNE   | DENCH     |
    | MARY       | TANDY     |
    | MENA       | TEMPLE    |
    +------------+-----------+
    6 rows in set (0.002 sec)
    */

    select a.first_name, a.last_name
    from actor a
    where a.last_name LIKE "L%"
    union
    select c.first_name, c.last_name
    from customer c
    where c.last_name LIKE "L%"
    order by last_name;
    ```