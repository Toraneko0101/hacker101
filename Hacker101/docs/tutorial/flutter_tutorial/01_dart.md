# クイズ(1:Dart言語)

## Q1. Dart言語の基本文法について知っていますか?

??? success

    ### 簡単に試したい場合

    ```text
    <online>
      https://dartpad.dev/
    
    <local>
      dart create <project-name>
    ```

    ### Hello World

    ```dart
    void main(){
      print("Hello, World");
    }
    ```

    ### var

    ```dart
    //ローカル変数にはvarを用いるのが公式の推奨事項らしい
    var name = "Hogehoge";
    var year = 1999;
    var syosuu = 3.14;
    var lists = ["Sun", "Mon", "Tues"];
    var dicts = {
      "hoge": ["hiyoko"],
      "url": "//piyopiyo/baby_chicken.jpg"
    };

    //Objectが単一の方に制限されていない場合
    Object name = "Hoge";

    //明示的な型定義
    String name = "Hoge";

    //NULL安全性 -> nullを許可するには、型宣言の末尾に?
    String? name;
    String name2;

    //final & const
    const bar = 100;
    final double hoge = 3.14 * bar; //実行時に値初期化可能
    ```

    ### 型の例など

    ```dart
    var name = "Hoge";
    print(name.runtimeType); //String(型を調べる)
    print(name is String); //型が一致しているか調べる
    ```

    ### 制御文と繰り返し文

    ```dart
    if(year >= 2010){
      print("Generation Alpha");
    }
    else if(year >= 1995){
      print("Generation Z");
    }
    else{
      print("Uncategorized");
    }

    if(12 is int){
      print("12はint型です")
    }

    //for
    for(final item in lists){
      print(item);
    }

    for(int month = 1; month <= 12; month++){
      print(month);
    }

    //while
    while(year < 2016){
      year += 1;
    }

    //do-while
    int i = 0;
    do{
      print(i);
      i += 1;
    }while(i <= 1);

    ```

    ### switch式

    ```dart
    // switch文(旧来)

    switch(charCode){
      case slash || star || plus || minus:
        token = operator(charCode);
      case comma || semicolon:
        token = punctuation(charCode);
      case >= digit0 && <= digit9:
        token = number();
      default:
        throw FormatException("Invalid");
    }

    // switch式
    // caseキーワードがなく、defaultは_, =>のあとに処理を書く
    token = switch(charCode){
      slash || star || plus || minus => operator(charCode),
      comma || semicolon => punctuation(charCode),
      >= digit0 && <= digit9 => number(),
      _ => throw FormatException("Invalid")
    }
    ```

    ### 関数

    ```dart
    //基本。
    //引数や戻り値の型は指定することが推奨されている

    int fibonacci(int n){
      if(n == 0 || n == 1) return n;
      return fibonacci(n - 1) + fibonacci(n - 2);
    }

    var result = fibonacci(10);
    //-------------------------


    /* 
      <名前付きパラメータ>
      {}で囲むと、名前付きパラメータにできる
      requiredがないと、必須でなくなる
      defaultでもrequiredでもないなら、null許容である必要がある
    */
    void greet({required String name, int? level, String value = "default"}) {
      print("Hello, ${name}.");
      if (level == null) {
        print("Something's wrong.");
      }
      ;
    }

    //mainはエントリーポイントとして機能する
    //戻り値はvoid型で、引数はList<String>型のOptionパラメータ
    void main() {
      greet(name: "Nyanko");
      greet(level: 72, name: "Nukonuko");
    }
    //------------------------------


    /*位置引数で、オプションの引数を作りたい場合*/
    String say(String from, String msg, [String device = "tin can telephone"]){
      var result = "$from says $msg with a $device.";
      return result;
    }

    //assertはアプリ実行時には消える。確認用
    assert(say("Bob", "Henry") == "Bob says Henry with a tin can telephone.");
    assert(say("Bob", "Henry", "carrier pigeon") == "Bob says Henry with a carrier pigeon.");
    //------------------------------

    //コマンドラインアプリ
    void main(List<String> args){
      print(args);
      assert(args.length == 2);
      assert(int.parse(args[0]) == 1);
      assert(args[1] == "test");
    }
    // -----------------------------


    //関数を引数にする
    void printElem(int element){print(element);};
    var list = [1,2,3];
    list.forEach(printElem);
    //------------------------------


    /*
      無名関数 
      {}をつけた場合、=>は不要であることに注意
    */
    const list = ["Apples", "Bananas", "Calamansis"];

    var uppercaseList = list.map((item){
      return item.toUpperCase();
    }).toList();

    for(var item in uppercaseList){
      print("$item: ${item.length}");
    }

    var lowercaseList = list.map((item) => item.toLowerCase()).toList();
    lowercaseList.forEach((item)=> print("$item: ${item.length}"));

    //APPLES: 6
    //BANANAS: 7
    //CALAMANSIS: 10
    //apples: 6
    //bananas: 7
    //calamansis: 10
    
    //-------------------------------

    /*
      ClosureとScope
      関数はスコープ内で定義された変数を記憶する
    */
    Function makeAdder(int addBy){
      return (int i) => addBy + i;
    }

    void main(){
      var add2 = makeAdder(2);
      var add4 = makeAdder(4);

      print(add2(3)); //5
      print(add4(3)); //7
    }

    ```

    ### Record

    ```dart
    /*
      Record: 複数の戻り値を返す
        Recordは、固定サイズで、名前付きフィールドにも位置フィールドにもできる
        アクセスする場合は、$1から始まることに注意
    */

    (int, String) getMember(int number, String name){
      return (number, name);
    }

    void main(){
      final gm = getMember(1, "Nyanko");
      print(gm.$1); //1
    }

    // 名前付きフィールドの指定
    // 異なる名前の名前付きフィールドを持つ場合、それは異なる型を意味する
    ({int a, int b}) recordAB = (b: 24, a: 12);

    // 位置フィールドの指定
    // レコードの型には影響しないので代入可能
    (int c, int d) recordCD = (1,2);
    (int e, int f) recordEF = (3,4);
    recordCD = recordEF;

    //レコードは不変なので、Getterのみが存在する。
    print(recordAB.a); //12
    print(recordCD.$1); //3

    ```

    ### List

    ```dart
    List<String> list = ["Hoge", "Fuga", "Bar"];
    //末尾挿入
    list.add("Foo");
    //挿入
    list.insert(1, "Nyanko");
    //削除
    list.remove("Bar");
    print(list); //[Hoge, Nyanko, Fuga, Foo]
    //該当要素の位置
    print(list.indexOf("Fuga")); //2
    
    //昇順
    list.sort((a,b) => a.compareTo(b));
    print(list); //[Foo, Fuga, Hoge, Nyanko]

    //二次元Listのsort
    var values = [
      [1,2],
      [2,3],
      [3,1],
    ];

    values.sort((a,b)=> a[1].compareTo(b[1]));
    print(values); //[[3, 1], [1, 2], [2, 3]]

    //抽出(List化していないと以下のような出力に)
    var ages = [20,30,40,50,60];
    var filteredAges = ages.where((age) => age > 30);
    print(filteredAges); //(40, 50, 60)

    //ランダム
    var ages2 = [1,2,3,4,5];
    ages2.shuffle();
    print(ages2); //[2, 4, 1, 3, 5]

    //要素を順々に処理
    List<String> list = ["hoge", "fuga", "nyanko"];
    list.forEach((elem) => print(elem)); //

    //すべての要素がTrueなら
    list.every((elem) => elem.length > 5); //false

    //各要素に任意の操作を行い、returnで返す
    List<int> list = [1,2,3,4,5];
    print(list.map((e) => e * e).toList()); //[1, 4, 9, 16, 25]

    //畳み込み(他の言語のreduce = fold)
    List<int> list = [1,2,3,4,5];
    int sum = list.fold(1, (first, second) => first * second);
    print(sum); //120
    ```

    ### import 
    
    ```dart
    import 'dart:math';
    
    import 'package:test/test.dart';

    import 'path/to/my_file.dart';
    ```

    ### Map

    ```dart
    Map<String, String> language = {
      "Python" : "battery pack",
      "Java": "I exists only for the JVM",
      "C++" : "Goot at high-spped processing", 
    };

    //値の追加(put, getでも可能)
    language["JS"] = "Can be both the front and backend";

    //値の削除
    language.remove("C++");

    //順次処理
    language.forEach((key, val){
      print("$key: $val");
    });
    //Python: battery pack
    //Java: I exists only for the JVM
    //JS: Can be both the front and backend

    //keyの存在
    language.containsKey("Java"); //true

    //取り出し
    print(language["Python"]);
    //battery pack

    // constをつければ値を固定可能
    Map<String, String> info = const {
      "name" : "Nyanko",
      "place": "Tokyo",
    };

    info["name"] = "Nekonuko";
    // Unsupported operation: Cannot modify unmodifiable map


    //Map連結
    final map1 = {
      "name" : "Nekonuko"
    };

    final map2 = {
      "place": "Tokyo"
    };

    map1.addAll(map2);
    print(map1); //{name: Nekonuko, place: Tokyo}

    //List, Map相互変換

    List<String> names = ["Nyanko", "Nukonuko", "Inuneko"];
    Map<int, String> namesMap = names.asMap();
    print(namesMap); //{0: Nyanko, 1: Nukonuko, 2: Inuneko}

    print(namesMap.keys.toList()); //[0, 1, 2]
    print(namesMap.values.toList()); //[Nyanko, Nukonuko, Inuneko]

    var keyList = [];
    var valList = [];
    namesMap.forEach((key, val){
      keyList.add(key);
      valList.add(val);
    } )

    print(keyList); //[0, 1, 2]
    print(valList); //[Nyanko, Nukonuko, Inuneko]

    ```

    ### クラス

    ```dart

    class Psi{
      String name;
      int level;

      /*
      以下と同様の意味
      Psi(name, level){
        this.name = name;
        this.level = level
      }
      */
      Psi(this.name, this.level);

      // Method(${}は文字列補間)
      void describe(){
        print("Psi: ${name}( ${level} )");
      }
    }

    

    void main(){
      var telepathy = Psi(
          "テレパシー", 
          5, 
        );

      telepathy.describe();
    }
    
    ```

    ### 継承

    ```dart

    //先頭に_がついていると、privateになる
    // --> ここでいうprivateとは同一ライブラリからのみアクセス可能という意味
    // --> 普通クラスはファイルごとに分けるので問題ないとは思うが
    
    class Car{
      String brand;
      int year;

      Car(this.brand, this.year);

      void displayInfo(){
        print("Brand: $brand, Year: $year!");
      }
    }

    class SuperCar extends Car{
      double topSpeed;

      //先に基底クラスのコンストラクタにアクセスする
      SuperCar(String brand, int year, this.topSpeed)
       : super(brand, year);
      
      void displaySuperCarInfo(){
        print("Brand: $brand, Year: $year, Top Speed: $topSpeed");
      }
      void boost(){
        print("Boost activated! Top Speed is now ${topSpeed + 50}");
      }
    }

    void main(){
      var myCar = Car("Toyota", 2021);
      myCar.displayInfo();

      var mySuperCar = SuperCar("Ferrari", 2042, 650.0);
      mySuperCar.displayInfo();
      mySuperCar.displaySuperCarInfo();
      mySuperCar.boost();

      //Brand: Toyota, Year: 2021!
      //Brand: Ferrari, Year: 2042!
      //Brand: Ferrari, Year: 2042, Top Speed: 650
      //Boost activated! Top Speed is now 700

    }
    ```

    ### アクセス修飾子

    ```dart
    // _がつくと、private。正確には他ファイルからアクセス不可。
    class Car{
      String _brand;
      int _year;

      Car(this._brand, this._year);

      //getter
      String get brand => _brand;
      int get year => _year;

      //Setter
      set brand(String brand) => _brand = brand;
      set year(int year) => _year = year;

      void displayInfo(){
        print("Brand: $brand, Year: $year!");
        //print($_brand); //The getter '$_brand' isn't defined for the class 'SuperCar'
      }
    }

    class SuperCar extends Car{
      double topSpeed;

      //先に基底クラスのコンストラクタにアクセスする
      SuperCar(String brand, int year, this.topSpeed)
       : super(brand, year);
      
      void displaySuperCarInfo(){
        //基底クラスのprivateフィールドにはgetterを通じてアクセス
        print("Brand: $brand, Year: $year, Top Speed: $topSpeed");
      }
      void boost(){
        print("Boost activated! Top Speed is now ${topSpeed + 50}");
      }
    }

    void main(){
      var myCar = Car("Toyota", 2021);
      myCar.displayInfo();

      var mySuperCar = SuperCar("Ferrari", 2042, 650.0);
      mySuperCar.displayInfo();
      mySuperCar.displaySuperCarInfo();
      mySuperCar.boost();

      //Brand: Toyota, Year: 2021!
      //Brand: Ferrari, Year: 2042!
      //Brand: Ferrari, Year: 2042, Top Speed: 650
      //Boost activated! Top Speed is now 700

    }
    ```

    ### 抽象クラス

    ```dart
    //インスタンスを作れない
    abstract class Car{
      void info();
    }

    class SuperCar implements Car {
      String? brand;
      int? year;

      @override
      void info(){
        print("Brand: $brand, Year: $year!");
      }
      
    }

    void main(){
      var superCar = SuperCar();
      superCar.brand = "Toyota";
      superCar.year = 2011;

      superCar.info();
    }
    ```

    ### Mixins

    ```dart
    //複数のクラスから機能を取り込む

    mixin SuperCar {
      String? brand;
      int? year;

      void info(){
        print("Brand: $brand, Year: $year!");
        print("Super");
      }
    }

    class HyperCar {
      String? brand;
      int? year;

      void fly(){
        print("$brand社の車は、$year年に作られ、飛行能力を備えている");
        print("Hyper");
      }
    }

    /*
      SuperHyperCarは、HyperCarを継承する
      HyperCarは、SuperCarをmixinする
      結果、SuperHyperCarは、SuperCarと、HyperCarクラスの機能を使える
    */
    class SuperHyperCar extends HyperCar with SuperCar{

    }

    void main(){
      var superHyperCar = SuperHyperCar();
      superHyperCar.brand = "Toyota";
      superHyperCar.year = 2055;

      superHyperCar.info();
      superHyperCar.fly();

      //Brand: Toyota, Year: 2055!
      //Super
      //Toyota社の車は、2055年に作られ、飛行能力を備えている
      //Hyper
    }
    ```

    ### Cascade

    ```dart
    //直前のオブジェクトに対する処理を、簡略化

    class Color {
      String name = "";
      List<int> code = [0, 0, 0];

      Color(this.name, this.code);

      void info(){
        print("color: $name, code: $code");
      }

      void inversion(){
        code = [
          255 - code[0],  
          255 - code[1],  
          255 - code[2],  
        ];
      }
    }

    void main(){
       Color("red", [255,0,0])
        ..info()
        ..inversion()
        ..name = "Cyan"
        ..info();
    
    //color: red, code: [255, 0, 0]
    //color: Cyan, code: [0, 255, 255]
    }
    ```

    ### static

    ```dart
    //instance不要。いつもの
    class Monster{
      int? atk;
      static int total_num = 0;

      Monster(this.atk){
        total_num++;
      }

      static void info(){
        print("モンスターは現在$total_num匹存在する");
      }
    }

    void main(){
      var goblin1 = Monster(12);
      var oak1 = Monster(23);
      print(Monster.total_num); //2
      Monster.info(); //モンスターは現在2匹存在する
    }
    ```

    ### 名前付きコンストラクタ

    ```dart

    //名前付きコンストラクタ(オーバーロードがない代わり)
    class Mushroom {
      String name;
      String habitat;
      int risk_level = 0;
      String symptoms = "";

      Mushroom(this.name, this.habitat);

      Mushroom.danger(
        this.name, 
        this.habitat, 
        this.risk_level, 
        this.symptoms
      );
      
      void info(){
        print("name: $name, habitat: $habitat");
        if(symptoms != ""){
          print("symptoms: $symptoms");
        }
      }
    }

    void main(){
      var mushrooms = [
        Mushroom.danger("ツキヨタケ", "ブナ林", 1, "下痢、嘔吐"),
        Mushroom("なめこ", "枯れ木や切り株"),
      ];

      mushrooms[0].info();
      mushrooms[1].info();

      //name: ツキヨタケ, habitat: ブナ林
      //symptoms: 下痢、嘔吐
      //name: なめこ, habitat: 枯れ木や切り株
    }

    ```

    ### コンストラクタの実行順

    ```dart

    /*
    1. 初期化子リスト
    2. 基底クラスの引数なしコンストラクタ(または、1により手動呼び出し)
    3. サブクラスのコンストラクタ

    下記の例で行くと
    4. 初期化子リスト super(brand, year)で基底クラスのコンストラクタを呼び出し
    5. 基底クラスのコンストラクタが実行
    6. サブクラスのコンストラクタが実行

    --> 初期化子リストを使わない場合、デフォルトコンストラクタが暗黙呼び出しされ
        デフォルトコンストラクタがない場合、コンパイルエラーが発生する

    */

    class Car{
      String brand;
      int year;

      Car(this.brand, this.year){
        print("Car constructor");
      }
    }

    class SuperCar extends Car{
      double topSpeed;
      //初期化子リストを複数指定したい場合、カンマで区切る
      SuperCar(String brand, int year, this.topSpeed)
        : super(brand, year){
          print("SuperCar Constructor");
        }
    }

    void main(){
      var mySuperCar = SuperCar("Ferrari", 2044, 750.9);

      //Car constructor
      //SuperCar Constructor
    }

    //-------※(補足: 引数なしのコンストラクタあり)-------
    class Car{
      String? brand;
      int? year;

      Car(){
        print("Car constructor");
      }
    }

    class SuperCar extends Car{
      double topSpeed;

      SuperCar(this.topSpeed)
        {
          print("SuperCar Constructor");
        }
    }

    void main(){
      var mySuperCar = SuperCar(700.0);
      //Car constructor
      //SuperCar Constructor
    }
    ```

    ### スーパーパラメータ

    ```dart
    class Car{
      String brand;
      int year;

      Car({required this.brand, required this.year}){
        print("Car constructor: brand = $brand, year = $year");
      }
    }

    //初期化子リストを使う必要がないので、コードが簡潔、可読性も上がるはず
    class SuperCar extends Car{
      double topSpeed;

      SuperCar({
        required super.brand, 
        required super.year, 
        required this.topSpeed}
      ){
        print("SuperCar constructor: topSpeed = $topSpeed");
        }
    }

    void main(){
      var mySuperCar = SuperCar(
        brand: "Ferrari", 
        year: 2044, 
        topSpeed: 709.0,
      );
      //Car constructor: brand = Ferrari, year = 2044
      //SuperCar constructor: topSpeed = 709
    }
    ```

    ### リダイレクトコンストラクタ

    ```dart
    //コンストラクタ内で別のコンストラクタにアクセス
    // this() == Car()

    class Car{
      String brand;
      int year;
      Car(this.brand, this.year);

      Car.old(String brand) : this(brand, 1900);

      void info(){
        print("brand = $brand, year = $year");
      }
    }

    void main(){
        Car.old("Ford").info();
        //brand = Ford, year = 1900
    }
    ```

    ### 定数コンストラクタ

    ```dart
    /*
      条件
        すべてのインスタンス変数がfinalである必要がある
      利点
        1 同じ値を持つ定数オブジェクトはメモリ内で共有 --> 使用量減少
        2 普遍性の保証
        3 コンパイル時に既に定数オブジェクトが作成されるため、ランタイム時の
          オーバーヘッド減少
      使用状況
        色や幾何学的な点等の、変更されないことが予測されるデータクラス
    */

    class Color{
      final int red, green, blue;

      //定数コンストラクタ(constをつける)
      const Color(this.red, this.green, this.blue);
    }

    void main(){
      // ※constをつけないと別のインスタンスになるので注意
      const red = Color(255,0,0);
      const green = Color(0, 255, 0);
      const anotherRed = Color(255, 0, 0);

      //同じオブジェクトか調べる
      print(identical(red, anotherRed)); //true
      print(identical(red, green)); //false

    }
    ```

    ### ファクトリーコンストラクタ

    ```dart
    /*
    使い道
      ・cacheを利用して、同じインスタンスを返す
      ・条件に基づき、異なるクラスのインスタンスを返す
    
    利点
      ・cacheで、パフォーマンスの向上
      ・既存のインスタンスの再利用可能
      ・条件に基づき、異なるインスタンスを生成できる
      --> 明示的にreturnしない限り、インスタンスは返されない
    */

    //cache編

    class Logger{
      final String name;
      static final Map<String, Logger> _cache = {};

      //factory constructor
      //既存のinstanceがcacheにあればそれを返す
      //なければ新しいinstanceを作成し、cacheに保存しつつ、それを返す
      factory Logger(String name){
        //nullの可能性はないので、!(null非check演算子をつけている)
        if(_cache.containsKey(name)){
          return _cache[name]!;
        }
        else{
          final logger = Logger._internal(name);
          _cache[name] = logger;
          return logger;
        }
      }

      Logger._internal(this.name);

      void log(String message){
        print("$name: $message");
      }
    }

    void main(){
      //factoryコンストラクタへの呼び出し
      var logger1 = Logger("UI");
      var logger2 = Logger("UI");
      var logger3 = Logger("Network");

      print(identical(logger1, logger2)); //true
      print(identical(logger1, logger3)); //false

      logger1.log("Button clicked"); //UI: Button clicked
      logger3.log("Data fetched"); //Network: Data fetched
    }

    //条件編

    class Shape{
      
      //基底クラスのコンストラクタにアクセスするのでそれ用
      const Shape();

      factory Shape.create(String type){
        if(type == "circle"){ return Circle();}
        else if(type == "square"){ return Square();}
        else{ throw "Unknown shape type: $type";}
      }
    }

    //toStringでprint時の出力調整
    class Circle extends Shape{
      @override
      String toString() => "Circle";
    }

    class Square extends Shape{
      @override
      String toString() => "Square";
    }

    void main(){
      var shape1 = Shape.create("circle");
      var shape2 = Shape.create("square");
      print(shape1); //Circle
      print(shape2); //Square
    }
    ```

    ### シングルトンパターンについて

    ```dart
    /*
    特徴
      ・インスタンスが1つしかないことが保証される
    
    作り方
      1 privateな静的フィールドを用意し、該当クラスのインスタンスを保持させる
      2 factoryコンストラクタを通じてアクセスさせる
      --> 新しいインスタンスを作成しない可能性がある場合はこちらを使うので
    */

    class MySingleton{
      //String? name;

      static final MySingleton _instance = MySingleton._internal();

      factory MySingleton(){
        return _instance;
      }

      MySingleton._internal(){
        //name = "Nyanko";
      }
    }

    void main(){
      var singleTon1 = MySingleton();
      var singleTon2 = MySingleton();
      
      print(identical(singleTon1 , singleTon2)); //true
    }
    ```

    ### 初期化子リストの活用

    ```dart
    /*
      インスタンス変数をfinalで宣言した際の代入に使える
    */

    class Point {
      final double x, y;

      Point(double inX, double inY)
        : x = inX,
          y = inY {
            print("$x, $y");
          }
    }

    //なお、普通はこう書く
    class Point {
      final double x,y;
      Point(this.x, this.y){print("$x, $y");}
    }
    ```

    ### Enum

    ```dart
    //定数をまとめたもの = 列挙型

    /*
      version 2.17以降、大幅に強化されている
      field, method, constuructorの追加が可能になった
      --> constructorについては定数コンストラクタ
      --> つまり同じ値を持つなら、メモリ内で共有される
    */

    enum Rarity{
      unique("緋色武器", "Rare:5"),
      epic("紫武器", "Rare:4"),
      legendary("金武器", "Rare:3"),
      rare("青武器", "Rare:2"),
      uncommon("緑武器", "Rare:1"),
      common("白武器", "Rare:0");

      final String commonly_name;
      final String category;

      const Rarity(this.commonly_name, this.category);

      void describe(){
        print("$commonly_name, $category");
      }
    }

    void main(){
      var weapon1 = Rarity.unique;
      var weapon2 = Rarity.common;
      var weapon3 = Rarity.unique;

      weapon1.describe(); //緋色武器, Rare:5
      weapon2.describe(); //白武器, Rare:0

      print(weapon1.name); //unique 列挙値の名前を取得可能
      print(weapon2.index); //1 indexを取得可能
      print(identical(weapon1, weapon3)); //true
    }
    ```

    ### typedefとinline function

    ```dart
    //------typedef: 型に別の名前を付ける(2.13までは関数型のみの使用)

    typedef IntBinaryOperation = int Function(int a, int b);

    int add(IntBinaryOperation operation, int a, int b){
      return operation(a,b);
    }

    void main(){
      int result = add((a, b) => a + b, 2, 3);
      print(result);
    }

    //----------inline func

    int add(int Function(int a, int b) operation, int a, int b){
      return operation(a,b);
    }

    void main(){
      int result = add((a,b)=> a + b, 2, 3);
      print(result);
    }

    /*
      どちらを使うべきか

      シンプルな関数型
        inline-func
        --> 別途typedefを定義する手間がないため
        --> コードを読む際にすぐに肩の詳細を把握できるため
      
      複雑な関数型で、再利用が多い
        --> typedefを用いることで可読性が登場する
        --> typedefを使うことで、コードの重複が避けられる
    */

    //sortの例, ジェネリクスを用いて汎用的にしている

    typedef Compare<T> = int Function(T a, T b);

    int sortList<T>(List<T> list, Compare<T> compare){
      list.sort(compare);
      return list.length;
    }

    void main(){
      var list = [3,1,4,1,5,9];
      var list2 = ["Nyanko", "Nekoinu", "Nukonuko"];
      sortList<int>(list, (a,b) => a - b);
      sortList<String>(list, (a,b) => b.compareTo(a));
      print(list); //[1, 1, 3, 4, 5, 9]
      print(list2); //[Nyanko, Nukonuko, Nekoinu]
    }
    ```

    ### DateTime

    ```dart

    void main(){
      var now = DateTime.now(); 
      print(now); //2024-07-16 00:26:41.027
      print(
        "${now.year}-${now.month}-${now.day} ${now.hour}:${now.minute}:${now.second}:${now.millisecond}:${now.microsecond}"
      );
      //2024-7-16 0:26:41:27:0

      //ISO8601
      print(now.toIso8601String()); //2024-07-16T00:27:39.985
    }
    ```

    ### ジェネリクス

    ```dart
    //データの型を指定し、誤った方のデータが入れられるのを防ぐ
    //同様の処理を行うクラスやメソッドをまとめられる

    class User<T>{
      final T id;
      User(this.id);
    }

    void main(){
      //Genericsを使用していることを明記するために、User<>()とする
      //書かなくても動く
      final user = User<int>(1);
      print(user.id); //1

      final user2 = User<String>("first");
      print(user2.id); //first
    }

    //継承する場合

    abstract class User<T>{
      final T id;
      User(this.id);

      void info();
    }

    //Superクラスのジェネリクスまで指定すること
    class SingleUser<T> extends User<T>{
      SingleUser(super.id);

      void info(){
        print("info SingleUser");
        print(id);
      }
    }

    class MultiUser<T, K> extends User<T>{
      final K secondId;
      MultiUser(super.id, this.secondId);

      void info(){
        print("info MultiUser");
        print(id);
        print(secondId);
      }
    }

    void main(){
      final user1 = SingleUser<String>("Nyanko");
      user1.info(); 
      //info SingleUser
      //Nyanko

      final user2 = MultiUser<String, int>("00001", 1);
      user2.info(); 
      //info MultiUser
      //00001
      //1
    }

    //Genericsの型制限

    
    ```



    ### 所感

    ```text
    ・pythonのような簡潔な記法とその他の言語のいいとこどりをしたかのようだ
    ・型推論や、NULL安全性を備えているのもGood
    ・さすが後発言語
    ```