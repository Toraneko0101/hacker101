# クイズ(1:コマンド)

## Q1 基本的なファイル操作のコマンドについて理解していますか?

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
    ```

    ### mv(ファイルの移動)

    ```bash
    $ mv ./neko.txt ../cmd_test2/neko.txt

    $ cd ../cmd_test2

    $ ls
    neko.txt
    ```

