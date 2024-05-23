# クイズ(中級2:データ型)

## Q48 "hoge".toUpperCase()が可能である理由が分かりますか?

??? success
    ### プリミティブの欠点
    - プロパティやメソッドを持てない

    ### オブジェクトの欠点
    - 重い

    ### プリミティブ値のプロパティ?やメソッド?にアクセスできる理由

    1. プリミティブのプロパティにアクセスすると、文字列の値を持ったオブジェクト(オブジェクトラッパー)が作られる
    2. ラッパーは、`toUpperCase()`のような便利なメソッドを持っている
    3. メソッドを実行し、文字列を返す
    4. オブジェクトは破棄され、プリミティブだけが残る

    ```js
    //変更が元の値に反映されない理由は、ラッパーに対する変更であるため
    let str = "hoge";
    console.log(str.length); //4
    const Str = str.toUpperCase()
    console.log(str); //HOGE
    ```

    ### ラッパーオブジェクトは作らない方がいい
    !!! warning
        ### こんなことになりたくなければ
        - コンストラクタを作れば、明示的にプリミティブのためのラッパーを作れるが......
        - オブジェクトは常にtrue扱い
 
        ```js
        let zero = new Number(0);
        if(zero){
          console.log("why????");
        }
        ```
    
    ### null/undefinedはメソッドを持たない
    - ラッパーオブジェクトはない

    ```js
    conosle.log(null.test); //Cannot read properties of null
    ```

## Q49. プリミティブな値に対してプロパティを追加することは可能だと思いますか? (y/n)

??? success
    ### 答え
    - `No`

    ### 理由
    - プロパティにアクセスされた時に作成されるラッパーオブジェクトは、プロパティに関する操作が終了すると削除されるため

    ```js
    let str = "Hello";
    str.test = 4;
    console.log(str.test); //undefined or error
    ```

## Q50. 数値型の区切りとしてアンダーバーが使えることを知っていますか?

??? success
     ###　使用例

    ```js
    let num1 = 100000000;
    let num2 = 100_000_000;
    console.log(num1 === num2); //true
    ```

## Q51. 数値の指数表記について知っていますか?

??? success
    ### 使用例

    ```js
    let billion = 1e9;
    console.log(billion); //1000000000

    let ms = 1e-6;
    console.log(ms); //0.000001
    console.log(1.23e-6); //0.00000123
    ```

## Q52. 10進数以外の表記法について知っていますか?

??? success
    ### 16進数

    ```js
    console.log(0xff); //255
    ```

    ### 8進数

    ```js
    console.log(0o377); //255
    ```

    ### 2進数

    ```js
    console.log(0b11); //3
    ```

## Q53. toString(base)で基数変換できることを知っていますか?

??? success

    ### 使用例
    - `base`に書いた基数へと変換する

    ```js
    let num = 255
    console.log(num.toString(16)); //ff
    console.log(num.toString(2)); //11111111
    //上限: 36進数 = 数値(10種類) + アルファベット(a~z)
    console.log(num.toString(36)); //73
    ```

## Q54. 123456.toString(16)が失敗する理由が分かりますか?

??? success
    ### 原因
    - 1つ目の.が小数部分の区切りとみなされるため

    ### 対策
    - 2つのドットを置く(JSは小数部分が空であると判断し、メソッドであると判断する)
    - `()`で囲む

    ```js
    123456..toString(16); //1e240
    (123456).toString(16);
    ```

## Q55. 小数点を整数として丸めるために用いる4種の組み込み関数を知っていますか?

??? success

    ### Math.floor
    - 切り捨て

    ```js
    Math.floor(3.1); //3
    Math.floor(-3.1); //-4
    ```

    ### Math.ceil
    - 切り上げ
  
    ```js
    Math.ceil(3.1); //4
    Math.ceil(-3.1); //-3
    ```

    ### Math.round
    - 四捨五入

    ```js
    Math.ceil(3.4); //3
    Math.ceil(3.8); //4
    Math.ceil(-1.1); //-1
    ```

    ### Math.trunc
    - 小数点以下を削除する
    - 分かりやすい

    ```js
    Math.trunc(3.9); //3
    Math.trunc(-3.9); //-3
    ```

## Q56. 小数点n桁に丸めたい場合に用いるtoFixed()について知っていますか?

??? success
    ### 他の方法
    - ${10^n}$ 倍した後、切り捨てを行い、元に戻す

    ```js
    let num = 1.23456;
    console.log(Math.floor(num * 100) / 100);
    ```

    ### toFixed()
    - Math.round()と同じように、四捨五入することに注意

    ```js
    let num = 31.29456;
    console.log(num.toFixed(1)); //31.3
    ```

    ### toFixed()の注意点
    - 返り値は`string`
    - 小数部分が指定桁より短い場合、末尾に0が挿入される

    ```js
    let num = 123.456;
    typeof num.toFixed(2); //string
    num.toFixed(10); //'123.4560000000'
    ```

## Q56. 小数点や大きな数の計算で、精度が落ちることを知っていますか?

??? success
    ### 小数点の例

    ```js
    0.1.toFixed(20); //0.10000000000000000555
    ```

    ```js
    //無限ループする
    let i = 0;
    while(i != 10){
      i += 0.2;
    }
    ```

    ### 大きな数の例

    ```js
    console.log(9999999999999999);
    //10000000000000000
    ```

    ### 大きな数を扱えない理由
    - JSは内部的には64bitで数値を表現している
    - 52bitが数値の格納
    - 11bitが小数点の位置
    - 1bitが符号のために使われる
    - つまり、52bitを超える計算をすると、精度の損失が生じる
  
    ### 小数点を正しく扱えない理由
    - `0.1`と`0.2`は2進数で考えると、無限小数
    - 可能な限り近い数値に丸めてはいるものの、小さな精度損失は生じる

    ```js
    console.log(0.1 + 0.2 === 0.3); //false 
    ```

    - 丸める必要がない場合は、精度損失は起こらない

    ```js
    console.log(0.25 + 0.25 === 0.5) //true
    ```

    ### 解決法1
    - `toFixed()`で丸める(数値に変換する必要がある)

    ```js
    let sum = 0.1 + 0.2;
    console.log(+sum.toFixed(2) === 0.3); 

    const sum1 = 0.28 + 0.14;
    console.log(+expr2.toFixed(2) === 0.42);
    ```

    ### 解決法2
    - 予め整数にしておく(無駄なこともある)

    ```js
    const expr = (0.1 * 10 + 0.2 * 10) / 10;
    console.log(expr === 0.3); //true

    const expr1 = (0.28 * 100 + 0.14 * 100) / 100;
    console.log(expr1 === 0.42); //false
    ```

    ### 解決法3
    - 両方使う
    - `0.5`はバイナリで表現できるので正確

    ```js
    6.35.toFixed(1); //6.3 ?????????
    6.35.toFixed(20); //6.34999999999999964473'

    let num = 6.35;
    num *= 10; //63.5
    console.log(num.toFixed(20) / 10) //6.35
    ```

## Q57. isFiniteとisNaNの意味を知っていますか?

??? success

    ### isNaN()
    - 引数を数値に変換し、`NaN`であるかテストする

    ### isNaN()の存在意義
    - `NaN`は自身を含めて、比較演算子を使ったときに`false`を返すから

    ```js
    console.log(NaN === NaN); //false
    console.log(isNaN(NaN)); //true
    console.log(isNaN("neko")); //true
    console.log(isNaN("12")); //false
    ```

    ### isFinite
    - 引数を数値に変換し、それが`NaN/Infinity/-Infinity`なら`false`を返す

    ```js
    console.log(isFinite("21")); //true
    //有限の数ではないので
    console.log(isFinite("str")); //false
    console.log(isFinite(Infinity)); //false
    ```

    ### 注意点
    !!! warning
        - 空文字は全ての数値関数で0扱い

        ```js
        Math.floor(""); //0
        isFinite(""); //true
        ```

## Q58. NaNや`0`と`-0`を正確に比較できるObject.isについて知っていますか?

??? success
    ### 使用例

    ```js
    Object.is(NaN, NaN); //true
    Object.is(0, -0); //false
    Object.is(3,4); // a === bと同じ
    ```

## Q59. parseIntやparseFloatを使うと、厳密でない数値変換ができることを知っていますか?

??? success
    ### 通常の数値変換

    ```js
    console.log(+"100neko"); //NaN
    ```

    ### parseInt
    - 先頭から数値を読み込み、エラーが起きた場合、それまでの値を整数値で返す

    ```js
    console.log(parseInt("100neko")); //100
    console.log(parseInt("100neko110")); //100
    ```

    ### parseFloat
    - 浮動小数を返す

    ```js
    console.log(parseInt("100.02neko")); //100.02
    ```

    ### 注意点
    - 1文字目から数値でなかった場合、NaNが返る

    ```js
    console.log(parseInt("a123")); //NaN
    ```

## Q60. parseIntを用いて、基数変換ができることを知っていますか?

??? success
    ### 2つ目の引数に基数を指定する 
    - 10進数に変換される

    ```js
    console.log( parseInt("0xff", 16) ); //255
    ```

## Q61. Mathの注意点と、以下の汎用性が高いプロパティ/メソッドについて知っていますか?

??? tips
    ### プロパティ一覧
    - Math.E
    - Math.LN10
    - Math.LN2
    - Math.PI

    ### メソッド一覧
    - Math.abs()
    - Math.sin()
    - Math.cos()
    - Math.tan()
    - Math.cbrt()
    - Math.clz32()
    - Math.exp()
    - Math.log()
    - Math.max()
    - Math.min()
    - Math.pow()
    - Math.random()
    - Math.sign()
    - Math.sqrt()

??? success
    ### 注意点
    - Numberのみで動作する
    - BigIntでは動作しない

    ### Math.E
    - 自然対数の底(e)を返す

    ```js
    Math.E; //2.718281828459045
    ```

    ### Math.LN10
    - 10の自然対数を返す
    - 使用例に関しては`Math.log()`を参照

    ```js
    Math.log(100) / Math.LN10; //2
    ```

    ### Math.LN2
    - 2の自然対数を返す

    ```js
    Math.log(8) / Math.LN2; //3
    ```

    ### Math.PI
    - 円周率を返す

    ```js
    Math.PI; //3.141592653589793
    ```

    ### Math.abs(x)
    - 絶対値を返す
    

    ```js
    Math.abs(-1.1); //1.1
    ```

    ### Math.sin(radian)
    - 正弦を返す

    ```js
    const degree = 30;
    const radian = degree * Math.PI / 180;
    Math.sin(radian); //0.499999999
    ```

    ### Math.cos(radian)
    - 余弦を返す

    ```js
    const degree = 60;
    const radian = degree * Math.PI / 180;
    Math.cos(radian); //0.500000001
    ```

    ### Math.tan(radian)
    - タンジェントを返す

    ```js
    const degree = 45;
    const radian = degree * Math.PI / 180;
    Math.tan(radian); //0.99999999999
    ```

    ### Math.cbrt(x)
    - 立方根を返す

    ```js
    Math.cbrt(8); //2
    Math.cbrt(64); //4
    ```

    ### Math.clz32(x)
    - 32bit整数で表現した時、先頭のゼロのビット数を返す

    ```js
    Math.clz32(2); //30
    Math.clz32(3); //30
    Math.clz32(4); //29
    ```

    ### Math.exp(x)
    - ${e^x}$を返す

    ```js
    Math.exp(1); //2.718281828459045
    ```

    ### Math.log(x)
    - 数値の自然対数を返す
    - 以下は ${log_2 8}$の場合

    ```js
    Math.log(8) / Math.log(2); //3
    ```

    ### Math.max(a,b,c,...)
    - 最大値を返す

    ```js
    Math.max(2,4,5,6,1,-2); //6
    ```

    ### Math.min(a,b,c,...)
    - 最小値を返す

    ```js
    Math.min(2,4,5,6,1,-2); //-2
    ```

    ### Math.pow(x,y)
    - 基数の指数乗 ${x^y} を返す

    ```js
    Math.pow(2,3); //8
    ```

    ### Math.random()
    - 0以上1未満の暗号的には安全でない乱数を提供する

    ```js
    Math.random(); //0.039060555186804846
    ```

    ### Math.sign()
    - xの正負 or 0を判定する

    ```js
    Math.sign(123); //1
    Math.sign(0); //0
    Math.sign("-2"); //-1
    ```
    
    ### Math.sqrt()
    - 平方根を返す

    ```js
    Math.sqrt(16); //4
    ```

## Q62. セキュリティが重要な場面でMath.random()を使ってはいけない理由を知っていますか?

??? success
    ### Math.random()は予測可能
    - `Math.random()`の疑似乱数は十分予測可能

    ### 代替案
    - `crypto.getRandomValues()`を使う
    - これは暗号的に安全なランダム性を提供する
    - 整数ベースの配列(例として `Uint8Array`)を引数とる。すべての要素は乱数で上書きされる。
    - 引数にとる配列のビット数は、大きい方が精度が高い。ただしコストがかかる

    ```js
    window.crypto.getRandomValues(new Uint32Array(10));
    //Uint32Array(10) 
    //[
    //  3099100682, 
    //  775590145, 
    //  2928724990, 
    //  646447561, 
    //  785295370, 
    //  2757531532, 
    //  3360385709, 
    //  1425235582, 
    //  1740525548, 
    //  343186764, 
    //  buffer: ArrayBuffer(40), 
    //  byteLength: 40, 
    //  byteOffset: 0, 
    //  length: 10, 
    //  Symbol(Symbol.toStringTag): 
    //  'Uint32Array'
    //]
    ```

    ### 数値と文字列が混じった暗号文をJSで提供する
    !!! info
        - 以下を理解する必要はない

    !!! warning
        - `randomValues[i] % charset.length`の計算上、`charset`の最初の要素が、より高い確率で選ばれる
        - しかし、配列の長さが十分に長ければ問題にはならない

    ```js
    function generateRandomString(length){
      const charset = 'abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ0123456789';
      const randomValues = new Uint32Array(length);
      crypto.getRandomValues(randomValues);

      let result = "";
      for(let i=0; i<length; i++){
        result += charset[randomValues[i] % charset.length];
      }

      return result;
    }

    //引数内の値を100にすれば、十分にランダム性は高くなる
    const randomString = generateRandomString(10);
    console.log(randomString); //U9y2W0xZgs
    ```

## Q63. Math.random()を使って、min~maxまでの整数値を作成する方法が分かりますか?

??? success

    ### 他にも解法はある
    - 区間の端を調整することが肝要
    - なお、疑似乱数であるため微小な偏りは生じる

    ```js
    randomInteger = (min, max) =>{
      //区間を1~3とした場合
      // Math.random() * (max - min + 1)は
      //0~3の間を取る
      //min - 0.5を足し、Math.random()した値を返すことで
      //0.5 ~ 3.5の間を取る
      //0.5 ~ 1.499999..なら1
      //1.5 ~ 2.499999..なら2
      //2.5 ~ 3.5なら3と、幅が等しくなる
      let rand = min - 0.5 + Math.random() * (max - min + 1);

      return Math.round(rand);
    }

    for(let i=0; i<10; i++){
      console.log(randomInteger(1,3));
      // 2,1,1,2,2,3,2,1,3,2
    }
    ```

## Q64　文字列を複数行で表したいとき、バッククォートが使えることを知っていますか?

??? success
    ### 使用例

    ```js
    let multiLine = `Numbers:
    * One
    * Two
    * Three
    `
    console.log(multiLine)

    // Numbers:
    // * One
    // * Two
    // * Three
    ```

## Q65. 特殊文字について知っていますか?

??? success
    ### 文字一覧

    | 文字        | 説明                                |
    | ----------- | ----------------------------------- |
    | \n          | 改行                                |
    | \r          | Winのテキストファイルなら\r\nで改行 |
    | \\          | バックスラッシュ                    |
    | \' \"       | クォート                            |
    | \t          | タブ                                |
    | \b, \f, \v  | バックスペース                      |
    | \x7a        | 16進数のUnicode文字                 |
    | \uxxxx      | 4桁の16新コード                     |
    | \u{xx...xx} | 4バイトを使うユニコード記号など     |

    ### ユニコードの例

    ```js
    console.log("\u00A9"); //©
    console.log("\u{1f61a}");//😚
    ```

## Q66. 文字へのアクセス方法と、文字列が不変であることを理解していますか?

??? success
    ### アクセス
    - `[]`で可能
    - `.charAt(pos)`を使うこともできる
    - 見つからなかった場合の挙動が異なる
    - 順に取得したい場合、for ... ofを使う

    ```js
    let str = "Hello";
    console.log(str[0]); //H
    console.log(str.charAt(0)); //H

    console.log(str[100]); //undefined
    console.log(str.charAt(100)); //""

    for(let char of str){
      console.log(char); //H,e,l,l,o
    }
    ```

    ### 不変

    ```js
    let str = "Hi";
    str[0] = 'N'
    console.log(str); //Hi

    //代入演算子は使える
    str += "Neko";
    console.log(str); //HiNeko
    ```

## Q67. 大文字小文字変換を行う方法を知っていますか?

??? success
    ### toUpperCase()
    - 大文字変換

    ```js
    let str = "neko";
    console.log(str.toUpperCase()); //NEKO
    ```

    ### toLowerCase()
    - 小文字変換

    ```js
    let str = "Neko";
    console.log(str.toLowerCase()); //neko
    ```

    ### 冒頭のみ大文字
    - .slice()については後述

    ```js
    let str = "neko";
    console.log(str[0].toUpperCase() + str.slice(1));
    ```

## Q68. 部分文字列の位置を検索する方法を知っていますか?

??? success
    ### str.indexOf(substr [, pos])
    - `str`の中から、前から順に`substr`を探す
    - `pos`が設定されている場合は、該当するidxから探す
    - 見つからなかった場合、-1を返す

    ```js
    let str = "Neko inu Neko";
    console.log(str.indexOf("Neko")); //0
    console.log(str.indexOf("Nezumi")); //-1
    console.log(str.indexOf("Neko", 3)); //9
    ```

    ### str.lastIndexOf(substr [, pos])
    - 逆の順で探す

    ```js
    let str = "Neko inu Neko";
    console.log(str.lastIndexOf("Neko")); //9
    console.log(str.lastIndexOf("Nezumi")); //-1
    console.log(str.lastIndexOf("Neko", 8)); //0
    ```

    ### ifの中でindexOfを使う
    - 見つからなかった場合-1なので、この時に`false`としたい
    - `~-1 === -(-1+1) === 0`になることを活用する

    ```js
    const str = "Neko inu Neko";

    if(~str.indexOf("Neko")){
      console.log("Found it"); //Found it
    }
    ```

    !!! warning
        - 32bitを超えると、正しくチェックできない    
        - 後述する、`includes()`を使うべき

## Q69. 特定の部分文字列を含むか調べたい場合に使えるメソッドについて知っていますか?

??? success
    ### str.includes(substr [, pos])
    - `pos`がある場合、この位置から検索を開始する

    ```js
    let str = "Neko inu Neko";
    console.log(str.includes("inu")); //true
    console.log(str.includes("inu", 10)); //false
    ```

    ### str.startsWith()
    - 該当文字で始まるか

    ```js
    console.log("Neko".startsWith("Ne")); //true
    ```

    ### str.endsWith()
    - 該当文字で終わるか

    ```js
    console.log("Neko".endsWith("koo")); //false
    ```

## Q70. 部分文字列を取得する3つの方法について知っていますか?

??? success
    ### .slice(start [, end])
    - `start~(end-1)`までの位置を取得
    - `end`がない場合は、終わりまでになる
    - 負の値を指定すると、末尾からカウントする

    ```js
    let str = "hogefuga";
    console.log(str.slice(2)); //gefuga
    console.log(str.slice(2,3)); //g
    console.log(str.slice(-4, -1));//fug
    ```

    ### .substring(start [, end])
    - 負の値はサポートされていない(0扱い)
    - startがendより大きい場合、startがend扱いになる

    ```js
    let str = "hogefuga";
    console.log(str.substring(4)); //fuga
    console.log(str.substring(2,4) === str.substring(4,2)) //true
    ```
  
    ### .substr(start [, length])
    - `end`の代わりに、`length`を指定可能
    - 負の値指定も可能

    ```js
    let str = "hogefuga";
    console.log(str.substr(4, 2)); //fu
    console.log(str.substr(-4, 2)); //fu
    ```

## Q71. lengthプロパティと、サロゲートペア文字に対してこれを用いる際の注意点について知っていますか?

??? success
    ### length
    - 文字列の長さを表現

    ```js
    const str = "neko";
    console.log(str.length); //4
    ```

    ### サロゲートペア文字
    - 2バイトの文字のペア(=4バイト)で表現される文字

    ### lengthはうまく処理できない

    ```js
    const str = "𝒳neko"; 
    console.log(str.length); //6
    console.log("𝒳"[0] + "𝒳"[1]); //𝒳
    ```

    ### サロゲートペアとUnicodeの目印
    - 1つ目の文字が`0xd800 ~ 0xdbff`内なら
    - それはサロゲートペアの最初のパートを意味する
    - 2つ目の文字は`0xdc00 ~ 0xdfff`である必要がある
    - これはサロゲートペア専用と予約される

## Q72. 文字列比較がUnicode順で行われることを知っていますか?

??? success
    ### 使用例

    ```js
    console.log("Z" < "a"); //true
    console.log("B" < "abc"); //true
    console.log("1" <  "a"); //true
    console.log("abc" < "abd"); //true
    ```

## Q73. 英語以外の言語で正しい比較をする際に用いられるIntlについて知っていますか？

??? success
    ### 普通に比較した場合

    ```js
    console.log('Österreich' > 'Zealand' ); //true
    ```

    ### 文字列の内部値
    - UTF-16を使ってエンコードされている
    - 各文字は対応する数値コードを持っていて、実際はそれを用いて大小比較をしている

    ### str.codePointAt(pos)
    - posの文字コードを返す

    ```js
    console.log("Ö".codePointAt(0)); //214
    console.log("z".codePointAt(0)); //122
    ```

    ### String.fromCodePoint(code);
    - 数値`code`で文字を生成する

    ```js
    console.log(String.fromCodePoint(122)); //z
    ```

    ### str1.localeCompare(str2 [, locals])を用いて正しく比較する
    - `str1 > str2`なら1を返す
    - `str1 < str2`なら-1を返す
    - 等価なら0を返す
    - localsで使用する言語を選択する

    ```js
    console.log("ä" > "z"); //true
    console.log("ä".localeCompare("z", "de")); //-1

    ```

## Q74. 分音記号と、比較時の注意点について知っていますか?

??? success
    ### 分音記号
    - 複合記号の組み合わせは多すぎるので、ベースと装飾に分けて表現したりする

    ### 例

    ```js
    console.log("S\u0307"); //Ṡ
    //複合しているのでlengthはこうなる
    console.log("Ṡ".length;) //2
    ```

    ### 問題点
    - 見た目が同じでもユニコード構成が異なることがある

    ```js
    //上のドット + 下のドット
    const s1 = 'S\u0307\u0323';
    //下のドット + 上のドット
    const s2 = 'S\u0323\u0307';

    console.log(s1 === s2); //false
    ```

    ### 解決策
    - `str.normalize()`を用いて、文字を1つの形にする(ユニコード正規化)
    - 引数が"NFC"の場合、合成
    - 引数が"NFD"の場合、分解される
    - 引数無しの場合、"NFC"

    ```js
    const s1 = 'S\u0307\u0323';
    const s2 = 'S\u0323\u0307';
    console.log(s1.normalize() === s2.normalize()); //true

    s1.normalize("NFC").length(); //1
    s1.normalize("NFD").length(); //3
    ```