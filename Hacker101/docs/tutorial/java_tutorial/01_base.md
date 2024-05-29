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

    # doubleの制限に引っかからないようにするためStrで渡す
    # この際、instanceを作成する
    jshell> new BigDecimal("3.14159263589793238")
              .multiply(BigDecimal.valueOf(0.2));
    $80 ==> 0.628318527179586476
    ```

    ### Object

    ```text
    ・BigDecimalも、Stringも参照型であれば、
      Objectが先祖になる
    ```