# クイズ(1:コマンド)

## Q1 基本的なファイル操作のコマンドについて理解していますか?(前半)

??? success

    ### 方針

    ```text
    ・便利なオプションなどは紹介しない
    ・1日1回は使うものを紹介
    ・その他の便利なコマンドについては用途別に後程触れる
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

## Q3 基本的なファイル操作等のコマンドについて理解していますか?(後半)

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

    ### which(コマンドの場所を絶対パスを入手する)

    ```bash
    $ which ls
    /usr/bin/ls
    ```

    ### history(コマンド履歴)とコマンド入力の注意点

    ```bash
    $ history
    # ...
    2044 which ls
    2045 history

    # たとえば、mysql -u<user> -p<passwd>という
    # コマンドを入力したとする
    # この場合、passwordの入力履歴が残ってしまう

    # 消すこともできるけど...
    # -dで特定の行番号を削除。-cなら全体clear
    $ history -d 2047

    # たとえばmysqlなら以下の様にすることでpassを残さずに済む
    $ mysql -uuser -p # passwordはEnter password:で入力
    ```

    ### locale(現在のロケールと使用可能なロケール情報表示)

    ```bash
    $ locale
    # LANG=ja_JP.UTF-8
    # LANGUAGE=
    # LC_CTYPE="ja_JP.UTF-8"

    # 指定可能なlocaleの一覧表示
    $ locale -a | grep ja
    ja_JP.utf8

    # 言語名の表示
    $ locale language
    Japanese

    # 地域名の表示
    $ locale territory
    Japan
    ```

    ### man(コマンドのマニュアルが読める)

    ```bash
    $ man ls
    ```
    
    ### manを日本語化する

    ```bash
    # 1 manコマンドの日本語化packageをinstall
    # sudo, aptについては後述
    
    $ sudo apt update
    $ sudo apt install manpages-ja
    $ sudo apt install manpages-ja-dev

    $ locale
    # 2 LANG=ja_JP.utf8でなければ下に進む

    $ locale -a
    # 3 ja_JP.utf8がなければ次のコマンドを実行

    # 4 日本語localeをinstall
    $ sudo apt install language-pack-ja

    # 5 日本語localeに設定する
    $ sudo update-locale LANG=ja_JP.utf8

    # 6 再起動して、manが日本語になっているか確認
    ```

    ### 英語に戻したいとき

    ```bash
    $ sudo update-locale LANG=en_US.utf8
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

    ### VScodeで使う場合

    ```text
    1. Ctrl + Shift + Pでコマンドパレットを開く
    2. Preferences: Open Keyboard Shortcutsを選択
    3. Control + <選択したいコマンド>
    4. When式に!terminalFocusInAnyを入力
    5. ターミナルにfoucsしているときはVSCodeの
       ショートカットが効かないようにしておく
    
    ⇒ おそらくDefaultでは、Ctrl + E, Q等が競合しているので
       この設定が必要だろうと思われる(ないとイライラする)
    
    ```

## Q5 権限を確認変更するコマンドを知っていますか?(ハードリンクについても解説する)

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
    
    ハードリンク
      ・リンク元と同じiノード番号を共有するファイル
      ・ファイルの実体を参照している
      ・（ファイルの実体をファイル名で操作できるのは
        ハードリンクのおかげ）
      ・リンク先のファイルが移動しても、
        ハードリンク先に影響はない
      ・同じファイルシステム内でのみ作成可能
      ・lnで作成可能

      ※touch,vim等で初めてファイルを作成するとき、
        1つ目のハードリンクが自動的に作成される
    
    シンボリックリンク
      ・実際のデータを含まない
      ・リンク先の絶対パスや相対パスを保持する
      ・リンク先のファイルが移動/削除されると、
        リンクは無効になる
      ・ファイルシステムをまたいだリンク作成が可能
      ・ln -sで作成可能
    ```

    ### ハードリンクとシンボリックリンクの例

    ```bash
    $ echo "This is test." > test

    # ハードリンク
    $ ln test test.hard

    # シンボリックリンク
    $ ln -s test test.sym

    # 情報確認
    $ ls -gG
    -rw-r--r-- 2 14  4月 28 12:50 test.hard
    -rw-r--r-- 1 13  4月 28 10:57 test.sh
    lrwxrwxrwx 1  4  4月 28 12:50 test.sym -> test

    # iノードの確認
    $ ls -i test*
    4757 test  
    4757 test.hard  
    1579 test.sh  
    3701 test.sym 

    # 元ファイルを削除する
    # ファイルの実体を1つ以上のハードリンクが参照しているため
    # iノードは削除されずに残っており、引き続きアクセス可能
    $ rm test
    $ cat test.hard
    This is test.

    # シンボリックリンクは、ファイルへの参照先を格納している
    # ため、参照元がなくなった以上、アクセス不可
    $ cat test.sym
    cat: test.sym: そのようなファイルやディレクトリはありません
    ```

    ### ファイル削除とiノード

    ```text
    ・Linuxにおけるファイル削除は、iノードを参照している
      リンクが1つ少なくなることを意味する
    
    ・ファイル削除≠iノードのデータ領域削除ではない

    ・リンクのカウントが0になったとき、iノード領域と
      ファイルの実体は初めて削除の対象となる
    ```

    ### 権限設定(chmod)

    ```text
    u user
    g group
    o other
    a all

    = 指定した権限にする
    + 指定した権限を付与する
    - 指定した権限を除去する

    r 読み取り
    w 書き込み
    x 実行

    2進数で制御可能
    765 -> rwxrw-r-x
    432 -> r---wx-w-
    100 -> --x------
    ```

    ```bash
    $ ls -l test.sh
    -rw-r--r-- 1 user user 13  4月 28 10:57 test.sh  

    $ chmod 774 test.sh

    # これで所有者,所有グループである場合test.shを実行可能
    $ ./test.sh
    success

    $ ls -l test.sh
    -rwxr--r-- 1 user user 13  4月 28 10:57 test.sh

    #実行権限を剥奪
    $ chmod ug-x test.sh

    $ ls -l test.sh
    -rw-r--r-- 1 user user 13  4月 28 10:57 test.sh

    # イコールでの指定方法(指定されなかった権限は消える)
    $ chmod o=wx test.sh

    $ ls -l test.sh
    -rw-r---wx 1 user user 13  4月 28 10:57 test.sh  
    ```

## Q6 ユーザ/グループを操作するコマンドについて知っていますか?

??? success

    ### 一覧

    | コマンド等  | 意味                                |
    | ----------- | ----------------------------------- |
    | who         | ログイン中のユーザ一覧を表示        |
    | id          | ユーザID, グループID等の表示        |
    | chown       | ファイルの所有者変更                |
    | sudo        | 一時的に他user,他グループの権限付与 |
    | su          | 別ユーザとしてログイン              |
    | adduser     | 新規ユーザを対話的に作成            |
    | useradd     | ユーザを非対話的に作成              |
    | /etc/passwd | ユーザ一覧の確認                    |
    | passwd      | パスワードの変更                    |
    | exit        | ログアウト                          |
    | groupadd    | 新規グループを作成                  |
    | /etc/group  | グループ一覧の確認                  |
    | gpasswd     | グループメンバを整理(groupのpassも) |
    | userdel     | ユーザの削除                        |
    | groupdel    | グループの削除                      |
    | groups      | ユーザが所属しているグループ表示    |

    ### who(ログインしているユーザを表示)

    ```bash
    $ who
    user pts/1        2024-04-25 10:44
    user pts/2       2024-04-25 10:44
    ```

    ### whoami(現在のセッションのユーザ名を表示)

    ```bash
    $ whoami
    user
    ```

    ```bash
    $ who

    ### chown(ファイルの所有者を変更)

    ```bash
    # chown <user>:<group> <file_name>
    $ sudo chown root:root test.sh
    $ ls -l test.sh
    -rw-r---wx 1 root root 13  4月 28 10:57 test.sh
    ```

    ### sudo 

    ```text
    ・コマンド実行の単位で
      現在のユーザに他のユーザ、他のグループの権限を与える
    
    ・一般ユーザにroot権限を一時的に付与するという目的で
      使われることが多い
    ```

    ```bash
    # -uや-gがない場合、rootユーザとして実行する
    $ sudo cat /etc/shadow

    # 実行するuser名を指定
    $ sudo -u root cat /etc/shadow

    # 実行するgroup名を指定
    $ sudo -g nezumi cat /etc/shadow
    cat: /etc/shadow: 許可がありません
    ```

    ###  sudoのパスワードは何を入力するの?

    ```texst
    ・実行しているユーザのパスワード
    ・root/他ユーザのパスワードではない

    ⇒一定期間内であれば2回目以降のパスワード入力は省略可能
    ⇒ sudo -kとすると、認証情報を破棄できる
    ```

    ### sudoを使えば誰でもroot権限で実行できるってこと?

    ```text
    ・current_userが、sudoコマンドを使ったとき、
      どの権限で何を実行できるのか
    
    ・これは、/etc/sudoersというファイルに書かれている

    ・/etc/sudoersはたとえrootでも、Editor越しに編集できず
      編集する際はvisudoを用いる必要がある

    ・sudoコマンドを使った履歴は、CentOSなら/var/log/secure
      Debian系なら、/var/log/auth.logに残される
    ⇒記録される！
    ```

    ```bash
    $ sudo less /etc/sudoers

    # user/group ホスト名=(User名:Group名)　できること

    # %を付けると、グループに関する指示となる
    # 下記の場合、root, sudoグループに属しているuser
    # は、任意のユーザ名/グループ名で任意のコマンドを実行可能

    # adminグループに属している場合、他のユーザとしてコマンド
    # 実行はできるが、他のグループとしては実行できない

    # User privilege specification
    root    ALL=(ALL:ALL) ALL

    # Members of the admin group may gain root privileges        
    %admin ALL=(ALL) ALL

    # Allow members of group sudo to execute any command
    %sudo   ALL=(ALL:ALL) ALL
    ```

    ### adduser/useradd(新規ユーザを作成する)

    ```bash
    #こっちなら作成される(対話的にuserを作成する)
    $ sudo adduser nezumi

    #homeが作成されない
    $ sudo useradd nezumi

    ```
    
    ### id(userid等の表示)

    ```bash
    $ id nezumi
    uid=1001(nezumi) gid=1002(nezumi) groups=1002(nezumi)
    ```

    ### /etc/passwd (ユーザの一覧を確認する)

    | field | 意味                        |
    | ----- | --------------------------- |
    | 1     | user名                      |
    | 2     | passwd(security上の理由でX) |
    | 3     | UserID                      |
    | 4     | GroupID                     |
    | 5     | Comment                     |
    | 6     | Homeディレクトリ            |
    | 7     | ログインシェル              |

    ```bash
    $ cat /etc/passwd | grep nezumi
    nezumi:x:1001:1002:,,,:/home/nezumi:/bin/bash
    ```

    ### passwd(パスワードの変更)

    ```bash
    # sudo passwd <user_name>
    $ sudo passwd nezumi
    ```

    ### su(別のユーザとしてログイン)

    ```bash
    #ディレクトリは変わらない
    $ su nezumi

    #別ユーザのHomeディレクトリに移動、環境変数も変更
    $ su - nezumi

    ```

    ### exit(ログアウト)

    ```bash
    # suコマンドを実行する前のユーザに戻る
    $ exit
    ```

    ### groupadd(新規グループを追加)

    ```bash
    sudo groupadd animal
    ```

    ### /etc/group(グループの一覧を表示)

    ```bash
    $ cat /etc/group | grep animal
    animal:x:1003:
    ```
    
    ### gpasswd (グループのメンバを整理+passwd)

    ```bash
    # gpasswd  <user_name> <group_name>
    # -a : 追加
    # -d : 削除

    $ sudo gpasswd -a nezumi animal
    ユーザ nezumi をグループ animal に追加

    $ cat /etc/group | grep animal
    animal:x:1003:nezumi

    $ sudo gpasswd -d nezumi animal
    ユーザ nezumi をグループ animal から削除

    $ cat /etc/group | grep animal
    animal:x:1003:

    # グループにパスワード設定
    $ gpasswd animal

    # グループからパスワード削除
    $ gpasswd -r animal
    ```

    ### groupdel(グループの削除)

    ```bash
    $ sudo groupdel animal
    $ cat /etc/group | grep animal
    # ヒットなし
    ```

    ### userdel(userの削除)

    ```bash
    $ sudo userdel nezumi
    $ cat /etc/passwd | grep nezumi
    # ヒットなし
    ```

    ### groups(所属しているグループ一覧を見る)

    ```bash
    $ groups 
    nezumi
    ```

    ### 補足(wsl2からのログイン)

    ```text
    ・windows terminal等から、wslを介してubuntuを起動した
    　場合、defaultのユーザはsudoグループに所属している

    ・そのため、sudo su -を用いる事で、
      rootのpasswd等を再設定可能
    ```

    ### visudoでsudoersファイルを編集する

    ```bash
    # User:nezumiがaddUserコマンドを使えるように編集する
    # コマンドの場所はwhich等で入手可能
    
    $ sudo visudo

    # nezumi ALL=(ALL) /usr/sbin/adduser

    $ sudo adduser neko
    # 正常に処理可能

    $ sudo userdel neko
    # 残念ですが、ユーザー nezumi は
    # '/usr/sbin/userdel neko' を root として 
    # ******* 上で実行することは許可されていません。

    # visudoが実行できる元のuserに戻る
    $ sudo visudo
    # 以前の設定を取り消しておく
    ```

## Q6.5 チュートリアルで使ったコマンドリスト(未分類)

??? success
    ### wc
