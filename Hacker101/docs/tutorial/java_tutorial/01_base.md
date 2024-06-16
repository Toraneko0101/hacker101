# クイズ(Q1: 基本文法)

## Q1 Javaの特徴について知っていますか?
??? success
    ### Javaとは

    ```text
    ・Java言語(プログラミング言語)
    ・JVM(Java Virtual Machine)
      この二つを中心としたプログラミング言語

    ・Sun MicroSystems社が当初開発をしており、
      2010年からは、Oracleが買収している
    ```

    ### Javaとオブジェクト指向

    ```text
    ・クラスという単位でプログラムをまとめ、全体を構築する
      事が多いため、こう呼ばれる
    
    ・Stream API等がJava8で取り入れられたため、
      最近はJSのような関数型の記法も行う
    ```

    ### Javaとバイトコード

    ```text
    ・Javaは高水準のプログラミング言語である
    ・最終的には機械語に変換されるわけだが、
      その過程には幾つかのステップがある

    1 プログラマがJavaのソースコードをtextファイルに記述

    2 javac(javaコンパイラ)がjavaファイルをコンパイラ
      ソースコードは、主に.classという拡張子を持つ
      JVMで実行可能なバイトコードへと変換される

      ※バイトコードはマシン/OSに依存せず、
        JVMが動く環境なら実行可能

      ※つまり、javaのコードを移植するときは、
        バイトコードと、各マシン/OSに対応するJVMが必要
    
    3 JVMはプログラムを実行する際に、
      必要なクラスファイルをロードする
      (クラスローダが、Javaアプリが使用するクラスを見つけて
        メモリにロードしてくれる)
    
    4 JVMがロードされたバイトコードを検証し、
      セキュリティ制約を満たしているか、
      メモリへの不正アクセスがないかなどを確かめる
    
    5 初期の段階ではインタプリタを使用し、
      バイトコードを1行ずつ解釈する
      (インタプリタがバイトコード命令を解釈して、
      それに対応したマシンコード命令を実行する)
    
    6 コードが頻繁に実行されると判断された場合は、
      JITコンパイラがそのコードを機械語に変換し、
      後続の処理ではそれを読み込むことで高速化する
    
    7 最終的にJITコンパイラによって生成された
    　機械語のコードがCPUによって実行される
    ```

    ### JDKについて

    ```text
    ・Java Development Kit

    ・javacなどのソースコードをバイトコードに変換する
      コンパイラや、JVM、標準ライブラリの開発に必要な
      ツールなどが含まれる
    ```

    ### Java SEについて

    ```text
    ・Java Standard Edition

    ・Java言語やJVM,標準で含まれるライブラリの仕様を意味する

    ・Java Community Process(JCP)が
      Javaの仕様策定や関連技術の開発、実装などを行う
      メンバーは主要なベンダーやオープンソースの団体
      意見が割れた場合は、投票によって決定される
    
    ・ただし、現在ではOpenJDKプロジェクトがJEPをベースとして
      次期バージョンのJDKの開発を進めて、
      JCPがその機能を追承認するようになっている
      (開発スピード向上が主な目的)

    OpenJDKプロジェクト
      ・JDKのオープンソース版という位置づけだが、
        現在リリースされているJavaアプリの開発、実行環境は
        これがベースとなっている
      ・個人でも参加可能
    
    JDKディストリビューション
      ・OpenJDKプロジェクトの成果物(ソース)をベースにした
        実行可能なJDKのパッケージの提供を行う
      ・著名なのはOracleが提供している、Oracle OpenJDK等
      (コンパイル、リンクを行い実行可能なパッケージを作成
      しているので、ビルドしてくれているという表現がよさそう)
    
    Oracle JDK vs Oracle OpenJDK
      ・後者は、無償提供される
      ・前者の場合は、外部向けのアプリの場合無償利用の対象外
      ・ただし、Oracle JDKは長期サポートされる
      ・Oracle OpenJDKは基本6カ月のサポート
        (※次のバージョンが6カ月後に出るので)
    
    サポート期間が過ぎるとどうなるのか
      ・バグや脆弱性が発見されても修正版はリリースされない
      ・安全に使うためには最新版に上げる必要がある
      ・LTSの場合は、3年サポートされる
        (つまり、6バージョンに1つがLTSになる)
        (この性質上、実際のシステムではLTSが多くつかわれる)
        (現場でJava11,17の姿を見かけるエンジニアも多そう)
    
    JEP
      ・JDK Enhancement Proposal
      ・JDKの機能拡張を提案する
      ・OpenJDKプロジェクトが申請されたJEPを受け取り、
        中心的なメンバーがレビューした後、
        取り組む価値のあるideaだとみなされれば開発が始まる
      ・開発がリリースの期限に間に合わなければ
        次のバージョンに回される
    
    Jakarta EE
      ・エンタープライズ向けの機能

      ・たとえば、複雑な業務アプリを効率的に構築、deploy、
        管理するために必要なAPIやサービスについて、標準化する

      ・そういえば、Spring Boot3.0にしたら、javaxから
        jakartaパッケージに変更が必要だったな。
        あれは、javaEEの商標権をOracleが所有していたため
        jakarta.*に移行する必要があったということだったのか
      (2018年、Oracleは、JavaEEの開発を中止し、JavaEEに
      関する成果物をEclipse Foundationに引き継がせた。)
    
    JavaMEについて
      ・SEであっても、携帯デバイスで動かせるようになったため
        使用されなくなっている
    ```

    ### (補足)提案されたJEPのリスト

    ```text
    JEPのリスト
      ・https://openjdk.org/jeps/0
    
    特定のバージョンで導入されたJEPのリスト
      ・https://openjdk.org/projects/jdk/
    
    Previewとついているものは、
    プレビュー版の、Java言語やJVMに関する変更点

    Incubatorとついているものは
    プレビュー版の、APIやツールに関する変更点
    ```

## Q2 Javaの情報源について知っていますか?

??? success
    ### 公式ドキュメントもいいけれど

    ```text
    ・JDK21のドキュメント
      https://docs.oracle.com/javase/jp/21/
      ※末尾の数字を変えればそのVersionになる
    ```

    ### その他

    ```text
    ・qiitaのJavaカテゴリ
      https://qiita.com/tags/java
    
    ・zennのJavaカテゴリ
      https://zenn.dev/topics/java

    ・stackoverflowのJavaカテゴリ
      https://stackoverflow.com/questions/tagged/java
    
    ・redditのsubreddit
      https://www.reddit.com/r/learnjava/
      https://www.reddit.com/r/java/
    ```

## Q3 環境構築について知っていますか?

??? success
    ### JavaのInstall

    ```text
    1 OpenJDKの22.0.1のinstall
      https://jdk.java.net/22/
      該当のURLから、ビルドされたパッケージをdownload
    
    2 解凍して、任意の階層に配置する

    3 Windows + R(Windowsなら)
      sysdm.cplと入力
      詳細設定 --> 環境変数 --> システム環境変数の新規を押す
      変数: JAVA_HOME
      値: 配置した階層

      例) JAVA_HOME  C:\jdk-22.0.1

    4 Windows + R
      wtと入力
      java --versionとして、versionが表示されればOK!
    ```

    ### IDEについて

    ```text
    ・IntelliJなどでやりたいのは、やまやまのやまだが
      今回はVSCodeで頑張っていく
      (※Eclipse Cheを使えば、ブラウザでVSCodeのような感覚で
        開発ができる！リモートの少人数開発なら試す価値あり?)
    
    ・MavenやGradleのあたりまでこれば、
      IDEの違いはあまり気にならなくなってくると思うし
      あまりIDEで重要な部分を覆い隠されると困るので

    <VSCodeでの設定>
      1 Extension Pack For Javaをinstall(拡張機能)
      2 settings.jsonを開き、以下の様に入力
        "java.jdt.ls.java.home": "C:\\jdk-22.0.1"
        (installしたjdkの場所をvscodeへ知らせている)

      3 Ctrl + Shift + Pでコマンドパレットを選択
      
      4 ひとまずNo Build toolsを選択し
        プロジェクトフォルダの名前と、保存先のフォルダを作成
      
      5 メニューバーから実行 -> デバッグの開始
        (Javaでデバッグする)
      
      6 Hello,World!と表示されればOK!

      ※bin配下に、classファイルが作られているはず
    ```

    ### 補足(単体ファイルの実行)

    ```bash
    # 以下の様にして、単体のファイルを実行することも可能
    $ cd src

    # ソースファイルからバイトコードを作成
    $ javac App.java

    # 実行
    $ java App
    ```

    ### 補足(コードの修正)

    ```java
    // 以下の様にしてみる
    public class App {
        public static void main(String[] args) throws Exception {
            System.out.println("Hello, World!");
            System.out.println("Hello, Nekoinu!");
        }
    }

    // 同じように実行 -> デバッグの開始
    /**
     * Hello, World!
     * Hello, Nekoinu!
     * (上記は出力結果)
    */
    ```

## Q4 基本的な構文について知っていますか?(順次処理)

??? success
    ### JShell

    ```text
    ・Java9で導入されたREPL(Read-Eval-Print-Loop)
    ・ユーザとインタプリタは、対話的にコードを実行できる
    ・Pythonなんかにもあるよね

    ・/exitで終了！
    ```

    ### 四則演算

    ```bash
    # 加算
    jshell> 5+2
    $1 ==> 7

    # 乗算
    jshell> 5*2
    $2 ==> 10

    # 減算
    jshell> 5-2
    $3 ==> 3

    # 商
    jshell> 5/2
    $4 ==> 2

    # 除算(実数の割り算:どちらかがdoubleなら暗黙的に型変換)
    jshell> 5.0/2
    $5 ==> 2.5

    # 余り
    jshell> 5 % 2
    $6 ==> 1
    ```

    ### 文字列

    ```bash
    jshell> "test"
    $7 ==> "test"

    # 連結
    jshell> "test" + "er"
    $8 ==> "tester"

    # 数値との連結(暗黙的に文字列に型変換)
    jshell> "test" + 123
    $9 ==> "test123"    

    # 評価順序
    jshell> "test" + (12 + 3)
    $10 ==> "test15"

    # 文字列型の数値
    jshell> "5" + 2
    $11 ==> "52"

    # escape(特殊文字)
    # 値を出力する命令はSystem.out.println
    jshell> System.out.println("\\")
    \

    # JShellは結果を再利用可能
    jshell> System.out.println($11)
    52

    # textブロック(java15まで使えなかったらしい、地獄)
    jshell> System.out.println($11)
    52

    jshell> """
      ...> test
      ...> foo
      ...> """
    $17 ==> "test\nfoo\n"

    jshell> System.out.println($17)
    test
    foo
    ```

    ### 例外処理

    ```bash
    jshell> import java.lang.ArithmeticException; 
      ...>  try{
      ...>    int a =  3 / 0;
      ...> }catch(ArithmeticException e){
      ...>     System.out.println("Error");
      ...> };

    Error
    ```

    ### 組み込み関数

    ```bash
    # javaではメソッドをシンボルと呼ぶので
    # メソッドを違えたときは、シンボルを見つけられないと出る
    jshell> "test".toUpperCase()
    $27 ==> "TEST"

    # tabを押すと、補完される
    # toまで入力して、<tab>を押してみる
    jshell> "test".to
    toCharArray()   toLowerCase()    toString()
    toUpperCase()
    jshell> "test".toCharArray()
    $28 ==> char[4] { 't', 'e', 's', 't' }

    # pythonのように文字列の繰り返しをしたいならrepeat
    # ミスったとき、引数の期待値の型を表示してくれるなんて
    # jshellは優しい
    jshell> "test".repeat(3)
    $29 ==> "testtesttest"

    # 置き換え
    # jsの場合、replaceだと最初の一個だったが、
    # javaだと、replaceで、jsでいうreplaceAllの挙動
    # replaceAllは、正規表現用になる
    # jsでいうreplaceがしたいなら、replaceFirst()がある
    # 正規表現については//で囲む必要はなく、""でOK
    jshell> "test".replace("t", "")
    $30 ==> "es"

    jshell> "test123".replaceAll("[a-z]", "0");
    $32 ==> "0000123"

    jshell> "test123".replaceFirst("[a-z]", "0");
    $33 ==> "0est123"
    ```

    ### シグネチャ

    ```text
    ・一般的にはsignature = 署名というイメージ

    ・メソッド名、引数の型、数、順序、戻り値の型が
      分かれば、メソッドが一意に識別できる(=署名になる)
      ので、プログラミングでは、それがシグネチャと呼ばれる
    
    ・オーバーロードは、同名の関数を定義し、
      それを引数やデータ型等によって使い分ける仕組み
      呼び出す際のシグネチャによって、対応するものが呼ばれる
    ```

    ```bash
    # この状態で<tab>を押すと、signatureが表示される
    jshell> "test".toUpperCase(
    シグネチャ:
    String String.toUpperCase(Locale locale)
    String String.toUpperCase()

    # つまり、toUpperCaseは、Locale型の引数を取るものと
    # 引数がないものがあるようだ。 --> オーバーロード

    # 更に<tab>を押すと、documentが表示される
    jshell> "test".toUpperCase(
    String String.toUpperCase(Locale locale)
    Converts all of the characters in this String to upper case  
    using the rules of the given Locale .Case mapping is based on
    the Unicode Standard version specified by the Character      
    class. Since case mappings are not always 1:1 char mappings, 
    the resulting String and this String may differ in length.   

    <次のページを表示するにはタブを再度押してください>

    # 英語が読めない場合は(これ私のことだ......)
    # Q2の情報源に従って調べるといい
    # 最新の場合は、jpとURLに指定してもない場合があるので注意

    # 勘でも行けないことはない
    # たとえば、文字列の一部を返すメソッドについて知りたい時
    # どうせ、substrとかなので、subだけ打って、<tab>
    # 案の定、候補にsubstringが出てきたので、<tab>
    # String String.substring(int beginIndex)
    # String String.substring(int beginIndex, int endIndex) 
    # と書かれていたので、下記のようにする 
    # 確実性はないので、大人しく公式を見た方がいい気もする
    jshell> "Test".substring(1,3)
    $36 ==> "es"

    ```

    ### format

    ```text
    ・java22、早く来てくれ～！

    ・考え方的には、""で区切って、+演算子で連結するのって
      面倒だよねっていう話
    ```

    ```bash
    # 書式指定子%s等を、formattedに渡した引数の値で置き換え
    jshell> "test%d".formatted(12+3)
    $37 ==> "test15"

    # 複数の引数を渡すことも可能
    jshell> "%s or %s".formatted("dead", "alive")
    $38 ==> "dead or alive"

    # %dなら整数, %sなら文字列
    # %,dとすることで、3桁ごとにカンマを入れる

    # %fは実数に使う
    # 以下では全体の桁数と、小数以下の桁数を指定し
    # 0パディングを行っている
    jshell> "test%011.3f".formatted(12345.1234)
    $43 ==> "test0012345.123"

    # Java23(LTS)が出て、早く上記の書き方が廃れてほしい
    # 22だと、--enable-previewが必要
    # STRの場合、static import要らず
    # プリミティブの場合は、値をそのまま使う
    # 参照型ならtoString()の結果を使う
    C:\tutorial>jshell --enable-preview 
    |  JShellへようこそ -- バージョン22.0.1
    |  概要については、次を入力してください: /help intro

    jshell> String name = "Hoge";
    name ==> "Hoge"

    jshell> STR."Hello \{name}!";
    $2 ==> "Hello Hoge!"

    # FMTの場合、static importがいる
    # %d等を\{}の前に指定可能
    # .も1文字であることに注意
    # pyみたいに{}の中でformatが指定できれば嬉しいんだけど
    jshell> import static java.util.FormatProcessor.FMT;
    jshell> var x = 10
    x ==> 10

    jshell> var y = 20.0
    y ==> 20.0

    jshell> FMT."%d\{x} / %.2f\{y} = %08.3f\{x/y}"
    $6 ==> "10 / 20.00 = 0000.500"
    ```

    ### stacktraceを見る

    ```text
    ・一番上が、実際に例外が発生した箇所
    ・一番下が、呼び出し元
    ・今回の場合、%dにStringを指定していることが原因である
      事がよくわかる
    ```

    ```bash
    jshell> "%d+%d".formatted("abc", 12);
    |  例外
        java.util.IllegalFormatConversionException:
          d != java.lang.String
    |   at Formatter$FormatSpecifier.failConversion       
          (Formatter.java:4534)
    |   at Formatter$FormatSpecifier.printInteger 
          (Formatter.java:3072)
    |   at Formatter$FormatSpecifier.print 
          (Formatter.java:3027)
    |   at Formatter.format 
          (Formatter.java:2797)
    |   at Formatter.format (Formatter.java:2734)
    |   at String.formatted (String.java:4494)
    |   at (#8:1)
    ```

    ### 基本的な型

    ```text
    var : 型推論
      ・左側がリテラルとか、
        コレクションAPIを使用している際には有用
      ・推論しているので、初期値が必要
      ・完全にvarを禁止しているprojectには、参画したくない
        --> 禁止しているのが悪いわけではなく
            何となくプログラミング以外に要する時間が多そう
            全てが冗長な現場である可能性が高い(偏見)
    
    int
      ・32bit整数
      ・64bitを使いたい場合は、longで
      ・16のshort, 8のbyteも用意されている
    
    double
      ・浮動小数点数
      ・64bit
      ・32bitの単精度を使いたい場合は、floatで
    
    char
      ・16bitのUnicode文字
    
    boolean
      ・true/false
    
    String
      ・文字列
      ・参照型
      ・JSの場合、文字列リテラルは基本型であったため
        メソッドを適用するのにラッパークラスが必要だったが
        Javaにその必要はないようだ

    
    <補足>
      ・代入演算子等については、特に不思議な箇所はない
    ```

    ```bash
    # 例

    jshell> var x = 12;
    x ==> 12

    jshell> int y,z;
    y ==> 0
    z ==> 0

    jshell> String cat = "tama";
    cat ==> "tama"

    # 16進数で指定してみた
    jshell> char ch1 = '\u0061';
    ch1 ==> 'a'
    ```

    ### 型変換

    ```bash
    # 数値 <=> 文字に変換するいつもの奴
    # 8と0のコード番号の差
    jshell> '8' - '0'
    $24 ==> 8

    jshell> ch1 = '0' + 8
    ch1 ==> '8'

    # 整数 <=> 浮動小数点数
    # 精度が消失する場合は、truncate

    # 暗黙的に変換されている(doubleの方が大きいので)
    jshell> int i = 234
    i ==> 234

    jshell> double d = i
    d ==> 234.0

    # 精度が失われる可能性がある場合は
    # 明示的にキャストする
    jshell> int j = (int)d
    j ==> 234

    # 文字列 <=>　数値

    jshell> int a = Integer.parseInt("3");
    a ==> 3
    # parseIntは.を解釈できず、エラる
    jshell> double b = Double.parseDouble("3.14");
    b ==> 3.14
    # カンマ対応
    jshell> import java.text.NumberFormat;

    jshell> NumberFormat.getInstance().parse("12,345");
    $34 ==> 12345

    # Java9以降は、String.valueOf()をする必要はない
    # 空文字との連結で十分
    jshell> String s = 123 + "";
    s ==> "123"
    ```

    ```text
    ・型は一見、煩わしく見えるかもしれないが、
      大きいシステムを作れば作るほど
      その必要性が分かる
    
    ・他人のためではなく、自分が何を指定したかも忘れるし
    ```

## Q5 標準APIについて知っていますか?

??? success

    ### Java API

    ```text
    通常のAPI
      ・OSやアプリが、他のアプリに対して
        機能の一部を簡単に利用できるよう提供する接続先
      ・車輪の再発明を防ぐことができる
      ・自分で書くより、大抵APIの方が最適化されている
    
    Web API
      ・HTTP/HTTPS等のプロトコルを使って通信
    
    Java API
      ・Javaに標準で含まれるクラスライブラリ
      ・JDKに含まれている
      ・単なるメソッド呼び出しで利用される
      ・(90年代には、Web API等は一般的ではなかったため
        Javaは単なるライブラリをAPIと呼んでいて今もそう)
    ```

    ### 日付関連

    ```bash
    jshell> java.time.LocalDate.now()
    $36 ==> 2024-05-29
    ```

    ### import

    ```text
    ・上記のような、完全修飾名を何度も書くのは面倒くさい
    ・import宣言を行うと、クラス名だけで良くなる

    ・import パッケージ名.クラス名を
      クラス定義よりも前に書けばOK
      (※package: クラスを目的に合わせてグループ化した単位)
      (※標準ライブラリ以外を使いたい場合は、
       jarファイルの中に保存して、まとめてDLする
       この際衝突しないようにパッケージ命名規約が設けられる)

    ・*を使ってもいいが、分かりにくくなる
    ・*は再帰的にはできない

    ・java.langは繰り返し使うとわかりきっているので
      明示的なimport宣言が不要になっている
    
    ・JShellでは、他に幾つかのpackageが予めimportされている
      が、覚えない方がよさそうだ
    ```

    ```bash
    # 現在時刻
    jshell> import java.time.LocalDateTime;
    jshell> var now = LocalDateTime.now()
    now ==> 2024-05-29T22:27:46.006632500;

    # 2週間前
    jshell> now.minusWeeks(2);
    $43 ==> 2024-05-15T22:27:46.006632500

    # 日付指定
    jshell> import java.time.LocalTime;
    jshell> import java.time.LocalDate;
    jshell> var hogeDate = LocalDate.of(2024,5,1);
    hogeDate ==> 2024-05-01
    jshell> var hogeTime = LocalTime.of(12,0,0,0);
    hogeTime ==> 12:00
    jshell> var hogeDateTime = LocalDateTime.of(hogeDate, hogeTime);
    hogeDateTime ==> 2024-05-01T12:00

    jshell> var hogeDateTime2 = LocalDateTime.of(2024,5,2,23,59,59,0);
    hogeDateTime2 ==> 2024-05-02T23:59:59

    # format
    # 基本的に%tから始まる
    # 詳細については公式参照

    # ダメな指定の仕方
    jshell> "%tY年%tm月%td日 %tH:%tM:%tS".formatted(
      LocalDateTime.now(), 
      LocalDateTime.now(), 
      LocalDateTime.now(), 
      LocalDateTime.now(), 
      LocalDateTime.now(), 
      LocalDateTime.now()
      )
    $52 ==> "2024年05月29日 22:35:24"

    # %の後に、<を付けると、直前の書式と同じ引数の値を使える
    jshell> "%tY年%<tm月%<td日 %<tH:%<tM:%<tS".formatted(LocalDateTime.now())
    $53 ==> "2024年05月29日 22:37:11"
    ```

    !!! info
        ### もう少しましな整形をする

        ```bash
        jshell> import java.time.format.DateTimeFormatter;
        jshell> var formatter = DateTimeFormatter.ofPattern("yyyy年M月d日");
        formatter ==> Value(YearOfEra,4,19,EXCEEDS_PAD)'年'Value(MonthOfYear)'月'Value(DayOfMonth)'日'

        jshell> formatter.format(LocalDate.of(2024,1,1));
        $57 ==> "2024年1月1日"

        jshell> formatter.parse("2024年1月2日");
        $58 ==> {},ISO resolved to 2024-01-02
        ```
    
    ### staticメソッドと、インスタンスを介した呼び出し

    ```text
    staticメソッド
      ・クラス名を指定して呼び出すメソッド
      ・クラスで共通
    
    インスタンスメソッド(呼び名あったんだ)
      ・インスタンスを介した呼び出し
    
    LocalDate.now()などは?
      ・LocalDateがクラス名なので、staticメソッド

    <補足>
      ・pythonだと、staticメソッドのほかにクラスメソッドが
        存在したが、あれは冗長だったので此方の方が良さそう
        pyで言うstaticメソッドはクラスに依存しない処理に
        使用していた記憶がある
    ```

    ```bash
    # formattedはインスタンスメソッド
    # format(java14までの主流)はstaticメソッド

    jshell> String.format("%s or %s", "dead", "alive");
    $59 ==> "dead or alive"

    jshell> "%s or %s".formatted("dead", "alive");
    $60 ==> "dead or alive"
    ```

    ### 後置補完とスニペットについて

    ```text
    ・"".formatとするのは基本的に誤りだが、JETBrains系のIDE
    　には、後置補完が備わっている

    ・例として、
      s.argとすると、function(s)になったり
      a.elseとすると、if(!a){}となったりする

    ・別にVSCodeでもカスタムスニペット使えばできると思う
      Vimでもできるだろう。上手にカスタマイズして！
    ```

    ### BigDecimal関連(例)

    ```text
    ・10進数で正確に値を扱いたいときに使う
    ・2進数で計算しないので、正確

    ・add/subtract/multiply/divide/remainder
    ・divideAndRemainderがある
    ```

    ```bash
    jshell> 579*0.05
    $61 ==> 28.950000000000003

    jshell> import java.math.BigDecimal;
    jshell> var b579 = BigDecimal.valueOf(579);
    b579 ==> 579

    jshell> var b0_05 = BigDecimal.valueOf(0.05);
    b0_05 ==> 0.05

    jshell> b579.multiply(b0_05);
    $65 ==> 28.95

    # 商と余りが配列で変える
    jshell> b579.divideAndRemainder(BigDecimal.valueOf(199))     
    $66 ==> BigDecimal[2] { 2, 181 }

    # divideで循環小数が答えの場合、例外が発生する
    jshell> b579.divide(BigDecimal.valueOf(7.0))
    |  例外java.lang.ArithmeticException: 
       Non-terminating decimal expansion; 
       no exact representable decimal result.
    |        at BigDecimal.divide (BigDecimal.java:1804)
    |        at (#73:1)
    
    # 丸め処理で回避可能
    # 小数点2桁で四捨五入している
    # なら、天井関数ならROUND_UPになる。調整して
    jshell> b579.divide(BigDecimal.valueOf(7.0), 2, BigDecimal.ROUND_HALF_UP);
    $75 ==> 82.71

    # 更に多くの桁を渡したい場合
    jshell> BigDecimal.valueOf(3.14159263589793238);
    $81 ==> 3.1415926358979323 #切れている

    # doubleの制限に引っかからないようにしたいなら
    # instanceを作成する
    # ただし、コンストラクタの引数として、doubleを渡すと、
    # 小数点以下の値は、1/2^nに近似されてしまう
    # そのためstringで渡している
    jshell> new BigDecimal("3.14159263589793238")
              .multiply(BigDecimal.valueOf(0.2));
    $80 ==> 0.628318527179586476
    ```

    ### Object

    ```text
    ・BigDecimalも、Stringも参照型であれば、
      Objectが先祖になる
    ```

    ### newについて

    ```text
    ・versionUpとともにJava APIにおいて
      単純なコンストラクタを用いずに
      objectを生成する方法が増えている

    ・理由としては
      可読性, 不変Objectの作成, 柔軟性と保守性
      入力値のvalidation, Builderパターンの使用などがある
    
    ※Builderパターン
      ・コンストラクタに対して数多くのparamをセットする
        必要がある際に、使うことが推奨される実装方法
    ```

    ### (補足)newとJava APIについて

    ```java
    // 旧式(可変長)
    List<String> list = new ArrayList<>();
    list.add("a");
    list.add("b");
    list.add("c");
    // (固定長, 不変だがlistを参照している)
    List<String> unmodifyList = Collections.unmodifiableList(list);

    //新式(固定長, 不変, 元配列を参照しない)
    List<String> list2 = List.of("a", "b", "c");
    //新式(可変長)
    List<String> list3 = new ArrayList<>(list2);
    list3.add("d");

    //Stream APIの場合(JShellの場合、改行に注意)
    List<String> list4 = Stream.of("a", "b", "c")
      .collect(Collectors.toCollection(ArrayList::new));
    list4.add("d");

    //安全なObject作成
    //nullに対して考えたくないとする

    //旧式
    Optional<String> opt1 = new Optional<>(null);

    //新式
    Optional<String> opt2 = Optional.empty();
    ```

    ### (補足) Builderパターン

    ```java
    //　単純なコンストラクタ
    public class Person{
      private String firstName;
      private String lastName;
      private int age;
      private String phone;
      private String address;

      public Person(
        String firstName, 
        String lastName,
        int age,
        String phone,
        String address
      ){
        this.firstName = firstName;
        this.lastName = lastName;
        this.age = age;
        this.phone = phone;
        this.address = address;
      }
    }

    var person1 = new Person(
      "torano", 
      "nekozirou", 
      12, 
      "000-0000", 
      "100-0000"
    );

    // Builder Pattern
    // --------------------------
    //やっていることは単純で、staticクラス

    public class Person{
      // builderで定義しているので不変
      private final String firstName;
      private final String lastName;
      private final int age;
      private final String phone;
      private final String address;

      //buildでnewするときに、fieldを対応付ける
      private Person(PersonBuilder builder){
        this.firstName = builder.firstName;
        this.lastName = builder.lastName;
        this.age = builder.age;
        this.phone = builder.phone;
        this.address = builder.address;
      }

      public static class PersonBuilder{
        //コンストラクタで定義するもの以外は可変
        private final String firstName;
        private final String lastName;
        private int age = 0;
        private String phone = "";
        private String address = "";

        public PersonBuilder(
          String firstName, 
          String lastName
        ){
          this.firstName = firstName;
          this.lastName = lastName;
        }

        public PersonBuilder age(int age){
          this.age = age;
          return this;
        }

        public PersonBuilder phone(String phone){
          this.phone = phone;
          return this;
        }

        public PersonBuilder address(String address){
          this.address = address;
          return this;
        }

        public Person build(){
          return new Person(this);
        }
      }
    }

    //自身を返してメソッドチェーン
    //最終的に、fieldにすべての値が入った状態でクラス作成
    //IDEの機能使わなくても
    //どの値を設定しているか分かりやすいね!
    var person = new Person.PersonBuilder("torano", "nekozirou")
      .age(30)
      .phone("000-0000")
      .address("100-0000")
      .build()
    ;
    ```

## Q6 GUIプログラミングについて知っていますか?

??? success
    ### GUIプログラミング

    ```text
    ・Pythonでいうと、Tkinterとかがあったね

    ・でも、Dart言語 + Flutterが大正義なんじゃないかな?
    
    ・React Nativeも有名だよね。
      (でもこっちはモバイル開発に特化してるから.....)
    
    ・ところでPythonの場合、FletというFlutterを使える
      フレームワークがあった気がする
    
    ・まずクロスプラットフォーム対応済みなのかが問題
    
    ・Javaの場合、何があるのかを以下で見ていく
      (実用的なのかは疑問符がつくけど)
    
    ・というか、DartはJavaと親和性があるんだし、
      言語を学ぶことはそれほど難しいことじゃないんだから
      JavaによるGUI開発は諦めて、Flutterを選んだ方が
      (これ以上はいけない)
    ```

    ### Swing

    ```text
    ・Java1.2で提供されたGUIライブラリ
    ・Javaに標準で含まれているというメリットあり
    ```

    ### JavaFX

    ```text
    ・Swingの後継のGUIライブラリ
    ・JDKにはもはや含まれていないが、Swingと比べると
      多少モダンといえなくもない
    ```

    ### Windowの表示

    ```java
    import javax.swing.*
    
    // JFrameクラスがWindowに当たる
    // 引数はWindowのタイトルになる
    var f = new JFrame("test");
    
    // Windowの表示状態を変更
    f.setVisible(true);

    // 大きさを変更
    f.setSize(600,400);

    // 位置を変更
    f.setLocation(200, 200);

    // 現在位置を取得(適当に動かしてから)
    f.getLocation();
    // $13 ==> java.awt.Point[x=346,y=87]
    ```

    ### 入力領域の配置

    ```java
    //画面の表示部品なので、コンポーネント
    // JTextFiled: 1行テキスト
    // JTextArea: 複数行テキスト
    // JButton: ボタン
    // JPanel: パネル
    // JLabel ラベル

    var t = new JTextField();
    
    // Windowにコンポーネントを配置
    // 位置指定はNorth, East, South, West, Center
    f.add("North", t);
    f.validate(); //画面更新

    // 文字列の設定
    t.setText("Nyanko");

    // 文字列の取得(適当に入力した後で)
    t.getText();
    // $18 ==> "Nekomoti"
    ```

    ### 入力のコピー

    ```java
    // もう一つ入力領域を作る
    // 引数を渡すと、予め入力された状態になる
    var t2 = new JTextField("second");
    f.add("South", t2);

    // 大文字でコピー
    t.setText(t2.getText().toUpperCase());
    ```

    ### ボタンの配置

    ```java
    var b1 = new JButton("Upper");
    f.add("Center", b1);
    f.validate();

    //処理の設定
    //ボタンを押したとき、t2の文字がt1にUpperCaseで
    //Copyされるようにする
    //Lambda式だねえ

    //ActionEvent(ボタンを押すなど)を行うと
    //addActionListenerが発火する
    b1.addActionListener(
      ae -> t.setText(t2.getText().toUpperCase())
    );
    ```

    ### 画像の挿入＆お絵描き

    ```java
    import javax.swing.*;
    var f = new JFrame("drawing");
    f.setVisible(true);

    //JLabelは文字のほかにアイコン画像も表示可能
    var label = new JLabel("test");
    f.add(label);
    //Windowのサイズを、配置された要素に合わせる
    f.pack();

    //画像を扱うクラス
    import java.awt.image.BufferedImage;
    //RGBにそれぞれ8bitの値を持たせている
    var image = new BufferedImage(600, 400, BufferedImage.TYPE_INT_RGB);
    //ImageIconで画像をアイコンとして扱う
    label.setIcon(new ImageIcon(image));
    //画像に合わせてwindowのサイズ調整
    f.pack();
    
    //描画処理(createGraphicsメソッドで、Graphics
    //objectを取得する)
    // ※ jshellでは/vとすることで、型名が見られる
    var g = image.createGraphics();
    //直線を引く(startx,starty,endx, endy)
    g.drawLine(500,0,600,400);
    //再描画
    label.repaint();
    //色指定
    g.setColor(java.awt.Color.RED);
    //(x,y,width,height)
    g.fillRect(300,200,150,100);
    label.repaint();
    ```

    ### 補足(参照型のコピー)

    ```java
    
    // ボタンをコピーしたいとか、思ったんじゃない?
    // 以下では、javaでobjectをcloneする方法について話す

    /*
      複製
      ・参照型はイコールでは同じメモリ番地を指すだけになる
      ・cloneはもはや推奨されていない(使うのやめようね!)

      ・Javaのcopyが面倒なのは、静的型付けであるためだろう
      ・型安全性を保つためという理由で、各fieldを明示的に
        copyする必要があるので、コードが冗長に見える
      ・jsでいうstructuredCloneやpyのdeepcopyのような
        標準ライブラリがないのも原因の一つ

      ・自分で定義したクラスならコピーコンストラクタが使える
      ・copyメソッドを自作することもできる

      ・自動化できないの?
        --> 手動で各フィールドをコピーするなんて
            こんな馬鹿らしいこともない
        --> シリアライズが利用できないだろうか?

    */

    // 補足(コレクションのコピー)
    public class Hoge{
      public int num;

      //通常のコンストラクタ
      public Hoge(int num){
        this.num = num;
      }

      //コピーコンストラクタ
      public Hoge(Hoge other){
        this.num = other.num;
      } 

      //copy用のメソッド
      public Hoge copy(){
        return new Hoge(this);
      }
    }

    List<Hoge> originalList = Arrays.asList(
      new Hoge(1),
      new Hoge(2),
      new Hoge(3)
    );

    List<Hoge> copyList = originalList.stream()
      .map(Hoge::copy)
      .collect(Collectors.toList());
    
    copyList.get(0).num = 10;
    System.out.println(copyList.get(0).num);
    //10
    System.out.println(originalList.get(0).num);
    //1

    //これだと同じものを参照するので無意味
    // List<Hoge> copyList2 = List.copyOf(originalList);

    // ---------------------------

    /*
      シリアライズ
        ・Objectのような構造的なデータを
          ネットワーク経由でやり取りできるよう
          バイト配列へ変換すること
      
      デシリアライズ
        ・シリアライズされたバイト配列を
          元のObjectに戻すこと
      
       @SuppressWarnings("unchecked")
        deepcopy内でのcastの安全性は確保されているので、
        コンパイラによる型チェック警告を抑制している

       T
        入力される型は汎用的なので
        汎用的な処理をするためにジェネリック型を使っている
      
      ObjectOutputStream
        出力先ストリームにデータを書き込む用のクラス
        writeObject(object)で書き込む
        引数には出力先のストリームを指定

      ObjectInputStream
        入力ストリームからデータを読み込む用のクラス
        readObject()で読み込む
        引数には入力元のストリームを生成
      
      ByteArrayOutputStream
        バイト配列の出力ストリームを新しく生成
        下記の場合、ObjectOutputStreamによって書き込まれる

        ※toByteArray()は、バイト配列を新しく作成
          ただし現在のバッファ内のデータはコピーされる

      ByteArrayInputStream
        バイト配列の入力ストリームを新しく生成
    */

    import java.io.*;

    class DeepCopyUtil {

      @SuppressWarnings("unchecked")
      public static <T> T deepCopy(T object){
        T copiedObject = null;
        try{

          var bos = new ByteArrayOutputStream();
          var oos = new ObjectOutputStream(bos);
          oos.writeObject(object);
          oos.flush();

          var bis = new ByteArrayInputStream(bos.toByteArray());
          var ois = new ObjectInputStream(bis);

          //Object型として返されたものを、ジェネリック型に
          //元のオブジェクトの型を保持している
          copiedObject = (T) ois.readObject();
        
        } catch(IOException | ClassNotFoundException e){
          e.printStackTrace();
        }

        return copiedObject;

      }
    }

    /*
      Selializeするには、対象となるクラスが
      java.io.Serializableインターフェースを実装している
      必要がある。
    */
    class MyClass implements Serializable {
      private int id;
      public MyClass(int id){
        this.id = id;
      }

      public int getId(){return id;}
      public void setId(int id){this.id = id;}

      //出力を分かりやすいようにしてるだけ
      @Override
      public String toString(){
        return STR."MyClass{id= \{id} }";
      }

    }

    MyClass original = new MyClass(1);
    MyClass copy = DeepCopyUtil.deepCopy(original);
    original.setId(2);

    System.out.println("Original: " + original);
    // Original: MyClass{id= 2}
    System.out.println("Copy: " + copy);
    // Copy: MyClass{id= 1}
    ```

## Q7 Javaプログラムのひな型について知っていますか?

??? success
    ### ひな型

    ```java
    //今回はhogeというpackageに属させることにした
    package hoge;
    
    //個別importしている
    import javax.swing.JButton;
    import javax.swing.JFrame;
    import javax.swing.JTextField;

    //クラス宣言
    public class SampleForm{
      //メインメソッド
      public static void main(String[] args){
        var frame = new JFrame("test");
        frame.setSize(600, 400);
        //Windowが閉じたときにprogramを終了させる処理
        //JShellの場合は、Windowを閉じると、JShell自体が
        //Resetされてしまうので入れない
        frame.setDefaultCloseOperation(
          JFrame.EXIT_ON_CLOSE
        );
        var text1 = new JTextField();
        frame.add("North", text1);

        var text2 = new JTextField();
        frame.add("South", text2);

        var button = new JButton("to Upper Case");
        frame.add(button);

        button.addActionListener(ae ->
          text2.setText(
            text1.getText().toUpperCase()
          )
        );

        frame.setVisible(true);
      }
    }
    ```

    ### 命名規則

    ```text
    ・Javaの場合、キャメルケースが大半
    ・定数はUpperCaseの場合が多い
    ・プロジェクトに従うというつまらない答えが正解
    ```

    ### 手動実行

    ```bash
    # 今、src/にいるとする
    # なお、git bashで実行している
    # cmd等で実行する場合は、パスの書き方を変更する事

    # bin/
    # └── hoge/
    # src/
    # └── hoge/
    #      └── SampleForm.java/

    # -d クラスファイルの出力先を指定
    $ javac -d ../bin hoge/SampleForm.java

    $ java --enable-preview \
      -cp C:/java_test/SampleForm/bin hoge.SampleForm
    ```

    ```text
    <解説>
      java: JDKのinstallディレクトリを指定
            pathを通しているので、javaで済んでいる
      
      --enable-preview
        ・Javaのプレビュー機能を有効にしている
      
      -cp C:\java_test\SampleForm\bin
        ・クラスパスを指定。
        ・コンパイルされたクラスファイルはbin配下に
          格納されているので、このディレクトリを指定
      
      hoge.SamleForm
        ・実行するメインクラスを指定
        ・hogeパッケージにある、SampleFormクラスの
          mainメソッドを実行している
    ```

## Q8 選択処理について知っていますか?

??? success
    ### 比較

    ```java
    //含む
    if("test".contains("es")){
      System.out.println("a");
    }
    //a

    //基本型の比較
    if(!(4 != 4)){
      System.out.println("a");
    }
    //a

    //参照型の比較, <なら負, >なら正が返る
    if("apple".compareTo("banana") != 0){
      System.out.println("not equal");
    }
    //not equal

    //日付型の比較
    import java.time.LocalDate;
    int future_or_past = LocalDate.now().compareTo(LocalDate.of(2024,1,1));

    if(future_or_past >= 1){
      System.out.println("future");
    }
    //future


    //参照型の比較
    String str = "Test";
    String upperStr = str.toUpperCase();
    // ==は同一オブジェクトか比較していることになるので注意
    if(upperStr.equals("TEST")){
      System.out.println("Yes");
    }
    //Yes
    ```

    ### 論理演算

    ```java
    if(false || true) System.out.println("Yes");
    //Yes

    if(true && true) System.out.println("Yes");
    //Yes

    int a = 10;
    if(a > 0 && a < 100) System.out.println("Yes");
    //Yes

    if(!!!false) System.out.println("Yes");
    //Yes

    //三項演算子
    System.out.println(a > 10 ? "Yes" : "No");
    //No
    ```

    ### if-else

    ```java
    int b = 10;

    //if-else
    if(b > 10){
      System.out.println("b > 10");
    }else if(b > 5){
      System.out.println("b > 5, b <= 10");
    }else{
      System.out.println("b <= 5");
    }
    // b > 5, b <= 10
    ```

    ### switch

    ```java
    //switch(break文はもう要らないよ！)
    switch(b){
      case 1, 2 -> System.out.println("one or two");
      case 0, 10 -> System.out.println("zero or ten");
      default -> System.out.println("other");
    }
    //zero or ten

    //式としてのswitch
    System.out.println(switch(a){
      case 1, 2 -> "one or two";
      case 0, 10 -> "zero or ten";
      default -> "other";
    })
    // zero or ten

    //値を返したい場合
    //yieldを書いた場合は、ブロックは省略できない
    //ブロックを省略した場合は、yieldも省略可能
    String result = switch(b){
      case 1,2 -> {yield "one or two";}
      case 0,10 -> {yield "zero or ten";}
      default -> {yield "other";}
    }

    System.out.println(result);
    //zero or ten

    //--------Java21以降(switch && pattern match)
    // 型と変数をswitchにかける

    Object o = "nyanko";
    var result = switch(o){
      case Integer i -> "%03d".formatted(i);
      case String s -> "--%s--".formatted(s);
      case null -> -1;
      default -> "default!";
    }
    // --nyanko--



    //(補足)昔のjavaで見かけたswitch(冗長！)
    switch(b){
      case 1:
      case 2:
        System.out.println("one or two");
        break;
      case 0:
      case 10:
        System.out.println("zero or ten");
        break;
      default:
        System.out.println("other");
        break;
    }
    ```

## Q9 Listについて知っていますか?

??? success
    ### List

    ```java
    //不変リスト
    var names = List.of("hoge", "fuga", "nyanko");
    System.out.println(names.get(1)); //fuga
    System.out.println(names.size()); //3

    //可変リスト
    var cat = new ArrayList<String>();
    cat.add("tama");
    cat.add("buchi");
    cat.add(1, "tora");
    cat.set(1, "kuro");
    System.out.println(cat.get(1)); //kuro
    
    //可変(要素数固定)
    var list = Arrays.asList("A", "B");
    list.set(1, "C");

    //初期化(可変リスト)
    var cat2 = new ArrayList<String>(
      Arrays.asList("tama", "buchi", "tora")
    );

    //DBI(アンチパターンに近い)
    //普通に、List.ofやStream.ofを使ってあげればよくね?
    //レガシーコードを理解するためには必要そう
    //無名クラスやインスタンスイニシャライザの学習で使える
    var cat3 = new ArrayList<String>(){
      {
      add("tama");
      add("buchi");
      add("tora");
      }
    };
    ```

    ### ジェネリクス

    ```text
    ・扱う型を、<>で囲んで指定する書き方
    ・Listは特定の型を割り当てるので、その型を<>で指定する
    ・基本型は指定できないことに注意
    ```

    ### 要素数によって型が異なる?

    ```java
    System.out.println(
      List.of(1,2).getClass()
    );
    //class java.util.ImmutableCollections$List12

    System.out.println(
      List.of(1,2,3).getClass()
    );
    //class java.util.ImmutableCollections$ListN

    //影響
    try{
      List.of(1,2).get(3);
    }catch(IndexOutOfBoundsException e){
      System.out.println(e);
    }
    //java.lang.IndexOutOfBoundsException: 
    //Index: 3 Size: 2

    try{
      List.of(1,2,3).get(4);
    }catch(ArrayIndexOutOfBoundsException e){
      System.out.println(e);
    }
    //java.lang.ArrayIndexOutOfBoundsException: 
    //Index 4 out of bounds for length 3
    ```

    ```text
    <解説>
      ・ArrayIndexOutOfBoundsExceptionは
        IndexOutOfBoundsExceptionのサブクラスであり
        配列に対して不正なindexを使ってアクセスした時に発生
      
      ・つまり、要素数が0 or 3以上の時、
        内部的には配列が用いられていると言える?
      
      ・いずれにせよ、内部の実装とAPIは分離されているので
        互換性の問題は起きないと考えられる
    ```

    ### ジェネリクスと、SyntaxError

    ```text
    ・ジェネリクスを使うメリットとして、
      型が異なる要素を構文エラーではじけるというものがある
    
    ・例外の場合、実行時まで分からないが、
      コンパイル時に問題を発見できるので時短になる
    ```

    ```java
    var name = new ArrayList<String>();
    name.add("cat");
    name.add(123);
    |  エラー:
    |  不適合な型: intをjava.lang.Stringに変換できません:        
    |      name.add(123);

    ```

    ### ジェネリクスと型推論

    ```java
    var names = List.of("neko", "inu", "nezumi");
    var mutableNames = new ArrayList<>(names);
    //jshell> /v mutableNames
    | //ArrayList<String> mutableNames = [neko, inu, nezumi]  

    //割当先の変数の方から推論することも可能
    List<String> strs = new ArrayList<>();
    ```

    ```text
    ・ArrayListが扱う方は、namesの型から推論できるため
      <>と書けている
    
    ・<>のことを、ダイヤモンドオペレータという
    ```

    ### ジェネリクスとラッパークラス

    ```java
    //ジェネリクスでは基本型は使えない
    var nums = List.of(1,2,3);
    // jshell> /v nums
    //  List<Integer> nums = [1, 2, 3]
    ```

    ```text
    ・基本型の値を、参照型として扱うためのクラスがある
    
    ・varを用いると、その型に型推論される

    | 基本型  | ラッパークラス |
    | ------- | -------------- |
    | int     | Integer        |
    | double  | Double         |
    | boolean | Boolean        |
    | char    | Character      |
    ```

## Q10 配列について知っていますか?

??? success
    ### 配列

    ```text
    ・Listで扱えない基本型が直接使える

    ・要素数をあらかじめ決定しておく必要がある
      (競プロなどでは使われたりする)

    ・可変リストのArrayListもArrayとついていることから
      分かる通り、内部的には配列を用いて値を格納している
    ```

    ```java
    //型について
    var scores = new int[3];
    //jshell> /v scores
    //  int[] scores = int[3] { 0, 0, 0 }

    //size()でもlength()でもなく、lengthで要素数を見る
    //C++みたいに統一してほしいところ
    System.out.println(scores.length); //3

    //初期化
    var scores2 = new int[]{0, 2, 4};

    //記法2(既に割り当てた変数に再割り当てする際はnew必須)
    int[] scores3 = {1, 2, 3};
    int[] scores3 = new int[]{1, 4, 5};

    //アクセス
    scores3[2] = 4;
    System.out.println(scores3[2]); //4
    ```

    ### 多次元配列

    ```java
    //添え字を指定するだけ

    var mat = new int[2][3];
    // mat ==> int[2][] { int[3] { 0, 0, 0 }, int[3] { 0, 0, 0 } }

    var mat2 = new int[][]{{1, 2}, {3, 4}}
    ```

## Q11 レコードについて知っていますか?

??? success
    ### 違う型の値をListで扱いたい場合

    ```text
    ・違う種類の値でも、Listでまとめて扱え（なくはない）
    ```

    ```java
    //実際には共通した型を扱っている
    //そのためString型や、Integer型のメソッドは使えない
    var exam = List.of("math", 80, true);
    //jshell> /v exam
    //List<Serializable&Comparable<? extends Serializable&Comparable<?>&java.lang.constant.Constable>&java.lang.constant.Constable> exam = [math, 80, true]

    //methodを用いるにはcastする必要がある
    System.out.println(
      ((String) exam.get(0)).toUpperCase()
    );
    //MATH
    ```

    ### レコード

    ```text
    ・Java16から導入された

    ・型を用いているため、キャスト無しでメソッドが使える

    ・レコードの要素をコンポーネントという

    ・コンポーネント名()とすることで、コンポーネントの値
      を取得可能
    
    ・本来はimmutableなクラス
    ```

    ```java
    // java22で--enable-previewを使用 or java23
    record Exam(String name, String subject, int score){};
    var e1 = new Exam("nyanko", "math", 80);

    System.out.println(
      STR."\{e1.name()}: \{e1.subject()} = \{e1.score()}"
    )
    // nyanko: math = 80

    ```

    ### レコード(補足)

    ```text
    ・fieldは、private finalになり、objectはimmutableに
    ・コンストラクタ、toString, equals, hashCodeは自動実装
    ・getter --> <field_name>()
    ・setter --> finalなのでなし

    ※Lombokのようなライブラリを使わなくてもよくなる
    ```

    ### レコードと通常のJavaクラスの対応(補足)

    ```java
    //record
    public record Title(String value){}

    //recode(コンストラクタ + method)
    public record Title(String value){
      public Title(String value){
        this.value = value + "Hogehogeeee";
      }

      public int length(){
        return this.value.length();
      }
    }

    //normal
    public final class Title{
      private final String value;

      public Title(String value){
        this.value = value;
      }

      public String value(){
        return value;
      }

      @Override
      public boolean equals(Object other){
        if(other == this) return true;
        if(other == null || obj.getClass() != this.getClass()) return false;
        var that = (Title) other;
        //格納している値同士で比較
        return Objects.equals(this.value, that.value);
      }

      @Override
      public int hashCode(){
        return Objects.hash(value);
      }

      @Override
      public String toString(){
        return "Title[" + "value=" + value + "]";
      }
    }
    ```

## Q12 Mapについて知っていますか?

??? success
    ### 不変Map

    ```java
    var pets = Map.of("cat", "tama", "dog", "tarou");
    System.out.println(pets.get("cat")); //tama

    //見つからない場合、デフォルト値
    System.out.println(pets.getOrDefault("nezumi", "tyu-"));
    // tyu-

    //サイズ確認
    System.out.println(pets.size()); //2
    ```

    ### 可変Map

    ```java
    //HashMap(要素の順番については保証しない)

    var animals = new HashMap<String, String>();

    animals.put("dog", "tarou");
    animals.put("cat", "tama");
    animals.put("cat", "kuro");

    System.out.println(animals.get("cat")); //kuro
    ```

    ### 不変のメリット

    ```text
    ・実際には多くのオブジェクトは変更不要

    ・想定しないデータ変更が起こらないため、
      エラーの原因を突き止めやすくなる
    ```

## Q13 反復処理について知っていますか?

??? success
    ### 通常のfor文

    ```java
    for(int i = 0; i < 5; i++){
      System.out.print(i + " ");
    }
    //0 1 2 3 4 

    //初期値; 繰り返し条件; 繰り返し時の処理なので
    //以下の様に無限ループも書ける
    int j = 0;
    for(;;){
      j++;
      System.out.print(j + " ");
      if(j >= 3) break;
    }
    // 1 2 3 
    ```

    ### while

    ```java
    int k = 0;
    while(true){
      k++;
      System.out.print(k + " ");
      if(k > 3) break;
    }
    // 1 2 3 4
    ```

    ### do-while

    ```java
    int l = 100;
    do{
      System.out.print(l + " ");
      l++;
    }while(l < 10);

    //100 
    //一回は実行されるというのが特徴
    ```

    ### continue, break

    ```java
    //continue(次のループへ)
    //break(ループを中止)

    for(int i=0; i<10; i++){
      if(i % 3 == 0) continue;
      if(i % 9 == 0) break;
      System.out.print(i + " ");
    }
    // 1 2 4 5 7 8 
    ```

    ### 二重ループ

    ```java
    import static java.util.FormatProcessor.FMT;

    for(int i=1; i<10; i++){
      for(int j=1; j<10; j++){
        System.out.print(FMT."%3d\{i*j}");
      }
      System.out.println("");
    }

    /*
      1  2  3  4  5  6  7  8  9
      2  4  6  8 10 12 14 16 18
      3  6  9 12 15 18 21 24 27
      4  8 12 16 20 24 28 32 36
      5 10 15 20 25 30 35 40 45
      6 12 18 24 30 36 42 48 54
      7 14 21 28 35 42 49 56 63
      8 16 24 32 40 48 56 64 72
      9 18 27 36 45 54 63 72 81
    */

    for(int i = 0; i < 5; i++){
      for(int j = 0; j <= i; j++){
        System.out.print(j+1);
      }
      System.out.println();
    }

    /*
    1
    12
    123
    1234
    12345
    */
    ```

    ### 迷路

    ```java
    package maze;

    import java.io.IOException;

    /**
     * レコード、ループ文、入出力の要素を取り入れた迷路
     * メソッドを用いていないので処理の流れが分かりにくい
    */
    public class Maze {
      public static void main(String[] args) throws IOException {
        record Position(int x, int y) {};
        int[][] map = {
            { 1, 1, 1, 1, 1, 1 },
            { 1, 0, 1, 0, 0, 1 },
            { 1, 0, 0, 0, 1, 1 },
            { 1, 0, 1, 0, 0, 1 },
            { 1, 1, 1, 1, 1, 1 },
        };

        var current = new Position(1, 1);
        var goal = new Position(4, 3);

        /**
        * 現在地: o
        * 移動不可マス: *
        * 移動可能マス: .
        */
        for (;;) {
          for (int y = 0; y < map.length; y++) {
            for (int x = 0; x < map[y].length; x++) {
              // 現在の位置から上下2マスまで表示
              boolean x_conditions = 
                current.x() - 2 <= x && x <= current.x() + 2;
              boolean y_conditions = 
                current.y() - 2 <= y && y <= current.y() + 2;
              if (!(x_conditions && y_conditions))
                continue;

              //描画
              if (x == current.x() && y == current.y()) {
                System.out.print("o");
              } else if (map[y][x] == 1) {
                System.out.print("*");
              } else if (x == goal.x() && y == goal.y()) {
                System.out.print("G");
              } else {
                System.out.print(".");
              }
            }
            System.out.println();
          }

          //参照型の要素比較
          if (current.equals(goal)) {
            System.out.println("GOAL!!!");
            break;
          }

          int ch = System.in.read();
          var next = switch (ch) {
            case 'a' ->
              new Position(current.x() - 1, current.y());
            case 'w' ->
              new Position(current.x(), current.y() - 1);
            case 's' ->
              new Position(current.x(), current.y() + 1);
            case 'd' ->
              new Position(current.x() + 1, current.y());
            default ->
              current;
          };

          // 次の移動地点が移動可能なら
          if (map[next.y()][next.x()] == 0)
            current = next;
        }
      }
    }
    ```

    ### 拡張for

    ```java
    //Java 5から

    var fruits = List.of("apple", "banana", "grape");

    for(var fruit: fruits){
      System.out.println(fruit);
    }
    // apple
    // banana
    // grape

    int[] nums = {2,3,5,7};
    for(int num: nums){
      System.out.print(num * 10 + " ");
    }
    // 20 30 50 70 
    ```


## Q14 デバッガの使い方について知っていますか?

??? success
    ### (補足)デバッガの使い方

    ```text
    <vscodeの場合>
      ・F5: 続行
      ・F10: ステップオーバー
      ・F11: ステップイン
      ・Shift+F11: ステップアウト
      ・Ctrl+Shift+F5: 再起動
      ・Shift+F5: 停止

    ブレークポイント
      ・特定の行に印をつけることで利用可能
      ・デバッガは該当の行の処理を行う前に停止する
    変数
      ・現在の変数の値が閲覧可能
    
    ウォッチ式
      ・特定の変数の値を監視し続ける

    ステップオーバー
      ・呼び出し先には入らず、次の行へ移動
    
    ステップイン
      ・呼び出し先に入り、該当関数の先頭行へ移動
    
    ステップアウト
      ・現在実行中のメソッドの残りの部分を実行し
        呼び出し元に戻る
    
    ※現在の行にメソッド呼び出しがない場合は
      ステップインもステップアウトも処理は同じ
    ```

## Q15 Streamについて知っていますか?

??? success
    ### Stream

    ```text
    ・Java8から導入

    ・Streamに対して、以下の処理を適用する
      値を操作する中間処理
      値をまとめる終端処理
    
    ・メソッドチェーンの形で書くため関数型の記法にそっくり

    ・ラムダ式が多く使われる

    ・元の集合には影響を与えない
    ```

    ### 変換

    ```text
    | 変換前         | 変換後 | 実際の記法  |
    | -------------- | ------ | ----------- |
    | List           | Stream | .stream()   |
    | Stream         | List   | .toList()   |
    | Array          | Stream | Stream.of() |
    | 複数行の文字列 | Stream | .lines()    |
    ```

    ### 終端処理

    ```text
    | 名前      | 意味                             |
    | --------- | -------------------------------- |
    | allMatch  | すべて満たすか                   |
    | anyMatch  | 一つでも満たすか                 |
    | noneMatch | 一つも満たさないか               |
    | collect   | Collectorsクラスの終端処理を利用 |
    | foreach   | 出力処理                         |
    ```

    ```java
    import java.util.stream.Collectors;

    var names = List.of("tama", "taro", "nyanko");
    
    names.stream()
      .collect(Collectors.joining("/"));
    // tama/taro/nyanko

    names.stream()
      .noneMatch(s -> s.contains("y"));
    //false

    //List.ofの形を返す
    var listof = names.stream().toList();
    //ArrayListの形を返す
    var arraylist = names.stream().collect(
      Collectors.toList()
    );
    arraylist.set(1, "neko");

    //中間処理がない場合、List自体がforEachを持っているので
    //Streamを介さずに呼び出せる
    names.stream().
      forEach(s -> System.out.println(s));
    // tama
    // taro
    // nyanko

    //メソッド参照
    names.forEach(System.out::println);
    // tama
    // taro
    // nyanko

    //直接指定もできる
    Stream.of(1,2,3).toList();
    ```

    ### 中間処理

    ```text
    | 名前     | 意味                 |
    | -------- | -------------------- |
    | filter   | trueとなる値だけ処理 |
    | skip     | 要素を飛ばす         |
    | limit    | 要素を制限する       |
    | distinct | 重複を排す           |
    | sorted   | 要素並び替え         |
    | map      | 関数適用             |
    | reduce   | 畳み込み処理         |

    ```

    ```java
    var names = List.of("neko", "nyanko", "nekonuko");

    names.stream()
      .filter(s -> s.length() > 5)
      .toList();
    // [nyanko, nekonuko]

    names.stream().skip(1).toList();
    //  [nyanko, nekonuko]

    names.stream().limit(2).toList();
    // [neko, nyanko]

    names.stream().sorted().toList();
    // [neko, nekonuko, nyanko]

    List.of("neko", "neko", "inu").stream()
     .distinct()
     .toList();
    //[neko, inu]

    List.of("neko", "inu", "nezumi").stream()
     .map(s -> s.repeat(2))
     .toList();
    // [nekoneko, inuinu, nezuminezumi]

    //合計値(Integer::sumでもいいんだけどね)
    //第1引数=初期値、第2引数=関数が基本形。
    //戻り値が初期値と入れ替わる
    List.of(1,2,3,4,5).stream()
      .reduce(0, (a,b) -> a + b);
    // 15

    //最小値 Integer::minでもいいけどね
    List.of(1,2,-2,4,5).stream()
      .reduce((a,b) -> a < b ? a : b); 
    // -2

    //連結
    List.of("neko", "inu", "nezumi").stream()
    　.reduce("", String::concat);
    //nekoinunezumi
    ```

    ### reduceとモノイド

    ```text
    モノイド
      ・閉包性、結合律、単位元の存在を持つ
      
      閉包性(集合上の二項演算)：
        演算の結果も同じ型になるため、型安全が保障される
      
      結合律：
        順序を変更しても結果が同じであるため並行処理が可能
      
      単位元：
        累積計算の際、単位元を初期値とすることで、
        最初の要素と単位元の演算から開始可能

        ※
          1. reduceの並列実行時には、以下の手順を踏む
          2. Streamが複数部品に分割される
          3. 各部品の先頭要素が単位元と演算される
          4. 後続の要素が、その結果と演算される
          5. 最後に、分割されたStreamを1つにまとめる
        
        ※そのため以下の場合、3スレッドで実行すると
        Stream.of(1, 2, 3)
          .parallel()
          .reduce(11, (a, b) -> a + b);

        (11 + 1) + (11 + 2) + (11 + 3) = 39になる 

        ※またStreamが空の場合、単位元が返されるが......
          各々のStreamを結合する際も、単位元は結果に影響を
          及ぼさないため、何の問題も起こらない


    ```

    ```java
    //正しく単位元を指定した場合

    Stream.of(1,2,3).parallel().reduce(0, (a,b) -> a + b);
    // 6

    Stream.of(1,2,3).parallel().reduce(1, (a,b)-> a * b);
    // 6


    //バイナリ演算子のオペランドの型が、推定できないと
    //型チェックエラーを吐く
    
    //ダメな例(genericsがないので、java.lang.Obejct扱い)
    //Object型のa,bに対して、+演算子適用はできないので
    List.of().stream().reduce(0, (a,b) -> a + b);

    //いい例

    List.<Integer>of().stream().reduce(0, (a,b) -> a + b);
    // 0
    ```

    ### その他の中間操作等

    ```java
    //mapToInt: 任意のObjectを受け取り、int値を返す
    //IntStreamは、toListを持たないため、toArrayを返した
    List<String> list = List.of("1", "2", "12");
    list.stream()
      .mapToInt(Integer::parseInt)
      .map(s -> s * 10)
      .toArray();
    // int[3] { 10, 20, 120 }

    //文字列を数字にして変換
    Stream.of("neko", "nyanko", "nukonuko")
      .mapToInt(String::length)
      .toArray();
    //int[3] { 4, 6, 8 }

    //1: Nの変換
    Stream.of("Neko", "Nyanko", "nukonuko")
      .flatMap(p -> Stream.of(p, p.length()))
      .toArray()
    ;
    // Object[6] { "Neko", 4, "Nyanko", 6, "nukonuko", 8 }

    //カスタムソート
     Stream.of("Neko", "Inu", "Nyaa--")
      .sorted((a,b) -> a.length() - b.length())
      .toList();
    // [Inu, Neko, Nyaa--]

    //peek(): 副作用を持つ処理を行いながら要素取り出し
    // forEachでは次にチェーン出来ないため、役立つ
    List.of("apple", "banana", "cherry")
      .stream()
      .peek(System.out::println)
      .map(String::toUpperCase)
      .forEach(System.out::println)

    // apple
    // APPLE
    // banana
    // BANANA
    // cherry
    // CHERRY
    ```

    ### その他の終端操作等

    ```java

    /*
     * 引数を指定したtoArray()
     * 返される配列の型はT[]となる
     * 一つのStreamに対する処理は1回しかできないので注意
     * var1のコメントを解除すると、var2でエラーになる
    */
    Stream<String> name = Stream.of("neko", "nyanko");
    //Object[] var1 = name.toArray();
    String[] var2 = name.toArray(String[]::new);

    //個数
    System.out.println(
      List.of("peach", "pine", "apple")
        .stream()
        .filter(n -> n.startsWith("p"))
        .count()
    ); //2

    //最小、最大
    Optional<Integer> minOpt = List.of(1, 2, 10)
      .stream()
      .min(Integer::compareTo);

    //Optionalは単一の値をラップするためのコンテナ
    //値が入っていなければthrow, 入っていれば該当値を返す
    int minValue = minOpt.orElseThrow();
    // 1

    //合計(Integer -> intに変換)
    List.of(1, 2, 10).stream()
      .mapToInt(Integer::intValue)
      .sum(); //13

    //先頭の要素
    List.of("neko", "inu", "nezumi")
      .stream()
      .skip(2)
      .findFirst()
      .orElseThrow()
    ;   //nezumi 
    //空の場合、Optional.empty()が返る
    ```

    ```text
    findAny, forEach vs findFirst, forEachOrdered
      ・並列処理に違いが出る
      ・findAny, forEachは逐次的に要素を処理し、
        ストリーム内の要素の順序を保持しない
    ```

    ### 基本型のStreamクラス

    ```text
    ・IntStream
    ・LongStream
    ・DoubleStream
    --> 専用のStreamクラス
    ```

    ```java
    int[] nums = {2,5,3};

    //Int(mapsToInt等で変換する必要がないので楽)
    //map,reduce等も使える
    IntStream.of(nums).sum();

    //範囲指定(rangeClosedの場合、endになる)
    IntStream.range(0,10)
      .map(s -> s*10)
      .toArray();
    // int[10] { 0, 10, 20, 30, 40, 50, 60, 70, 80, 90 }

    //疑似乱数
    //ints(個数,最小,最大)
    new Random().ints(10,0,100).toArray();
    //int[10] { 87, 34, 88, 46, 61, 14, 79, 31, 80, 2 }

    //iterate(繰り返し)
    IntStream.iterate(0, i -> i<10, i -> i+1).toArray();
    // int[10] { 0, 1, 2, 3, 4, 5, 6, 7, 8, 9 }

    //繰り返し条件を省いたもの(limitで制限する)
    IntStream.iterate(2, i -> i*2).limit(5).toArray();   
    // int[5] { 2, 4, 8, 16, 32 }

    LongStream.iterate(2, i-> i*i).limit(6).toArray();   
    // long[6] { 2, 4, 16, 256, 65536, 4294967296 }

    // -> Stream
    IntStream.range(0,3)
      .mapToObj(n -> "*".repeat(n))
      .toList();
    //  [, *, **]

    // -> IntStream
    Stream.of("neko", "inu")
      .mapToInt(s -> s.length())
      .toArray();
    // int[2] { 4, 3 }
    ```

## Q16 Optionalについて知っていますか?

??? success
    ### Optional

    ```text
    ・値があるかないかを管理する
    ```

    ```java
    //Streamの返り値で、optionalになる場合
    Optional<String> o1 = 
      List.of("apple", "banana", "cherry")
        .stream()
        .findAny();
    
    //空である場合
    Stream.of().findAny();
    // Optional.empty

    //Optionalの作成
    var o2 = Optional.of("test");

    //空Optionalの作成
    Optional.empty();

    //値を取り出す(値があると確信を持てるとき限定)
    o2.get();
    //o2.get().getClass(); class java.lang.String

    //値を取り出す(default値有り)
    Optional.empty().orElse("default"); //default

    //値を持っているか確認。
    Optional.empty().isPresent(); //false
    Optional.of("test").isEmpty(); //false

    //値があるときだけ処理を行う
    Optional.of("test")
      .ifPresent(
        s -> System.out.println(s.toUpperCase())
      );
    ```

## Q17 メソッドについて知っていますか?

??? success
    ### 基本的なメソッド

    ```java
    void greeting(String name){
      System.out.println(STR."Hello, \{name}");
    }

    greeting("nyanko");
    // Hello, nyanko

    int twice(int x){return x * 2;}
    int twenty = twice(10);
    ```

    ### staticメソッド

    ```java
    //static = 変数orメソッドがインスタンスではなく
    //暮らす事態に属していることを示すキーワード

    public class MethodSample{
      static int twice(int x){return x*2 ;}
    }

    MethodSample.twice(10); //20
    ```

    ### public static void main(String[] args)

    ```text
    エントリーポイント
      ・ここから処理が始まる
      ・Java22までなら、この形でないとエントリーポイント
        と認識してくれない
    
    String[] args
      ・コマンドラインから呼び出したときのパラメータを
        String[]に入れて、argsという名前を付けている
    
    public
      ・JVMがmainメソッドを発見できるようにするため

    void
      ・返り値はない
      ・mainは開始点として呼ばれるだけなので返り値は不要
    
    static
      ・JVMがJavaアプリの実行を開始するとき、
        使用可能なクラスのObjectはまだない
        
      ・staticでない場合、例えばTestクラスの中に
        エントリーポイントがあるとして、
        そのクラス外で、インスタンスを作成する必要に駆られる

      ・staticであるため、instanceを作らずに
        mainメソッドにアクセス可能
    ```

    ### レコード + メソッド(Q11の補足参照)

    ```java
    record Student(String name, int math, int english){
      int average(){
        return ((this.english() + this.math()) / 2);
      }
    }
    
    var student = new Student("hoge", 70, 98);
    student.average(); //84
    ```

    ### java.util.function

    ```java
    //ラムダ式のような記法(Stream API以外)

    //Function<T, R>: Tの型を受け取り、Rの型を返す
    Function<Integer, Integer> twice = n -> n*2;
    System.out.println(twice.apply(10));

    // Consumer(T): Tの型を受け取り、voidを返す
    Consumer<String> greet = s -> System.out.println("Hello " + s);
    greet.accept("Nyanko!");
    // Hello Nyanko!

    //Predicate<T>: .test()でbooleanを返す
    //Supplier<T>: .get()で何かを返す。引数無し
    //UnaryOperator<T>: 引数と返り値の型同一

    //※removeIf, replaceAll, sort, forEach等は
    //単なるArrayに対しても使える

    List<String> names = Arrays.asList("neko", "nyanko");
    names.forEach(n -> System.out.println(n));
    ```

    ### メソッド参照

    ```text
    ・static -> クラス名::メソッド名
    ・normal -> instance::メソッド名

    メリット
      ・格好いい（主観）
      ・コードがシンプルになり、読みやすくなる
      ・ラムダ式内で、不要な変数名を定義する必要がなくなる
    
    デメリット
      ・引数の個数が自明でない
    ```

    ```java
    var lists = List.of("neko", "inu", "nezumi");
    lists.forEach(item -> System.out.println(item));
    lists.forEach(System.out::println);

    var modifyList = lists.stream()
      .map(String::toUpperCase)
      .collect(Collectors.toList());
    
    int[] array = List.of("1", "2", "3")
      .stream()
      .mapToInt(Integer::parseInt)
      .toArray();
    ```

    ### オーバーロード

    ```text
    ・引数の組み合わせが異なる、同名のメソッドを複数定義
    ```

    ```java
    int multiply(int x, int y){
      return x * y;
    }

    int multiply(int x, int y, int z){
      return x*y*z;
    }

    multiply(7,8);
    multiply(7,8,9);
    ```

## Q18 再帰について知っていますか?

??? success
    ### 再帰

    ```text
    ・関数の中で、関数自身を呼び出す事
    ```

    ### スタック

    ```text
    ・ローカル変数/メソッドの呼び出し情報が格納される場所

    ・無限に再帰を行うと、スタックの領域に限界が生じ、
      スタックオーバーフローが起きる
    ```

    ### Javaにおける再帰

    ```java
    for(int i=0; i < 5; i++){System.out.println(i);}

    void loop(int i){
      if(i >= 5) return;
      System.out.println(i);
      loop(i + 1);
    }

    loop(0);
    ```

## Q19 ファイルアクセスと例外について知っていますか?

??? success
    ### Filesクラス

    ```text
    ・ファイルの操作に関するメソッドが存在する

    | method                             | desc           |
    | ---------------------------------- | -------------- |
    | String readString(Path)            | 文字列読み込み |
    | Path writeString(Path, String)     | 文字列書き込み |
    | long size(Path)                    | size取得       |
    | FileTime getLastModifiedTime(Path) | 最終更新日時   |
    | boolean exists(Path)               | 存在確認       |
    | boolean isDirectory(Path)          | フォルダ確認   |
    | Stream<Path> list(Path)            | ファイル一覧   |
    ```


    ### ファイル書き込み

    ```java
    /*
     * current_dirに文字列を書き込んだファイルを保存
    */
    import java.io.IOException;
    import java.nio.file.Files;
    import java.nio.file.Path;

    public class WriteFile{
      public static void main(String[] args) throws IOException {
        var message = """
          test
          message
          """;
        //Path名を指定
        var p = Path.of("test.txt");
        //fileに文字列保存(path, str)
        Files.writeString(p, message);
      }
    }
    ```

    ### ファイル読み込み

    ```java
    import java.io.IOException;
    import java.nio.file.Files;
    import java.nio.file.Path;

    public class ReadFile{
      public static void main(String[] args) throws IOException{
        var p = Path.of("test.txt");
        String s = Files.readString(p);
        System.out.println(s);
      }
    }
    ```

    ### 例外

    ```text
    try/catch/finally
      ・実行時エラーの処理をする
    
    check例外
      ・catch句 or throws句必須
      ・ExceptionクラスのRuntimeException以外
    
    非check例外
      ・必須ではない
      ・Errorクラス, RuntimeExceptionクラス
    
    例外の基クラス
      ・Throwable
    ```

    ```java
    import java.nio.file.*;
    var p = Path.of("test.txt");
    String s;

    try{
      s = Files.readString(p);
    }
    catch(NoSuchFileException e){
      System.out.println("ファイルが見つからない");
    }
    finally{
      System.out.println("必ず表示される");
    }
    System.out.println(s);
    ```

    ### throw

    ```text
    ・敢えて例外を投げるときに使用
    ```

    ```java
    void greeting(String name){
      //Illeagal...は非check例外
      if (name == null)
        throw new IllegalArgumentException();
      System.out.println(STR."Hello \{name} ");
    }

    greeting("neko");
    // Hello neko 

    greeting(null);
    //|  例外java.lang.IllegalArgumentException
    //|        at greeting (#1:3)
    //|        at (#3:1)
    ```

    ### throws

    ```text
    ・method内で、検査例外のcatch句を書かないなら
      呼び出し側で処理する必要がある
    
    ・その際、どの例外を投げるのかを記述するために使用
    ```

    ```java
    //methodから検査例外をthrowする場合
    public class Main{
      public static void main(String[] args){
        Sample s = new Sample();
        try{
          s.hello("");
        }catch(IllegalArgumentException e){
          System.out.println("blank");
        }catch(Exception e){
          System.out.println("please input name");
        }
      }
    }

    public class Sample{
      public void hello(String name) throws Exception{
        if ("".equals(name))
          throw new Exception();
        
        if(name == null)
          throw new IlleagalArgumentException();
        
        System.out.println(STR."Hello, \{name}");
      }
    }
    ```

## Q20 Server関連の処理について知っていますか?

??? success

    ### Server

    ```java
    import java.io.IOException;
    import java.io.InputStream;
    import java.net.ServerSocket;
    import java.net.Socket;

    public class SimpleServer{
      public static void main(String[] args)
        throws IOException{

        //ServerSocket(port_number):port指定
        //Socket:ネットワーク通信部分の担当
        var server = new ServerSocket(1600);
        System.out.println("Waiting...");

        //クライアント待ち受け
        Socket soc = server.accept();
        System.out.println(
          "connect from " + soc.getInetAddress()
        );

        //データ入力に使うクラス。
        InputStream input = soc.getInputStream();
        System.out.println(input.read()); //data受信
        input.close();

        //socketを閉じる
        soc.close();
      }
    }
    ```

    ### client

    ```java
    import java.io.IOException;
    import java.io.OutputStream;
    import java.net.Socket;

    public class SimpleClient{
      public static void main(String[] args)
        throws IOException{
        
        //localhost::1600に対して接続
        var soc = new Socket("localhost", 1600);
        //データ出力(送信)に使うクラス
        //socketの場合、getOutputStreamメソッドで得る
        OutputStream output = soc.getOutputStream();
        output.write(234); //data送信
        output.close();
        soc.close();

      }
    }
    ```

    ### TCP/UDP

    ```text
    javaにおけるTCP通信
      ・Socket.ServerSocket
    
    javaにおけるUDP通信
      ・DatagramSocket
    ```

    ### try-with-resources

    ```text
    ・tryブロックを抜ける際に、autoでcloseする

    try(closeが必要なオブジェクトの変数割り当て){処理}
    ```

    ```java
    import java.io.IOException;
    import java.io.InputStream;
    import java.net.ServerSocket;
    import java.net.Socket;

    public class SimpleServer {
      public static void main(String[] args)
          throws IOException {
        try (var server = new ServerSocket(1600);) {
          System.out.println("Waiting...");

          try (
              Socket soc = server.accept();
              InputStream input = soc.getInputStream();) {
            System.out.println(
                "connect from " + soc.getInetAddress());
            System.out.println(input.read());
          }
        }
      }
    }

    //Waiting...
    //connect from /127.0.0.1
    //234
    ```

    ```java
    import java.io.IOException;
    import java.io.OutputStream;
    import java.net.Socket;
    import java.net.ConnectException;

    public class SimpleClient {
      public static void main(String[] args)
          throws IOException {
        
        //Serverが起動していない時の例外処理付き
        try (
            var soc = new Socket("localhost", 1600);
            OutputStream is = soc.getOutputStream();) {
          is.write(0);
        } catch (ConnectException e) {
          System.out.println("Server is not booted.");
        }
      }
    }
    ```

## Q21 HTTP関連の処理について知っていますか?

??? success

    ### HTTP(コラム)

    ```text
    HTTPプロトコル
      ・ブラウザ/サーバ間で、Web情報をやり取りするための
        通信規則(URL入力 --> HTTPリクエスト --> レスポンス)
      
      ・HTTPメッセージを、クライアント/サーバ間でやりとり

    HTTPメッセージの構造(HTTP/1.1の場合)
      1. start line
      2. header
      3. blank
      4. body
    
    例(HTTP/1.1の場合)
      POST /list HTTP/1.1
      Host: example.com
      Content-Type: text/plain; charset=utf8

      Hello world!

    HTTP/1.2以降
      ・送信するデータの形式は、バイナリになった
      ・1.1までは、テキスト形式
    ```

    ### HTTPメッセージの構成(現在)

    ```text
    Header Section
      Header Field
      Header Field
      Header Field
    Content
    Trailer Section
      Trailer Field
      Trailer Field
      Trailer Field

    Header Section
      ・従来はHeaderと呼ばれていたもの
      ・コンテンツの前に来るものは此処に格納
    
    Content
      ・データが格納される領域。従来はbody or payload
    
    Trailer Section
      ・従来はHeaderと呼ばれていたもの
      ・コンテンツの後に来るものは此処に格納
    ```

    ### HTTPフィールドについて

    ```text
    ・HTTPメッセージのメタデータ(メッセージそのものや
      コンテンツに関する情報)

    ・コンテンツを送った後にもTrailer Filedを送れる

    ・区切り文字は定義されていないが、key:valueのように
      区切り文字を使って表した表現をFiled Lineと呼んだり
      key --> フィールド名
      val --> フィールド値
    ```

    ### コンテンツについて

    ```text
    ・レスポンスで言うと、Webページのデータ
    ・リクエストで言うと、Uploadするファイルなど
    ・相手にデータを送信する際は、コンテンツに格納する
    ```

    ### HTTPリクエストメッセージ

    ```text
    ```