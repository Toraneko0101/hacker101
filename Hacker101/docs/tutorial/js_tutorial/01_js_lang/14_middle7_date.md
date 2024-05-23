# クイズ(中級7:日付)

## Q131 日付や時刻を管理するために使える、Dateの作成方法を知っていますか?

??? success
    ### 作成 new Date()
    - 現在の日付と時刻でDateオブジェクトを作成

    ```js
    const now = new Date();
    console.log(now); //Sat Jan 27 2024 14:52:04 GMT+0900 (日本標準時)
    ```

    - localeが日本でない場合

    ```js
    const now = new Date();
    console.log(now); //2024-01-27T05:54:15.314Z
    ```

    ### 任意の時刻のDate()を生成 new Date(milliseconds)
    - `milliseconds`: 1970-1-1からの経過時間をミリ秒で指定

    ```js
    const Jan02_1970 = new Date(24 * 3600 * 1000);
    console.log(Jan02_1970) //Fri Jan 02 1970 09:00:00 GMT+0900 (日本標準時)
    ```

    ### 1970-01-01以前の日付を指定したい場合
    - 負のタイムスタンプを記載

    ```js
    const Dec31_1969 = new Date(-24*3600*1000);
    console.log(Dec31_1969); //Wed Dec 31 1969 09:00:00 GMT+0900 (日本標準時)
    ```

    ### 文字列を渡すこともできる new Date(dateString)
    - `dateString`: 結構柔軟に設定できるが、後程触れる形式に合わせた方がいい(`YYYY-MM-DDTHH:mm:ss.sssZ`)
    - 時刻が設定されていない場合は、GMT(標準時)の深夜0時扱い
    - ただしタイムゾーンが設定されている場合は、タイムゾーンの深夜0時に調整される

    ```js
    const date = new Date("2020-01-01");
    const date1 = new Date("2020/01/01");
    const date2 = new Date("2017 1 2"); //Mon Jan 02 2017 00:00:00 GMT+0900 (日本標準時)
    ```

    ### 変数を渡すこともできる
    - `new Date(year, month[, date, hours, minutes, seconds, ms])`
    - `year`は4桁である必要がある
    - `date`は実際の月の日。指定がない場合は1
    - 他は指定がない場合0

    !!! warning
        - `month`は`0`が1月を意味する
        - そのため12月を指定したい場合は、`11`

    ```js
    new Date(2020, 0, 1, 0,0,0,0); 
    //Wed Jan 01 2020 00:00:00 GMT+0900 (日本標準時)
    ```

## Q132. Dateオブジェクトから、月や年だけを抽出する際に使えるメソッドを知っていますか?

??? success
    ### getFullYear()
    - 年を取得

    ### getMonth()
    - 月を取得(0~11)

    ### getDate()
    - 月の日を取得(1~31)

    ### getHours(), getMinutes(), getSeconds()
    - 時刻、分、秒を取得

    ### getMilliseconds()
    - ミリ秒を取得

    !!! warning
        - `getYear()`を使わないこと
        - これは2桁の年を返す場合がある

    ### getDay()
    - 週の曜日を取得する
    - 値は`0~6`
    - `0`が日曜日を意味する。`6`が土曜日

    !!! warning
        - これらのメソッドはすべて、ローカルタイムゾーン(たとえば日本の時刻)を基準としている

    ### 使用例

    ```js
    const now = new Date();
    console.log(now.getFullYear()); //2024
    console.log(now.getMonth()); //0
    console.log(now.getDate()); //27
    console.log(now.getHours()); //16
    console.log(now.getMinutes()); //39
    console.log(now.getSeconds()); //45
    console.log(now.getMilliseconds()); //223
    console.log(now.getDay()); //6
    ```

## Q133. UTCの年月日を取得するメソッドについて知っていますか?

??? success
    ### getUTCFullYear()
    - タイムゾーン`UTC+0`の年を返す

    ### 使用例

    ```js
    const now = new Date();
    console.log(now.getUTCFullYear()); //2024
    console.log(now.getUTCMonth()); //0
    console.log(now.getUTCDate()); //27
    console.log(now.getUTCHours()); //7
    console.log(now.getUTCMinutes()); //39
    console.log(now.getUTCSeconds()); //45
    console.log(now.getUTCMilliseconds()); //223
    console.log(now.getUTCDay()); //6
    ```

    ### getTime()
    - 日付のタイムスタンプ(1970-1-1からの経過時間ミリ単位)を返す

    ### getTimezoneOffset()
    - ローカルタイムゾーンとUTCの差を分単位で返す
    - ローカルのタイムゾーンがUTCより先の場合は負
    - 後の場合は正の値になる
    - なお、夏時間が使用されている地域では、時期によって返り値が変わる場合がある
    - 歴史上の理由でタイムゾーンが変わった場合も異なることがある
    - 参考[MDN : getTimezoneOffset()](https://developer.mozilla.org/ja/docs/Web/JavaScript/Reference/Global_Objects/Date/getTimezoneOffset)

    ### 使用例2

    ```js
    const now = new Date();
    now.getTime(); //1706341440948

    now.getTimezoneOffset(); //-540 UTC+9にいる場合
    ```

## Q134. setFullYear等で、日付の構成要素を変更できることを知っていますか?

??? success
    ### setFullYear(year[,month, date])
    - オプションで月日を設定することも可能

    ### setMonth(month[,date])
    - オプションで日の設定も可能

    ### setDate(date)
    - 日の設定を行う

    ### setHours(hour [,min, sec, ms])
    - オプションで分秒、ミリ秒の設定も可能
    - `setMinutes(), setSeconds(), setMilliseconds()`も同じなので略

    ### setTime(milliseconds)
    - UNIX時をミリ秒で設定

    ### UTC関連
    - `setUTCFullYears()`のようにUTCパターンも存在する

    ### 例

    ```js
    const date = new Date();
    //0時1分2秒
    date.setHours(0, 1, 2, 0);
    //1970-02-03
    date.setFullYear(1970, 1, 3);

    console.log(date);
    //Tue Feb 03 1970 00:01:02 GMT+0900
    ```

## Q135. Dateが範囲外の値を指定した時、自動的に調整してくれることを知っていますか?

??? success

    ### 閏年
    - 2月28日から2日増やしたい場合を考える
    - うるう年とその他を考える必要はない
    - `Date`は自動的に補正してくれる

    ```js
    const date = new Date(2013, 0, 32);
    //2013-2-1

    const date2 = new Date(2016, 1, 28);
    date2.setDate(date2.getDate() + 2);
    console.log(date2); //Mar 01 2016
    ```

    ### 差分を取得する
    - 1秒前

    ```js
    const date = new Date(2016, 0, 1, 0, 0, 0, 0);
    date.setSeconds(date.getSeconds() - 1);
    console.log(date); //Thu Dec 31 2015 23:59:59 GMT+0900
    ```

## Q136. 日付を数値へ変換するとき、タイムスタンプになることを知っていますか?

??? success
    ### 時間の計測(妥協案)
    - 減算を行うと、ミリ秒単位の差分になる

    ```js
    const start = new Date();

    const arr = Array.from({length: 1e7}, (_, i)=> i);

    const end = new Date();

    console.log(end - start); //390
    ```

## Q137. 差分を測定する場合、Date.now()が適切な理由を知っていますか?

??? success
    ### Date.now()
    - 現在のタイムスタンプを返す

    ### new Date().getTime()より速い理由
    - 中間のDateオブジェクトを作らないため

    ### 時間の計測(改善案)

    ```js
    const start = Date.now();
    const arr = Array.from({length: 1e7}, (_, i)=> i);
    const end = Date.now();
    console.log(end - start);

    ```

## Q138. ベンチマークテストのやり方とその注意点について知っていますか?

??? success
    ### 測定例

    ```js
    const diffSubtract = (date1, date2) =>{
      return date2 - date1;
    }

    const diffGetTime = (date1, date2) =>{
      return date2.getTime() - date1.getTime();
    }

    const bench = f =>{
      const date1 = new Date(0);
      const date2 = new Date();

      const start = Date.now();
      for(let i = 0; i<1e6; i++) f(date1, date2);
      return Date.now() - start;
    }

    let time1 = 0;
    let time2 = 0;

    //ヒートアップ
    bench(diffSubtract);
    bench(diffGetTime);

    //複数回実行する
    for(let i = 0; i<10; i++){
      time1 += bench(diffSubtract);
      time2 += bench(diffGetTime);
    }
    console.log("Time of diffSubtract: " + time1/10 + " ms"); //107.6ms
    console.log("Time of diffGetTime: " + time2/10 + " ms"); //6.1ms
    ```

    ### 測定例のような結果になる理由
    - `diffSubtract()`は減算時に型変換をする必要があるため
    - 型変換のオーバーヘッドは、`getTime()`によるアクセスより大きい

    ### 注意点
    - JSエンジンは何度も実行されるコード(ホットコード)に対して高度な最適化を行う。
    - そのためヒートアップ(メイン実行の前の実行)を追加することで速度は変わる

    ### 複数回実行するわけ
    - 1回の実行だと、たまたまCPUが並列で重い処理を行っており、速度が落ちた可能性があるため
    - 複数回行うことで、信頼度が上がる

    ### マイクロベンチマークについて
    - 非常に小さな関数をベンチマークの対象にするくらいなら、JSエンジンの仕組みを学んだ方がいい
    - 参考:[JSエンジンの最適化について](https://mrale.ph/)

## Q139. 文字列を日付に変換する際、Date.parse()を用いた方が安全な理由が分かりますか?

??? success
    ### Dateのコンストラクタに文字列を渡す場合
    - 予期しない結果を引き起こす可能性がある
    - これは`Date()`に数値やnull,undefinedを渡すことも可能であるため

    ```js
    new Date(null); //Thu Jan 01 1970 09:00:00 GMT+0900
    new Date(undefined); //Invalid Date
    ```

    ### Date.parse(str)の決め事
    - 文字列のフォーマットは`YYYY-MM-DDTHH:mm:ss.sssZ`と決められている
    - `YYYY-MM-DD`:日付
    - `T`:区切り文字
    - `HH:mm:sss.sss`:時間
    - `Z`:タイムゾーンのフォーマット
    - 例えば日本なら`+09:00`とする

    ### Date.parse(str)の例
    - `YYYY`や`YYYY-mm`や`YYYY-mm-DD`の短縮表記も可能
    - フォーマットに誤りがある場合`NaN`が返る

    ```js
    const date = Date.parse("2023-01-27T19:21:36.400+09:00");

    //timestampからnew Dateオブジェクトを作る
    cosnt dateObj = new Date(date);
    //Fri Jan 27 2023 19:21:36 GMT+0900
    ```

## Q140 更に高精度の時間計測が必要な場合に、何が使えるか知っていますか?

??? success
    ### performance.now()
    - ブラウザ限定
    - マイクロ秒の精度で、ページ読み込み開始からのミリ秒を返す

    ```js
    //小数4桁目以上は、精度エラーで正しくない。
    performance.now(); //254699.09999999404
    ```

    ### [seconds, nanoseconds] = process.hrtime()
    - Node.js限定
    - 現在のUnix時をナノ秒単位で返す
