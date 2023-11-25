# Docker チートシート

**このチートシートを改善したいですか？ [Contributing](#contributing) セクションを参照してください！**

## 目次

* [Dockerの利点](#why-docker)
* [前提条件](#prerequisites)
* [インストール](#installation)
* [コンテナ](#containers)
* [イメージ](#images)
* [ネットワーク](#networks)
* [レジストリとリポジトリ](#registry--repository)
* [Dockerfile](#dockerfile)
* [レイヤー](#layers)
* [リンク](#links)
* [ボリューム](#volumes)
* [ポートの公開](#exposing-ports)
* [ベストプラクティス](#best-practices)
* [Docker-Compose](#docker-compose)
* [セキュリティ](#security)
* [ヒント](#tips)
* [貢献](#contributing)

## Dockerの利点

"Dockerを使用することで、開発者は任意の言語、任意のツールチェーンを使用して任意のアプリケーションを構築できます。"Dockerized"なアプリケーションは完全に移植可能であり、同僚のOS XおよびWindowsラップトップ、クラウド上でUbuntuを実行しているQAサーバ、およびRed Hatを実行している本番データセンターのVMなど、どこでも実行できます。

開発者は、Docker Hubで利用可能な13,000以上のアプリケーションのうちの1つを使用してすぐに始めることができます。Dockerは変更と依存関係を管理および追跡し、開発者が構築するアプリケーションの動作を理解するためにシステム管理者がより簡単にできるようにします。そして、Docker Hubを使用することで、開発者はビルドパイプラインを自動化し、公開またはプライベートなリポジトリを介して共同作業者とアーティファクトを共有できます。

Dockerは、開発者がより高品質なアプリケーションをより速く構築および配信するのに役立ちます。" -- [Dockerとは](https://www.docker.com/what-docker#copy1)

## 前提条件

私は[Dockerプラグイン](https://github.com/robbyrussell/oh-my-zsh/wiki/Plugins#docker)を使用した[Oh My Zsh](https://github.com/ohmyzsh/oh-my-zsh)を、Dockerコマンドの自動補完のために使用しています。効果は個人差があります。

### Linux

Dockerの最小要件は[3.10.xカーネル](https://docs.docker.com/engine/installation/binaries/#check-kernel-dependencies)です。

### MacOS

10.8 “Mountain Lion” 以降が必要です。

### Windows 10

BIOSでHyper-Vを有効にする必要があります。

使用可能な場合は、VT-Dも有効にする必要があります（Intelプロセッサ）。

### Windows Server

Dockerおよびdocker-composeをインストールするための最小バージョンはWindows Server 2016です。このバージョンにはいくつかの制約があります。例として、複数の仮想ネットワークとLinuxコンテナがあります。Windows Server 2019以降が推奨されています。

## インストール

### Linux

Dockerが提供するこの簡単なインストールスクリプトを実行してください：

```sh
curl -sSL https://get.docker.com/ | sh
```

ランダムなシェルスクリプトを実行することに抵抗がある場合は、[インストール](https://docs.docker.com/engine/installation/linux/)手順をご覧ください。

Dockerの初心者の場合は、[チュートリアルシリーズ](https://docs.docker.com/engine/getstarted/)に従うと良いでしょう。

### macOS

[Docker Community Edition](https://www.docker.com/community-edition)をダウンロードしてインストールしてください。Homebrew-Caskを使用している場合は、`brew install --cask docker`と入力するだけです。または、[Docker Toolbox](https://docs.docker.com/toolbox/overview/)をダウンロードしてインストールしてください。[Docker For Mac](https://docs.docker.com/docker-for-mac/)も良いですが、VirtualBoxのインストールほど完成度が高くありません。[比較を参照してください](https://docs.docker.com/docker-for-mac/docker-toolbox/)。

> **注意** Docker Toolboxはレガシーです。Docker Community Editionを使用する必要があります。[Docker Toolbox](https://docs.docker.com/toolbox/overview/)を参照してください。

Docker Community Editionをインストールしたら、Launchpadでdockerアイコンをクリックしてください。そして、コンテナを起動します：

```sh
docker run hello-world
```

これで、実行中のDockerコンテナがあります。

Dockerの初心者の場合は、おそらく[チュートリアルシリーズ](https://docs.docker.com/engine/getstarted/)に従うと良いでしょう。

### Windows 10

Windows用のDocker Desktopをインストールする手順は[こちら](https://docs.docker.com/desktop/windows/install/)で見つけることができます。

インストール後、管理者としてpowershellを開き、次のコマンドを実行してください：

```powershell
# インストールされたdockerのバージョンを表示：
docker version

# 'hello-world'をPullして、作成して、実行：
docker run hello-world
```

このチートシートを続けるためには、システムトレイのDockerアイコンを右クリックし、設定に移動してください。ボリュームをマウントするには、設定でC:/ドライブを有効にする必要があります。これにより情報がコンテナに渡されます（この記事で後述します）。

WindowsコンテナとLinuxコンテナの切り替えは、システムトレイのアイコンを右クリックし、コンテナのオペレーティングシステムを切り替えるボタンをクリックします。これにより、現在実行中のコンテナが停止し、コンテナのOSが戻されるまでアクセスできなくなります。

さらに、デスクトップにWSLまたはWSL2がインストールされている場合、Windows用のLinuxカーネルをインストールすることができます。手順は[こちら](https://techcommunity.microsoft.com/t5/windows-dev-appconsult/using-wsl2-in-a-docker-linux-container-on-windows-to-run-a/ba-p/1482133)で確認できます。これにはWindows Subsystem for Linux機能が必要です。これにより、WSLオペレーティングシステムがDockerで実行される際の効率向上が得られ、WSLオペレーティングシステムからコンテナにアクセスできます。これには[Windowsターミナル](https://docs.microsoft.com/en-us/windows/terminal/get-started)の使用が推奨されています。

### Windows Server 2016 / 2019

Microsoftの手順に従って、[こちら](https://docs.microsoft.com/en-us/virtualization/windowscontainers/deploy-containers/deploy-containers-on-server#install-docker)で見つけることができます。

2019年の最新エッジバージョンを使用する場合、サーバーコアイメージのみですので（デスクトップインターフェースはありません）、powershellのみで作業する準備をしてください。このマシンを起動すると、ログインして直ちにpowershellウィンドウが表示されます。[Chocolatey](https://chocolatey.org/install)を使用してテキストエディタやその他のツールをインストールすることがお勧めです。

インストール後、これらのコマンドが機能します：

```powershell
# インストールされたdockerのバージョンを表示：
docker version

# 'hello-world'をPullして、作成して、実行：
docker run hello-world
```

Windows Server 2016はLinuxイメージを実行できません。

Windows Server Build 2004は、Hyper-Vアイソレーションを使用して同時にLinuxおよびWindowsコンテナを実行できます。コンテナを実行する際は、```--isolation=hyperv```コマンドを使用して、コンテナを別個のカーネルインスタンスで隔離します。

### バージョンの確認

いつでも現在実行しているDockerのバージョンを知っていることは非常に重要です。これは、実行中のものと互換性のある機能を知ることができるため非常に役立ちます。また、Dockerストアからテンプレートコンテナを取得しようとするときに、実行中のものを知っていることも重要です。それを考慮に入れて、現在実行中のDockerのバージョンを知る方法を見てみましょう。

* [`docker version`](https://docs.docker.com/engine/reference/commandline/version/)は現在実行中のDockerのバージョンを表示します。

サーバーバージョンを取得します：

```console
$ docker version --format '{{.Server.Version}}'
1.8.0
```

また、生のJSONデータをダンプすることもできます：

```console
$ docker version --format '{{json .}}'
{"Client":{"Version":"1.8.0","ApiVersion":"1.20","GitCommit":"f5bae0a","GoVersion":"go1.4.2","Os":"linux","Arch":"am"}
```

## コンテナ

[基本的な独立したDockerプロセス](http://etherealmind.com/basics-docker-containers-hypervisors-coreos/)。コンテナは仮想マシンに対してスレッドがプロセスに対してあるようなものです。または、それをステロイドを使用したchrootsと考えることもできます。

### ライフサイクル

* [`docker create`](https://docs.docker.com/engine/reference/commandline/create)はコンテナを作成しますが、起動はしません。
* [`docker rename`](https://docs.docker.com/engine/reference/commandline/rename/)はコンテナの名前を変更できます。
* [`docker run`](https://docs.docker.com/engine/reference/commandline/run)は、コンテナを作成して開始します。
* [`docker rm`](https://docs.docker.com/engine/reference/commandline/rm)はコンテナを削除します。
* [`docker update`](https://docs.docker.com/engine/reference/commandline/update/)はコンテナのリソース制限を更新します。

通常、オプションなしでコンテナを実行すると、すぐに開始されて停止します。実行中に保持する場合は、`docker run -td container_id`というコマンドを使用します。これにより、擬似TTYセッションを割り当てる`-t`オプションと、コンテナを自動的にデタッチする`-d`オプションが使用されます（コンテナをバックグラウンドで実行し、コンテナIDを表示します）。

一時的なコンテナが必要な場合は、`docker run --rm`を使用して、コンテナが停止した後にそれを削除できます。

ホスト上のディレクトリをDockerコンテナにマップしたい場合は、`docker run -v $HOSTDIR:$DOCKERDIR`を使用します。また、[ボリューム](https://github.com/wsargent/docker-cheat-sheet/#volumes)も参照してください。

コンテナに関連付けられたボリュームも削除したい場合は、コンテナの削除に`-v`スイッチを含める必要があります。例：`docker rm -v`。

Docker 1.10では、個々のコンテナに対して利用可能な[ログドライバ](https://docs.docker.com/engine/admin/logging/overview/)もあります。カスタムログドライバ（例：syslog）を使用してDockerを実行するには、`docker run --log-driver=syslog`を使用します。

もう1つの便利なオプションは`docker run --name yourname docker_image`です。なぜなら、`--name`を実行コマンド内で指定すると、作成時に指定した名前でコンテナを呼び出すことができるからです。

### 開始と停止

* [`docker start`](https://docs.docker.com/engine/reference/commandline/start)はコンテナを開始して実行します。
* [`docker stop`](https://docs.docker.com/engine/reference/commandline/stop)は実行中のコンテナを停止します。
* [`docker restart`](https://docs.docker.com/engine/reference/commandline/restart)はコンテナを停止して再開します。
* [`docker pause`](https://docs.docker.com/engine/reference/commandline/pause/)は実行中のコンテナを一時停止し、その場で凍結します。
* [`docker unpause`](https://docs.docker.com/engine/reference/commandline/unpause/)は実行中のコンテナを再開します。
* [`docker wait`](https://docs.docker.com/engine/reference/commandline/wait)は実行中のコンテナが停止するまでブロックします。
* [`docker kill`](https://docs.docker.com/engine/reference/commandline/kill)は実行中のコンテナにSIGKILLを送信します。
* [`docker attach`](https://docs.docker.com/engine/reference/commandline/attach)は実行中のコンテナに接続します。

実行中のコンテナからデタッチする場合は、`Ctrl + p, Ctrl + q`を使用します。
ホストプロセスマネージャとコンテナを統合したい場合は、デーモンを`-r=false`オプションで起動し、その後`docker start -a`を使用します。[こちら](https://docs.docker.com/engine/admin/host_integration/)を参照してください。

コンテナポートをホストを介して公開したい場合は、[ポートの公開](#exposing-ports)セクションを確認してください。

クラッシュしたDockerインスタンスの再起動ポリシーについては、[こちら](http://container42.com/2014/09/30/docker-restart-policies/)を参照してください。

#### CPUの制約

CPUを制限することができます。これは、すべてのCPUのパーセンテージを使用するか、特定のコアを使用するかの選択があります。

例えば、[`cpu-shares`](https://docs.docker.com/engine/reference/run/#/cpu-share-constraint)設定を使用することができます。この設定は少し奇妙です - 1024はCPUの100%を意味するため、コンテナにすべてのCPUコアの50%を取らせたい場合は、512を指定する必要があります。詳細については、<https://goldmann.pl/blog/2014/09/11/resource-management-in-docker/#_cpu>を参照してください：

```sh
docker run -it -c 512 agileek/cpuset-test
```

また、[`cpuset-cpus`](https://docs.docker.com/engine/reference/run/#/cpuset-constraint)を使用して特定のCPUコアのみを使用することもできます。詳細といくつかの素敵なビデオについては、<https://agileek.github.io/docker/2014/08/06/docker-cpuset/>を参照してください：

```sh
docker run -it --cpuset-cpus=0,4,6 agileek/cpuset-test
```

注意：Dockerはコンテナ内で引き続きすべてのCPUを**見る**ことができますが、すべてを使用しているわけではありません。詳細については、<https://github.com/docker/docker/issues/20770>を参照してください。

#### メモリの制約

Dockerには[メモリの制約](https://docs.docker.com/engine/reference/run/#/user-memory-constraints)も設定できます：

```sh
docker run -it -m 300M ubuntu:14.04 /bin/bash
```

#### キャビリティ

Linuxのキャビリティは、`cap-add`および`cap-drop`を使用して設定できます。詳細については、<https://docs.docker.com/engine/reference/run/#/runtime-privilege-and-linux-capabilities>を参照してください。これはセキュリティ向上のために使用するべきです。

FUSEベースのファイルシステムをマウントするには、`--cap-add`および`--device`を組み合わせる必要があります：

```sh
docker run --rm -it --cap-add SYS_ADMIN --device /dev/fuse sshfs
```

単一のデバイスにアクセス権を付与する場合：

```sh
docker run -it --device=/dev/ttyUSB0 debian bash
```

すべてのデバイスにアクセス権を付与する場合：

```sh
docker run -it --privileged -v /dev/bus/usb:/dev/bus/usb debian bash
```

特権コンテナに関する詳細は[こちら](
https://docs.docker.com/engine/reference/run/#runtime-privilege-and-linux-capabilities)。

### インフォメーション

* [`docker ps`](https://docs.docker.com/engine/reference/commandline/ps)は実行中のコンテナを表示します。
* [`docker logs`](https://docs.docker.com/engine/reference/commandline/logs)はコンテナからログを取得します（カスタムログドライバを使用できますが、ログは1.10では`json-file`および`journald`のみで使用可能です）。
* [`docker inspect`](https://docs.docker.com/engine/reference/commandline/inspect)はコンテナのすべての情報（IPアドレスを含む）を表示します。
* [`docker events`](https://docs.docker.com/engine/reference/commandline/events)はコンテナからイベントを取得します。
* [`docker port`](https://docs.docker.com/engine/reference/commandline/port)はコンテナの公開ポートを表示します。
* [`docker top`](https://docs.docker.com/engine/reference/commandline/top)はコンテナ内の実行中のプロセスを表示します。
* [`docker stats`](https://docs.docker.com/engine/reference/commandline/stats)はコンテナのリソース使用統計を表示します。
* [`docker diff`](https://docs.docker.com/engine/reference/commandline/diff)はコンテナのファイルシステムの変更を表示します。

`docker ps -a`は実行中および停止したコンテナを表示します。

`docker stats --all`はすべてのコンテナのリストを表示し、デフォルトでは実行中のコンテナのみを表示します。

### インポート / エクスポート

* [`docker cp`](https://docs.docker.com/engine/reference/commandline/cp)はコンテナとローカルファイルシステム間でファイルまたはフォルダーをコピーします。
* [`docker export`](https://docs.docker.com/engine/reference/commandline/export)はコンテナファイルシステムをtarボールアーカイブストリームに変換してSTDOUTに出力します。

### コマンドの実行

* [`docker exec`](https://docs.docker.com/engine/reference/commandline/exec)はコンテナ内でコマンドを実行します。

実行中のコンテナに入るには、実行中のコンテナ「foo」に新しいシェルプロセスをアタッチして使用します：`docker exec -it foo /bin/bash`。

## イメージ

イメージは単なる[Dockerコンテナのテンプレート](https://docs.docker.com/engine/understanding-docker/#how-does-a-docker-image-work)です。

### ライフサイクル

* [`docker images`](https://docs.docker.com/engine/reference/commandline/images)はすべてのイメージを表示します。
* [`docker import`](https://docs.docker.com/engine/reference/commandline/import)はtarボールからイメージを作成します。
* [`docker build`](https://docs.docker.com/engine/reference/commandline/build)はDockerfileからイメージを作成します。
* [`docker commit`](https://docs.docker.com/engine/reference/commandline/commit)はコンテナからイメージを作成し、実行中の場合は一時停止します。
* [`docker rmi`](https://docs.docker.com/engine/reference/commandline/rmi)はイメージを削除します。
* [`docker load`](https://docs.docker.com/engine/reference/commandline/load)はtarアーカイブからSTDINとしてイメージを読み込みます（0.7以降）。
* [`docker save`](https://docs.docker.com/engine/reference/commandline/save)はすべての親レイヤー、タグ、およびバージョンを含むイメージをtarアーカイブストリームとしてSTDOUTに保存します（0.7以降）。

### インフォメーション

* [`docker history`](https://docs.docker.com/engine/reference/commandline/history)はイメージの履歴を表示します。
* [`docker tag`](https://docs.docker.com/engine/reference/commandline/tag)はイメージにタグを付けます（ローカルまたはレジストリ）。

### クリーンアップ

特定のイメージを削除するには `docker rmi` コマンドを使用できますが、もはやどのコンテナにも使用されていないイメージを安全にクリーンアップするためのツールがあります。それが [docker-gc](https://github.com/spotify/docker-gc) です。docker 1.13以降では、未使用のイメージを削除するための `docker image prune` も使用できます。詳細は[Prune](#prune)を参照してください。

### イメージの読み込み/保存

ファイルからイメージを読み込む：

```sh
docker load < my_image.tar.gz
```

既存のイメージを保存する：

```sh
docker save my_image:my_tag | gzip > my_image.tar.gz

### Networks

* [`docker network create`](https://docs.docker.com/engine/reference/commandline/network_create/) creates a new network.
* [`docker network connect`](https://docs.docker.com/engine/reference/commandline/network_connect/) connects a container to a network.
* [`docker network disconnect`](https://docs.docker.com/engine/reference/commandline/network_disconnect/) disconnects a container from a network.
* [`docker network ls`](https://docs.docker.com/engine/reference/commandline/network_ls/) lists networks.
* [`docker network inspect`](https://docs.docker.com/engine/reference/commandline/network_inspect/) shows information about a network.
* [`docker network rm`](https://docs.docker.com/engine/reference/commandline/network_rm/) removes one or more networks.

Dockerのネットワーキングに関する詳細は[こちら](https://docs.docker.com/network/)を参照してください。

### Registry and Repository

Dockerイメージのレジストリとリポジトリに関する詳細は[こちら](https://docs.docker.com/registry/)を参照してください。

### Dockerfile

Dockerfileの詳細については、[こちら](https://docs.docker.com/engine/reference/builder/)を参照してください。

### Layers

Dockerのイメージレイヤに関する詳細は[こちら](https://docs.docker.com/storage/storagedriver/)を参照してください。

### Links

Dockerのコンテナリンクに関する詳細は[こちら](https://docs.docker.com/network/linking/)を参照してください。

### Volumes

Dockerのボリュームに関する詳細は[こちら](https://docs.docker.com/storage/volumes/)を参照してください。

### Exposing Ports

* [`docker run -p`](https://docs.docker.com/engine/reference/commandline/run/#publish-or-expose-port) publishes a container's port(s) to the host.
* [`docker port`](https://docs.docker.com/engine/reference/commandline/port) shows the public-facing port of a container.

### Best Practices

Dockerのベストプラクティスに関する詳細は[こちら](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/)を参照してください。

### Docker-Compose

* [`docker-compose`](https://docs.docker.com/compose/reference/overview/)は、複数のサービスを使用してアプリケーションの実行を定義および管理するためのツールです。

### Security

Dockerセキュリティに関する詳細は[こちら](https://docs.docker.com/engine/security/)を参照してください。

### Tips

Dockerのヒントに関する詳細は[こちら](https://docs.docker.com/engine/userguide/eng-image/dockerfile_best-practices/)を参照してください。

### Contributing

このチートシートの改善にご協力いただける場合は、[Contributing](#contributing)セクションを参照してください。

### コンテナのインポート/エクスポート

ファイルからイメージとしてコンテナをインポートする：

```sh
cat my_container.tar.gz | docker import - my_image:my_tag
```

既存のコンテナをエクスポートする：

```sh
docker export my_container | gzip > my_container.tar.gz
```

### 保存されたイメージの読み込みとエクスポートされたコンテナをイメージとしてインポートする際の違い

`load`コマンドを使用してイメージを読み込むと、その履歴を含む新しいイメージが作成されます。
`import`コマンドを使用してコンテナをイメージとしてインポートすると、その履歴を除外して新しいイメージが作成されるため、イメージのサイズが読み込む場合よりも小さくなります。

## ネットワーク

Dockerには[ネットワーク](https://docs.docker.com/engine/userguide/networking/)機能があります。Dockerをインストールすると自動的に3つのネットワークインターフェースが作成されます（ブリッジ、ホスト、なし）。デフォルトで新しいコンテナはブリッジネットワークに起動されます。複数のコンテナ間で通信を可能にするには、新しいネットワークを作成してその中でコンテナを起動できます。これにより、ネットワークに接続されていないコンテナから隔離されながら、コンテナ同士が通信できるようになります。さらに、コンテナの名前をそのIPアドレスにマッピングすることも可能です。詳細については[ネットワークの作業](https://docs.docker.com/engine/userguide/networking/work-with-networks/)を参照してください。

### ライフサイクル

* [`docker network create`](https://docs.docker.com/engine/reference/commandline/network_create/) NAME 新しいネットワークを作成します（デフォルトのタイプはブリッジ）。
* [`docker network rm`](https://docs.docker.com/engine/reference/commandline/network_rm/) NAME 名前または識別子で1つ以上のネットワークを削除します。ネットワークを削除するときには、そのネットワークに接続されているコンテナはありません。

### インフォメーション

* [`docker network ls`](https://docs.docker.com/engine/reference/commandline/network_ls/) ネットワークの一覧表示
* [`docker network inspect`](https://docs.docker.com/engine/reference/commandline/network_inspect/) NAME 1つまたは複数のネットワークに関する詳細情報を表示します。

### 接続

* [`docker network connect`](https://docs.docker.com/engine/reference/commandline/network_connect/) NETWORK CONTAINER ネットワークにコンテナを接続します
* [`docker network disconnect`](https://docs.docker.com/engine/reference/commandline/network_disconnect/) NETWORK CONTAINER ネットワークからコンテナを切断します

コンテナに[特定のIPアドレスを指定](https://blog.jessfraz.com/post/ips-for-all-the-things/)することができます：

```sh
# あなたのIPブロックのサブネットとゲートウェイを指定して新しいブリッジネットワークを作成します
docker network create --subnet 203.0.113.0/24 --gateway 203.0.113.254 iptastic

# そのブロック内の特定のIPでnginxコンテナを実行します
$ docker run --rm -it --net iptastic --ip 203.0.113.2 nginx

# 他の場所からIPをcurlします（これが公開IPブロックであると仮定しています）
$ curl 203.0.113.2
```

## レジストリ＆リポジトリ

リポジトリは、一緒にコンテナのファイルシステムを作成する、タグ付きイメージの*ホストされた*コレクションです。

レジストリは*ホスト*であり、リポジトリを保存し、[リポジトリのアップロードとダウンロードを管理するためのHTTP API](https://docs.docker.com/engine/tutorials/dockerrepos/)を提供するサーバです。

Docker.comは中央レジストリを含む複数のリポジトリを保存し、それに対するHTTP APIを提供する[インデックス](https://hub.docker.com/)をホストしています。ただし、中央のDockerレジストリは[イメージの検証をうまく行っていません](https://titanous.com/posts/docker-insecurity)ので、セキュリティに不安がある場合は避けるべきです。

* [`docker login`](https://docs.docker.com/engine/reference/commandline/login) - レジストリにログインします。
* [`docker logout`](https://docs.docker.com/engine/reference/commandline/logout) - レジストリからログアウトします。
* [`docker search`](https://docs.docker.com/engine/reference/commandline/search) - イメージを検索します。
* [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull) - イメージをレジストリからローカルマシンにプルします。
* [`docker push`](https://docs.docker.com/engine/reference/commandline/push) - イメージをローカルマシンからレジストリにプッシュします。

### ローカルレジストリの実行

[ドッカーディストリビューション](https://github.com/docker/distribution)プロジェクトを使用して、[ローカルデプロイの手順](https://github.com/docker/docker.github.io/blob/master/registry/deploying.md)を見ることで、ローカルレジストリを実行できます。

また、[メーリングリスト](https://groups.google.com/a/dockerproject.org/forum/#!forum/distribution)も参照してください。

## Dockerfile

[構成ファイル](https://docs.docker.com/engine/reference/builder/)。これを実行すると`docker build`が実行され、Dockerコンテナがセットアップされます。 `docker commit`よりもはるかに望ましいです。

以下は、Dockerfileを作成するために使用できる一般的なテキストエディタとその構文強調モジュールです。

* [jEdit](http://jedit.org)を使用している場合、[Dockerfile](https://github.com/wsargent/jedit-docker-mode)の構文強調モジュールが利用できます。
* [Sublime Text 2](https://packagecontrol.io/packages/Dockerfile%20Syntax%20Highlighting)
* [Atom](https://atom.io/packages/language-docker)
* [Vim](https://github.com/ekalinin/Dockerfile.vim)
* [Emacs](https://github.com/spotify/dockerfile-mode)
* [TextMate](https://github.com/docker/docker/tree/master/contrib/syntax/textmate)
* [VS Code](https://github.com/Microsoft/vscode-docker)
* また、[Docker meets the IDE](https://domeide.github.io/)も参照してください。

### インストラクション

* [.dockerignore](https://docs.docker.com/engine/reference/builder/#dockerignore-file)
* [FROM](https://docs.docker.com/engine/reference/builder/#from) - 後続のインストラクションに対するベースイメージを設定します。
* [MAINTAINER (非推奨 - 代わりにLABELを使用)](https://docs.docker.com/engine/reference/builder/#maintainer-deprecated) - 生成されたイメージのAuthorフィールドを設定します。
* [RUN](https://docs.docker.com/engine/reference/builder/#run) - 現在のイメージの上に新しいレイヤーでコマンドを実行し、結果をコミットします。
* [CMD](https://docs.docker.com/engine/reference/builder/#cmd) - 実行中のコンテナに対してデフォルトを提供します。
* [EXPOSE](https://docs.docker.com/engine/reference/builder/#expose) - Dockerに、実行時にコンテナが指定したネットワークポートでリッスンしていることを通知します。注意：ポートを実際にアクセス可能にはしません。
* [ENV](https://docs.docker.com/engine/reference/builder/#env) - 環境変数を設定します。
* [ADD](https://docs.docker.com/engine/reference/builder/#add) - 新しいファイル、ディレクトリ、またはリモートファイルをコンテナにコピーします。キャッシュを無効にします。`ADD`は避けて代わりに`COPY`を使用してください。
* [COPY](https://docs.docker.com/engine/reference/builder/#copy) - 新しいファイルやディレクトリをコンテナにコピーします。デフォルトでは、これはUSER/WORKDIRの設定に関係なくrootとしてコピーします。所有権を他のユーザー/グループに付与するには `--chown=<user>:<group>` を使用します（`ADD`も同様）。
* [ENTRYPOINT](https://docs.docker.com/engine/reference/builder/#entrypoint) - 実行可能なコンテナを構成します。
* [VOLUME](https://docs.docker.com/engine/reference/builder/#volume) - 外部のマウントされたボリュームや他のコンテナのためのマウントポイントを作成します。
* [USER](https://docs.docker.com/engine/reference/builder/#user) - 後続のRUN / CMD / ENTRYPOINTコマンドのユーザー名を設定します。
* [WORKDIR](https://docs.docker.com/engine/reference/builder/#workdir) - 作業ディレクトリを設定します。
* [ARG](https://docs.docker.com/engine/reference/builder/#arg) - ビルド時の変数を定義します。
* [ONBUILD](https://docs.docker.com/engine/reference/builder/#onbuild) - このイメージを別のビルドのベースとして使用するときにトリガー命令を追加します。
* [STOPSIGNAL](https://docs.docker.com/engine/reference/builder/#stopsignal) - コンテナが終了するために送信されるシステムコールシグナルを設定します。
* [LABEL](https://docs.docker.com/config/labels-custom-metadata/) - イメージ、コンテナ、またはデーモンにキー/値メタデータを適用します。
* [SHELL](https://docs.docker.com/engine/reference/builder/#shell) - Dockerがコマンドを実行するために使用するデフォルトのシェルを上書きします。
* [HEALTHCHECK](https://docs.docker.com/engine/reference/builder/#healthcheck) - Dockerにコンテナがまだ動作しているかどうかをテストする方法を指定します。

### チュートリアル

* [Flux7のDockerfileチュートリアル](https://www.flux7.com/tutorial/docker-tutorial-series-part-3-automation-is-the-word-using-dockerfile/)

### 例

* [Examples](https://docs.docker.com/engine/reference/builder/#dockerfile-examples)
* [Best practices for writing Dockerfiles](https://docs.docker.com/engine/userguide/eng-image/dockerfile_best-practices/)
* [Michael Crosby](http://crosbymichael.com/) による [Dockerfilesのベストプラクティス](http://crosbymichael.com/dockerfile-best-practices.html) / [take 2](http://crosbymichael.com/dockerfile-best-practices-take-2.html) も参照してください。
* [優れたDockerイメージの構築](http://jonathan.bergknoff.com/journal/building-good-docker-images) / [より優れたDockerイメージの構築](http://jonathan.bergknoff.com/journal/building-better-docker-images)
* [メタデータを使用したコンテナ構成の管理](https://speakerdeck.com/garethr/managing-container-configuration-with-metadata)
* [優れたDockerfileの書き方](https://rock-it.pl/how-to-write-excellent-dockerfiles/)

## レイヤー

Dockerのバージョン管理されたファイルシステムは、レイヤーに基づいています。これらは、[ファイルシステムのgitのコミットや変更セットのようなもの](https://docs.docker.com/engine/userguide/storagedriver/imagesandcontainers/)です。

## リンク

リンクは、Dockerコンテナがお互いと[TCP/IPポートを介して通信する方法](https://docs.docker.com/engine/userguide/networking/default_network/dockerlinks/)です。 [Atlassian](https://blogs.atlassian.com/2013/11/docker-all-the-things-at-atlassian-automation-and-wiring/)は実例を示しています。また、[ホスト名によるリンクの解決](https://docs.docker.com/engine/userguide/networking/default_network/dockerlinks/#/updating-the-etchosts-file)もあります。

これは、[ユーザー定義のネットワーク](https://docs.docker.com/network/)によってある程度非推奨にされています。

注意：コンテナがリンクを介してのみ通信するようにする場合は、dockerデーモンを `-icc=false` で起動して、プロセス間通信を無効にします。

コンテナが CONTAINER という名前（`docker run --name CONTAINER` で指定）で、Dockerfileで公開ポートを持っている場合：

```Dockerfile
EXPOSE 1337
```

次に、以下のようにして別のコンテナ LINKED を作成する場合：

```sh
docker run -d --link CONTAINER:ALIAS --name LINKED user/wordpress
```

その後、CONTAINERの公開されたポートとエイリアスが、次の環境変数で LINKED に表示されます：

```sh
$ALIAS_PORT_1337_TCP_PORT
$ALIAS_PORT_1337_TCP_ADDR
```

これを使用して接続できます。

リンクを削除するには、`docker rm --link` を使用します。

一般的に、Dockerサービス間のリンクは、本番環境でDockerを大規模に使用する予定の場合、"サービスディスカバリ"のサブセットです。詳細については、[The Docker Ecosystem: Service Discovery and Distributed Configuration Stores](https://www.digitalocean.com/community/tutorials/the-docker-ecosystem-service-discovery-and-distributed-configuration-stores)を参照してください。

## ボリューム

Dockerのボリュームは、[浮遊するファイルシステム](https://docs.docker.com/engine/tutorials/dockervolumes/)です。特定のコンテナに接続する必要はありません。ポータビリティのために[データ専用のコンテナ](https://medium.com/@ramangupta/why-docker-data-containers-are-good-589b3c6c749e)からマウントされたボリュームを使用できます。Docker 1.9.0以降、Dockerにはデータ専用のコンテナを置き換える名前付きボリュームがあります。データコンテナではなく、名前付きボリュームを使用することを検討してください。

### ライフサイクル

* [`docker volume create`](https://docs.docker.com/engine/reference/commandline/volume_create/)
* [`docker volume rm`](https://docs.docker.com/engine/reference/commandline/volume_rm/)

### 情報

* [`docker volume ls`](https://docs.docker.com/engine/reference/commandline/volume_ls/)
* [`docker volume inspect`](https://docs.docker.com/engine/reference/commandline/volume_inspect/)

ボリュームは、リンクを使用できない（TCP/IPのみ）状況で役立ちます。たとえば、2つのDockerインスタンスがファイルシステムに何かを残して通信する必要がある場合などです。

`docker run --volumes-from`を使用して、複数のDockerコンテナにボリュームをマウントできます。

ボリュームは隔離されたファイルシステムなので、一時的なコンテナ間の計算の状態を保存するためによく使用されます。つまり、無状態で一時的なコンテナがレシピから実行され、消去され、次に一時的なコンテナの2番目のインスタンスが前回終了した場所から続行できます。

詳細については、[advanced volumes](http://crosbymichael.com/advanced-docker-volumes.html)を参照してください。[Container42](http://container42.com/2014/11/03/docker-indepth-volumes/)も参考になります。

MacOSホストディレクトリをDockerボリュームとして[マップできます](https://docs.docker.com/engine/tutorials/dockervolumes/#mount-a-host-directory-as-a-data-volume)：

```sh
docker run -v /Users/wsargent/myapp/src:/src
```

[勇気を出して](https://docs.docker.com/engine/tutorials/dockervolumes/#/mount-a-shared-storage-volume-as-a-data-volume)、リモートNFSボリュームも使用できます。

[ここ](http://container42.com/2013/12/16/persistent-volumes-with-docker-container-as-volume-pattern/)で説明されているように、データ専用のコンテナを実行して、いくらかのデータの移植性を提供することも検討してください。

[ファイルをボリュームとしてマウント](#volumes-can-be-files)することもできることに注意してください。

## ポートの公開

ホストコンテナを介して入力ポートを公開することは[手間がかかりますが可能です](https://docs.docker.com/engine/reference/run/#expose-incoming-ports)。

これは、`-p`を使用してコンテナポートをホストポートにマッピングすることによって行われます（localhostインターフェースのみを使用）：

```sh
docker run -p 127.0.0.1:$HOSTPORT:$CONTAINERPORT \
  --name CONTAINER \
  -t someimage
```

[EXPOSE](https://docs.docker.com/engine/reference/builder/#expose)を使用して、Dockerに実行時にコンテナが指定されたネットワークポートでリッスンすることを伝えることができます。

```Dockerfile
EXPOSE <CONTAINERPORT>
```

`EXPOSE`はポート自体を公開しないことに注意してください。これを行うのは`-p`だけです。

ローカルホストのポートでコンテナのポートを公開するには、次のコマンドを実行します：

```sh
iptables -t nat -A DOCKER -p tcp --dport <LOCALHOSTPORT> -j DNAT --to-destination <CONTAINERIP>:<PORT>
```

DockerをVirtualboxで実行している場合は、[forwarded_port](https://docs.vagrantup.com/v2/networking/forwarded_ports.html)を使用してポートを転送する必要があります。次のようにVagrantfileでポートの範囲を定義して、動的にマッピングできるようにします。

```ruby
Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
  ...

  (49000..49900).each do |port|
    config.vm.network :forwarded_port, :host => port, :guest => port
  end

  ...
end
```

ホストコンテナでポートをどのようにマップしたかを忘れた場合は、`docker port`を使用して表示できます：

```sh
docker port CONTAINER $CONTAINERPORT
```

## ベストプラクティス

ここには、一般的なDockerのベストプラクティスと経験談が含まれます。

* [The Rabbit Hole of Using Docker in Automated Tests](http://gregoryszorc.com/blog/2014/10/16/the-rabbit-hole-of-using-docker-in-automated-tests/)
* [Bridget Kromhout](https://twitter.com/bridgetkromhout)氏による、Dramafeverでの[本番でDockerを実行する](http://sysadvent.blogspot.co.uk/2014/12/day-1-docker-in-production-reality-not.html)ための有益なブログ記事があります。
* Lystからの[ベストプラクティスのブログ記事](http://developers.lyst.com/devops/2014/12/08/docker/)もあります。
* [Dockerを使用した開発環境の構築](https://tersesystems.com/2013/11/20/building-a-development-environment-with-docker/)
* [Dockerコンテナ内のDiscourse](https://samsaffron.com/archive/2013/11/07/discourse-in-a-docker-container)

## Docker-Compose

Composeは、複数のコンテナからなるDockerアプリケーションを定義し、実行するためのツールです。Composeを使用すると、YAMLファイルを使用してアプリケーションのサービスを構成します。その後、1つのコマンドで構成からすべてのサービスを作成および起動できます。Composeのすべての機能について詳しく知りたい場合は、[機能のリスト](https://docs.docker.com/compose/overview/#features)を参照してください。

次のコマンドを使用すると、アプリケーションを起動できます：

```sh
docker-compose -f <docker-compose-file> up
```

また、-dフラグを使用してdocker-composeをデタッチモードで実行することもでき、その後、次のコマンドでいつでも停止できます：

```sh
docker-compose stop
```

downコマンドを使用して、すべてを停止し、コンテナを完全に削除することもできます。データボリュームも削除するには`--volumes`を渡します。

## セキュリティ

ここには、Dockerに関するセキュリティのヒントがあります。Docker [security](https://docs.docker.com/engine/security/security/) ページには詳細が記載されています。

最初に注意すべきこと：Dockerはrootとして実行されます。`docker`グループに属している場合、実質的には[ルートアクセス権限](https://web.archive.org/web/20161226211755/http://reventlov.com/advisories/using-the-docker-command-to-root-the-host)があります。DockerのUnixソケットをコンテナに公開すると、コンテナに[ホストへのルートアクセス権限](https://www.lvh.io/posts/dont-expose-the-docker-socket-not-even-to-a-container/)が与えられます。

Dockerはあくまで防御手段の一環であるべきです。セキュリティ対策を講じ、強化する必要があります。

コンテナが露出するものを理解するためには、[Understanding and Hardening Linux Containers](https://www.nccgroup.trust/globalassets/our-research/us/whitepapers/2016/april/ncc_group_understanding_hardening_linux_containers-1-1.pdf)を[Aaron Grattafiori](https://twitter.com/dyn___)氏が読むべきです。これは、コンテナに関連する問題についての完全かつ包括的なガイドで、多くのリンクと脚注があり、さらに有益なコンテンツへと続いています。以下のセキュリティのヒントは、過去にコンテナを強化したことがある場合に役立ちますが、理解の代替手段ではありません。

### セキュリティのヒント

最高のセキュリティを実現するためには、Dockerを仮想マシン内で実行することが望ましいです。これはDockerセキュリティチームリーダーによるものです — [スライド](http://www.slideshare.net/jpetazzo/linux-containers-lxc-docker-and-security) / [ノート](http://www.projectatomic.io/blog/2014/08/is-it-safe-a-look-at-docker-and-security-from-linuxcon/)。その後、AppArmor / seccomp / SELinux / grsecなどを使用して、[コンテナの権限を制限](http://linux-audit.com/docker-security-best-practices-for-your-vessel-and-containers/)します。詳細については、[Docker 1.10セキュリティ機能](https://blog.docker.com/2016/02/docker-engine-1-10-security/)を参照してください。

DockerイメージIDは[機密情報](https://medium.com/@quayio/your-docker-image-ids-are-secrets-and-its-time-you-treated-them-that-way-f55e9f14c1a4)であり、外部に露出すべきではありません。それらをパスワードのように扱ってください。

[Thomas Sjögren](https://github.com/konstruktoid)による[Dockerセキュリティチートシート](https://github.com/konstruktoid/Docker/blob/master/Security/CheatSheet.adoc)も参照してください。コンテナのハードニングに関する素晴らしい情報が含まれています。

[docker benchセキュリティスクリプト](https://github.com/docker/docker-bench-security)を確認し、[ホワイトペーパー](https://blog.docker.com/2015/05/understanding-docker-security-and-best-practices/)をダウンロードしてください。

Snykの[Docker Image Security Best Practicesチートシート](https://snyk.io/blog/10-docker-image-security-best-practices/)

はじめに、[Alpine Linux](https://en.wikipedia.org/wiki/Alpine_Linux)など、grsecurity / paxの不安定なパッチが組み込まれたカーネルを使用することから始めるべきです。本番でgrsecurityを使用している場合、[安定版のパッチ](https://grsecurity.net/announce.php)についても、RedHatと同じように[商用サポート](https://grsecurity.net/business_support.php)を検討するべきです。月額$200で、デボップス予算には少ない金額です。

Docker 1.11以降、フォーク爆弾を防ぐためにコンテナ内で実行されるアクティブなプロセスの数を簡単に制限できます。これには、カーネル構成でCGROUP_PIDS=yが含まれている、Linuxカーネルバージョン4.3以上が必要です。

```sb
docker run --pids-limit=64
```

また、Docker 1.11以降では、プロセスが新しい権限を取得するのを防ぐ機能も利用できます。この機能はLinuxカーネルバージョン3.5から利用可能です。詳細については、[この](http://www.projectatomic.io/blog/2016/03/no-new-privs-docker/)ブログ記事を参照してください。

```sh
docker run --security-opt=no-new-privileges
```

[Container Solutions](http://container-solutions.com/is-docker-safe-for-production/)による[Dockerセキュリティチートシート](http://container-solutions.com/content/uploads/2015/06/15.06.15_DockerCheatSheet_A2.pdf)（PDF形式なので使用が難しいですが、以下にコピーしています）から：

相互プロセス通信をオフにする：

```sh
docker -d --icc=false --iptables
```

コンテナを読み取り専用に設定する：

```sh
docker run --read-only
``

### ユーザーネームスペース

[ユーザーネームスペース](https://s3hh.wordpress.com/2013/07/19/creating-and-using-containers-without-privilege/)に関する取り組みもあります。これは1.10に含まれていますが、デフォルトでは有効になっていません。

Ubuntu 15.10でユーザーネームスペース（"usernsをリマップ"）を有効にするには、[ブログの例に従ってください](https://raesene.github.io/blog/2016/02/04/Docker-User-Namespaces/)。

### セキュリティビデオ

* [Dockerを安全に使用する](https://youtu.be/04LOuMgNj9U)
* [Dockerを使用してアプリケーションを安全に保護する](https://youtu.be/KmxOXmPhZbk)
* [コンテナセキュリティ：コンテナは本当に制限されていますか？](https://youtu.be/a9lE9Urr6AQ)
* [Linuxコンテナ：未来またはファンタジー？](https://www.youtube.com/watch?v=iN6QbszB1R8)

### セキュリティロードマップ

Dockerのロードマップでは、[seccompサポート](https://github.com/docker/docker/blob/master/ROADMAP.md#11-security)について語っています。
[bane](https://github.com/jfrazelle/bane)と呼ばれるAppArmorポリシージェネレータがあり、[セキュリティプロファイル](https://github.com/docker/docker/issues/17142)についても作業中です。

## ヒント

出典:

* [5分でわかる15のDockerのヒント](http://sssslide.com/speakerdeck.com/bmorearty/15-docker-tips-in-5-minutes)
* [CodeFresh Everyday Hacks Docker](https://codefresh.io/blog/everyday-hacks-docker/)

### Prune

新しい[データ管理コマンド](https://github.com/docker/docker/pull/26108)はDocker 1.13として着陸しました:

* `docker system prune`
* `docker volume prune`
* `docker network prune`
* `docker container prune`
* `docker image prune`

### df

`docker system df`は、異なるDockerオブジェクトが現在使用しているスペースのサマリーを表示します。

### Heredoc Docker Container

```sh
docker build -t htop - << EOF
FROM alpine
RUN apk --no-cache add htop
EOF
```

### 最後のID

```sh
alias dl='docker ps -l -q'
docker run ubuntu echo hello world
docker commit $(dl) helloworld
```

### コマンドでコミット（Dockerfileが必要）

```sh
docker commit -run='{"Cmd":["postgres", "-too -many -opts"]}' $(dl) postgres
```

### IPアドレスの取得

```sh
docker inspect $(dl) | grep -wm1 IPAddress | cut -d '"' -f 4
```

または、[jq](https://stedolan.github.io/jq/)がインストールされている場合:

```sh
docker inspect $(dl) | jq -r '.[0].NetworkSettings.IPAddress'
```

または、[goテンプレート](https://docs.docker.com/engine/reference/commandline/inspect)を使用する場合:

```sh
docker inspect -f '{{ .NetworkSettings.IPAddress }}' <container_name>
```

または、Dockerfileからイメージをビルドするときにビルド引数を渡す場合:

```sh
DOCKER_HOST_IP=`ifconfig | grep -E "([0-9]{1,3}\.){3}[0-9]{1,3}" | grep -v 127.0.0.1 | awk '{ print $2 }' | cut -f2 -d: | head -n1`
echo DOCKER_HOST_IP = $DOCKER_HOST_IP
docker build \
  --build-arg ARTIFACTORY_ADDRESS=$DOCKER_HOST_IP
  -t sometag \
  some-directory/
```

### ポートマッピングの取得

```sh
docker inspect -f '{{range $p, $conf := .NetworkSettings.Ports}} {{$p}} -> {{(index $conf 0).HostPort}} {{end}}' <containername>
```

### 正規表現でコンテナを検索

```sh
for i in $(docker ps -a | grep "REGEXP_PATTERN" | cut -f1 -d" "); do echo $i; done
```

### 環境設定の取得

```sh
docker run --rm ubuntu env
```

### 実行中のコンテナを停止

```sh
docker kill $(docker ps -q)
```

### すべてのコンテナを削除（強制！実行中または停止中のコンテナ）

```sh
docker rm -f $(docker ps -qa)
```

### 古いコンテナを削除

```sh
docker ps -a | grep 'weeks ago' | awk '{print $1}' | xargs docker rm
```

### 停止したコンテナを削除

```sh
docker rm -v $(docker ps -a -q -f status=exited)
```

### 停止後にコンテナを削除

```sh
docker stop $(docker ps -aq) && docker rm -v $(docker ps -aq)
```

### ダングリングイメージを削除

```sh
docker rmi $(docker images -q -f dangling=true)
```

### すべてのイメージを削除

```sh
docker rmi $(docker images -q)
```

### ダングリングボリュームを削除

Docker 1.9以降:

```sh
docker volume rm $(docker volume ls -q -f dangling=true)
```

1.9.0では、フィルタ `dangling=false` は _動作しません_ - 無視され、すべてのボリュームがリストされます。

### イメージの依存関係を表示

```sh
docker images -viz | dot -Tpng -o docker.png
```

### Dockerコンテナのサイズを削減

- `RUN` レイヤー内のAPTのクリーニング - これは他の `apt` コマンドと同じレイヤーで行う必要があります。そうでないと、前のレイヤーは依然として元の情報を保持し、イメージはまだ肥大化します。
    ```Dockerfile
    RUN {apt commands} \
      && apt-get clean \
      && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*
    ```
- イメージのフラット化
    ```sh
    ID=$(docker run -d image-name /bin/bash)
    docker export $ID | docker import – flat-image-name
    ```
- バックアップ用
    ```sh
    ID=$(docker run -d image-name /bin/bash)
    (docker export $ID | gzip -c > image.tgz)
    gzip -dc image.tgz | docker import - flat-image-name
    ```

### 実行中のコンテナのシステムリソース利用状況を監視する

単一のコンテナのCPU、メモリ、およびネットワークI/Oの使用状況を確認するには、次のコマンドを使用できます：

```sh
docker stats <container>
```

IDでリストされたすべてのコンテナの場合：

```sh
docker stats $(docker ps -q)
```

名前でリストされたすべてのコンテナの場合：

```sh
docker stats $(docker ps --format '{{.Names}}')
```

イメージでリストされたすべてのコンテナの場合：

```sh
docker ps -a -f ancestor=ubuntu
```

タグなしのすべてのイメージを削除する：

```sh
docker rmi $(docker images | grep "^" | awk '{split($0,a," "); print a[3]}')
```

正規表現でコンテナを削除する：

```sh
docker ps -a | grep wildfly | awk '{print $1}' | xargs docker rm -f
```

終了したすべてのコンテナを削除する：

```sh
docker rm -f $(docker ps -a | grep Exit | awk '{ print $1 }')
```

### ボリュームはファイルになり得る

ファイルをボリュームとしてマウントできることに注意してください。たとえば、次のようにして構成ファイルを注入できます：

```sh
# コンテナからファイルをコピー
docker run --rm httpd cat /usr/local/apache2/conf/httpd.conf > httpd.conf

# ファイルを編集
vim httpd.conf

# 修正された構成でコンテナを起動
docker run --rm -it -v "$PWD/httpd.conf:/usr/local/apache2/conf/httpd.conf:ro" -p "80:80" httpd
```

## 貢献

このチートシートへの貢献方法は次のとおりです。

### README.md を開く

[README.md](https://github.com/wsargent/docker-cheat-sheet/blob/master/README.md) のリンクをクリックしてください。 <-- このリンク

![こちらをクリック](../images/click.png)

### ページの編集

![これを編集](../images/edit.png)

### 変更を加えてコミット

![これを変更](../images/change.png)

![コミット](../images/commit.png)

