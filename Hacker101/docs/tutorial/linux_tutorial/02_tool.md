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