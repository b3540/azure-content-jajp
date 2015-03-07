﻿<properties
  pageTitle="Azure での Linux およびオープン ソース コンピューティング"
  description="このトピックには、Linux の基本的な使用法、Azure での Linux イメージの実行やアップロードに関するいくつかの基本的な概念、特定のテクノロジと最適化に関するその他のコンテンツなど、Azure での Linux およびオープン ソース コンピューティングの一覧が含まれています。"
  documentationCenter=""
  authors="squillace"
  manager="timlt"
  editor="tysonn"/>

<tags
  ms.service="virtual-machines"
  ms.devlang="NA"
  ms.topic="article"
  ms.tgt_pltfrm="vm-linux"
  ms.workload="infrastructure-services"
  ms.date="02/17/2015"
  ms.author="rasquill"/>


<!--The next line, with one pound sign at the beginning, is the page title-->
# Azure での Linux およびオープン ソース コンピューティング

このドキュメントは、Microsoft とそのパートナーによって書かれた、Microsoft Azure での Linux ベースの仮想マシンの実行およびその他のオープン ソースのコンピューティング環境とアプリケーションに関するすべてのトピックをまとめて一覧にしたものです。Azure とオープン ソース コンピューティング環境はめまぐるしく変化するため、常に新しいトピックを追加し、時代遅れになったものは削除する努力を続けています*が*、このドキュメントが既に時代遅れになっていることはほぼ確実です。見落としがありましたら、コメントでお知らせいただくか、[Github リポジトリ](https://github.com/Azure/azure-content/)にプル要求を送信してください。

セクションは次のように分類されています (トピックが複数のコンセプト、ディストリビューション、テクノロジに関連しているため、複数のセクションにリンクが出現します)。

- [ディストリビューション](#distros) &mdash; 特定のディストリビューションに関係するトピックです。
- [基本](#basics) &mdash; 知っているか知る必要があるさまざまな基本的な事項です。
- [コミュニティのイメージとリポジトリ](#images) &mdash; その他の役に立つ情報、リポジトリ、およびバイナリの場所を示しています。
- [言語とプラットフォーム](#langsandplats)
- [サンプルとスクリプト](#samples)
- [認証と暗号化](#security) &mdash; 重要なセキュリティ関連のトピック。必ずしも Azure に限定されていません。
- [開発、管理、および最適化](#devops) &mdash; 頻繁に変更になる大規模なカテゴリ。
- [サポート、トラブルシューティング、および "うまくいかない場合"](#supportdebug) &mdash; 実際の対応。

さらに、さまざまな Linux オプション、イメージ リポジトリ、ケース スタディに関するトピック、および独自のカスタム イメージをアップロードする操作方法に関するトピックがあります。 

- [Azure Marketplace](http://azure.microsoft.com/marketplace/virtual-machines/)
- [MSOpenTech VM Depot](https://vmdepot.msopentech.com/List/Index)
- [イベントおよびデモンストレーション:Microsoft Openness CEE](http://www.opennessatcee.com/)
- [方法: 独自のディストリビューション イメージのアップロード](http://azure.microsoft.com/documentation/articles/virtual-machines-linux-create-upload-vhd/) (および [Azure での動作保証済みディストリビューション](http://azure.microsoft.com/documentation/articles/virtual-machines-linux-endorsed-distributions/)の使用手順)
- [注:Azure で実行するための Linux の一般的な要件](http://azure.microsoft.com/documentation/articles/virtual-machines-linux-create-upload-vhd-generic/)
- [注:Azure 上の Linux の概要](http://azure.microsoft.com/documentation/articles/virtual-machines-linux-introduction/)


### <a id='distros'>ディストリビューション</a>

大量の Linux ディストリビューションがあり、通常はパッケージ管理システムによって分類されています。Debian、Ubuntu などの dpgk ベースのものや、CentOS、SUSE、RedHat などの rpm ベースのものがあります。一部の企業は Microsoft の正式なパートナーとしてディストリビューション イメージを提供しており、そのイメージは動作保証されています。その他は、コミュニティによって提供されます。このセクションのディストリビューションには、それが他のテクノロジの例としてのみ使用されている場合でも、公式な記事があります。


#### [Ubuntu](http://azure.microsoft.com/marketplace/partners/Canonical/)

Ubuntu は、dkpg および apt-get パッケージ管理に基づく、非常に人気のある、Azure での動作保証済み Linux ディストリビューションです。

1. [方法:独自の Ubuntu イメージのアップロード](http://azure.microsoft.com/documentation/articles/virtual-machines-linux-create-upload-vhd-ubuntu/)
2. [方法:Ubuntu LAMP スタック](http://azure.microsoft.com/documentation/articles/virtual-machines-linux-install-lamp-stack/)
2. [イメージ:LAPP スタック](http://azure.microsoft.com/marketplace/partners/bitnami/lappstack54310ubuntu1404/)
3. [方法:MySQL クラスター](http://azure.microsoft.com/documentation/articles/virtual-machines-linux-mysql-cluster/)
4. [方法:Node.js および Cassandra](http://azure.microsoft.com/documentation/articles/virtual-machines-linux-nodejs-running-cassandra/)
5. [方法:IPython Notebook](http://azure.microsoft.com/documentation/articles/virtual-machines-python-ipython-notebook/)
6. [詳細:Docker コンテナーを使用して Linux で ASP.NET 5 を実行する](http://blogs.msdn.com/b/webdev/archive/2015/01/14/running-asp-net-5-applications-in-linux-containers-with-docker.aspx)
7. [イメージ:Redis サーバー](http://azure.microsoft.com/marketplace/partners/cognosys/redisserver269ubuntu1204lts/)
8. [イメージ:Minecraft Server](http://azure.microsoft.com/marketplace/partners/bitnami/craftbukkitminecraft179r030ubuntu1210/)
9. [イメージ:Moodle](http://azure.microsoft.com/marketplace/partners/bitnami/moodle270ubuntu1404/)
11. [イメージ:サービスとしての Mono](http://azure.microsoft.com/marketplace/partners/aegis/monoasaserviceubuntu1204/)

#### [Debian](https://vmdepot.msopentech.com/List/Index?sort=Featured&search=Debian)

Debian は、dpgk および apt-get パッケージ管理に基づく、Linux およびオープン ソース環境における重要なディストリビューションです。MSOpenTech VM Depot では複数のイメージを使用します。.

#### CentOS

CentOS Linux ディストリビューションは、Red Hat Enterprise Linux (RHEL) のソースから派生した、安定した、予測可能で管理しやすい再現可能なプラットフォームです。

1. [MSOpenTech VM Depot](https://vmdepot.msopentech.com/List/Index?sort=Featured&search=centos)
2. [イメージ ギャラリー](http://azure.microsoft.com/en-in/marketplace/partners/OpenLogic/)
3. [方法:Azure 用のカスタム CentOS ベースの VM の準備](http://azure.microsoft.com/documentation/articles/virtual-machines-linux-create-upload-vhd-centos/)
4. [ブログ:OpenLogic から CentOS 仮想マシン イメージをデプロイする方法](http://azure.microsoft.com/blog/2013/01/11/deploying-openlogic-centos-images-on-windows-azure-virtual-machines/)
6. [方法:AMQP および Service Bus 用の Apache Qpid Proton-C のインストール](http://msdn.microsoft.com/library/azure/dn235560.aspx)
7. [イメージ:OpenLogic CentOS 6.3 上の Apache 2.2.15](http://azure.microsoft.com/marketplace/partners/cognosys/apache2215onopenlogiccentos63/)
8. [イメージ:OpenLogic CentOS 6.3 上の Drupal 7.2、LAMP サーバー](http://azure.microsoft.com/marketplace/partners/cognosys/drupal720lampserveronopenlogiccentos63/)

#### SUSE Enterprise Linux and OpenSUSE

9. [MSOpenTech VM Depot](https://vmdepot.msopentech.com/List/Index?sort=Featured&search=OpenSUSE)
11. [方法:MySQL のインストールと実行](http://azure.microsoft.com/documentation/articles/virtual-machines-linux-mysql-use-opensuse/)
12. [方法:カスタム SLES または openSUSE VM の準備](http://azure.microsoft.com/documentation/articles/virtual-machines-linux-create-upload-vhd-suse/)  
13. [[SUSE フォーラム] 方法:新しいパッチ サーバーへの移動](https://forums.suse.com/showthread.php?5622-New-Update-Infrastructure)
14. [イメージ:SAP Cloud Appliance Library の SUSE Linux Enterprise Server](http://azure.microsoft.com/marketplace/partners/suse/suselinuxenterpriseserver11sp3forsapcloudappliance/)

#### CoreOS

CoreOS は、カスタマイズを細かく制御できる純粋なコンピューティングのスケーリングのための軽量で最適化されたディストリビューションです。

10. [イメージ ギャラリー](http://azure.microsoft.com/en-in/marketplace/partners/coreos/)  
11. [方法:Azure での CoreOS の使用](http://azure.microsoft.com/documentation/articles/virtual-machines-linux-coreos-how-to/)
12. [ブログ:TechEd Europe -- Windows Docker クライアント と Linux コンテナー](http://azure.microsoft.com/blog/2014/10/28/new-docker-coreos-topics-linux-on-azure/)
13. [ブログ:ますます大規模、高速、オープンになる Azure](http://azure.microsoft.com/blog/2014/10/20/azures-getting-bigger-faster-and-more-open/)
14. [Github:Azure で CoreOs をデプロイするためのクイック スタート](https://github.com/timfpark/coreos-azure)
15. [Github:Spring Boot、MongoDB、および CoreOS での Java アプリのデプロイ](https://github.com/chanezon/azure-linux/tree/master/coreos/cloud-init)

#### [Oracle Linux](http://azure.microsoft.com/marketplace/?term=Oracle+Linux)
  2. [Azure 用の Oracle Linux 仮想マシンの準備](http://azure.microsoft.com/documentation/articles/virtual-machines-linux-create-upload-vhd-oracle/)

#### FreeBSD

12. [MSOpenTech VM Depot](https://vmdepot.msopentech.com/List/Index?sort=Date&search=FreeBSD)
13. [ブログ:Azure での FreeBSD の実行](http://azure.microsoft.com/blog/2014/05/22/running-freebsd-in-azure/)
14. [ブログ:FreeBSD の簡単なデプロイ](http://msopentech.com/blog/2014/10/24/easy-deploy-freebsd-microsoft-azure-vm-depot/)
15. [ブログ:カスタマイズされた FreeBSD イメージのデプロイ](http://msopentech.com/blog/2014/05/14/deploy-customize-freebsd-virtual-machine-image-microsoft-azure/)
17. [方法:Azure Linux エージェントのインストール](http://azure.microsoft.com/documentation/articles/virtual-machines-linux-agent-user-guide/)
18. [Marketplace:Linux ファイル サーバーの Kaspersky AV](http://azure.microsoft.com/marketplace/partners/kaspersky-lab/kav-for-lfs-kav-for-lfs/)

### <a id='basics'>基本</a>

1. [基本:Azure コマンド ライン インターフェイス (cli)](http://azure.microsoft.com/documentation/articles/xplat-cli/)
4. [基本:証明書の使用と管理](http://msdn.microsoft.com/library/azure/gg981929.aspx)
5. [基本:Linux ユーザー名の選択](http://azure.microsoft.com/documentation/articles/virtual-machines-linux-usernames/)
6. [基本:Azure ポータルを使用した Linux VM へのログオン](http://azure.microsoft.com/documentation/articles/virtual-machines-linux-how-to-log-on/)
7. [基本:SSH](http://azure.microsoft.com/documentation/articles/virtual-machines-linux-use-ssh-key/)
8. [基本:Linux 用のパスワードまたは SSH プロパティをリセットする方法](http://azure.microsoft.com/documentation/articles/virtual-machines-linux-use-vmaccess-reset-password-or-ssh/)
9. [基本:ルートの使用](http://azure.microsoft.com/documentation/articles/virtual-machines-linux-use-root-privileges/)
10. [基本:Linux VM へのデータ ディスクの接続](http://azure.microsoft.com/documentation/articles/virtual-machines-linux-how-to-attach-disk/)
11. [基本:Linux VM からのデータ ディスクの切断](http://azure.microsoft.com/documentation/articles/virtual-machines-linux-how-to-detach-disk/)
12. [基本に関するブログ:Linux と Azure での記憶域、ディスク、およびパフォーマンスの最適化](http://blogs.msdn.com/b/igorpag/archive/2014/10/23/azure-storage-secrets-and-linux-i-o-optimizations.aspx)
13. [基本:RAID](http://azure.microsoft.com/documentation/articles/virtual-machines-linux-configure-raid/)
14. [基本:テンプレートを作成するための Linux VM のキャプチャ](http://azure.microsoft.com/documentation/articles/virtual-machines-linux-capture-image/)
15. [基本:Azure Linux エージェント](http://azure.microsoft.com/documentation/articles/virtual-machines-linux-agent-user-guide/)
16. [基本:Azure VM 拡張機能と機能](http://msdn.microsoft.com/library/azure/dn606311.aspx)
17. [基本:Cloud-init で使用するカスタム データの VM への挿入](http://azure.microsoft.com/documentation/articles/virtual-machines-how-to-inject-custom-data/)
18. [基本に関するブログ:12 の手順から成る Azure 上での高可用性 Linux の構築](http://blogs.technet.com/b/keithmayer/archive/2014/10/03/quick-start-guide-building-highly-available-linux-servers-in-the-cloud-on-microsoft-azure.aspx)
19. [基本に関するブログ:xplat、node.js、jhawk を使用した、Azure 上での Linux のプロビジョニングの自動化](http://blogs.technet.com/b/keithmayer/archive/2014/11/24/step-by-step-automated-provisioning-for-linux-in-the-cloud-with-microsoft-azure-xplat-cli-json-and-node-js-part-1.aspx)
20. [基本:Azure の Docker VM 拡張機能](http://azure.microsoft.com/documentation/articles/virtual-machines-docker-vm-extension/)
23. [Azure サービス管理 REST API](https://msdn.microsoft.com/ja-jp/library/azure/ee460799.aspx) リファレンス

### <a id='images'>コミュニティのイメージとリポジトリ</a>
3. [MSOpenTech VM Depot](https://vmdepot.msopentech.com/List/Index) &mdash; 仮想マシン イメージを提供するコミュニティ用
4. [Github](https://github.com/Azure/) &mdash; xplat-cli およびその他のさまざまなツールとプロジェクト用
5. [Docker ハブ レジストリ](https://registry.hub.docker.com/) &mdash; Docker コンテナー イメージ用レジストリ


### <a id='langsandplats'>言語とプラットフォーム</a>
#### [Azure Java デベロッパー センター](http://azure.microsoft.com/develop/java/)

1. [イメージ](https://vmdepot.msopentech.com/List/Index?sort=Featured&search=java)
2. [方法:AMQP 1.0 を使用した Java からの Service Bus の使用](http://msdn.microsoft.com/library/azure/jj841073.aspx)
3. [方法:Azure ポータルを使用した Linux 上での Tomcat7 の設定](http://azure.microsoft.com/documentation/articles/virtual-machines-linux-setup-tomcat7-linux/)
4. [ビデオ:サービス管理用の Azure Java SDK](http://channel9.msdn.com/Shows/Cloud+Cover/Episode-157-The-Java-SDK-for-Azure-Management-with-Brady-Gaster)
5. [ブログ:Java 用 Azure 管理ライブライの概要](http://azure.microsoft.com/blog/2014/09/15/getting-started-with-the-azure-java-management-libraries/)
5. [GitHub のリポジトリ:Azure Toolkit for Eclipse with Java](https://github.com/MSOpenTech/WindowsAzureToolkitForEclipseWithJava)
6. [リファレンス:Azure Toolkit for Eclipse with Java](http://msdn.microsoft.com/library/azure/hh694271.aspx)
7. [GitHub のリポジトリ:IntelliJ IDEA と Android Studio 用の MS Open Tech ツールのプラグイン](https://github.com/MSOpenTech/msopentech-tools-for-intellij)
7. [ブログ:OpenJDK での MSOpenTech の使用](http://msopentech.com/blog/2014/10/21/ms-open-techs-first-contribution-openjdk/)
8. [イメージ:WebSphere](http://azure.microsoft.com/marketplace/partners/msopentech/was-8-5-was-8-5-5-3/)
9. [イメージ:WebLogic](http://azure.microsoft.com/marketplace/?term=weblogic)
10. [イメージ:Windows 上の JDK6](http://azure.microsoft.com/marketplace/partners/msopentech/jdk6onwindowsserver2012/)
11. [イメージ:Windows 上の JDK7](http://azure.microsoft.com/marketplace/partners/msopentech/jdk7onwindowsserver2012/)
12. [イメージ:Windows 上の JDK8](http://azure.microsoft.com/marketplace/partners/msopentech/jdk8onwindowsserver2012r2/)

#### JVM 言語

1. [スカラ:Azure Cloud Services での Play Framework アプリケーションの実行](http://msopentech.com/blog/2014/09/25/tutorial-running-play-framework-applications-microsoft-azure-cloud-services-2/)

#### SDK の種類、インストール、アップグレード
4. [Azure サービス管理 SDK:Java](http://dl.windowsazure.com/javadoc/)
5. [Azure サービス管理 SDK:Go](https://github.com/MSOpenTech/azure-sdk-for-go)
5. [Azure サービス管理 SDK:Ruby](https://github.com/MSOpenTech/azure-sdk-for-ruby)
- [方法:Ruby on Rails のインストール](http://azure.microsoft.com/documentation/articles/virtual-machines-ruby-rails-web-app-linux/)
- [方法:Capistrano、Nginx、Unicorn、PostgreSQL を使用した、Ruby on Rails のインストール](http://azure.microsoft.com/documentation/articles/virtual-machines-ruby-deploy-capistrano-host-nginx-unicorn/)
6. [Azure サービス管理 SDK:Python](https://github.com/Azure/azure-sdk-for-python)
- [方法:Django Hello World Web アプリケーション (Mac-Linux)](http://azure.microsoft.com/documentation/articles/virtual-machines-python-django-web-app-linux/)
7. [Azure サービス管理 SDK:Node.js](https://github.com/MSOpenTech/azure-sdk-for-node)
8. [Azure サービス管理 SDK:PHP](https://github.com/MSOpenTech/azure-sdk-for-php)
- [方法:Azure VM での LAMP スタックのインストール](http://azure.microsoft.com/documentation/articles/virtual-machines-linux-install-lamp-stack/)
- [ビデオ:Azure VM での LAMP スタックのインストール](http://channel9.msdn.com/Shows/Azure-Friday/LAMP-stack-on-Azure-VMs-with-Guy-Bowerman)
9. [Azure サービス管理 SDK:.NET](https://github.com/Azure/azure-sdk-for-net)
10. [ブログ:Mono、ASP.NET 5、Linux、Docker](http://blogs.msdn.com/b/webdev/archive/2015/01/14/running-asp-net-5-applications-in-linux-containers-with-docker.aspx)

### <a id='samples'>サンプルとスクリプト</a>

1. [Patrick Chanezon の Azure Linux Github リポジトリ](https://github.com/chanezon/azure-linux)
3. [ビデオ:**usbip** を使用して Linun のオンプレミス USB データを Azure に移動する方法](http://channel9.msdn.com/Blogs/Open/On-premises-USB-devices-on-Linux-on-Azure-via-usbip)
4. [ビデオ:fernapp を使用してブラウザーで Azure の Linux ベースの GUI にアクセスする](http://channel9.msdn.com/Blogs/Open/Accessing-Linux-based-GUI-on-Azure-over-browser-with-fernapp)
5. [ビデオ:Azure ファイル プレビューを使用した Linux 上の共有記憶域 - パート 1](http://channel9.msdn.com/Blogs/Open/Shared-storage-on-Linux-via-Azure-Files-Preview-Part-1)
6. [ビデオ:Service Bus と Web Sites を使用して Azure 上に Linux デバイスを組み込む](http://channel9.msdn.com/Blogs/Open/Embracing-Linux-devices-on-Azure-via-Service-Bus-and-Web-Sites)
7. [ビデオ:ネイティブ Linux ベースの memcached アプリケーションを Microsoft Azure に接続する](http://channel9.msdn.com/Blogs/Open/Connecting-a-Linux-based-native-memcache-application-to-Windows-Azure)
8. [ビデオ:Azure 上の 高可用性 Linux サービスの負荷分散OpenLDAP と MySQL](http://channel9.msdn.com/Blogs/Open/Load-balancing-highly-available-Linux-services-on-Windows-Azure-OpenLDAP-and-MySQL)


### <a id='data'>データ</a>

このセクションには、NoSQL、リレーショナル、ビッグ データなどの異なる複数の記憶域のアプローチとテクノロジに関する情報が含まれています。

#### Nosql

1. [ブログ:Azure の 8 つのオープン ソースの NoSql データベース](http://openness.microsoft.com/blog/2014/11/03/open-source-nosql-databases-microsoft-azure/)
2. Couchdb
- [Slideshare (MSOpenTech):Azure 上の CouchDb とエクスペリエンス](http://www.slideshare.net/brianbenz/experiences-using-couchdb-inside-microsofts-azure-team)
- [ブログ:node.js、CORS、および Grunt を使用して CouchDB をサービスとして実行する](http://msopentech.com/blog/2013/12/19/tutorial-building-multi-tier-windows-azure-web-application-use-cloudants-couchdb-service-node-js-cors-grunt-2/)
3. MongoDB
- [方法:MongoLab アドオンを使用して Azure で MongoDB 対応の Node.js アプリケーションを作成する](http://azure.microsoft.com/documentation/articles/store-mongolab-web-sites-nodejs-store-data-mongodb/)
4. Cassandra
- [方法:Azure 上の Linux で Cassandra を実行して Node.js からアクセスする](http://azure.microsoft.com/documentation/articles/virtual-machines-linux-nodejs-running-cassandra/)
5. Redis
- [ブログ:Azure Redis Cache Service での Redis on Windows](http://msopentech.com/blog/2014/05/12/redis-on-windows/)
- [ブログ:Redis のプレビュー リリースの ASP.NET セッション状態プロバイダーの通知](http://blogs.msdn.com/b/webdev/archive/2014/05/12/announcing-asp-net-session-state-provider-for-redis-preview-release.aspx)
6. RavenHQ
- [ブログ:Azure Marketplace での RavenHQ の提供開始](http://azure.microsoft.com/blog/2014/08/12/ravenhq-now-available-in-the-azure-store/)

#### ビッグ データ
2. Hadoop/Cloudera  
	- [ブログ:Azure Linux VM 上での Hadoop のインストール](http://blogs.msdn.com/b/benjguin/archive/2013/04/05/how-to-install-hadoop-on-windows-azure-linux-virtual-machines.aspx)
	- [方法:HDInsight を使用して Hadoop と Hive を使用する](http://azure.microsoft.com/documentation/articles/hdinsight-get-started/)  
3. [Azure HDInsight](http://azure.microsoft.com/services/hdinsight/) -- Azure 上で Hadoop サービスを完全に管理する

#### リレーショナル データ
2. MySQL
- [方法:MySQL のインストールと実行](http://azure.microsoft.com/documentation/articles/virtual-machines-linux-mysql-use-opensuse/)
- [方法:Azure での MySQL パフォーマンスの最適化](http://azure.microsoft.com/documentation/articles/virtual-machines-linux-optimize-mysql-perf/)
- [方法:MySQL クラスター](http://azure.microsoft.com/documentation/articles/virtual-machines-linux-mysql-cluster/)
- [方法:Marketplace を使用して MySQL データベースを作成する](http://azure.microsoft.com/documentation/articles/store-php-create-mysql-database/)
- [方法:Python と Visual Studio を使用した Azure Websites の Django と MySQL](http://azure.microsoft.com/documentation/articles/web-sites-python-ptvs-django-mysql/)
- [方法:WebMatrix を使用した Azure Websites のPHP と MySQL](http://azure.microsoft.com/documentation/articles/web-sites-php-mysql-use-webmatrix/)
7. MariaDB
- [方法:MariaDbs の複数のマスター クラスターを作成する](http://azure.microsoft.com/documentation/articles/virtual-machines-mariadb-cluster/)
7. PostgreSQL
- [方法:Capistrano、Nginx、Unicorn、PostgreSQL を使用した、Ruby on Rails のインストール](http://azure.microsoft.com/documentation/articles/virtual-machines-ruby-deploy-capistrano-host-nginx-unicorn/)


### <a id='security'>認証と暗号化</a>

認証と暗号化は、ソフトウェア開発において、重要なトピックで、Web 上には、適切なセキュリティ手法の学習と使用方法に関して膨大な数のトピックが存在します。ここでは、Linux とオープンソース ワークロードを使用して起動し、すばやく実行するための基本的な用法、および Azure でのリモート セキュリティ機能のリセットや削除に使用するツールについていくつか説明します。

4. [基本:証明書の使用と管理](http://msdn.microsoft.com/library/azure/gg981929.aspx)
7. [基本:SSH](http://azure.microsoft.com/documentation/articles/virtual-machines-linux-use-ssh-key/)
8. [基本:Linux 用のパスワードまたは SSH プロパティをリセットする方法](http://azure.microsoft.com/documentation/articles/virtual-machines-linux-use-vmaccess-reset-password-or-ssh/)
9. [基本:ルートの使用](http://azure.microsoft.com/documentation/articles/virtual-machines-linux-use-root-privileges/)

### <a id='devops'>開発、管理、および最適化</a>

このセクションでは、まずビデオ シリーズを含むブログ エントリを紹介します。[ビデオ: Azure Virtual Machines、Chef、Puppet および、Docker を使用した Linux VM の管理](http://azure.microsoft.com/blog/2014/12/15/azure-virtual-machines-using-chef-puppet-and-docker-for-managing-linux-vms/)。ただし、開発、管理、そして最適化は、非常に幅広く、かつ変化の激しい世界です。そのため、次に挙げる事項を最初に考慮する必要があります。

1. Docker
	- [Azure での Linux 用 Docker VM の拡張機能](http://azure.microsoft.com/documentation/articles/virtual-machines-docker-vm-extension/)
	- [Azure クロスプラットフォーム コマンド ライン インターフェイス (xplat-cli) での Docker VM 拡張機能の使用](http://azure.microsoft.com/documentation/articles/virtual-machines-docker-with-xplat-cli/)
	- [Azure プレビュー ポータルでの Docker VM 拡張機能の使用](http://azure.microsoft.com/documentation/articles/virtual-machines-docker-with-portal/)
	- [Azure Marketplace での Docker のすばやい開始](http://azure.microsoft.com/documentation/articles/virtual-machines-docker-ubuntu-quickstart/)
2. [CoreOS での Fleet](http://azure.microsoft.com/documentation/articles/virtual-machines-linux-coreos-how-to/)
3. Deis
	- [GitHub のリポジトリ:
Azure 上の CoreOs クラスターへの Deis のインストール](https://github.com/chanezon/azure-linux/tree/master/coreos/deis)
4. Kubernetes
	- [Kubernetes Visualizer](http://azure.microsoft.com/blog/2014/08/28/hackathon-with-kubernetes-on-azure)
5. Jenkins と Hudson
	- [ブログ:Azure 用 Jenkins スレーブ プラグイン](http://msopentech.com/blog/2014/09/23/announcing-jenkins-slave-plugin-azure/)
	- [GitHub のリポジトリ:Azure 用 Jenkins ストレージ プラグイン](https://github.com/jenkinsci/windows-azure-storage-plugin)
	- [サード パーティ:Azure 用 Hudson スレーブ プラグイン](http://wiki.hudson-ci.org/display/HUDSON/Azure+Slave+Plugin)
	- [サード パーティ:Azure 用 Hudson ストレージ プラグイン](https://github.com/hudson3-plugins/windows-azure-storage-plugin)
10. Chef
	- [Chef と仮想マシン](http://azure.microsoft.com/documentation/articles/virtual-machines-windows-install-chef-client/)
	- [ビデオ:Chef の概要とそのしくみ](https://msopentech.com/blog/2014/03/31/using-chef-to-manage-azure-resources/)

12. Azure Automation
	- [ビデオ:Linux VM で Azure Automation を使用する方法](http://channel9.msdn.com/Shows/Azure-Friday/Azure-Automation-104-managing-Linux-and-creating-Modules-with-Joe-Levy)
13. Linux 用の Powershell DSC
    - [ブログ:Linux 用 Powershell DSC を実行する方法](http://blogs.technet.com/b/privatecloud/archive/2014/05/19/powershell-dsc-for-linux-step-by-step.aspx)
    - [Github:Docker クライアント DSC](https://github.com/anweiss/DockerClientDSC)
13. [Ubuntu Juju](https://juju.ubuntu.com/docs/config-azure.html)

### <a id='supportdebug'>サポート、トラブルシューティング、および "うまくいかない場合"</a>

1. Microsoft サポート ドキュメント
	- [サポート:Microsoft Azure での Linux イメージのサポート](http://support2.microsoft.com/kb/2941892)


<!--Anchors-->
[ディストリビューション]: #distros
[基本]: #basics
[コミュニティのイメージとリポジトリ]: #images
[言語とプラットフォーム]: #langsandplats
[サンプルとスクリプト]: #samples
[認証と暗号化]: #security
[開発、管理、および最適化]: #devops
[サポート、トラブルシューティング、および "うまくいかない場合"]: #supportdebug

<!--HONumber=45--> 