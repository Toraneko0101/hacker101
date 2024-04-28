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
      
      3 cacheはsudo apt-get cleanで消すことが可能
    ```