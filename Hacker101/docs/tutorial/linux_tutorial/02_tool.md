# クイズ(2:ツール)

## Q7 パッケージ管理ツール(apt等)について知っていますか?

??? success

    ### Linuxディストリビューション

    ```text
    ・Linux(カーネル)と、その他のソフトウェア群を
      1つにまとめて、簡単にinstallできるようにしたもの
    
    ・その他のソフトウェア群には、たとえばvim等のエディタや
      python等のスクリプト言語。GUI関連などが含まれる
    
    ・distributionの名の通り、様々な配布形態が存在する

    ・幾つかの系統に分かれており、
      RedHat系や、Debian系、Arch系などが有名

    ・たとえばyumは、red hat系のパッケージ管理システム
    ・たとえばaptは、debian系のパッケージ管理システムである
    ```

    ### パッケージ管理システム

    ```text
    ・パッケージのinstall/uninstall/update
    　を行うためのツール

    ・ディストリビューションの系統が異なると、
      パッケージ管理システムも異なることが多い

    ・パッケージについてはリポジトリから探してくる
  
    ・パッケージ同士の依存関係も解決してくれる
    ```

    ### パッケージ(Package)

    ```text
    ・ソフトウェアに関するファイル一式をまとめたもの

    ・実行ファイルや設定ファイル、ドキュメント
      プログラムを動かすためのライブラリやソースが含まれる
    
    ・パッケージはバイナリ形式(既にコンパイル済み)の場合や、
      ソース形式(install後にコンパイルする)であるケースがある
    ```

    ### ライブラリ(Library)

    ```text
    ・再利用可能なコードの集まり
    ・コードは特定の機能を提供するために開発されている
    ・一般的に、モジュールより粒度が大きい
    ```

    ### モジュール(Module)

    ```text
    ・複数の関数、クラス、定数等を１つのファイルにまとめた物
    ```

    ### リポジトリ

    ```text
    ・パッケージの保管場所(配布サイト)
    ⇒aptリポジトリの項も参照
    ```

    ### パッケージ管理システム一覧

    | パッケージ管理システム | ディストリビューション |
    | ---------------------- | ---------------------- |
    | rpm                    | rhel                   |
    | yum                    | rhel                   |
    | dpkg                   | ubuntu/debian          |
    | apt                    | ubuntu/debian          |

    ### dpkg vs apt

    ```text
    dpkg
      ・依存関係が存在するとき、install/uninstallを中断
      ・ローカルにあるパッケージしかinstallできない
        (事前にDLしておく必要がある)
    
    apt
      ・自動で依存関係を解決する
      ⇒依存関係のあるpackageをrepositoryから自動install
      ⇒依存関係のあるpackageを自動uninstall

      ・dpkgを使って、packageをinstallした場合、
        依存関係が壊れている場合がある
        sudo apt install -fで修復可能
      
      ・リモートリポジトリにあるパッケージもinstall可能

      ・apt-getとapt-cacheを包括するコマンドである

      ・内部的にはdpkgを使用して、installしている

    apt-get
      ・パッケージのintall/uninstallを行う
    
    apt-cache
      ・パッケージの情報を参照、検索するコマンド
      
    使い分け
      ・基本的にCLIではaptを用いる

      ・bashやDockerfile等のスクリプトを介して使用する
        場合は、apt-getやapt-cacheを用いる
    ```

    ### aptリポジトリの場所

    ```text
    ・package管理システムではデフォルトで幾つかのリポジトリ
      が設定されている
    
    ・目的のパッケージが標準のリポジトリにない場合
      サードパーティのリポジトリを追加する
    
    ・aptコマンドが利用するリポジトリの情報は、以下の通り
    ```

    ```bash
    # /etc/apt/sources.listには標準のaptリポジトリが記載

    # deb         : debパッケージを取得する
    # http://...  : 取得先のURL
    # jammy       : バージョン名
    # main        : 後述
    $ cat /etc/apt/sources.list | grep ^[^#] | less -SFX
    deb http://archive.ubuntu.com/ubuntu/ jammy main restricted  
    deb http://archive.ubuntu.com/ubuntu/ jammy-updates main restricted
    deb http://archive.ubuntu.com/ubuntu/ jammy universe
    deb http://archive.ubuntu.com/ubuntu/ jammy-updates universe 
    deb http://archive.ubuntu.com/ubuntu/ jammy multiverse       
    deb http://archive.ubuntu.com/ubuntu/ jammy-updates multiverse
    deb http://archive.ubuntu.com/ubuntu/ jammy-backports main restricted universe multiverse
    deb http://security.ubuntu.com/ubuntu/ jammy-security main restricted
    deb http://security.ubuntu.com/ubuntu/ jammy-security universe
    deb http://security.ubuntu.com/ubuntu/ jammy-security multiverse

    # /etc/apt/sources.list.d ディレクトリ配下には
    # サードパーティのリポジトリ情報が記載
    # mariadbやgroongaをinstallした時の情報が残っている

    $ ls /etc/apt/sources.list.d 
    archive_uri-http_sfo1_mirrors_digitalocean_com_mariadb_repo_10_6_ubuntu-jammy.list
    groonga-ubuntu-ppa-jammy.list
    ubuntu-toolchain-r-ubuntu-test-jammy.list
    ```

    ### 補足(main, restricted, universe, multiverse)

    ```text
    ※Ubuntu特有

    main
      ・再配布フリー
      ・Ubuntuチームが完全にサポートしているリポジトリ
    
    Resticted
      ・制限付きのライセンス
      ・公式にサポートされる
    
    Universe
      ・すべてフリー、OSS
      ・公式にはサポートされない

    Multiverse
      ・フリーでないソフトウェアが含まれる
    ```

    ### deb vs deb-src

    ```text
    deb : バイナリパッケージ
    deb-src: ソースパッケージ

    ※通常は、バイナリパッケージだけで十分
    ※ソースパッケージを有効化するとDLするメタデータの情報
      等が増加する。defaultでコメントアウトされているので安心
    ```

    ### aptを用いたinstall

    ```bash
    sudo apt update
    sudo apt install <package_name>
    ```

    ```text
    sudo apt update
      1 /etc/apt/sources.listを参考に
        それぞれのaptリポジトリが保有している
        パッケージのリストを取得する
      
      2 保有しているパッケージは/var/lib/apt/lists
        に格納される
      
      ※リポジトリが保有するパッケージのリスト
        (=パッケージインデックス)は日々更新されているので
        install前にはこのコマンドを実行する
      
      ※パッケージリストに載っていないパッケージは
        downloadできない
    
    sudo apt install <package_name>
      1 aptリポジトリから、packageをdownload/installする

      2 install時のpackageは、/var/cache/apt/archivesに
        cacheされる
      
      3 cacheはsudo apt cleanで消すことが可能
    ```

    ### よく使用するaptのサブコマンド一覧

    ```text
    サブコマンド
      ・git,awsのように、第1引数にさらにコマンドのような
     　 文字列が続くものを指す
      
      例) aws codecommit,  git commit
    
    Unix哲学
      ・各プログラムが1つのことをうまくやる

      ⇒サブコマンドはこれに反しているように感じるが、
        そもそもコマンドは1つ以上のことを行う
      
      ⇒gitはバージョン管理という1つのことをこなしている
        だから問題はない
    ```

    | サブコマンドを含む | 説明                           |
    | ------------------ | ------------------------------ |
    | apt update         | パッケージインデックスの更新   |
    | apt upgrade        | パッケージの更新               |
    | apt full-upgrade   | 保留されているパッケージの更新 |
    | apt autoremove     | 不要になったパッケージの削除   |
    | apt install        | パッケージのinstall            |
    | apt remove         | パッケージの削除               |
    | apt purge          | パッケージの完全削除           |
    | apt show           | パッケージの詳細情報表示       |
    | apt list           | パッケージ検索(完全一致)       |
    | apt search         | パッケージ検索(部分一致)       |
    | apt clean          | cacheディレクトリのお掃除      |
    | apt autoclean      | 未installのcache削除           |
    | apt depends        | 依存関係を表示                 |

    ### サブコマンドの補足説明

    ```text
    apt upgrade
      ・現在システムにinstallされている全てのパッケージに対し
        aptリポジトリ内のパッケージリストを参照し、
        利用可能な最新versionをinstallする
      
      ・既存のパッケージが削除されることはない

    apt full-upgrade
      ・システム全体をupgradeするために、
        必要な場合は、現在install済みのpackageを削除可能
    
    apt remove
      ・installした実行ファイルやライブラリを削除
      ・設定ファイルは削除しない
      ・再びinstallした時は、前の設定が引き継がれる
    
    apt purge
      ・installした実行ファイルやライブラリを削除
      ・設定ファイルも削除
    
    apt list --installed
      ・install済みのパッケージ一覧を表示している
    
    apt list --upgradable
      ・upgrade可能なpackage一覧を表示
    ```

    ```bash
    # install一覧を表示した例
    $ sudo apt list --installed | grep vim

    vim-common/jammy-updates,jammy-security,now 2:8.2.3995-1ubuntu2.16 all [インストール済み、自動]
    vim-runtime/jammy-updates,jammy-security,now 2:8.2.3995-1ubuntu2.16 all [インストール済み、自動]
    vim-tiny/jammy-updates,jammy-security,now 2:8.2.3995-1ubuntu2.16 amd64 [インストール済み、自動]
    vim/jammy-updates,jammy-security,now 2:8.2.3995-1ubuntu2.16 amd64 [インストール済み、自動]


    # 依存関係を表示した例
    $ sudo apt depends vim    

    vim
      依存: vim-common (= 2:8.2.3995-1ubuntu2.16)
      依存: vim-runtime (= 2:8.2.3995-1ubuntu2.16)
      依存: libacl1 (>= 2.2.23)
      依存: libc6 (>= 2.34)
      依存: libgpm2 (>= 1.20.7)
      依存: libpython3.10 (>= 3.10.0)
      依存: libselinux1 (>= 3.1~)
      依存: libsodium23 (>= 1.0.14)
      依存: libtinfo6 (>= 6)
      提案: <ctags>
        exuberant-ctags
        universal-ctags
      提案: vim-doc
      提案: vim-scripts
    
    # パッケージの説明を表示した例
    $ sudo apt show vim 2> /dev/null | head -2

    Package: vim
    Version: 2:8.2.3995-1ubuntu2.16
    ```

    ### aptリポジトリを追加する

    ```text
    ・サードパーティ製のリポジトリを追加する方法
    ```

    ```bash
    # add-apt-repository
    $ sudo add-apt-repository ppa:jonathonf/vim

    # 更新
    $ sudo apt update
    $ sudo apt upgrade

    $ sudo apt show vim 2> /dev/null | head -2
    Package: vim
    Version: 2:9.0.0749-0york0~22.04

    # /etc/apt/sources.list.d配下を見ると
    # 以下のものが増えている
    # jonathonf-ubuntu-vim-jammy.list
    ```

## Q8 プロセスとジョブについて知っていますか?

??? success

    ### プロセス

    ```text
    ・メモリ上で実行状態にあるプログラム

    ・コマンドを実行する際、以下の手順を踏む
      1 実行ファイルがメモリに格納される
      2 メモリ上でCPUがプログラムを実行

      ⇒この時、メモリ上で実行状態にあるプログラム = プロセス
    
    ・プロセスは既に存在しているプロセスから作成される
      例) lsコマンドの実行
          shell(親プロセス) -> ls(子プロセス)
    
    ・プロセスはプロセスIDという、システム全体で
      一意のIDを持つ。これはプロセス終了まで変化しない
      
      ⇒解放されるとそのIDは、別の新しいプロセスで
        再利用できるようになる
    
    ・プロセスには、実行ユーザが設定されており、
      他人のプロセスを操作することはできない
    ```

    ### ps(実行中のプロセスを表示)

    ```bash

    # PID: プロセスID
    # TTY: 実行端末
    # TIME: 実行時間
    # CMD: コマンド

    $ ps
      PID TTY          TIME CMD
      541 pts/0    00:00:00 bash
    84873 pts/0    00:00:00 ps

    # ハイフンありなしで違う意味を持つので注意
    # u: ユーザ指向のformatで表示
    # -u: 指定ユーザのプロセスを全表示
    # a: tty(端末)を持つすべてのプロセス表示
    # x: tty(端末)を持たない全てのプロセス表示
    # -> ax : 全てのプロセス表示

    # VSZ: 仮想メモリ領域のサイズ
    # RSS: 物理メモリ領域のサイズ
    # STAT: プロセスの状態
    # START: 開始時刻
    # TIME: 総実行時間
    $ ps u -u nezumi | less -SFX
      USER         PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
      nezumi      2565  0.0  0.0  16920  5548 ?        Ss   17:13   0:00 /lib/systemd/systemd --user
      nezumi      2566  0.0  0.0 168936  1424 ?        S    17:13   0:00 (sd-pam)
      nezumi      2571  0.0  0.0  12312  4112 pts/3    S    17:13   0:00 bash
      nezumi      3338  0.0  0.0  12540  5096 pts/3    S    17:16   0:00 -bash
      nezumi     88278  0.0  0.0   9420  1056 pts/3    S+   23:04   0:00 sleep 20
    ```

    ### STATの意味合い(プロセスの状態)について

    | 項目 | 意味                              |
    | ---- | --------------------------------- |
    | R    | 実行中/実行可能。実行キューにある |
    | S    | 割り込み可能。通常待ち状態        |
    | D    | 割り込み不可。通常I/O待ち         |
    | Z    | ゾンビプロセス                    |
    | T    | 停止中                            |
    | +    | フォアグラウンドで実行中          |
    | s    | セッションリーダー                |
    | <    | 優先度が高い                      |
    | N    | 優先度が低い                      |
    | l    | マルチスレッドのプロセス          |

    ```text
    s(セッションリーダ)
      ・制御端末とやり取りするプロセス
      ・端末と切断される時、Session内の全プロセスが停止する
    
    ※プロセスには主に4つIDが関連付けられている
      PID(プロセスID)
        ・プロセスを識別する
      
      PPID(親プロセスID)
        ・プロセスを開始したプロセスのPID
      
      PGID(プロセスグループID)
        ・プロセスグループリーダのPID
        ・PID=PGIDなら、当該プロセスがリーダ
      
      SID(セッションID)
        ・セッションリーダのPID
        ・PID=SIDなら、当該プロセスがリーダ
    
    何のために存在するの?
      ・Session/プロセスグループは、関連する多数のプロセスを
        1単位として扱えるようにしている。
      ・Session -> 複数のプロセスグループ -> プロセス
      ・通常、Shellがセッションリーダとなり、Shellによって
        実行される全てのパイプラインがプロセスグループになる

      ・セッションリーダであるShellが終了されると、
        その子であるプロセスグループを簡単にKillできる！
    ```



    ### スレッドとプロセス

    ```text
    マルチプロセス
      ・1つのPCで複数のプロセスを並行して進める
    
    マルチスレッド
      ・プロセスを複数に分割し、プロセス内で
      　並行処理をできるようにしたもの

      ・CPUコアが複数ある場合、それぞれのスレッドを
        割り当てることで、CPUコアを効率的に使用可能
    
    プロセスの特徴
      ・各プロセスはメモリ空間やファイルハンドラなどの
        リソースを共有しない

      ・プロセス間で情報を共有したい場合は、プロセス間通信
      　を行う必要があるので、オーバーヘッドが生じる
    
    スレッドの特徴
      ・同一プロセスから生成されたスレッドは、メモリ空間等の
        リソースを共有する

      ・グローバル変数に変更を加えると、同一プロセス内の
        他スレッドは変更された値を参照できる

      ・同一プロセス内のスレッドが、同じタイミングで
      　同じリソースにアクセスすると競合が生じる

      ・プロセスが終了されると、プロセスに含まれる
        全てのスレッドも終了する
    ```

    ### ジョブ

    ```text
    ・シェルから見た処理単位
    ・コマンドラインの1行が、1つのジョブに相当

    ・ジョブはシェルの中で一意のIDを持つ
      つまり、複数ターミナルを立ち上げている場合、
      ジョブ番号は重複しているように見える
    ```

    ```bash
    # ジョブとプロセスの例(下記に利用価値はなくあくまで例示)
    # ※jobsはjob一覧を表示する

    # プロセスがコマンド単位であることが分かる
    $ ps u -u user | less -SFX
    user    3771  0.0  0.0  13720  1656 pts/0    R+   09:03   0:00 ps u -u user
    user    3772  0.0  0.0   9804  2636 pts/0    S+   09:03   0:00 less -SFX
    
    # CTRL + Z
    [1]+  停止 ps u -u user | less -SFX  

    # jobs(ジョブ一覧を表示)
    # 複数コマンドで1つのジョブになっていることが確認できる
    jobs
    [1]+  停止 ps u -u user | less -SFX 
    ```

    ### bg/fgコマンドとSTAT

    ```bash
    # フォアグラウンド(シェルの画面内)で実行中のジョブを
    # 一時停止し、
    # バックグラウンド(シェルの画面外)で再開してみる

    $ sleep 100

    # Ctrl + Z : ジョブを一時停止
    # ジョブは自動的にバックグラウンドに移動する
    # この時、左端に表示されているのがジョブ番号となる
    [1]+  停止                  sleep 100
    
    # STATを見ると、Tになっている。
    $ ps u
    USER     PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
    user   96986  0.0  0.0   9420  1016 pts/0    T    07:26   0:00 sleep 100

    # bg <digit>で該当するジョブ番号を
    # バックグラウンドで実行する
    # バックグラウンドで実行されているためS+ではなく、S
    $ bg 1
    [1]+ sleep 100 &

    $ ps u
    user   96986  0.0  0.0   9420  1016 pts/0    S    07:26   0:00 sleep 100

    # fg <digit>で該当するジョブ番号を
    # フォアグラウントに戻す
    $ fg 1
    sleep 100
    ```

    ### 最初からバックグラウンドで実行したい場合

    ```bash
    # アンパサンドを付けて実行
    $ sleep 100 &
    [1] 73751

    # bgで実行されていることが確認できる
    $ ps -o pid,command
        PID STAT COMMAND
      19714 Ss   -bash
      73844 S    sleep 100
      74092 R+   ps -o pid,stat,command
    ```

    ### バックグラウンドで実行するジョブとは?

    ```text
    バックグラウンドで実行するジョブ
      ・時間のかかるジョブ
      ・PCの起動中、常に実行する必要があるジョブ
      ・複数のジョブを同時に実行したい場合
    
    フォアグラウンドで実行した場合
      ・ジョブの実行中は、シェルが停止する
      ⇒つまり、コマンド実行中は、他の処理を実行できない
    ```


    ### SIGNAL

    ```text
    ・割り込みによって、プロセスに特定のふるまいをするよう
      通知することができる仕組み
    
    ・たとえば、CTRL + Cを押すことで、SIGINTというシグナルが
      fgで実行中のジョブ内のプロセスに対して送信される
    ⇒通知を受け取ったプロセスは、たとえ処理の途中で合っても
      処理を終了して、消滅する
    ```

    | 動作 | 説明                         |
    | ---- | ---------------------------- |
    | TERM | プロセス終了                 |
    | CORE | プロセス終了とコアダンプ出力 |
    | STOP | プロセス一時停止             |
    | IGN  | このシグナルを無視           |
    | CONT | 停止中の場合、プロセス再開   |

    | SIGNAL名 | 動作 | 意味                         |
    | -------- | ---- | ---------------------------- |
    | SIGHUP   | TERM | 端末の切断によるプロセス終了 |
    | SIGINT   | TERM | keyboardからの割り込み       |
    | SIGILL   | CORE | 不正な命令                   |
    | SIGTSTP  | STOP | 端末から入力された一時停止   |
    | SIGSTOP  | STOP | プロセスの一時停止           |
    | SIGCONT  | CONT | 一時停止からの再開           |
    | SIGTERM  | TERM | 終了シグナル                 |
    | SIGKILL  | TERM | KILLシグナル                 |


    ### KILLとCTRL + C

    ```text
    KILL
      ・KILL <signal-id>として使用
      ・デフォルトの場合、SIGNAL名は、TERM(15)
      ・SIGTERMはプログラムに対して終了指示を行う
      ・プログラムが異常状態で、終了指示が効かない場合は、
        KILL(9):強制終了を使う場合がある
      
      例)
        KILL -15 <process_id> 終了させる
        KILL -9 <process_id>  強制終了させる
        KILL -TERM, KILL -KILLという形で名前で指定してもよい
    
    CTRL+C
      ・fgで実行しているジョブにSIGINTシグナルを送る
      ⇒ジョブは終了する
    
    CTRL+Z
      ・fgで実行しているジョブにSIGTSTPシグナルを送る
      ⇒ジョブは一時停止する
    
    fg,bg
      ・SIGCONTを送り、一時停止されたジョブを再開する
      ⇒ジョブは再開される
    
    補足(KILLALL)
      ・killと異なり、プロセス名を指定することで、
      　複数のプロセスを一度に終了可能
      ・プロセス名は完全一致する必要がある

      $ sleep 100 &
      $ sleep 200 &
      $ ps | grep sleep
      148637 pts/0    00:00:00 sleep
      148650 pts/0    00:00:00 sleep

      $ killall sleep
      [1]-  Terminated              sleep 100
      [2]+  Terminated              sleep 200
    
    補足(終了と強制終了)
      ・SIGTERMのような終了では、終了前に使用していたリソース
        の解放やロックしていたファイルの解放を行う
        ⇒クリーンアップ

      ・SIGKILLのような強制終了では、クリーンアップはない
        
    ```

    ### pstree(プロセスの親子関係を表示)

    ```bash
    #本当にプロセスが親から作成されるのか確認してみる

    # <user_name>: 指定したユーザのプロセスを表示する
    
    # ※pstreeとlessが、bashから作成されている
    $ pstree user | less -SFX
    bash---su---bash-+-less
                     `-pstree
    bash

    systemd---(sd-pam)

    # 全体表示(-pを付けるとPIDが見れる)
    # systemd : システムの初期化プロセス
    # agetty: システムのコンソールにログインする為のプロセス
    # cron: 定期的に実行されるタスクを管理する
    # dbus-daemon: プロセス間通信を可能にするサービス
    # SessionLeader: 端末に対する制御等を行う
    # Relay(1214): プロセス間の通信を中継している
    # bash: システムと対話する用のシェル
    # su: 別ユーザに切り替えて使用しているので表示されている
    # bash: 切り替えた後のシェル
    # less: 実際に実行しているコマンド
  
    $ pstree | less -SFX

    systemd-+-2*[agetty]
           |-chronyd(269)---chronyd(273)
           |-cron(158)
           |-dbus-daemon(160)
           |-init-systemd(Ub(2)-+-SessionLeader(1208)---Relay(1214)(1209)---bash(1214)---su(1525)---bash(1549)-+-less(15225)
           |                    |                                                                              `-pstree(15224)
           |                    |-init(6)---{init}(7)
           |                    |-login(550)---bash(616)
           |                    `-{init-systemd(Ub}(8)
    ...skipping...
    ```

    ### top(実行中プロセスの情報を表示)

    ```text

    CPU使用率の高いプロセスが表示され、一定間隔で更新される

    top: 起動してからの時間や、ログインユーザの数など
    %TASKs: プロセスの数。ほとんど、Sleep状態になっている
    %CPU: CPUの状態。今回の場合、ほぼidle状態のようだ
    MiB Mem: メモリ領域
    MiB Swap:スワップ領域

    1行目だけ見たい場合は、uptimeコマンドが使える

    $ uptime
    12:17:54 up 2 days,  9:06,  1 user,  load average: 0.20, 0.05, 0.01

    ```

    ```bash
    $ top
    top - 12:11:34 up 2 days,  9:00,  1 user,  load average: 0.02, 0.04, 0.00
    Tasks:  40 total,   1 running,  39 sleeping,   0 stopped,   0 zombie
    %Cpu(s):  0.2 us,  0.2 sy,  0.0 ni, 99.6 id,  0.0 wa,  0.0 hi,  0.0 si,  0.0 st
    MiB Mem :   7788.1 total,   6771.2 free,    668.6 used,    348.3 buff/cache
    MiB Swap:   2048.0 total,   2028.4 free,     19.6 used.   6889.5 avail Mem

    USER      PR  NI    VIRT    RES    SHR S  %CPU  %MEM     TIME+ COMMAND                                                                              
    root      20   0  166160  13464   8348 S   1.3   0.2   1:49.05 systemd
    root      20   0   43260  37000  10244 S   0.7   0.5   0:52.07 python3
    user      20   0   13992   3912   3360 R   0.3   0.0   0:00.06 top                             
    root      20   0    2280   1260   1188 S   0.0   0.0   0:00.03 init-systemd(Ub
    ```

    ### プロセスの処理順について

    ```text
    ・psコマンドにおけるSTATがSのプロセスが複数あるとき、
      どの順番で実行されるのか、考える
    
    ・CPUは、たとえ1つのコアしかなかったとしても、
      複数のプロセスを高速で切り替えることで疑似的な
      並列処理を実現している
    
    ・複数のコアがある場合、真の並列処理が実現できる

    First Come First Serve
      ・先着順でプロセスを実行
      ・平均待ち時間が長くなる
    
    Shortest First Serve
      ・ジョブの処理時間が短い順に実行
      ・平均待ち時間を最小に抑えられる
    
    Prority Based Scheduling
      ・優先度が高い順に実行
      ・nice(大きいほど優先度が低い)などが影響する
    
    Round Robin
      ・CPUを割り当てる固定時間を設定する
      ・切り替えることでプロセスを1つのコアで同時実行
    ```

    ### プロセス名でkillする

    ```bash
    $ sleep 100 &
    [1] 146187

    # pgrep -lならプロセス名を表示可能
    $ pgrep sleep
    146187
    # 以下の様にしてもいいけれど
    $ kill 146187

    # -eで終了させたプロセスIDを表示可能
    # killと同じように別シグナルを送ることもできる
    
    # ※正規表現でkillするので、意図しないプロセスのkillに注意
    # 例としてssleepというコマンドが存在するなら
    # 下記の指定でkillされる。事前にpgrepで見てからの方が安全
    $ pkill sleep
    [1]+  Terminated              sleep 100
    ```

## Q9. ログアウト後もプロセスを継続したい場合(nohup,screen,tmux)が使えることを知っていますか?

??? success

    ### nohup

    ```text
    nohup
      ・SIGHUPを無視する
    
    nohup + &
      ・バックグラウンドで処理を実行
      ・端末の切断やログアウト時にもプロセスは中断されない
      ・PCがシャットダウンされるまで継続される

    ・切断中の出力は、カレントのnohup.outに保管される
    ・別ファイルで保存したい場合は、リダイレクトを使う

    使用場面
      ・sshの接続はいつの間にか切れていることがある
      ・そんな時、jobが途中で終了されていると悲しい
      ・nohup等を使えば、ログアウト後もjobを継続できる
    ```

    ```bash
    # nohup: ログアウトしても中断されていない
    $ nohup sleep 100 &
    $ exit
    # 再ログイン
    $  ps aux | grep sleep 
    user     83838  0.0  0.0   9420  1016 ?        S    14:39   0:00 sleep 100
    ```

    ### screen

    ```text
    ・1つのターミナルで複数のスクリーンを開き、
      切り替えることが可能になる
    
    ・実行画面をsessionとして残しておき、
      再ログイン後に、再度実行画面に戻ることができる
    
    ⇒ssh接続が切れても、プロセスは実行され続けるので
      再接続してから、結果を見ることができる
    ```

    ### よく使うscreen関連のコマンド

    | コマンド         | 効果                           |
    | ---------------- | ------------------------------ |
    | screen -S <name> | 新しいsessionを名前付きで作成  |
    | (Ctrl+a) + d     | sessionを一時的に離れる        |
    | screen -r <id>   | sessionに入る                  |
    | screen -ls       | sessionの一覧表示              |
    | (Ctrl+a) + S     | 画面の水平分割                 |
    | (Ctrl+a) + c     | 新しいウィンドウ作成           |
    | (Ctrl+a) + tab   | 画面移動                       |
    | (Ctrl+a) + \|    | 画面の垂直分割                 |
    | (Ctrl+a) + X     | 分割したスクリーンの消去       |
    | (Ctrl+a) + x     | 画面をロックする               |
    | (Ctrl+a) + [     | コピーモード(Spaceで開始/終了) |
    | (Ctrl+a) + ]     | ペースト                       |
    | echo $TERM       | 今sessionの中にいるかチェック  |
    | echo $STY        | 同上                           |
    | screen -X quit   | 現在のsessionを削除            |


    ```bash
    $ echo $TERM
    # sessionの中にいるなら、session.*となる
    screen.xterm-256color

    $ echo $STY
    # sessionの中にいるなら、表示される
    91511.hogehoge

    # 現在のsession以外を削除したい場合
    $ screen -S <session_name> -X quit
    ```

    ### tmux概要

    ```text
    ・Terminal Multiplexerの略

    terminal
      -> 複数のsession
        -> 複数のwindow
          -> 複数のpane

    session
      ・tmuxを起動すると生成される
      ・sessionごとに状態を維持可能
    
    window
      ・sessionがもつ複数のwindow
      ・screenの水平/垂直画面分割と同じ
    
    pane
      ・windowがもつ複数の区画
    ```

    ### よく使われるtmuxのキー操作

    ```text
    ・window操作の場合、
      最初にprefix-keyである、CTRL + bを入力する事が多い
    ```

    ### session関連

    ```bash
    # session起動
    $ tmux

    # 名前付きsessionの起動
    $ tmux new -s <session_name>

    # sessionの終了
    $ exit

    # sessionの一時的な中断
    # (Ctrl + b) + d

    # 中断していたsessionに戻る
    $ tmux a -t <session_name>

    # session一覧を表示
    # (Ctrl + b) + s : tmux上で
    $ tmux list-sessions

    # 指定したsessionをsession外から終了させる
    $ tmux kill-session -t <session_name>

    #
    ```

    ### window関連

    | Window関連キー | 意味                     |
    | -------------- | ------------------------ |
    | ?              | 各キーの説明表示         |
    | ctrl+z         | tmuxを一時中断           |
    | fg             | tmuxに復帰               |
    | c              | 新規window作成           |
    | w              | window選択               |
    | 数字           | 指定したwindowに移動     |
    | p,n            | 前/次のwindowに移動      |
    | l              | 以前のwindowに移動       |
    | '              | window番号を指定して移動 |
    | ,              | window名の変更           |
    | .              | window番号の変更         |
    | &              | 現在のwindow破棄         |
    | exit           | window削除               |

    ### pane関連

    | pane関連キー | 意味                         |
    | ------------ | ---------------------------- |
    | "            | pane垂直分割                 |
    | %            | pane水平分割                 |
    | o            | 次のpaneに移動               |
    | <arrow>      | pane間のfocusを移動          |
    | ;            | 以前のpaneに移動             |
    | x            | pane破棄(確認あり)           |
    | exit         | pane破棄(確認なし)           |
    | z            | pane最大化/復帰              |
    | q            | pane番号の表示               |
    | t            | 時計を表示                   |
    | space        | レイアウト変更               |
    | {            | paneの順序を前方向に入れ替え |
    | }            | paneの順序を後方向に入れ替え |
    | !            | paneのwindow化               |


    ### copy

    ```text
    ~/.tmux.confに
    set-window-option -g mode-keys viとして
    vimライクに操作することが一般的
    ```

    | コピー関連キー | 説明             |
    | -------------- | ---------------- |
    | [              | copyモードの開始 |
    | space          | copy選択開始     |
    | enter          | copy選択終了     |
    | esc            | 選択内容削除     |
    | h,j,k,l        | カーソル移動     |
    | ]              | paste            |
    | v              | 矩形モード       |
    | q              | copyモード終了   |
    | Ctrl+u         | scrollUp         |
    | Ctrl+d         | scrollDown       |
    | Ctrl+b         | pageUp           |
    | Ctrl+f         | pagedown         |

    ### その他
    | キー | 説明         |
    | ---- | ------------ |
    | :    | コマンド入力 |



## Q10 cron(ジョブ管理ツール)について知っていますか?

??? success
    ### cron概要

    ```text
    ・ジョブ管理ツール
    ・ジョブを予約して、指定日時/間隔にジョブを実行させる
    ・主に反復的なタスクを予約するのに使う
    ```

    ### crontab

    ```text
    ・実行したいコマンド/スクリプトを
      /var/spool/cron/crontabs/<user_name>配下に
      登録することで定期的な実行が可能となる

    ・最初にcrontab -eとすると、上記フォルダに、
    　ファイルが自動で作成される

    ・crondというデーモンは、上記フォルダを参照して、
      定期的にコマンドを実行する

    ```

    ### cronの記述方法

    ```bash
    $ crontab -e

    # 編集の仕方を聞かれるので、vim.basicを選ぶといい
    # vim-tinuはvimの簡易版

    # たとえば以下の様に記載する(*時2分にtouch)
    # 分,時,日,月,曜日,コマンド
    # 毎分/時/...に実行したい場合は*とする
    2 * * * * touch /tmp/test.txt
    ```

    ### allowとdeny

    ```text
    ・crontabはdefaultでroot/一般ユーザの両方が使える

    ・ユーザのcrontabの使用を制限したい場合、
      User名を、/etc/cron.allow, /etc/cron.denyに記述する
    
    ・cron.allowにユーザが登録されていると、
      ホワイトリストとして扱われる
      ⇒登録されていないユーザは使えない
    ```

    ```bash
    $ cat /etc/cron.allow

    # 以下の様に記載
    root
    user
    
    # allowリストに載っていないユーザでcrontabを行う
    $ su nezumi
    $ crontab -e
    You (nezumi) are not allowed to use this program (crontab)   
    See crontab(1) for more information
    ```

    ### crontab -eは危険?

    ```text
    ・crontab -rでcronの設定ファイルは削除される
    ・rとeは隣り合わせなので誤タップが起きる

    ⇒頻繁に編集するものではないので、
      復元できないという悲劇がおきたり......
    ```

    ```bash
    # 解決法1(手動バックアップ)
    $ crontab -l > crontab_backup_$(whoami)_$(date -I)

    # cronの設定を削除
    $ crontab -r

    # バックアップから復元
    $ sudo cp ./crontab_backup_user_2024-04-29 /var/spool/cron/crontabs/user

    # 解決法2 aliasに記載する
    # alias = コマンドの置き換え, 常に指定するoptionを選択

    # ~/.bashrcを開く
    # -iは削除時に常に確認する
    alias crontab='crontab -i'

    # 助かったってわけ
    $ crontab -r
    crontab: really delete user's crontab? (y/n) n
    ```


    ### cronとログ出力

    ```text
    ・Ubuntu22では、/var/log/cron.logへの
      cron実行履歴書き出しが無効化されている
    
    ・理由としては、機密保護とパフォーマンスの問題が
      上げられる
    ```

    ```bash
    # 履歴を記録したい場合
    $ sudo vim /etc/rsyslog.d/50-default.conf

    # 以下の行をコメントアウト
    # cron.* /var/log/cron.log

    # 再起動させて反映
    $ sudo systemctl restart rsyslog

    # cronジョブが行われた後で確認

    $ cat /var/log/cron.log
    Apr 29 22:53:01 YOUR_TERMINAL cron[158]: (user) RELOAD (crontabs/user)
    Apr 29 22:54:01 YOUR_TERMINAL CRON[193135]: (user) CMD (touch /tmp/test.txt)

    # logを保存する必要がなければdefaultに戻しておく

    ```

    ### anacron

    ```text
    ・定期的にジョブを実行するdaemon

    ・日付、月、曜日、週を指定する
      (※時間指定はできない。1日1回のみ実行される)
      (実際にはanacronが1時間ごとに
      /var/spool/anacron/cron.*を見て
      最終実行日時が今日の日付と異なっていれば実行する
      )
    
    ★指定した日時にPCの電源が落ちていると、
      起動後にジョブを実行するという特徴あり
      ( -> cronの場合は実行しない)
    
    ・設定ファイルは/etc/anacrontab
      rootユーザのみが設定可能
    
    ・ランダムにジョブの実行タイミングをずらすので
      負荷分散が可能
    ```

    ### 補足(run-parts)

    ```bash
    # 指定directory内の実行ファイルを一括で実行する
    $ ls -gG
    -rwxr--r-- 1 25  4月 29 23:25 test
    
    $ file test
    -test: Bourne-Again shell script, ASCII text executable

    # --reportは実行されたファイル名を表示
    $ run-parts --report ./
    .//test:
    success

    # 注意点
    # run-partsは.を含むファイルを実行できない
    # #!/bin/bashや#!/bin/shがないと以下のような
    # エラーを吐いて停止する
    run-parts: failed to exec .//test: Exec format error  
    ```

    ### (補足)cron関連の他ファイル

    ```text
    /etc/crontab
      ・システム全体に適用したいジョブを記載
      ・すべてroot権限で実行される

      ・defaultでも記載があるが、anacronがある場合は、
        そちらの設定が優先される(日単位以上の場合は)
      ⇒ない場合は、run-partsとcronを用いて
        /etc/cron.*内のファイルが一定間隔で実行されるように
        なっている。(たとえばhourlyなら1時間毎)
    
    /etc/cron.hourly, daily, weekly, monthly
      ・directory配下のfileは1時間/日/週/月毎に実行

    /etc/cron.d
      ・システム全体のcronとして任意のタイミングで実行したい
        そんな時に、スクリプトを置く
      ・anacronのファイルもここに置かれる
    ```

    ### anacron例

    ```bash
    # installされているか確認。
    # されていないならsudo install anacron
    $ apt list 2> /dev/null | grep anacron
    anacron/jammy,now 2.3-31ubuntu2 amd64 [インストール済み] 

    # 1 = ジョブの実行頻度。@monthly等の指定も可能
    # 5 = jobの開始までの遅延時間
    # cron.daily: タイムスタンプを記録するファイル
    $ cat /etc/anacrontab | grep daily
    1 5 cron.daily  run-parts --report /etc/cron.daily

    # /etc/cron.d/anacronを見ると以下の様にある
    # defaultでは7~23時の30分 + 遅延を加えた時間に
    # /var/spool/anacron/cron.*のtimestampを見るようだ
    30 7-23 * * *   root    [ -x /etc/init.d/anacron ] ...

    ```

    ### at, atq, atrm(1回のみの実行)

    ```text
    ・1回きりの実行ならatがよい

    ・atは特定の日時を指定する
    ・atqはatで予約している登録を表示
    ・atrm <job_id>でatで登録された処理を削除可能
    ```

    ```bash
    # install等
    $ apt list | grep ^at/
    at/jammy 3.2.5-1ubuntu1 amd64

    $ sudo apt install at

    # 日時を入力すると、コマンドの入力を促される
    # Ctrl + Dで終了できる
    $ at 0:22 2024-04-30
    warning: commands will be executed using /bin/sh
    at Tue Apr 30 00:22:00 2024
    at> echo "neko" > hoge.txt
    at> <EOT>
    job 1 at Tue Apr 30 00:22:00 2024

    # 指定時刻以降に
    $ cat hoge.txt
    neko

    # 時間のみの指定も可能(次に22:02になったときに実行)
    $ at 22:02

    # 現在時刻を基準とした指定
    $ at now +1minute

    # 実行予定のジョブ一覧(at -lも可)
    # userならuserが予約したジョブのみ
    # rootなら全員のジョブが表示される
    $ atq
    2       Tue Apr 30 00:24:00 2024 a toraneko

    # ジョブ削除
    $ atrm 2

    # 表示されなくなった
    $ atq
    ```

## Q11 wsl2ではできないことがあることを知っていますか?(VirtualBoxの初期設定についても話す)
??? success

    ### wsl2の場合(who,users,last,lastlog)

    ```text
    ※以下のユーザアクセスログ関連のコマンドは、
    　(2024年現在)wsl2では機能しない
    　https://github.com/microsoft/WSL/issues/573

    who
      ・ログイン中のユーザを表示する
      ・wコマンドでもユーザの情報を表示可能
      ・/var/run/utempを参照する
    
    users
      ・ログイン中のユーザを一覧表示
    
    last
      ・ユーザのログイン履歴を一覧表示
      ・/var/log/wtmpを参照する
    
    lastlog
      ・ユーザが最後にログインした情報を一覧表示
      ・/var/log/lastlogを参照
    ```

    ```bash
    # suによるログインではなく、
    # アカウントによるログインで区分けしているのだろうか?

    $ su - nezumi
    # lastlog
    $ lastlog | grep nezumi   
    nezumi **一度もログインしていません**

    # who(PSから起動した時間になる)
    $ who
    user pts/1        2024-04-30 07:10

    # users
    $ users
    user

    # last
    $ last | grep nezumi
    # 出力無し
    ```

    ### VirtualBoxの場合

    ```bash
    # nezumiとしてログイン(GUIで)
    # suによるログインの場合、whoはうまく表示されなかった
    $ who
    nezumi seat0  2024-04-30 13:11 (login screen)
    nezumi tty  2024-04-30 13:11 (tty2)

    $ users
    nezumi nezumi
    

    $ last
    nezumi tty2  tty2         Tue Apr 30 13:11 still logged in
    nezumi seat0 login screen Tue Apr 30 13:11 still logged in

    $ lastlog
    # 機能しなかった(/var/log/lastlogが空)
    # 以下のような形で代用できそう
    $ last | grep $(whoami) | head -1

    ```

    ### (補足)VirtualBOXのinstall

    ```text
    1 https://www.virtualbox.org/wiki/Download_Old_Builds
      から使いたいversionを選ぶ
      (最新versionでもいいけれど)
    
    2 https://www.ubuntulinux.jp/home
      からubuntuの.isoファイルをdownload
      (※たぶん、ミラーサーバからdownloadすることになるよ！)
      (勿論違うdistributionでもいい)

    3 .exeを起動して、VirtualBoxをinstall
    
    4 .isoファイルをあてて、
      memory容量や、core数、storage容量を決める
      (windowsで使う分も残しておくように)
    
    5 手動インストールを選択
      (keyboardレイアウトを変更したい場合は特に)
    
    6 起動

    7 Ubuntuをinstall
      (住んでいる地域やkeyboardレイアウトを選択)
    
    8 仮想ディスクは削除しておく
    ```

    ### Virtualboxの注意点

    ```text
    自動installを選んだ場合
      ・keyboard配列が異なる場合がある
      ・たとえば_が入力できないとか
    
    HOSTキーとかいうやつ
      ・Windowsでは右CTRL
      ・ファイル⇒環境設定⇒入力⇒仮想マシン
        ホストキーの組み合わせ⇒ショートカットから変更可能
    
    Terminalの起動
      ・CTRL + ALT + T
    ```

## Q12 journald(ログ管理ツール)について知っていますか?

??? success

    ### logger

    ```text
    ・任意のログを生成する。
    ・以下で検証したいときに使える
    ```

    ```bash
    $ logger "neko"
    ```

    ### サービスとデーモン

    ```text
    常駐プログラム
      ・常にメモリ上で展開されているプログラム
      ⇒つまり、常に動いている(もしくは動けるようにしている)

    サービス
      ・Windows系の常駐プログラム
      ・(起動が遅い時に、サービスを確認したりする)
    
    Daemon(デーモン)
      ・Unix系OSの常駐プログラム
      ・一般的に、PID1が親で、ttyが?であることが多い
      ・末尾にdがついていることが多い
      ・由来は守護神。昔神々は雑事を守護神に任せ、サボった。
      　我々も雑事は常駐プログラムに任せ、サボることにしよう
    ```

    ### syslog(昔のログ管理システム)

    ```text
    syslogの歴史
      1 もともとMailサーバ用のログアプリとして開発された
      2 これを便利だと感じた開発者たちが他のアプリでも
      　syslogを使ってログ情報を出力するようになったため、
        UNIX界隈の事実上の標準となった
    
    そもそもログ(log)とは?
      ・行動、状態、結果を記録する物
      ・「いつ、だれが、どこで、何をした」⇒行動
      ・「いつ、何が、どのような状態だった」⇒状態
      ・「何かをした結果、どうなった」⇒結果

      ・障害発生時や開発時、データ分析時などは
        ログが大きな手掛かりとなる
    
    syslogの機能1(ログ書き込み)
      1 Applicationがログ情報をファイルに保存しておきたい
      　場合、syslogへログ情報を受け渡す
      2 syslogは、各アプリ用のログファイルへ書き込む

      ※syslogがない時用に、個々のアプリは
        直接ファイルに出力することもできる
    
    syslogの機能(ログ収集)
      1 複数のサーバのログ情報がある
      2 Applicationはsyslogへログ情報を渡す
      3 syslogはログ情報をログサーバへ転送する
      4 ログサーバ内のsyslogが各アプリ用のlog-fileへ書き込む

    syslogとsyslogd
      ・syslogは上記の機能を体系づけたプロトコル
      ・syslogdはプロトコルをもとに実装されたデーモン
      ・実際に常駐しているのはsyslogd
    ```

    ### rsyslog(一応現役のログ管理ツール)

    ```text
    syslogの問題点
      ・UDP(user datagram protocol)で情報伝達を行っていた
        ため、ログ情報の紛失が生じていた
      
    rsyslog
      ・rocket-fast system for log processing
      (信頼性のrだと主張している記事もあるが、公式はこっち)
      (https://www.rsyslog.com/)

      ・TCP(Transmission Control Protocol)で接続を確立する
        ため、ログ情報の紛失が起こりにくい
      ・(※UDPによる接続もできるけど)

      ・SSL/TLSを使用したログ情報の暗号化が可能
    ```

    ### rsyslogの設定ファイルについて

    ```text
    ・/etc/rsyslog.conf
      ・設定ファイルについては 
        $IncludeConfig /etc/rsyslog.d/*.conf
        という記述が末尾に見られる
      
      ・設定ファイルの読み込み順番は数字を参考にしている
        たとえば20-*.confと、50-*.confなら、20が先。
        facilityとpriorityが同じ場合上書きされる
    
    ・/etc/rsyslog.d/*
      
      記法：
        [facility].[priority] <output>
      
      意味：
        [ログの種類].[ログのレベル] <出力先>
      
      例： 
        mail.* -/var/log/mail.log

      補足：
        ・ディレクトリの前の-は、同期をとらないことを意味する
        ・大量のログの場合、付けるとパフォがよくなる
        ・ただし、書き込み直後にシステムがクラッシュすると、
          情報が失われる可能性がある
        ・重要なメッセージの場合は、付けない。errorログも
        ・たいていの場合、/var/log配下に出力する
      
     *.emerg :omusrmsg:*
      ・emergレベルのメッセージは、全ユーザの
        コンソール画面に通知する
      
      ・omusrmsg:root,nezumiとすれば、ログイン中のルートと
        nezumiというユーザのコンソール画面のみに通知される
      
      ・omusrmsg
        (=output module for sending user messages
        https://github.com/rsyslog/rsyslog/blob/master/tools/omusrmsg.c)
      
    ```

    ### facility(defaultの設定)

    | 種類     | 有効 | 非同期 | 説明              |
    | -------- | ---- | ------ | ----------------- |
    | kern     | 〇   | 〇     | カーネル関連      |
    | auth     | 〇   | ✖      | 認証関連          |
    | authpriv | 〇   | ✖      | 認証関連(private) |
    | cron     | ✖    | ✖      | cron関連          |
    | mail     | 〇   | 〇     | errorは非同期     |
    | news     | -    | -      | news関連          |
    | uucp     | -    | -      | uucp関連          |
    | daemon   | ✖    | 〇     | daemon関連        |
    | user     | ✖    | 〇     | user関連          |
    | lpr      | ✖    | 〇     | プリンタ関連      |
    | syslog   | -    | -      | syslog関連        |
    | local    | -    | -      | 自由設定          |

    ### priority

    | 種類    | 説明           |
    | ------- | -------------- |
    | emerg   | 緊急           |
    | alert   | 即時対応必須   |
    | crit    | 重要           |
    | err     | 一般的なエラー |
    | warning | 一般的な警告   |
    | notice  | 一般的な通知   |
    | info    | 一般的な情報   |
    | debug   | デバッグ情報   |
    | none    | 出力しない     |


    ### journald(systemd環境で用いられるログツール)概要

    ```text
    systemd概要
      ・Linuxシステムを起動、運用するための中心的なツール
      ・様々なサービスやプロセスを管理する
      ・自前のログシステムであるjournalを搭載している
      ⇒syslogd, rsyslogdの起動は必要なくなる
    
    rsyslogはお役御免になったのか?
      1 journaldがシステム上のログを受け取る
      2 必要に応じてrsyslogへ転送する(defaultは転送しない)
    ```

    ### journaldはどのログを管理しているか

    ```text
    kernelからのmessage
      1 リングバッファに保存される
      2 journaldが、/dev/kmsg, /proc/kmsgという
        仮想ファイルを経由して読み取り
    
    Userからのmessage
      0 (例として、logger "hello"とする)
      1 /var/log/syslogに送られる(Ubuntuの場合)
      2 journaldが/var/log/syslogを読み取る

    ⇒　結果として、全てのログをjournaldで把握可能
    ```

    ### ログの消滅と保存

    ```text
    /etc/systemd/journald.conf
      ・journaldの設定ファイル
      ・Storageはログの保存方法を意味する

      ・persistentなら/var/log/journalに保存
      ・volatileなら/run/log/journalに保存。揮発性
      ・autoなら/var/log/journalの存在による
        なければ/run/log/journalに保存。揮発性
    
    /var/log/journal
      ・Ubuntu18,20,22ならinstall時に含まれる
      ・つまりdefaultの場合、journaldログは永続保存！
      (=> わざわざrsyslogに転送する必要はなさそうだ)
    
    SystemMaxUse, RuntimeMaxUse
      ・logが大きくなりすぎないように上限を記載するといい
      ・デフォルトはファイルシステムの10%
    ```

    ```bash
    # ForwardToSyslog(rsyslogに送るか)
    # MaxLevelSyslog(どのレベルの情報を送るか)
    # がoffになっている。そのためrsyslogへは転送されていない

    $ cat /etc/systemd/journald.conf |  \
      grep -E ForwardToSyslog\|MaxLevelSyslog

    #ForwardToSyslog=yes
    #MaxLevelSyslog=debug
    ```

    ### journalを使う

    ```bash
    # 全てのログを読む
    $ journalctl

    # 起動番号でフィルタ(-b)
    # 現在の起動が0で、-1が前回, -2が...
    $ journalctl --list-boots

    # 前回起動時のログを見る
    $ journalctl -b -1

    # 時間をもとにしたフィルタ
    # (今日の0~10時までのログ)
    $ journalctl --since "today" --until "10:00"

    # 直近のメッセージを表示(更新があると追加)
    $ journalctl -f

    # 現在以降のメッセージを表示
    $ journalctl --since "now" -f
    # 別ttyで
    $ logger "neko"
    # 表示された
    5月 01 10:39:41 PC toraneko[199971]: neko

    # その他にも以下のoptionがある
    # -r: 逆順
    # -k; カーネルに関するmessageのみを表示
    # -u: 指定ユニット(サービス)に対するメッセージ表示
    # -n <digit>: 新しい方からdigit件取得
    # -e:初期のカーソル位置が最新行
    # -x:詳細情報(-bとは併用できない)
    # -b <-digit>: digit回前の起動時のログ取得
    # _UID: 指定したユーザIDのログを出力
    # -o verbose: ログの全情報を出力する

    $ journalctl --since "2024-05-01 10:00:00" \
      --until "2024-05-01 11:00:00" \
      -u cron

    $ journalctl --since "2024-01-01" \
      --until "2024-01-01 23:59:59" \
      -rn 100 -u systemd-timesyncd

    # UID=1001の全行動を、最も詳しいレベルで出力する
    $ journalctl _UID=1001 -o verbose
    ```

    ### journalの削除(必要に応じてbackupを取ること)

    ```text
    ・rmを使って、/var/log/journal/*を削除するのは
      一般的ではない
    ```

    ```bash
    # 100MB以下になるまでjournalファイルを削除する
    $ sudo journalctl --vacuum-size=100M

    # 1週間前より前のjournalファイルを削除する
    $ sudo journalctl --vacuum-time=1weeks

    # journalファイルの容量が確認できる
    $ journalctl --disk-usage
    Archived and active journals take up 72.0M in the file system.
    ```

## Q13 logrotate(ログ世代管理ツール)について知っていますか?

??? success
    ### logrotate


    ```text
    ・ログが増え続けるとディスクが圧迫されるため、
      一定量や一定期間でログを削除/上書きすることが一般的
    
    ・全体設定は/etc/logrotate.confに記載され
    ・個々の設定は/etc/logrotate.d/配下の*.confに記載される
      logrotate.d/*に設定がある場合は、個々の設定を優先
    ```

    ### cronとlogrotate

    ```text
    ・logrotateはdaemonではなく、cronによって実行される
    ・そのため動作していることを確認すべき
    ```

    ```bash
    $ sudo systemctl status cron | grep active
    Active: active (running) since Tue 2024-04-30 07:10:05 JST; 1 day 4h ago
    ```

    ### logrotate.confと主要なlogrotateの記法

    ```text
    daily/weekly/monthly/yearly
      ・ローテーション周期(rotate)
      ・デフォルトはweekly
    
    rotate N
      ・管理する世代
      ・デフォルトはrotate 4
      ・指定しないとrotate 0になり、古いログが全削除される
      ・※0にしても、現在のログファイルは消えない
          rotate 1なら、*.logと、*.log.1が残る
    
    compress/nocompress/delaycompress
      ・圧縮するか否か
      ・デフォルトはnocompress
      ・1世代目は圧縮されない
      ・圧縮する場合は、compressで(gzipファイルを作成)
    
    ifempty/notifempty
      ・空ファイルでもrotateするか
      ・デフォルトはnotifempty(しない)
      ・する場合は、ifemptyに
      ・notifemptyの場合、古いデータが残り続けることがある
    
    missingok
      ・ログファイルが存在しなくてもエラーを出さず処理続行
    
    create/nocreate
      ・rotate時に、logファイルを古いlogファイルにmv
      ・同名の空ファイルを作る
      ・permissionを設定しない場合、元fileの属性を引き継ぐ
      ・(つまり、*.log -> *.log.1となり、新しい*.logを作成)
      ・nocreateなら行わない
    
    maxage N
      ・N日より前のログを削除
    
    size/maxsize/minsize N
      ・基本的にNバイトを超えたらrotate
      ・size : rotate期間を無視
      ・maxsize : rotate期間中でもrotate
      ・minsize : rotate期間中ならrotateしない
      ・k,M,Gで単位を指定可能
    
    oldfir [dir]/mail [add]
      ・古いログを転送
      ・olddirなら古いログを指定dirに転送
      ・mailなら古いログを指定mail-addressに転送
    
    dateext
      ・古いログファイルを更新する際、suffixを日付にする

    ~ endscript
      ・endscript -> scriptを終了
      ・prerotate -> rotate前にscriptを実行する
      ・postrotate -> rotate後にscriptを実行する
      ・preremove -> 削除前に実行
      ・sharedscripts -> 同じlogファイルに関するscriptは
                         rotate前/後に一度だけ実行される

    copytruncate
      ・createの場合、logファイルをrenameしてから
        同名の空ファイルを作る
      ⇒daemonでfileを開いていた場合、
        *.log.1ファイルが開いており、
        *.logファイルが閉じている状態になることがある。
      
      copytruncateの場合
        1 *.logをcopyしたものを、*.log.1とする
        2 *.logの中身を削除する
        ⇒*.log.1を閉じて、*.logを開き直す必要はなくなる

      
        
    ```

    ### logrotate.dの例

    ```bash
    # /etc/logrotate.d/apt

    # 12カ月保持されるようだ
    # 空でもエラーを出さないようにしている。
    /var/log/apt/term.log {
      rotate 12
      monthly
      compress
      missingok
      notifempty
    }

    /var/log/apt/history.log {
      rotate 12
      monthly
      compress
      missingok
      notifempty
    }

    # 実際に上記のファイルを見てみる
    $ ls /var/log/apt/* | grep history
    /var/log/apt/history.log
    /var/log/apt/history.log.1.gz
    /var/log/apt/history.log.2.gz
    /var/log/apt/history.log.3.gz
    /var/log/apt/history.log.4.gz
    /var/log/apt/history.log.5.gz
    /var/log/apt/history.log.6.gz

    # 1か月以内にinstallしていたatコマンドについてみる
    # ※journalctlでもわかるだろうけど

    $ cat /var/log/apt/history.log | \
      grep "apt install at" -A 3 -B 1
    
    Start-Date: 2024-04-30  00:19:28
    Commandline: apt install at
    Requested-By: user (1000)
    Install: at:amd64 (3.2.5-1ubuntu1)
    End-Date: 2024-04-30  00:19:29
    ```

    ### journald vs logrotate

    ```text
    journald
      ・binary形式でログを保存
      ・journaldが収集したログをjournalctlを使用して見る
      ・journalctlには強力なフィルタリング機能がある
    
    logrotate
      ・/var/log/*配下にログを保存するのはそれぞれのアプリ
      ・logrotateはappやdaemon単位で、ログを世代管理する
      ・そのため、テキスト形式
    ```

    ### logrotateを試してみる

    ```text
    ・次のrotateまで待つことは面倒なので、-fを使う
    ・-fは強制的にrotateする(logファイルは事前に閉じること)
    ```

    ```bash
    # rotate用のtestファイルを作成
    $ mkdir /tmp/rotate
    $ touch /tmp/rotate/test.log
    $ sudo chmod 755 /tmp/rotate

    # 設定ファイルを作成
    $ sudo vim /etc/logrotate.d/test.conf

    /tmp/rotate/test.log {
        daily
        maxsize 1M
        create 660 root root
        rotate 2
    }

    # -f で rotate
    $ sudo logrotate -f /etc/logrotate.d/test.conf

    $ ls /tmp/rotate
    test.log  test.log.1

    # 2回目のrotate
    $ sudo logrotate -f /etc/logrotate.d/test.conf
    $ ls /tmp/rotate
    test.log  test.log.1  test.log.2

    # 3回目のrotate (2世代までになっていることが確認できた)
    $ sudo logrotate -f /etc/logrotate.d/test.conf
    test.log  test.log.1  test.log.2
    ```

## Q14 Linuxの標準的なDirectory構成について知っていますか?

??? success
    ### man hier

    ```text
    ・ファイルシステム階層を説明してくれる、マニュアル

    ファイルシステム
      ・ディスク等のストレージ(外部記憶装置)に保存されている
        データをファイルとして管理するシステムのこと
    ```

    ### FHS(FileSystem Hierarchy Standard)
    
    ```text
    ・Linuxファイルシステムの標準仕様を指す

    ・Linuxのファイルシステムの構成がdistribution独自のもの
      にならないのは、FHSという仕様に基づいて構築されるから

    ・第三者のソフトウェアが上手く動作するのも、
      FHSのおかげといえる

    ・木構造
    ```

    ### Partition

    ```text
    /dev/sda
      ・Linuxでは、storageをデバイスファイルとして管理する
    
    /dev/sda1 , /dev/sda2, ...
      ・実際にOSからストレージを利用する際は、
        partition(パーティション)という論理的な区画に分割
        して使用している
    
    mount
      ・partitionの中にファイルシステムを作って、
        そのファイルシステムをマウント(利用可能な状態に)する
      
      ・OS等のソフトウェアは、ファイルシステムを通じて
        ストレージのデータを利用している
    ```

    ### lsblk

    ```text
    ・ディスクが何本ささっているのか
    ・パーティションがどのように分割されているのか
    ・マウント先のファイルは何なのか
    
    ⇒こんな事を調べるときにlsblkを使う
    ```

    ```bash
    # Virtualbox(Ubuntu)のsda
    # snap用のfileであるloopは鬱陶しいので除外する

    $ lsblk | grep -v "\s/snap/"
    NAME   MAJ:MIN RM   SIZE RO TYPE MOUNTPOINTS
    sda      8:0    0    25G  0 disk 
    ├─sda1   8:1    0     1M  0 part 
    ├─sda2   8:2    0   513M  0 part /boot/efi
    └─sda3   8:3    0  24.5G  0 part /snap
                                    /var/snap/firefox/common/host-hunspell
                                    /
    sr0     11:0    1  1024M  0 rom  
    ```

    ### fdiskで現在のパーティションの状態をチェックする

    ```bash
    # VirtualBoxの例(EFIシステムを利用している = UEFI)
    # UEFIとBIOSについては後述
    
    $ sudo fdisk -l /dev/sda
    ディスク /dev/sda: 25 GiB, 26843545600 バイト, 52428800 セクタ
    Disk model: VBOX HARDDISK   
    単位: セクタ (1 * 512 = 512 バイト)
    セクタサイズ (論理 / 物理): 512 バイト / 512 バイト
    I/O サイズ (最小 / 推奨): 512 バイト / 512 バイト
    ディスクラベルのタイプ: gpt
    ディスク識別子: HOGEHOGE-HOGE-HOGE-HOGE-HOGEHOGEHOGE

    デバイス   開始位置 最後から   セクタ サイズ タイプ
    /dev/sda1      2048     4095     2048     1M BIOS 起動
    /dev/sda2      4096  1054719  1050624   513M EFI システム
    /dev/sda3   1054720 52426751 51372032  24.5G Linux ファイルシステム

    ```


    ### 各ディレクトリについて

    | ディレクトリ | 端的に言うと                        |
    | ------------ | ----------------------------------- |
    | /            | 頂点                                |
    | /bin         | 非常時の利用コマンド                |
    | /sbin        | システム管理,boot時に必要なファイル |
    | /boot        | boot時に必要なファイル              |
    | /dev         | デバイスファイル                    |
    | /home        | ホームディレクトリ                  |
    | /root        | ルートのホームディレクトリ          |
    | /lib         | 共有ライブラリ                      |
    | /media       | 取り外し可能メディア                |
    | /mnt         | 一時的にmountしたファイルシステム   |
    | /opt         | アドオン                            |
    | /run         | 実行中のシステムデータ              |
    | /tmp         | 一時ファイル                        |
    | /proc        | 実行中のプロセスなど                |
    | /usr         | 読み取り可能の共通プログラム        |
    | /var         | サイズ可変のファイル                |


    ```text
    /
      ・Root
      ・木構造の頂点に位置する
      ・ルートディレクトリを含むファイルシステムを
        ルートファイルシステムと呼ぶ

    /bin
      ・シングルユーザモードでも利用できるコマンドを置く
      ・つまり、基本的かつ非常時に利用するコマンドを配置
      ・サブディレクトリ不可
    
    /sbin
      ・システム管理やブートローダに関する必須コマンドの
        バイナリ（実行ファイル）が配置されている
      ・サブディレクトリ不可
    
    /boot
      ・systemの起動に必要なファイル
      ・vmlinuz(Linuxカーネル)
    
    /dev
      ・デバイスファイルが格納されている
      ・デバイスの入出力はこのファイルを介して行われる
      ・ls -lで、ブロックデバイスかキャラクタデバイスか
        判別可能(先頭がb or c)
      ・たとえば、ターミナルは1文字ずつデータ転送を行い、
        ストレージはブロック単位でデータ転送を行うという事が
        以下から分かる
      
    $ cat <(ls -l /dev/console) <(ls -l /dev/sda)
    crw--w---- 1 root tty 5, 1  5月  2 09:01 /dev/console
    brw-rw---- 1 root disk 8, 0  5月  2 09:01 /dev/sda

    /etc
      ・システム全体の設定ファイル(.confや.dとかね)を保存
      
      ・実行可能なバイナリファイルはおかれない。
        サブディレクトリにファイルを格納するのが一般的
    
    /home
      ・各ユーザのホームディレクトリが置かれる
      ・個々のユーザが自由にファイルを保存可能
      ・ユーザごとのアプリ設定は、隠しファイルに保存される
        (たとえば.bashrcとか)
      
    /lib
      ・システムの起動時に必要な共有ライブラリを保存
      ・ルートファイルシステムでコマンドを実行するのに
        必要な共有ライブラリも保存
      ・各種コマンド実行時に読み込まれる
    
    /media
      ・CD/DVDディスクやUSBドライブなどの
        取り外し可能なメディアのマウントポイントが置かれる
    
    /mnt
      ・一時的にファイルシステムをマウントするポイントになる
      ・wslを使う場合は、windowsのCドライブが/mnt配下に
      　マウントされており、相互アクセスが可能
    
    /opt
      ・特定のアプリやパッケージのアドオン等を置く
      ・パッケージ管理システムでは管理されない
    
    /root
      ・rootユーザのホームディレクトリ
    
    /run
      ・実行中のシステムのデータなどを保存する
      ・揮発性でshutdown時に消える

    /tmp
      ・一時データや一時データの置き場所
      ・一般ユーザは基本的にアクセス可能で
        勝手にファイルを作成できるので、
        test -e /tmp/<hoge>のような指定を行うべきではない
    
    /proc
      ・実行中のプロセスや、カーネルの情報が格納される
      ・実際にはファイルシステム上ではなく、
        メモリ上に作成される疑似ファイルで、
        CPU,デバイス,メモリなどのシステム情報が格納される
    
    /usr
      ・読み取り専用の共通プログラムが配置される
      ・一般ユーザの実行コマンドは/usr/bin
      ・システム管理用のコマンドは、/usr/sbin
      ・ライブラリについては、/usr/lib (pythonとか)
      ・ローカルで管理するファイルは、/usr/local
        自作スクリプトは、/usr/local/binに置く
      ・マニュアル(man)等の特定のOSの全ての環境で共通の
        変更する恐れのないファイルは/usr/share
    
    /var
      ・systemが起動している最中に書き込みを行うファイル
      ・サイズが可変するファイルを保存する
      ・ログについては、/var/log
      ・キャッシュについては、/var/cache
      ・ライブラリについては、/var/lib
      ・ローカルで管理するファイルについては、/var/local
      ・一時的にデータを保管する領域については、/var/spool
    ```

    ```text
    シングルユーザモード
      ・rootのみがログインして操作できるモードで、
        メンテナンスや障害時、管理上重要な操作を行う際に
        利用される。（ネットワークを介したアクセスは不可）
      
    共有ライブラリ
      ・複数のプログラム間で共有されて使われている
      　プログラム実行時に読み込まれるライブラリのこと

      ・lib.*.so.*というファイル名をしている

      ・lddコマンドで各コマンドやプログラムが
        どの共有ライブラリを読み込むのか調べることが可能
        (※信頼できない実行ファイルに対して
        　 lddコマンドを使ってはダメ
        　 依存関係を調べる最中に、
           ファイルが実行される可能性あり)
      
      $ ldd /usr/bin/ls
        linux-vdso.so.1 (0x00007fffb74e4000)
        libselinux.so.1 => /lib/x86_64-linux-gnu/libselinux.so.1 (0x00007fe7e5f57000)
        libc.so.6 => /lib/x86_64-linux-gnu/libc.so.6 (0x00007fe7e5d2e000)
        libpcre2-8.so.0 => /lib/x86_64-linux-gnu/libpcre2-8.so.0 (0x00007fe7e5c97000)
        /lib64/ld-linux-x86-64.so.2 (0x00007fe7e5fb4000)

      # 代替手段
      $ odjdump -p /usr/bin/ls | grep NEEDED
        NEEDED               libselinux.so.1
        NEEDED               libc.so.6
    ```

## Q15 Linuxの起動プロセスの流れについて知っていますか?

??? success

    ### 自分のPCがBIOSかUEFIか確かめる

    ```text
    ・そもそもVirtualBoxの場合
      設定⇒システム⇒EFIを有効化に✓がついているか
      確認すればいいだけの話だが......

    ・✓：UEFI
    ・✖：BIOS

    ※windowsの場合、msinfo32コマンドが使えるね
    ```

    ```bash
    # BIOSの場合
    $ ls /sys/firmware/efi
    ls: '/sys/firmware/efi'にアクセスできません
        そのようなファイルやディレクトリはありません
    
    $ efibootmgr
    EFI variables are not supported on this system.
    ```

    ```bash
    # UEFIの場合
    $ ls /sys/firmware/efi
    config_table  fw_platform_size  
    mok-variables  runtime-map
    efivars       fw_vendor         
    runtime        systab

    $ efibootmgr
    BootCurrent: 0002
    Timeout: 0 seconds
    BootOrder: 0004,0000,0001,0002,0003
    Boot0000* UiApp
    Boot0001* UEFI VBOX CD-ROM VB2-01700376 
    Boot0002* UEFI VBOX HARDDISK VB971aaef7-e7aa9ac9 
    Boot0003  EFI Internal Shell
    Boot0004* ubuntu

    ```

    ### BIOSとUEFI

    ```text
    BIOS
      ・Basic Input/Output System
    
    UEFI
      ・Unified Extensible Firmware Interface
    
    共通点
      ・ハードウェア（マザーボード）の
      　ROMに組み込まれている、ファームウェア
    
    相違点
      ・起動方法(MBR/GPT)
      ・大容量ハードに対応しているか(2TB / 8ZiB)
      ・セキュリティの追加（遠隔診断や起動前の検査）
      ・インターフェースの向上(起動画面のGUI化)
      ・ネットワーク機能の追加
      ・起動スピードの向上
      ・セキュアブート(ない/ ある)

    ファームウェア
      ・ハードウェアを制御するためのソフトウェア

    マザーボード
      ・もっとも主要な電子回路基板
    ```

    ### UEFIの起動方法

    ```text
    1  PCの電源を入れる

    --- UEFI ----

    2  マザーボード上のUEFIファームウェアが起動

    3  UEFIのコード自体に問題がないかチェック
      
    4  ハードウェアを検出、検証(POST: power-on self-test)
       ※起動に必要なハードウェアを検出できない場合
         致命的なエラーとして処理し、エラーメッセージを表示
    
    5  起動に必要なハードウェアを初期化
       システムに接続して、使用可能な状態にする

    6  ファームウェアはGUID Partition Tableをスキャンして、
       EFIシステムパーティション(ESP)を見つける

    7  NVRAM内のブートエントリに設定された優先順位に従い、
       ESP内のブートローダを発見、起動

    8 ESP内のブートローダをメモリにロード

    9 ブートローダ(GRUB2)が、カーネル(vmlinuz)と
      initramfsをメモリにロードする

    --- Kernel ---

    10 カーネルが起動される

    11 カーネルが初期化処理を行う

    12 カーネルによりinitramfsが仮のルートとしてmountされる
       (本物のルートファイルシステムをマウントするために
       必要なドライバやスクリプトにアクセスできるように)

    13 initramfsのinitからsystemdを起動する

    14 ディスク内のルートファイルシステムをマウント

    15 本物のルートファイルシステムに切り替え

    --- init ---

    16 /sbin/init(PID:1)を実行し、起動シーケンスを開始

    17 default.targetに記述されている依存関係を満たすように
       Unitを立ち上げる(daemon等も起動)

    18 ログインプロンプト等からログイン
    ```

    ### BIOSの起動プロセス(相違点だけ)

    ```text
    1 PCの電源を入れる
    2 BIOSを起動
    3 POSTでハードウェアの検証、初期化
    4 ブートドライブを探す
    5 先頭に格納されているMBR(Master Boot Record)を見つける
    6 MBR内のブートローダを起動
    7 ブートローダがkernelとinitramfsをロードしてからは同じ

    ※MBRは、サイズが小さいため、ブートローダ全てを含まない
    　実際には、以下のような手順を踏む
      1 1次ブートローダが、2次ブートローダをメモリにロード  
        (2次ブートローダはOSを起動するプログラムを含む)
      2 2次ブートローダが、OSの初期化プログラムをロード
      3 実行
      4 制御をkernelへ移行
    ```

    ### まとめ

    ```text
    電源ON ->
    UEFI起動 -> 
    POST -> 
    GPT -> 
    ESP -> 
    boot entry -> 
    boot loader-> 
    kernel -> 
    initramfs -> 
    (initramfs内の)systemd -> 
    ファイルシステム切り替え -> 
    /sbin/init-> 
    unit起動　-> 
    ログイン可能状態に
    ```

## Q16 GPT, ESP, ブートエントリ/ローダ, vmlinuz, initramfsという言葉の意味を理解できましたか?

??? success
    ### ブートエントリとefibootmgr

    ```text
    ・前項のとおり、ブートエントリはブートローダの
      読み込み順を指定する
    
    ・NVRAMは、不揮発性のランダムメモリ
    　つまり、この読み込み順は保存されている

    ・ブートエントリの確認や、変更はefibootmgrを通して行う
    ```

    ```bash
    # Bootローダの順番は、0001->0002->0003となる
    $ efibootmgr -o 0001,0002,0003

    # Bootエントリを追加する
    $ efibootmgr -c \
      -L "<displayname>" \
      -d "<your_disk>" \
      -l "<your_boot_loader>"
    ```

    ### ブートローダ

    ```text
    ブートローダ
      ・カーネルをメモリにロードするためのプログラム
    
    LILO(Linux Loader)
      ・古くからLinuxで使われてきたブートローダ
      ・ファイルシステムを認識しない
      ・カーネルの位置はストレージの物理的な位置情報で判断
    
    GRUB Legacy(GRand Unified BootLoader)
      ・カーネルの位置はファイルパスで判断可能
      ・BIOSの場合、こちらを用いる
      ・MBRの容量上、2つのステージに分かれており、
        Stage1はStage2をロードするために存在する。
    
    GRUB2
      ・Stageは分けない
      ・動的に読み込まれる複数のモジュールで構成
      ・ブートエントリを見る限り、自分の環境では、ubuntuの
      　ブートローダとして、下記が呼び出されているようだ
        /boot/efi/EFI/ubuntu/shimx64.efi
      
      ・shimx64.efiはセキュアブートをサポートするための物で
        Linuxカーネルやinitramfsをロードするために使われる
    ```

    ### セキュアブート

    ```text
    ・検証されたブートローダやカーネル、EFIアプリのみを
      起動するようにする仕組み
    
    ・事前に署名されていない、カスタマイズされたOSを起動する
      際は、セキュアブートをOFFにする必要がある
    
    ・VirtualBoxなら、設定から適用可能
      (v7.0.0でバグがあるので注意)

    ・以下の様にし、5バイト目が1ならSecureBoot有効
    $ cat /sys/firmware/efi/efivars/SecureBoot* | od
    ```

    ### GUID Partition Table

    ```text
    ・Master Boot Recordの代替として作られたもの

    ・外部記憶装置のパーティションを管理するために使う

    ・従来のMBRが2TiBまでの領域しか管理できないのに対し、
      8ZiBまでの領域を管理可能
    
    ・保護用MBR -> GPTヘッダ->パーティション情報
    　の順で配置されており、
      最初のセクションはMBRとの互換性のために存在
      次のセクションでPartition Tableの位置情報を格納する
      (⇒そのため、その領域のブートローダを使える)
    
    ・次に、パーティションのうち、どこをブート対象にするか
    　という話なのだが、UEFIではディスク上の特定の領域(ESP)
      の中身をブート対象にすると予め決めている

    ・たとえば以下の場合、ディスク/dev/sdaのESPは2なので
      /dev/sda2がブート対象となる
    
    ・ここで、ブート対象の中には、複数のブートローダが存在
      する可能性がある。そこでエントリを見て、ブート対象を
      特定する必要があるわけだ

    $ sudo parted /dev/sda print
    モデル: ATA VBOX HARDDISK (scsi)
    ディスク /dev/sda: 26.8GB
    セクタサイズ (論理/物理): 512B/512B
    パーティションテーブル: gpt
    ディスクフラグ: 

    番号  開始    終了    サイズ  ファイルシステム  名前                  フラグ
    1    1049kB  2097kB  1049kB                                          bios_grub
    2    2097kB  540MB   538MB   fat32             EFI System Partition  boot, esp
    3    540MB   26.8GB  26.3GB  ext4
    ```

    ### EFI system partition

    ```text
    ・EFI(=Extensible Firmware Interface)
    
    ・partedコマンドでわかる通り、FAT形式のパーティション
    ・ブートローダを含んでいる

    ・partedコマンドでは、boot, espフラグが相当する
    $ sudo parted /dev/sda print | grep fat32
    2    2097kB  540MB   538MB   fat32             EFI System Partition  boot, esp

    ・gdiskコマンドではEF00コードが、EFIパーティション
    $ sudo gdisk -l /dev/sda | grep EF00
    2  4096 1054719  513.0 MiB  EF00  EFI System Partition

    
    FAT32
      ・FATはファイルの位置情報を記録する領域
      ・Windowsでいうと95以降でサポートされているFAT
    ```

    ### vmlinuz

    ```text
    ・Linux(カーネル)は、/bootに、vmlinuzというファイル名で
      保存される
    ```

    ```bash
    ls -gG /boot/vmlinuz* --time-style="+"
    lrwxrwxrwx 1       24  /boot/vmlinuz -> vmlinuz-6.5.0-28-generic
    -rw------- 1 14284040  /boot/vmlinuz-6.5.0-28-generic
    -rw-r--r-- 1 14271880  /boot/vmlinuz-6.5.0-9-generic
    lrwxrwxrwx 1       23  /boot/vmlinuz.old -> vmlinuz-6.5.0-9-generic
    ```

    !!! info

        ### シンボリックリンクとバージョン管理

        ```text
        ・たとえば、vmlinuzのversionを、6.5.0-9に戻したく
          なった場合のことを考える
        
        ・特定のversionをそのまま呼び出していた場合、
          呼び出し先を変更する必要がある
        
        ・上記の例の場合、シンボリックリンクを介して
          呼び出しているので、リンク先を変えるだけでいい
          (ln -fs: sだけだとファイルが存在するとなるため)
          (参照先がdの場合は、-nオプションも必要)
          (-vも付けると、リンク先を表示してくれるのでよい)
        ```

    ```bash
    # kernelのvを見る
    $ uname -r
    6.5.0-28-generic

    # fileの種類を見る
    $ sudo file /boot/vmlinuz-6.5.0-28-generic 
    /boot/vmlinuz-6.5.0-28-generic: 
      Linux kernel x86 boot executable bzImage, 
      version 6.5.0-28-generic (buildd@lcy02-amd64-001) 
      #29-Ubuntu SMP PREEMPT_DYNAMIC Thu Mar 28 23:46:48 
      UTC 2024, RO-rootFS, swap_dev 0XD, Normal VGA
    
    # bzImage形式：つまり圧縮されていることが分かる
    ```

    ### 補足(kernelとkernel-modules)

    ```text
    ・圧縮されているにしても、vmlinuzの大きさは14M
    ・これは、ハードウェアにアクセスし操作するためのドライバ
      を、カーネルモジュールとして切り出しているからである
    
    カーネルモジュールの利点
      ・カーネルのサイズを小さくできる
      ・使う時だけロード、不要になったらアンロードできる
      ・ハードウェア構成が変わってもカーネルの再構築が
        必要ないため、停止時間が生じない
    ```

    ```bash
    # 現在読み込まれているカーネルモジュール一覧を表示
    $ lsmod

    # 該当のversionのカーネルモジュールのバイナリが存在する
    $ /lib/modules/$(uname -r)/kernel

    # モジュール構成ファイル
    $ /lib/modules/$(uname -r)/modules.*

    # モジュールの依存関係を確認する
    $ modprobe -D isofs
    insmod /lib/modules/6.5.0-28-generic/kernel/fs/isofs/isofs.ko.zst 

    # モジュールの格納場所を確認する
    # ※tr -sは連続文字をまとめるためのコマンド
    $ modinfo isofs | grep filename | tr -s "[:space:]"
    filename: /lib/modules/6.5.0-28-generic/kernel/fs/isofs/isofs.ko.zst
    ```

    ### initramfs

    ```text
    前提
      1 カーネルがブートローダに起動される
      2 ルートファイルシステムをmountしたい

      2における問題点
        ・ルートファイルシステムをマウントするためには、
          ディスクのドライバをカーネルに組み込む必要がある
        
        ・カーネルの肥大化は本意ではない
      
      解決策
        ・カーネルでは小さなルートファイルシステム用の
        　ドライバやスクリプトだけを用意
        
        ・実際のルートのマウントに必要なドライバなどは
        　小さなルートファイルシステムの中に含んでおく
    
    initramfs(RAM上の初期ファイルシステム)
      ・ミニルートと呼ばれる
      ・メモリ上に展開可能な、極小のルートファイルシステム
      ・cpio形式でアーカイブされたうえで、gzipで圧縮済み
      ・実際のルートファイルシステムにアクセスするために
        必要なモジュールを提供する

    ※initramfsは本物のルートがマウントされ、
      PID=1のプロセスが起動し始めると、RAMから削除される

    ※カーネルモジュールの中でも、起動に必要な一部分が
      initramfsの中に含まれていると考えてよさそうだ
    ```

    ### 補足(default.targetについて)

