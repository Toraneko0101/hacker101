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
    ### 基本
    
    ```text
    ・コマンドは左から順番に評価される
    ```

    ### 標準ストリーム

    | 入出力名             | 番号  | 意味         |
    | -------------------- | ----- | ------------ |
    | 標準入力             | 0     | データ入力元 |
    | 標準出力             | 1     | データ出力先 |
    | 標準エラー出力       | 2     | エラー出力先 |
    | 自分で開いたファイル | 3以降 | -            |

    ### リダイレクトについて

    ```text
    ・ <, >, >>等
    ・コマンドの入力元/出力先を変更する

    ---------------

    変更するということはデフォルトの入力元/出力先があるの?
      ・/proc/self/fd/<digit>等を見るといいかもしれない

    ---------------
    
    ファイルディスクリプタ(FD)
      ・カーネルが、操作対象のファイルを
        識別する際に必要となる

      ・descriptorと言っていることから分かる通り識別子
      ・標準入力ならFD0, 出力ならFD1, エラー出力ならFD2
      ・自分で開いたファイルには3以降の数字が割り当てられる

      ・カーネルに処理を依頼するとき、
        例えば、1> hoge.txtとすると、カーネルは標準出力先が
        hoge.txtに変更されることを理解する。
      
      ※　1> hoge.txt の場合
          標準出力　を hoge.txtにリダイレクト
    ```

    ### デフォルトの入出力先

    | ファイル             | 詳細                  |
    | -------------------- | --------------------- |
    | /dev/self/fd/<digit> | FD~につながるファイル |
    | /dev/stdin           | 標準入力(FD0)         |
    | /dev/stdout          | 標準出力(FD1)         |
    | /dev/stderr          | 標準エラー出力(FD2)   |

    ### リダイレクト先変更、早見表
    | -         | 意味                                   |
    | --------- | -------------------------------------- |
    | >         | 標準出力先。1> でも可。上書きモード    |
    | 2>        | 標準エラー出力先                       |
    | >>        | 標準出力。追記モードで開く             |
    | 2>>       | 標準エラー出力の追記                   |
    | \|        | 前のコマンドの標準出力を次の標準入力に |
    | >&        | FDの出力先を、他のFDにリダイレクト     |
    | 2>&1      | 標準エラー出力を、標準出力と同じに     |
    | <         | 標準入力先。0<と同義                   |
    | tee       | ファイルと標準出力へ同時出力           |
    | /dev/null | ファイル。書き込まれたデータを捨てる   |

    ### 例

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

    # 2>&1 

    # 1 FD1の参照が output.txtになる
    # 2 FD2の参照先がFD1の複製(output.txt)になる
    $ ls ---  > output.txt 2>&1  
    $  cat output.txt
    ls: unrecognized option '---'
    詳しくは 'ls --help' を実行して下さい。

    # 1 FD2の参照先がFD1の複製(/dev/self/fd/1)になる
    # 2 FD1の参照先がoutput.txtになる
    $ ls --- 2>&1 > output.txt
    ls: unrecognized option '---'
    詳しくは 'ls --help' を実行して下さい。

    # 出力/Error出力を、出力したくない場合
    # FD1の参照先が/dev/nullになる
    # FD2の参照先がFD1(/dev/null)になる
    $ ls --- > /dev/null 2>&1

    # | (パイプ) ※wcは改行数,単語数,文字数を表示する
    $ cat output.txt | wc
    2 8 83

    # /dev/null (空で上書きする)
    $ cat /dev/null > output.txt | wc
    0 0 0

    # tee
    $ echo "neko" | tee hello.txt
    neko
    $ cat hello.txt
    neko

    ```

    ### まとめ

    ```text
    デフォルトの場合
      ・標準出力はFD1(/proc/self/fd/1)に
      ・標準エラー出力はFD2(/proc/self/fd/2)に出力される
      ・リダイレクトを行うことで、出力先を変更可能

      ※wsl2を用いている場合、
        /proc/self/fd/*は、シンボリックリンクを介して、
        /dev/pts/<digit>等の仮想ターミナルに接続されている
      
      ⇒したがって、デフォルトの場合、
        出力/エラー出力は仮想ターミナルに表示される
    ```

## Q3 基本的なファイル操作のコマンドについて理解していますか?(後半)

??? success

    ### less/more/head/tail(ファイル内容の表示)

    ```bash
    # less :qで表示終了, vimに近い操作
    # more: 末尾まで行くと自動的にプロンプトに戻る
    # たぶん、lessしか使わないと思う

    # head, tailそれぞれ、先頭、末尾から指定行表示

    # 適当な行数のファイルを用意しておく

    # 使い方
    $ cat alphabet.txt | head -3
    $ cat alphabet.txt | tail -3
    $ cat alphabet.txt | more
    $ cat alphabet.txt | less
    ```

    ### grep(パターンマッチ検索)

    ```bash
    # grep <正規表現> <file名>
    # file内の特定の文字列がある行を抽出する

    # -oならonly matchingでマッチした部分だけ
    # -Eなら拡張正規表現, -PでPerl正規表現
    # ディレクトリ内のサブファイルまで検索範囲に含めたい場合
    # -r, シンボリックリンクの先まで含めたいときは-R

    $ grep [abc] alphabet.txt
    a
    b
    c

    # 複数ファイル検索
    $ grep ^b.*$ alphabet.txt animal.txt
    alphabet.txt:b
    animal.txt:bear

    # ディレクトリ配下のファイル検索
    # -hで、ファイル名は消せる(-Hで表示する)
    $ grep ^b.*$ -r cmd_test
    cmd_test2/animal.txt:bear
    cmd_test2/alphabet.txt:b

    # 拡張正規表現
    $ grep -E c{2}o{2} animal.txt
    Raccoon

    # \bは単語の境界 \wは単語のみ
    $ echo "Neko inu nezumi" | grep -oE "\b\w*mi\b"
    nezumi

    # Perl正規表現(meta文字, 先読み, 最長一致などが使える)
    $ echo "foobar" | grep -oP "foo(?=bar)"
    foo
    ```

## Q4 コマンド入力時の便利なショートカットを知っていますか?

??? success

    ### 入力関連
    | ショートカット | 詳細                           |
    | -------------- | ------------------------------ |
    | Tab            | 入力補完                       |
    | Ctrl+A         | 行の先頭に移動                 |
    | Ctrl+E         | 行の末尾に異動                 |
    | Ctrl+B         | 1文字戻る                      |
    | Ctrl+F         | 1文字進む                      |
    | Ctrl+H         | カーソルの左側を1文字削除      |
    | Ctrl+D         | カーソルの右側を1文字削除      |
    | Ctrl+W         | カーソルより前の単語を削除     |
    | Ctrl+U         | 文頭からカーソルを削除         |
    | Ctrl+K         | カーソルから行末まで削除       |
    | Ctrl+Y         | 直前に削除した文字列を貼り付け |

    ### 実行関連

    | ショートカット | 詳細                         |
    | -------------- | ---------------------------- |
    | Ctrl+C         | 実行中のコマンドを強制終了   |
    | Ctrl+Z         | 実行中のコマンドを一時停止   |
    | Ctrl+L         | 画面をクリアする             |
    | Ctrl+P         | コマンドの1つ前の履歴        |
    | Ctrl+N         | コマンドの1つ後の履歴        |
    | Ctrl+R         | コマンド履歴を検索           |
    | Ctrl+D         | 入力中でない場合はログアウト |

    ### 使用例

    ```bash
    $ ls al
    # Tab(入力補完)
    $ ls alphabet.txt
    # Ctrl + W(単語削除)
    $ ls
    # Ctrl + U(カーソルから文頭まで削除)
    $
    # Ctrl + Y(削除した部分を復活)
    $ ls

    # Ctrl + R(コマンド検索)
    (reverse-i-search)`': ls
    alphabet.txt  animal.txt
    ```

    ### Ctrl + Dでログアウトしたくない時

    ```bash

    # 文字を消去しているときに、勢い余ってログアウトする(泣)


    # .bashrcを開く
    # 下記の設定を行うと、3回までならログアウトされなくなる
    ignoreeof=3

    #Ctrl + D
    # シェルから脱出するには "logout" を使用してください。
    # シェルから脱出するには "logout" を使用してください。
    # シェルから脱出するには "logout" を使用してください。
    # ログアウト
    ```

## Q5 権限、ユーザ関連の基本的なコマンドを知っていますか?

??? success

    ### 権限確認(ls -l)


    ```bash
    # test用のファイルを作成

    echo "echo success" > test.sh

    # 権限確認
    $ ls -l test.sh
    -rw-r--r-- 1 user user 13  4月 28 10:57 test.sh  

    # (補足)--time-styleで時刻のformatは変えられる
    # -gで所有者。-Gで所有グループは表示しない風にできる

    $ ls -gG --time-style="+%Y/%m/%d %H:%M:%S" test.sh
    -rw-r--r-- 1 13 2024/04/28 10:57:05 test.sh
    ```

    ### -rw-r--r-- 1 user user

    | 位置       | 意味                     |
    | ---------- | ------------------------ |
    | 1文字目    | ファイルの種類を意味する |
    | 2~4文字目  | 所有者の権限             |
    | 5~7文字目  | 所有グループの権限       |
    | 8~10文字目 | その他ユーザの権限       |
    | 1          | ハードリンクの数         |
    | 後ろ2つ    | 所有user & 所有グループ  |

    ### ファイルの種類(1文字目)とは

    | symbol | 説明                                                                                                                  |
    | ------ | --------------------------------------------------------------------------------------------------------------------- |
    | -      | 普通のファイル                                                                                                        |
    | d      | ディレクトリ                                                                                                          |
    | l      | シンボリックリンク<br>windowsでいうショートカット<br>このファイルへの読み書きは参照先ファイルに反映                   |
    | c      | キャラクタデバイス<br>マウスやキーボード等への読み書きに使用<br>対応するデバイスとは1バイト単位でデータをやり取りする |
    | b      | ブロックデバイス<br>SSD等への読み書きに使用<br>対応するデバイスとはブロック単位でデータをやり取りする                 |
    | p      | 名前付きパイプ<br>このファイルへ書き込まれたデータは、<br>同じOSの別プロセスから読み出せる                            |
    | s      | ソケット<br>このファイルへ書き込まれたデータは、<br>別OSのプロセスから読み書き可能                                    |

    ### rwx?

    ```text
    r: 読み取り権限
    w: 書き込み権限
    x: 実行権限
    ```

    ### ハードリンクとシンボリックリンク

    ```text
    iノード
      ・fileやdirectoryに関するメタデータを管理する領域
        permissionやgroup属性, size, 更新日時を管理する領域
      ・領域のsizeはパーティションのサイズによって変動する
    
    iノード番号
      ・ls -iで確認可能
      ・ls -lはiノードから情報を取得している
    
    ハードリンク
      ・リンク元と同じiノード番号を共有するファイル
      ・ファイルの実体を参照している
      ・リンク先のファイルが移動しても、
        ハードリンク先に影響はない
    
    シンボリックリンク
      ・実際のデータを含まない
      ・別ファイルや別ディレクトリへのパスを保持する
      ・リンク先のファイルが移動/削除されると、
        リンクは無効になる
    ```