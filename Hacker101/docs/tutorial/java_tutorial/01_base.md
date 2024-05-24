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

## Q4 基本的な構文について知っていますか?

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

    ### 例外

    ```bash
    jshell> import java.lang.ArithmeticException; 
      ...>  try{
      ...>    int a =  3 / 0;
      ...> }catch(ArithmeticException e){
      ...>     System.out.println("Error");
      ...> };
      
    Error
    ```
