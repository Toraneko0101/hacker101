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
      2 メモリ上でCPUがプログラムを実行される、
      この時、メモリ上で実行状態にあるプログラム = プロセス
    
    ・プロセスは既に存在しているプロセスから作成される
      例) lsコマンドの実行
          shell(親プロセス) -> ls(子プロセス)
    
    ・プロセスはプロセスIDという、システム全体で
      一意のIDを持つ。これはプロセス終了まで変化しない
    
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

    ### STATの意味合いについて

    | 項目 | 意味                           |
    | ---- | ------------------------------ |
    | R    | 実行可能                       |
    | S    | 割り込み可能。待ち状態         |
    | D    | ディスクのI/O待ち状態          |
    | Z    | ゾンビプロセス                 |
    | +    | フォアグラウンドにあるプロセス |
    | s    | セッションリーダー             |
    | <    | 優先度が高い                   |
    | N    | 優先度が低い                   |
    | l    | マルチスレッドのプロセス       |

    ### ジョブ

    ```text
    ・シェルから見た処理単位
    ・コマンドラインの1行が、1つのジョブに相当
    ```

    ### 
    
    ### cron概要

    ```text
    ・ジョブ管理ツール
    ・cronジョブを予約すると、指定した日時/間隔で
      ジョブが実行される
    ・主に反復的なタスクを予約するのに使う

    ・crontabというコマンドで
    ```