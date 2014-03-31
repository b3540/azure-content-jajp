#データ管理とビジネス分析

クラウドでのデータの管理と分析は、クラウド以外での場合と同様に重要です。この作業を行うために、Windows Azure ではリレーショナル データおよび非リレーショナル データを操作するためのさまざまなテクノロジが用意されています。この記事では、各オプションを紹介します。

##目次

- [BLOB ストレージ](#blob)
- [仮想マシンでの DBMS の実行](#dbinvm)
- [SQL データベース](#sqldb)
	- [SQL データ同期](#datasync)
	- [仮想マシンを使用した SQL データ レポート](#datarpt)
- [テーブル ストレージ](#tblstor)
- [Hadoop](#hadoop)

## <a name="blob"></a>BLOB ストレージ

"BLOB" という単語は "Binary Large OBject" の略語で、BLOB がバイナリ情報のコレクションであることを正確に表しています。BLOB は単純ではありますが、非常に便利です。[図 1](#Fig1) は、Windows Azure BLOB ストレージの基礎を示しています。

<a name="Fig1"></a>![BLOB の図][blobs]
 
**図 1: Windows Azure BLOB ストレージはバイナリ データ (BLOB) をコンテナーに格納する**

BLOB を使用するには、まず、Windows Azure *ストレージ アカウント*を作成します。その一環として、このアカウントを使用して作成するオブジェクトを格納する Windows Azure データセンターを指定します。作成される各 BLOB は、どこにあるとしても、ストレージ アカウント内のいずれかのコンテナーに属します。BLOB にアクセスするために、アプリケーションは次の形式で URL を用意します。

http://&lt;*StorageAccount*&gt;.blob.core.windows.net/&lt;*Container*&gt;/&lt;*BlobName*&gt;

&lt;*StorageAccount*&gt; は新しいストレージ アカウントが作成されたときに割り当てられる一意の識別子で、&lt;*Container*&gt; および &lt;*BlobName*&gt; は、特定のコンテナーの名前と、そのコンテナー内の BLOB の名前です。

Windows Azure には、2 つの異なる種類の BLOB があります。選択肢は以下のとおりです。

- *ブロック* BLOB: 各ブロック BLOB には、最大 200 GB のデータを格納できます。名前からわかるように、ブロック BLOB はいくつかのブロックに分割されています。ブロック BLOB の転送中に障害が発生した場合は、BLOB 全体を送信し直さなくても、最後のブロックを再転送するだけで再送信を再開できます。ブロック BLOB はストレージに対する非常に一般的なアプローチであり、現在最も広く使用されている種類の BLOB です。

- *ページ* BLOB: 各ページ BLOB の最大容量は 1 TB です。ページ BLOB はランダム アクセス用に設計されており、各ページ BLOB はいくつかのページに分割されます。BLOB 内の各ページに対し、アプリケーションはランダムに読み書きを実行できます。たとえば、Windows Azure の仮想マシンでは、作成した VM がページ BLOB を OS ディスクとデータ ディスクの両方の永続的なストレージとして使用します。

ブロック BLOB とページ BLOB のどちらを選択するかによって、アプリケーションが BLOB データにアクセスする方法が異なります。方法には、次のようなものがあります。

- REST ベース (つまり HTTP ベース) のアクセス プロトコルを通じた直接アクセス。Windows Azure アプリケーションも外部アプリケーションも (内部設置型で実行されているアプリケーションも含めて)、この方法を使用できます。
- Windows Azure のストレージ クライアント ライブラリを使用。このライブラリによって、直接の REST ベースの BLOB アクセス プロトコル上に、開発者にとってより使いやすいインターフェイスが提供されます。この方法でも、Windows Azure アプリケーションと外部アプリケーションの両方が、このライブラリを使用して BLOB にアクセスできます。
- Windows Azure Drive を使用。この方法では、Windows Azure アプリケーションがページ BLOB を NTFS ファイル システムのローカル ドライブとして扱うことができます。アプリケーションにとって、ページ BLOB は標準ファイル I/O を使用してアクセスする通常の Windows ファイル システムのように見えます。実際には、読み取りと書き込みは、Windows Azure Drive を実装している基になるページ BLOB に送信されます。

ハードウェアの障害に備え、可用性を向上させるために、各 BLOB は Windows Azure データセンター内の 3 台のコンピューター間で複製されます。BLOB に書き込みを行うと、3 つのコピーがすべて更新されるので、以降の読み取りで結果の一貫性が損なわれることはありません。BLOB のデータが同じ Geo の少なくとも 500 マイル離れた他の Windows Azure データセンターにコピーされるように指定することもできます。この *Geo レプリケーション*と呼ばれるコピーは、BLOB の更新後数分以内に行われ、障害復旧のために役立ちます。

BLOB 内のデータは、Windows Azure の*コンテンツ配信ネットワーク (CDN)* を通じて利用可能にすることもできます。BLOB データのコピーを世界中の多数のサーバーにキャッシュすることによって、CDN は繰り返しアクセスされる情報へのアクセスを高速化できます。

BLOB は単純なので、さまざまな状況に適しています。ビデオおよびオーディオの格納とストリーミングが最適な例で、バックアップやその他の種類のデータ アーカイブも同様です。開発者は BLOB を使って任意の種類の非構造化データを保持することもできます。バイナリ データの格納およびアクセスのための簡単な方法があることは、非常に便利です。


## <a name="dbinvm"></a>仮想マシンでの DBMS の実行

今日の多くのアプリケーションは、何らかの種類のデータベース管理システム (DBMS) に依存しています。SQL Server のようなリレーショナル システムが最も頻繁に使用されますが、一般に *NoSQL* テクノロジと呼ばれる非リレーショナルな方法も、しだいに利用されるようになってきています。クラウド アプリケーションがこれらのデータ管理のオプションを使用できるようにするために、Windows Azure の仮想マシンでは VM 上で DBMS (リレーショナルまたは NoSQL) を実行できます。[図 2](#Fig2) は、SQL Server でのこのしくみを示しています。

<a name="Fig2"></a>![仮想マシンでの SQL Server の図][SQLSvr-vm]
 
**図 2: Windows Azure の仮想マシンを使用すると、BLOB によって提供される永続性を利用して VM で DBMS を実行できる**

開発者にとってもデータベース管理者にとっても、このシナリオは自社のデータセンターで同じソフトウェアを実行しているように見えます。たとえば、ここで示した例では、SQL Server のほぼすべての機能を使用することができ、システムに対して完全な管理アクセス権を持つことができます。もちろん、ローカルで実行する場合と同様に、データベース サーバーを管理する責任も生じます。

[図 2](#Fig2) に示されているように、データベースはサーバーが実行されている VM のローカル ディスクに格納されているように見えます。しかし、実際には、これらの各ディスクは Windows Azure BLOB に書き込まれています (自社のデータセンターで SAN を使用する場合と似ていて、BLOB は LUN のように機能します)。Windows Azure BLOB と同様に、含まれているデータはデータセンター内で 3 回複製され、要請された場合は同じリージョンの他のデータセンターに Geo レプリケーションされます。信頼性の向上のため、SQL Server データベース ミラーリングのようなオプションも使用できます。

VM で SQL Server を使用するもう 1 つの方法では、ハイブリッド アプリケーションを作成します。このアプリケーションでは、アプリケーション ロジックは内部設置型で実行され、データは Windows Azure 上に存在します。たとえば、複数の場所やさまざまなモバイル デバイスで実行されるアプリケーションが同じデータを共有する必要がある場合に、このことが役立ちます。クラウド データベースと内部設置型のロジック間の通信をより単純にするために、Windows Azure の仮想ネットワークを使用して、Windows Azure データセンターと自社の内部設置型データセンター間に仮想プライベート ネットワーク (VPN) 接続を作成できます。


## <a name="sqldb"></a>SQL データベース

クラウドで構造化データを管理する場合、多くの人が最初に思いつくのは、VM で DBMS を実行する方法です。しかし、それは唯一の方法ではなく、常に最適な方法というわけでもありません。場合によっては、サービスとしてのプラットフォーム (PaaS) というアプローチを使用してデータを管理する方が便利です。Windows Azure では、それをリレーショナル データで行うことができる、SQL データベースと呼ばれる PaaS テクノロジが用意されています。[図 3](#Fig3) は、このオプションを示しています。

<a name="Fig3"></a>![SQL データベースの図][SQL-db]
 
**図 3: SQL データベースには共有 PaaS リレーショナル ストレージ サービスが用意されている**

SQL データベースは、各ユーザーに独自の SQL Server の物理インスタンスを提供するわけではありません。代わりに、ユーザーごとの論理 SQL データベース サーバーを持つマルチテナント サービスを提供します。すべてのユーザーが、サービスによって提供されるコンピューティングとストレージ容量を共有します。BLOB ストレージと同様に、SQL データベースのすべてのデータは、データベースに組み込みの高可用性 (HA) を提供するために、Windows Azure データセンター内の 3 台の異なるコンピューターに格納されます。

アプリケーションにとっては、SQL データベースは SQL Server のように見えます。アプリケーションは、リレーショナル テーブルに対する SQL クエリの発行、T-SQL ストアド プロシージャの使用、および複数のテーブルにまたがるトランザクションの実行を行うことができます。アプリケーションは、SQL Server へのアクセスにも使用される表形式データ ストリーム (TDS) プロトコルを使用して SQL データベースにアクセスするので、Entity Framework、ADO.NET、JDBC、およびその他の使い慣れたデータ アクセス インターフェイスを使用してデータを操作できます。

しかし、SQL データベースは Windows Azure データセンターで実行されるクラウド サービスなので、システムの物理的な側面 (ディスク使用量など) を管理する必要がありません。ソフトウェアの更新やその他の低レベルの管理タスクの処理について心配する必要もありません。もちろん、ユーザー側で自分のデータベースの制御 (スキーマやユーザー ログインの管理) を行うこともできますが、多くの日常的な管理タスクはクラウド側で処理されます。

SQL データベースはアプリケーションからは SQL Server のように見えますが、その動作は物理マシンや仮想マシンで実行される DBMS と正確に同じではありません。共有ハードウェア上で実行されるため、パフォーマンスはそのハードウェアのすべてのユーザーによって課される負荷に応じて変化します。つまり、SQL データベースのストアド プロシージャのパフォーマンスが、日によって異なることが考えられます。

現在、SQL データベースでは、最大で 150 GB のサイズのデータベースを作成できます。より大きなデータベースを扱う必要がある場合のために、*フェデレーション*と呼ばれるオプションが用意されています。この機能を使用するために、データベース管理者は 2 つ以上の*フェデレーション メンバー*を作成します。各メンバーが、独自のスキーマを持つ個別のデータベースになります。データはこれらのメンバーに分散され (このことは、しばしば*シャーディング*と呼ばれます)、各メンバーには一意の*フェデレーション キー*が割り当てられます。アプリケーションはこのデータに対する SQL クエリを発行し、クエリのターゲットとなるフェデレーション メンバーを特定するフェデレーション キーを指定します。こうすることで、大量のデータに対して従来のリレーショナルなアプローチを使用できます。他の機能と同じように、これにもトレードオフがあります。たとえば、クエリもトランザクションも複数のフェデレーション メンバーにまたがることはできません。しかし、リレーショナル PaaS サービスが最適な選択であり、これらのトレードオフが許容できる場合、SQL フェデレーションの使用は良いソリューションになります。

SQL データベースは、Windows Azure やその他の場所 (内部設置型のデータセンターなど) で実行されているアプリケーションで使用できます。このことは、リレーショナル データが必要なクラウド アプリケーションにとっても、クラウドにデータを格納する利点が活かせる内部設置型アプリケーションにとっても便利です。モバイル アプリケーションは、SQL データベースを利用して共有リレーショナル データを管理できる場合があります。たとえば、世界中の複数の販売店で実行される在庫管理アプリケーションのような場合です。

SQL データベースについて考える場合、明らかな (かつ重要な) 問題があります。それは、どのようなときに VM で SQL Server を実行する必要があり、どのようなときに SQL データベースの方が適切な選択であるかということです。この場合にもトレードオフがあるので、どちらのアプローチが適しているかは要件によって異なります。

この問題を考えるための 1 つの簡単な方法は、SQL データベースを新しいアプリケーション用として見ることです。既存の内部設置型アプリケーションをクラウドに移動している場合には、VM での SQL Server がより適した選択です。しかし、この判断をより詳細な方法で検討することが有益な場合もあります。たとえば、SQL データベースはセットアップと管理が最小限で済むため、使いやすくなっています。しかし、VM での SQL Server の実行は、共有サービスではないためにパフォーマンスを予測しやすく、SQL データベースよりも大きな非フェデレーション データベースもサポートされます。一方、SQL データベースではデータと処理の両方の組み込みレプリケーションが提供され、ごくわずかな作業で効率的に可用性の高い DBMS を利用できます。SQL Server はより細かく制御でき、多めのオプションが用意されていますが、SQL データベースはより簡単にセットアップでき、管理作業が大幅に少なくて済みます。

最後に指摘しておく必要がある重要な点は、SQL データベースが Windows Azure で利用可能な唯一の PaaS データ サービスではないということです。Microsoft パートナーによって、その他のオプションも提供されています。たとえば、ClearDB は MySQL PaaS を提供し、Cloudant は NoSQL オプションを販売しています。PaaS データ サービスは多くの状況で適切なソリューションであり、データ管理へのこのアプローチは Windows Azure の重要な部分です。


### <a name="datasync"></a>SQL Data Sync

SQL データベースは 1 つの Windows Azure データセンター内で各データベースの 3 つのコピーを保持しますが、複数の Windows Azure データセンター間でデータを自動的に複製するわけではありません。代わりにその機能を果たすサービスが、SQL データ同期です。[図 4](#Fig4) に、このしくみを示します。

<a name="Fig4"></a>![Diagram of SQL data sync][SQL-datasync]
 
**図 4: SQL データ同期は SQL データベースのデータを 他の Windows Azure および内部設置型データセンターのデータと同期させる**

図に示されているように、SQL データ同期は異なる場所にあるデータを同期させることができます。たとえば、SQL データベースにデータが格納されている複数の Windows Azure データセンターでアプリケーションを実行しているとします。SQL データ同期を使用すると、それらのデータを同期させることができます。SQL データ同期は、Windows Azure データセンターと、内部設置型のデータセンターで実行されている SQL Server のインスタンス間でデータを同期させることもできます。これは、内部設置型のアプリケーションによって使用されるデータのローカル コピーと、Windows Azure で実行されているアプリケーションによって使用されるクラウド コピーの両方を保持するために便利です。また、図には示されていませんが、SQL データ同期は SQL データベースと Windows Azure または他の場所にある VM で実行されている SQL Server 間でデータを同期させるために使用することもできます。

同期は双方向にすることができ、どのデータを同期させるかと、どのくらいの頻度で同期を行うかを指定できます (データベース間の同期はアトミックではありませんが、常に少なくともいくらかの遅延はあります)。また、どのように使用される場合でも、SQL データ同期での同期のセットアップは完全に構成に基づいて行われるので、コードを記述する必要はありません。


### <a name="datarpt"></a>仮想マシンを使用した SQL データ レポート

データベースにデータが記録されていれば、だれかがそのデータを使用してレポートを作成しようとすることが考えられます。Windows Azure は、SQL Server Reporting Services (SSRS) を Windows Azure の仮想マシンで実行することができます。仮想マシンで実行されている SQL Server Reporting Services は、オンプレミスで実行されている SQL Server Reporting Services と機能上は同じです。したがって、Windows Azure SQL データベースに保存されたデータに対し、SSRS を使用してレポートを実行することができます。[図 5](#Fig5) は、この処理のしくみを示しています。

<a name="Fig5"></a>![SQL レポートの図][SQL-report]
 
**図 5: Windows Azure の仮想マシンで動作する SQL Server Reporting Services が、SQL データベースに格納されているデータのレポート サービスを提供.**

ユーザーがレポートを見られるようにするには、だれかがレポートの体裁を定義する必要があります (ステップ 1.)。VM 上の SSRS では、この作業を 2 つのツールを使用して行うことができます。それは、SQL Server 2012 の一部である SQL Server Data Tools と、その前バージョンである Business Intelligence (BI) Development Studio です。SSRS と同様に、これらのレポート定義はレポート定義言語 (RDL) で表現されます。作成されたレポート用 RDL ファイルは、クラウドの VM にアップロードされます (ステップ 2.)。これで、レポート定義を使用する準備ができました。

次に、アプリケーションのユーザーがレポートにアクセスします (ステップ 3.)。アプリケーションはこの要求を SSRS VM に渡します (ステップ 4.)。SQL レポートは SQL データベースなどのデータ ソースに接続して、必要なデータを取得します (ステップ 5.)。SSRS はこのデータおよび関連する RDL ファイルを使用してレポートをレンダリングし (ステップ 6.)、アプリケーションにレポートを返します (ステップ 7.)。これをアプリケーションがユーザーに表示します (ステップ 8.)。

アプリケーションにレポートを埋め込むという、ここで示されているシナリオは、唯一のオプションではありません。VM 上のレポート マネージャー、VM 上の SharePoint などでレポートを表示することもできます。1 つのレポートに他のレポートへのリンクを含めることで、レポートを組み合わせることもできます。

Windows Azure VM 上の SSRS は、クラウドにおけるレポート ソリューションとして完全な機能を備えています。レポートには、SSRS でサポートされているあらゆるデータ ソースを使用できます。アプリケーションとレポートには、埋め込みのコードやアセンブリを追加することで、その動作をカスタマイズすることができます。レポート サーバーのコンテンツとエンジンが同じ仮想サーバー上で一緒に実行されるため、レポートの実行とレンダリングは高速です。



## <a name="tblstor"></a>テーブル ストレージ

リレーショナル データは多くの状況で便利ですが、常に正しい選択とは限りません。たとえば、アプリケーションで大量の大まかに構造化されたデータへの高速で簡単なアクセスが必要な場合、リレーショナル データベースはうまく機能しないことがあります。NoSQL テクノロジが、より適切なオプションになる可能性もあります。

Windows Azure テーブル ストレージは、このような種類の NoSQL アプローチの 1 つの例です。名前とは異なり、テーブル ストレージは標準リレーショナル テーブルをサポートしていません。代わりに、データのセットを特定のキーに関連付ける*キー/値ストア*と呼ばれるものを提供し、アプリケーションがキーを提示することでデータにアクセスできるようにします。[図 6](#Fig6) は、基礎部分を示しています。

<a name="Fig6"></a>![テーブル ストレージの図][SQL-tblstor]
 
**図 6: Windows Azure テーブル ストレージは大量のデータへの高速で簡単なアクセスを提供するキー/値ストアである**

BLOB のように、各テーブルは Windows Azure のストレージ アカウントに関連付けられています。テーブルは BLOB と同じように、次のような形式の URL を使って名前を指定されます。

http://&lt;*StorageAccount*&gt;.table.core.windows.net/&lt;*TableName*&gt;

図で示されているように、各テーブルはいくつかのパーティションに分割され、それぞれを別のコンピューターに格納できます (SQL フェデレーションと同様のシャーディングの形式です)。Windows Azure アプリケーションも、他の場所で実行されるアプリケーションも、REST ベースの OData プロトコルまたは Windows Azure のストレージ クライアント ライブラリを使用してテーブルにアクセスできます。

テーブルの各パーティションにはいくつかの*エンティティ*が保持され、それぞれに 255 個の*プロパティ*が含まれています。各プロパティには、名前、型 (Binary、Bool、DateTime、Int、String など)、および値があります。リレーショナル ストレージとは異なり、これらのテーブルには固定スキーマがないため、同じテーブル内の異なるエンティティが異なる型のプロパティを持つことができます。たとえば、1 つのエンティティが名前を含む String プロパティだけを持ち、同じテーブルの他のエントリが顧客 ID 番号と信用格付けを含む 2 つの Int プロパティを持つ場合があります。

テーブル内の特定のエンティティを識別するために、アプリケーションはそのエンティティのキーを提示します。特定のパーティションを識別する*パーティション キー*と、そのパーティション内のエンティティを識別する*行キー*です。たとえば、[図 6](#Fig6) ではクライアントによってパーティション キーが A で行キーが 3 のエンティティが要求され、テーブル ストレージがそのエンティティと、それに含まれているすべてのプロパティを返します。

このような構造であるため、テーブルは大きくすることができ (1 つのテーブルに最大で 100 TB のデータを含めることが可能)、含まれているデータにすばやくアクセスすることができます。ただし、制限もあります。たとえば、複数のテーブルや、1 つのテーブル内の複数のパーティションにまたがるトランザクション更新がサポートされていません。テーブルに対する一連の更新は、対象となるすべてのエンティティが同じパーティションにある場合にだけ、アトミック トランザクションにグループ化できます。プロパティの値に基づいてテーブルを照会する方法もなく、複数のテーブルの結合はサポートされていません。また、リレーショナル データベースとは異なり、テーブルはストアド プロシージャをサポートしていません。

Windows Azure テーブル ストレージは、大量の大まかに構造化されたデータへの高速で低コストのアクセスを必要とするアプリケーションに適した選択です。たとえば、多数のユーザーのプロファイル情報を格納するインターネット アプリケーションで、テーブルを使用する場合があります。この状況では高速なアクセスが重要であり、アプリケーションは SQL の全機能を必要としない可能性が高いと考えられます。スピードとサイズのために一部の機能を諦めることが有益である場合もあるので、テーブル ストレージは一部の問題には最適なソリューションになります。


## <a name="hadoop"></a>Hadoop

多くの組織が、数十年間にわたってデータ ウェアハウスを構築してきました。これらの収集された情報は、多くの場合はリレーショナル テーブルに格納され、それをユーザーが処理して、さまざまな方法でデータから知識を得ています。たとえば、SQL Server では、そのために SQL Server Analysis Services のようなツールを使用するのが一般的です。

しかし、非リレーショナル データに対する分析を行う場合もあります。データの形式はさまざまです。センサーや RFID タグの情報、サーバー ファームのログ ファイル、Web アプリケーションによって生成されたクリックストリーム データ、医療診断機器の画像などがあります。こうしたデータは、非常に大きくて、従来のデータ ウェアハウスでは効率的に使用できない場合もあります。このような Big Data の問題は、数年前まではあまりありませんでしたが、現在ではかなり一般的になってきています。

このような種類のビッグ データを分析するために、この業界では 1 つのソリューションに集中してきました。それが、オープン ソース テクノロジの Hadoop です。Hadoop は物理マシンまたは仮想マシンのクラスター上で実行され、操作対象のデータをこれらのマシンに分散して並列処理します。Hadoop が使用できるマシンが多いほど、行っている操作の完了が早くなります。

この種類の問題は、パブリック クラウドに自然に適合します。ほとんどの時間がアイドル状態になっている内部設置型サーバーの集合を維持するのではなく、クラウドで Hadoop を実行すれば、必要なときだけ VM を作成できます (料金もその分だけになります)。さらに良いことに、Hadoop で分析するビッグ データがクラウドで作成される場合が増えてきているので、データの移動に関する問題を減らすことができます。このような相乗効果を活用できるように、Microsoft では Hadoop サービスを Windows Azure 上で提供しています。[図 7](#Fig7) は、このサービスの最も重要なコンポーネントを示しています。

<a name="Fig7"></a>![Hadoop の図][hadoop]

**図 7: Hadoop on Windows Azure は、複数の仮想マシンを使用してデータを並列処理する MapReduce ジョブを実行します。**

Hadoop on Windows Azure を使用するには、まず、このクラウド プラットフォームに Hadoop クラスターの作成を依頼し、必要な VM の数を指定します。Hadoop クラスターを自分でセットアップするのは簡単ではない作業なので、Windows Azure に作成させるのが便利です。クラスターの使用が終了したら、それをシャットダウンします。使用していないコンピューティング リソースのために支払いをする必要はありません。

一般に*ジョブ*と呼ばれる Hadoop アプリケーションは、*MapReduce* として知られているプログラミング モデルを使用します。図で示されているように、MapReduce ジョブのロジックは多数の VM にわたって同時に実行されます。データを並列処理することによって、Hadoop は単一マシンのソリューションより大幅に速くデータを分析できます。

Windows Azure では、MapReduce ジョブの対象となるデータが、通常は BLOB ストレージに保持されます。しかし、Hadoop の MapReduce ジョブでは、データは *Hadoop 分散ファイル システム (HDFS)* に格納されていると想定されています。HDFS は、いくつかの点で BLOB ストレージに似ています。たとえば、複数の物理サーバー間でデータを複製します。Hadoop on Windows Azure は、図で示されているように、この機能を重複させるのではなく、代わりに HDFS API を通じて BLOB ストレージを公開します。MapReduce ジョブのロジックからは通常の HDFS ファイルにアクセスしているように見えますが、実際には BLOB からそこにストリーミングされたデータを操作しています。複数のジョブが同じデータに対して実行される場合をサポートするために、Hadoop on Windows Azure ではデータを BLOB から VM で実行されている HDFS 全体にコピーすることもできます。

MapReduce ジョブは、現在では主に Java で記述されており、Hadoop on Windows Azure でもそのアプローチがサポートされています。Microsoft では、他の言語 (C#、F#、JavaScript など) による MapReduce ジョブの作成もサポートされています。目標は、この Big Data テクノロジに、より大きな開発者のグループが、より簡単にアクセスできるようにすることです。

Hadoop には、HDFS と MapReduce の他に、ユーザーが自分で MapReduce ジョブを記述しなくてもデータを分析できるようにするためのテクノロジも含まれています。たとえば、Pig はビッグ データの分析用に設計された高レベル言語であり、Hive は SQL 風の HiveQL と呼ばれる言語を提供します。Pig も Hive も実際には HDFS データを処理する MapReduce ジョブを生成しますが、ユーザーからはその複雑さが見えないようになっています。
どちらも Hadoop on Windows Azure に付属しています。

Microsoft は、Excel 用 HiveQL ドライバーも提供しています。Excel アドインを使用すると、ビジネス アナリストは HiveQL クエリ (結果的には MapReduce ジョブ) を Excel から直接作成し、PowerPivot やその他の Excel ツールを使用して結果を処理および視覚化することができます。Hadoop on Windows Azure には、機械学習ライブラリの Mahout、グラフ マイニング システムの Pegasus など、その他のテクノロジも含まれています。

Big Data 分析は重要なので、Hadoop も重要です。Microsoft は、Hadoop を Windows Azure 上の管理されたサービスとして Excel などの使い慣れたツールへのリンクと共に提供することで、より多くのユーザーがこのテクノロジにアクセスできるようになることを目指しています。

より広い意味では、すべての種類のデータが重要です。そのため、Windows Azure には、データ管理とビジネス分析用のさまざまなオプションが用意されています。どのようなアプリケーションを作成する場合でも、役に立つ何かがこのクラウド プラットフォームで見つかる可能性があります。

[blobs]: ./media/cloud-storage/Data_01_Blobs.png
[SQLSvr-vm]: ./media/cloud-storage/Data_02_SQLSvrVM.png
[SQL-db]: ./media/cloud-storage/Data_03_SQLdb.png
[SQL-datasync]: ./media/cloud-storage/Data_04_SQLDataSync.png
[SQL-report]: ./media/cloud-storage/Data_05_SQLReporting.png
[SQL-tblstor]: ./media/cloud-storage/Data_06_TblStorage.png
[hadoop]: ./media/cloud-storage/Data_07_Hadoop.png

