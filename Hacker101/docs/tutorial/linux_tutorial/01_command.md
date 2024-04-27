# クイズ(1:コマンド)

## Q1 基本的なファイル操作のコマンドについて理解していますか?(前半)

??? success

    ### 方針

    ```text
    ・便利なオプションなどは紹介しない
    ・1日1回は使うものを紹介
    ```

    ### ファイル関連の用語

    | 用語                 | 意味                             |
    | -------------------- | -------------------------------- |
    | ルートディレクトリ   | 最上位のディレクトリ             |
    | カレントディレクトリ | 現在アクセス中のディレクトリ     |
    | ホームディレクトリ   | ログイン時のカレントディレクトリ |
    | 絶対パス             | ルートディレクトリからのパス     |
    | 相対パス             | カレントディレクトリからのパス   |
    | ./                   | カレントディレクトリを表す       |
    | ../                  | 1階層上のディレクトリを表す      |
    | ~                    | ホームディレクトリを表す         |

    ### コマンドの書式

    ```text
    $ cmd (option) (args)
    ```

    ### mkdir(ディレクトリ作成)

    ```bash
    $ mkdir cmd_test

    # -pでディレクトリを再帰的に作成可能

    $ mkdir -p cmd_test2/neko.txt
    ```

    ### cd(階層移動)

    ```bash
    $ cd cmd_test

    # 1つ親に移動
    $ cd ..

    # homeディレクトリに異動
    $ cd ~

    # rootに移動
    $ cd /

    # cdを用いて移動する前に戻る
    $ cd -
    ```

    ### touch(ファイル作成, timestamp更新)

    ```bash
    # 該当のファイルがない場合は、新規作成
    $ touch neko.txt

    # ある場合は、ファイルの最終更新日を変更
    $ ls -l
    -rw-r--r-- 1 user user 0  4月 27 16:59 neko.txt

    $ touch neko.txt
    $ ls -l
    -rw-r--r-- 1 user user 0  4月 27 17:00 neko.txt
    ```

    ### ls(ディレクトリの内容表示)

    ```bash
    $ ls
    neko.txt

    #lは詳細情報。aは隠しファイル(先頭.も表示)
    $ ls -la
    drwxr-xr-x  2 user user 4096  4月 27 16:59 .
    drwxr-x--- 25 user user 4096  4月 27 16:56 ..
    -rw-r--r--  1 user user    0  4月 27 17:00 neko.txt
    ```

    ### vi(ファイルを操作する)

    ```bash
    # 殆どの場合、vimを使うような気もするが
    # vimについては後程解説する

    $ vi neko.txt

    # iキーを押して、挿入モードに
    # 適当に入力
    # escキーを押して通常モードに
    # :を押してから、以下のコマンドでファイル操作可能
    
    # :w 保存
    # :q 終了
    # :wq 保存してから終了
    # :w! 強制保存
    # :q! 強制終了
    # :wq! 保存してから強制終了

    # nekodesu
    # ~
    # ~
    # ~
    # :wq
    ```

    ### cat(ファイルの内容出力)

    ```bash
    $ cat neko.txt
    nekodesu
    ```

    ### pwd(現在のディレクトリを確認)

    ```bash
    $ pwd
    /home/user/cmd_test

    $ cd ..
    $ cd -
    $ pwd
    /home/user/cmd_test
    ```

    ### cp(ファイルをコピーする)

    ```bash
    $ cp neko.txt neko2.txt

    $ ls
    neko.txt  neko2.txt
    ```

    ### rm(ファイルを削除する)

    ```bash
    $ rm neko2.txt
    $ ls
    neko.txt

    # ディレクトリを削除する場合は、rオプションが必要
    # rmdirの場合、ディレクトリ内にファイルがあるとerrorになるため
    # 此方を使う場合が多い
    $ mkdir hoge
    $ touch hoge/test.txt
    $ rm -r hoge
    ```

    ### mv(ファイルの移動)

    ```bash
    $ mv ./neko.txt ../cmd_test2/neko.txt

    $ cd ../cmd_test2

    $ ls
    neko.txt
    ```

    ### find(ファイル検索)

    ```bash
    # find <検索パス> -name <file名>
    # -name: ファイル名がパターンと一致するファイル
    # -iname: 大文字小文字を区別しない
    # -path: 引数にスラッシュを含む場合は、こっちで

    $ pwd
    /home/user/cmd_test2

    $ find ./ -name neko*
    ./neko.txt

    $ find ./ -path ./neko*
    ./neko.txt
    ```

    ### echo(テキスト表示)

    ```bash
    # -n 末尾の改行を抑制
    # -e エスケープシーケンスを解釈

    $ echo "neko\ninu\n"
    neko\ninu\n

    $ echo -en "neko\ninu\n"
    neko
    inu
    ```

## Q2 標準ストリームとリダイレクトについて理解していますか?

??? success
    ### 標準ストリーム

    | 入出力名       | 番号 | 意味         |
    | -------------- | ---- | ------------ |
    | 標準入力       | 0    | データ入力元 |
    | 標準出力       | 1    | データ出力先 |
    | 標準エラー出力 | 2    | エラー出力先 |

    ### リダイレクトについて

    ```text
    ・ <, >, >>等
    ・コマンドの入力元/出力先を変更する

    変更するということはデフォルトの入力元/出力先があるの?
      ・/dev/pts/<digit>等を見るといいかもしれない

    ファイルディスクリプタ(FD)
      ・カーネルが、操作対象のファイルを
        識別する際に必要となる

      ・descriptorと言っていることから分かる通り識別子
      ・標準入力なら0, 出力なら1, エラー出力なら2
      ・自分で開いたファイルには3以降の数字が割り当てられる

      ・カーネルに処理を依頼するとき、
        例えば、1> hoge.txtとすると、カーネルは標準出力先が
        hoge.txtに変更されることを理解する。
      
      ※　1> hoge.txt の場合
          標準出力　を hoge.txtにリダイレクト
    ```

    | -         | 意味                                   |
    | --------- | -------------------------------------- |
    | >         | 標準出力先。1> でも可。上書きモード    |
    | 2>        | 標準エラー出力先                       |
    | >>        | 標準出力。追記モードで開く             |
    | 2>>       | 標準エラー出力の追記                   |
    | \|        | 前のコマンドの標準出力を次の標準入力に |
    | >&        | 出力先を、他のFDにリダイレクト         |
    | 1>&2      | 標準出力を、エラー出力と同じにする     |
    | <         | 標準入力先。0<と同義                   |
    | tee       | ファイルと標準出力へ同時出力           |
    | /dev/null | ファイル。書き込まれたデータを捨てる   |

    ```bash
    # >
    $ echo "hello" > hello.txt
    $ cat hello.txt
    hello

    # >>
    $ echo "neko" > hello.txt
    $ echo "inu" >> hello.txt
    $ cat hello.txt
    neko
    inu

    # 2>
    $ ls --- 2> error.txt
    $ cat error.txt
    ls: unrecognized option '---'
    詳しくは 'ls --help' を実行して下さい。
    
    # <
    $ cat < neko.txt
    nekodesu

    # 1>&2 
    $ echo "neko" 1>&2 >  error.txt
    $ cat error.txt
    neko

    # | (パイプ) ※wcは改行数,単語数,文字数を表示する
    # nekoなのに、5になっているのは、末端の改行文字に起因する
    $ cat error.txt | wc
    1 1 5

    # /dev/null
    $ cat error.txt > /dev/null

    # tee
    $ echo "neko" | tee hello.txt
    neko
    $ cat hello.txt
    neko

    ```


    

