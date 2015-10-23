<properties
	pageTitle="オンライン データ ソースから Machine Learning Studio にデータをインポートする | Microsoft Azure"
	description="さまざまなオンライン ソースから Azure Machine Learning Studio にトレーニング データをインポートする方法"
	keywords="import data,data format,data types,data sources,training data"
	services="machine-learning"
	documentationCenter=""
	authors="bradsev"
	manager="paulettm"
	editor="cgronlun"/>

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/07/2015"
	ms.author="bradsev;garye;gopitk" />


# リーダー モジュールで各種オンライン データ ソースから Azure Machine Learning Studio にデータをインポートする

[AZURE.INCLUDE [import-data-into-aml-studio-selector](../../includes/machine-learning-import-data-into-aml-studio.md)]

## はじめに

[リーダー][reader] モジュールで実験を実行しながら、Azure Machine Learning Studio 内からさまざまなオンライン データ ソースのデータにアクセスすることができます。

- HTTP を使用した Web URL
- HiveQL を使用した Hadoop
- Azure BLOB ストレージ
- Azure テーブル
- Azure SQL Database または Azure VM の SQL Server
- データ フィード プロバイダー (現在は OData)

このドキュメントでは、サポートされている各ソースについての説明と、これらのソースから Azure Machine Learning の実験にデータを移動するために必要な情報を取り上げます。

Azure Machine Learning Studio で実験を行うためのワークフローは、キャンバスにコンポーネントをドラッグ アンド ドロップすることによって作成します。オンライン データ ソースにアクセスするには、[リーダー][reader] モジュールを実験に追加し、**[データ ソース]** を選択したうえで、データにアクセスするために必要なパラメーターを指定します。サポートされているオンライン データ ソースは、以下の表にまとめました。サポートされているファイル形式と、データにアクセスするためのパラメーターも記載されています。

> [AZURE.NOTE]この記事では、[リーダー][reader] モジュールに関する一般的な情報を記載しています。アクセス可能なデータの種類、形式、パラメーターの詳細や、よく寄せられる質問への回答については、「[リーダー][reader]」モジュールの概要をご覧ください。

> [AZURE.NOTE]このトレーニング データは実験が実行されている間にアクセスされるため、利用できるのはその実験の中だけです。それに比べてデータセット モジュールに保存されているデータには、ワークスペース内の任意の実験からアクセスすることができます。


## サポートされるオンライン データ ソース
Azure Machine Learning の**リーダー** モジュールは、以下のデータ ソースをサポートしています。

データ ソース | 説明 | パラメーター |
---|---|---|
HTTP を使用する Web URL |コンマ区切り (CSV)、タブ区切り (TSV)、ARFF (Attribute-Relation File Format)、サポート ベクター マシン (SVM-Light) の各形式のデータを HTTP を使用する任意の Web URL から読み取ります。 | <b>URL</b>: サイトの URL とファイル名、拡張子を含むファイルの完全名を指定します。<br/><br/><b>データ形式</b>: サポートされているいずれかのデータ形式 (CSV、TSV、ARFF、SVM-light) を指定します。データにヘッダー行がある場合、その行が列名の割り当てに使用されます。|
Hadoop/HDFS|Hadoop の分散ストレージからデータを読み取ります。必要なデータは、HiveQL という SQL に似たクエリ言語で指定します。Machine Learning Studio にデータを追加する前に、HiveQL を使用してデータを集計したり、データのフィルタリングを実行したりすることもできます。|<b>Hive データベース クエリ</b>: データを生成するために使用する Hive クエリを指定します。<br/><br/><b>HCatalog サーバー URI</b>: *&lt;your cluster name&gt;.azurehdinsight.net* 形式でクラスターの名前を指定します。<br/><br/><b>Hadoop ユーザー アカウント名</b>: クラスターをプロビジョニングするために使用する Hadoop ユーザー アカウント名を指定します。<br/><br/><b>Hadoop ユーザー アカウントのパスワード</b>: クラスターをプロビジョニングする際に使用する資格情報を指定します。詳細については、[HDInsight での Hadoop クラスターの作成](article-hdinsight-provision-clusters)に関するページを参照してください。<br/><br/><b>出力データの場所</b>: データを Hadoop 分散ファイル システム (HDFS) に保存するか、Azure に保存するかを指定します。<br/><ul>出力データを HDFS に保存する場合、HDFS サーバーの URI を指定します。(必ず、HTTPS:// プレフィックスなしの HDInsight クラスター名を使用してください)。<br/><br/>出力データを Azure に保存する場合は、Azure ストレージ アカウント名、ストレージ アクセス キー、ストレージ コンテナー名を指定する必要があります。</ul>|
SQL データベース |Azure 仮想マシン上で動作する SQL Server データベースに保存されているデータまたは Azure SQL Database に保存されているデータを読み取ります。 | <b>データベース サーバー名</b>: データベースが実行されているサーバーの名前を指定します。<br/><ul>Azure SQL Database の場合は、生成されたサーバー名を入力してください。通常、*&lt;generated\_identifier&gt;.database.windows.net* 形式となります。 <br/><br/>Azure 仮想マシンでホストされる SQL Server の場合は、*tcp:&lt;Virtual Machine DNS Name&gt;, 1433* 形式で入力します。</ul><br/><b>データベース名</b>: サーバー上のデータベースの名前を指定します。<br/><br/><b>サーバー ユーザー アカウント名</b>: データベースへのアクセス権限を持つアカウントのユーザー名を指定します。<br/><br/><b>サーバーのユーザー アカウントのパスワード</b>: ユーザー アカウントのパスワードを指定します。<br/><br/><b>あらゆるサーバー証明書を承諾する</b>: データを読み取る前に行うサイトの証明書の確認をスキップする場合は、このオプションを使用します (セキュリティは低下します)。<br/><br/><b>データベース クエリ</b>: 読み取るデータを表す SQL ステートメントを入力します。|
Azure テーブル|Azure Storage の Table サービスからデータを読み取ります。<br/><br/>大量のデータを低頻度で読み取る場合、Azure Table サービスを使用してください。高い柔軟性、非リレーショナル (NoSQL)、圧倒的なスケーラビリティ、低コスト、高い可用性を特徴とするストレージ ソリューションです。| アクセスの対象がパブリックな情報であるか、ログイン資格情報を必要とするプライベートなストレージ アカウントであるかによって**リーダー**のオプションは変化します。その点を決めるのが、<b>認証の種類</b>です。"PublicOrSAS" と "Account" のいずれかの値を取ります。その値によって、一連のパラメーターが異なります。<br/><br/><b>パブリックな URI または Shared Access Signature (SAS) の URI</b>: パラメーターは次のとおりです。<br/><br/><ul><b>テーブルの URI</b>: テーブルのパブリック URL または SAS URL を指定します。<br/><br/><b> プロパティ名のスキャン対象となる行を指定します。</b>: 指定した行数をスキャンする場合は <i>TopN</i> を、テーブル内のすべての行を取得する場合は <i>ScanAll</i> を値として指定します。<br/><br/>データが均一で予測可能である場合は、*TopN* を選択し、N の値を入力することをお勧めします。大きなテーブルでは、その方が読み取り時間が短くなります。<br/><br/>データを構成する一連のプロパティが、テーブルの深さと位置によって異なる場合は、*ScanAll* オプションを選択してすべての行をスキャンしてください。これによって、プロパティとメタデータの変換結果に整合性を確保することができます。<br/><br/></ul><b>プライベート ストレージ アカウント</b>: 次のパラメーターがあります。<br/><br/><ul><b>アカウント名</b>: 読み取るテーブルを含んだアカウントの名前を指定します。<br/><br/><b>アカウント キー</b>: アカウントに関連付けられたストレージ キーを指定します。<br/><br/><b>テーブル名</b>: 読み取るデータを含んだテーブルの名前を指定します。<br/><br/><b>プロパティ名のスキャン対象行</b>: 指定した行数をスキャンする場合は <i>TopN</i> を、テーブル内のすべての行を取得する場合は <i>ScanAll</i> を値として指定します。<br/><br/>データが均一で予測可能である場合は、*TopN* を選択し、N の値を入力することをお勧めします。大きなテーブルでは、その方が読み取り時間が短くなります。<br/><br/>データを構成する一連のプロパティが、テーブルの深さと位置によって異なる場合は、*ScanAll* オプションを選択してすべての行をスキャンしてください。これによって、プロパティとメタデータの変換結果に整合性を確保することができます。<br/><br/>|
Azure BLOB ストレージ | 画像、非構造化テキスト、バイナリ データなど、Azure Storage の BLOB サービスに保存されているデータを読み取ります。<br/><br/>BLOB サービスを使用して、データをパブリックに公開したり、アプリケーション データをプライベートに保存したりすることができます。HTTP または HTTPS 接続を使用してどこからでもデータにアクセスできます。 | アクセスの対象がパブリックな情報であるか、ログイン資格情報を必要とするプライベートなストレージ アカウントであるかによって**リーダー** モジュールのオプションは変化します。その点を決めるのが<b>認証の種類</b>です。"PublicOrSAS" と "Account" のいずれかの値を取ります。<br/><br/><b>パブリックな URI または Shared Access Signature (SAS) の URI</b>: 次のパラメーターがあります。<br/><br/><ul><b>URI</b>: BLOB ストレージのパブリック URL または SAS URL を指定します。<br/><br/><b>ファイル形式</b>: BLOB サービスのデータの形式を指定します。サポートされている形式は CSV、TSV、ARFF です。<br/><br/></ul><b>プライベート ストレージ アカウント</b>: 次のパラメーターがあります。<br/><br/><ul><b>アカウント名</b>: 読み取る BLOB を含んだアカウントの名前を指定します。<br/><br/><b>アカウント キー</b>: アカウントに関連付けられたストレージ キーを指定します。<br/><br/><b>コンテナー、ディレクトリ、BLOB のパス</b>: 読み取るデータを含んだ BLOB の名前を指定します。<br/><br/><b>BLOB ファイル形式</b>: BLOB サービスにおけるデータの形式を指定します。サポートされているデータ形式は CSV、TSV、ARFF、CSV (エンコーディング指定付き)、Excel です。<br/><br/><ul>形式が CSV または TSV である場合は、ファイルにヘッダー行が含まれているかどうかを必ず指定してください。<br/><br/>Excel を選択すると、Excel ブックからデータを読み取ることができます。<i>[Excel データ形式]</i> オプションで、データの保存先が Excel ワークシートの範囲か Excel テーブルかを指定してください。<i>[Excel シートまたは埋め込みテーブル]</i> オプションで、読み取りの対象となるシートまたはテーブルの名前を指定します。</ul><br/>|
データ フィード プロバイダー | サポートされているフィード プロバイダーからデータを読み取ります。現在サポートされているのは Open Data Protocol (OData) 形式だけです。 | <b>データ コンテンツの種類</b>: OData 形式を指定します。<br/><br/><b>ソース URL</b>: データ フィードに使用する完全 URL を指定します。<br/>次の URL は、Northwind サンプル データベースから読み取る例です: http://services.odata.org/northwind/northwind.svc/|


<!-- Module References -->
[reader]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/

<!---HONumber=Oct15_HO2-->