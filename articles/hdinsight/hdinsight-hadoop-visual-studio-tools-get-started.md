<properties 
	pageTitle="HDInsight Tools for Visual Studio を使用する方法 | Microsoft Azure" 
	description="Visual Studio Hadoop Tools for HDInsight をインストールして、Hadoop クラスターに接続し、Hive クエリを実行する方法について説明します。" 
	keywords="hadoop tools,hive query,visual studio"
	services="HDInsight" 
	documentationCenter="" 
	authors="mumian" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="hdinsight" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="big-data" 
	ms.date="04/08/2015" 
	ms.author="jgao"/>

# HDInsight Tools for Visual Studio を使用して Hive クエリを実行する

HDInsight Tools for Visual Studio を使用して HDInsight クラスターに接続し、Hive クエリを送信する方法について説明します。HDInsight の使用に関する詳細については、「[HDInsight での Hadoop 入門][hdinsight.introduction]」と「[HDInsight の概要][hdinsight.get.started]」をご覧ください。Storm クラスターへの接続に関する詳細については、「[Visual Studio を使用して HDInsight で Apache Storm の C# トポロジを開発する][hdinsight.storm.visual.studio.tools]」をご覧ください。

>[AZURE.NOTE]最新のリリースでは、Hive エディター Intellisense のサポート、Hive スクリプトのローカルでの検証、YARN ログへのアクセスといった一部の新機能が追加されました。


## 前提条件

このチュートリアルを完了して、Visual Studio で Hadoop ツールを使用するには、次が必要になります。

- 次のソフトウェアを搭載したワークステーション

	- Windows 8.1、Windows 8、Windows 7
	- 下記のいずれかのバージョンの Visual Studio
		- Visual Studio 2012 Professional/Premium/Ultimate の[アップデート 4](http://www.microsoft.com/download/details.aspx?id=39305)
		- Visual Studio 2013 Community/Professional/Premium/Ultimate の[アップデート 4](https://www.microsoft.com/download/details.aspx?id=44921)
		- Visual Studio 2015 RC (Community/Enterprise)

	>[AZURE.NOTE]現時点では HDInsight Tools for Visual Studio は英語版のみになります。


## Hadoop Tools for Visual Studio のインストール

HDInsight Tools for Visual Studio は、Microsoft Azure SDK for .NET バージョン 2.5.1 以降に付属しています。[Web Platform Installer](http://go.microsoft.com/fwlink/?LinkId=255386) でインストールできます。お使いの Visual Studio バージョンに対応するものを選択する必要があります。また、この Hadoop ツールでは、32 ビットと 64 ビットの両方の Microsoft Hive ODBC ドライバーもインストールされます。

![Hadoop ツール: HDinsight Tools for Visual Studio Web Platform installer][1]


>[AZURE.NOTE]Visual Studio 2015 または 2012 を使用していて、Azure SDK 2.5 をインストールしている場合は、最新バージョンをインストールする前に以前のバージョンを手動で削除する必要があります。Visual Studio 2013 では、即時更新をサポートしています。

## Azure サブスクリプションへの接続
HDInsight Tools for Visual Studio を使用して、HDInsight クラスターへの接続、いくつかの基本的な管理操作の実行、Hive クエリの実行が可能です。

>[AZURE.NOTE]HDInsight Emulator の使用法については、「[HDInsight Emulator の概要](../hdinsight-get-started-emulator.md/#vstools)」をご覧ください。

>[AZURE.NOTE]汎用の Hadoop クラスター (プレビュー) に接続する方法の詳細については、「[Visual Studio を使用した Hive クエリの書き込みと送信 (ブログの投稿)](http://blogs.msdn.com/b/xiaoyong/archive/2015/05/04/how-to-write-and-submit-hive-queries-using-visual-studio.aspx)」をご覧ください。


**Azure サブスクリプションに接続するには**

1.	Visual Studio を開きます。
2.	**[ビュー]** メニューで、**[サーバー エクスプローラー]** をクリックして、サーバー エクスプローラー ウィンドウを開きます。
3.	**[Azure]** を展開して、**[HDInsight]** を展開します。 

	>[AZURE.NOTE]**HDInsight タスクの一覧****その他のウィンドウ****表示****HDInsight タスクの一覧ウィンドウ**  
4.	Azure サブスクリプションの資格情報を入力し、**[サインイン]** をクリックします。この操作は、このワークステーションで、まだ一度も Visual Studio から Azure サブスクリプションに接続していない場合にのみ必要です。
5.	サーバー エクスプローラーに、既存の HDInsight クラスターの一覧が表示されます。クラスターが 1 つもない場合は、Azure ポータル、Azure PowerShell、HDInsight SDK を使用してプロビジョニングできます。詳細については、「[HDInsight クラスターのプロビジョニング][hdinsight-provision]」をご覧ください。

	![Hadoop ツール: HDInsight Tools for Visual Studio サーバー エクスプローラー クラスターの一覧][5]
6.	HDInsight クラスターを展開します。**Hive データベース**、既定のストレージ アカウント、リンクされたストレージ アカウント、**Hadoop サービス ログ**が表示されます。さらに、エンティティを展開できます。 

Azure サブスクリプションに接続した後で、次を実行できます。

**Visual Studio から Azure ポータルに接続するには**

- サーバー エクスプローラーで、**[Azure]**、**[HDInsight]** の順に展開し、[HDInsight クラスター] を右クリックして、**[Manage Cluster in Azure Portal]** をクリックします。

**Visual Studio から質問をしたりフィードバックを提供したりするには**

- **[ツール]** メニューで、**[HDInsight]**、**[MSDN フォーラム]** の順にクリックして質問するか、**[Give Feedback]** をクリックします

## リンクしているリソースへの移動 

サーバー エクスプローラーで、既定のストレージ アカウント、すべてのリンクされたストレージ アカウントを確認できます。既定のストレージ アカウントを展開すると、そのストレージ アカウントのコンテナーを表示できます。既定のストレージ アカウントと既定のコンテナーがマークされます。コンテナーのいずれかの右クリックしてコンテンツを表示できます。

![HDInsight Tools for Visual Studio サーバー エクスプローラー クラスターの一覧][2]

## Hive クエリを実行する
[Apache Hive][apache.hive] は Hadoop に構築されるデータ ウェアハウス基盤であり、データを集約、照会、分析できます。HDInsight Tools for Visual Studio は Visual Studio からの Hive クエリの実行をサポートします。Hive の詳細については、「[HDInsight での Hive の使用][hdinsight.hive]」を参照してください。

HDInsight クラスターに対して Hive スクリプトをテストするには、数分以上かかる場合があります。HDInsight Tools for Visual Studio では、ライブ クラスターに接続しなくても、Hive スクリプトをローカルで検証できます。

加えて、HDInsight Tools for Visual Studio では、特定の Hive ジョブの YARN ログを収集して表示することで、Hive ジョブの内容を見ることができます。

###**hivesampletable** の表示 
すべての HDInsight クラスターには、*hivesampletable* という Hive テーブルのサンプルが付属します。このテーブルを使用して Hive テーブルの一覧表示、テーブル スキーマの表示、Hive テーブル内の行の一覧表示を行う方法を説明します。



**Hive テーブルを一覧表示し、Hive テーブル スキーマを表示するには**

1.	**サーバー エクスプローラー**から、**[Azure]**、**[HDInsight]**、任意のクラスター、**[Hive データベース]**、**[既定]**、**[hivesampletable]** の順に展開し、テーブル スキーマを表示します。
4.	**[hivesampletable]** を右クリックし、**[上位 100 行を表示]** をクリックして、行を一覧表示します。これは、Hive ODBC ドライバーを使用して、次の Hive クエリを実行することと同等です。

		SELECT * FROM hivesampletable LIMIT 100

	行カウントをカスタマイズできます。
 
	![Hadoop ツール:  HDinsight Hive Visual Studio スキーマ クエリ][6]

###Hive テーブルの作成

GUI を使用して Hive テーブルを作成するか、Hive クエリを使用できます。Hive クエリの使用については、[Hive クエリの実行](#run.queries)をご覧ください。

**Hive テーブルを作成するには**

1. **サーバー エクスプローラー**から、**[Azure]**、**[HDInsight クラスター]**、HDInsight クラスター、**[Hive データベース]** の順に展開し、**[既定]** を右クリックして、**[テーブルの作成]** をクリックします。
2. テーブルを構成します。
3. **[テーブルの作成]** をクリックして、新しい Hive テーブルを作成するためのジョブを送信します。

	![Hadoop ツール: HDinsight Visual Studio ツールで Hive テーブルを作成する][7]

###<a name="run.queries"></a>Hive クエリの検証と実行
Hive クエリを作成して実行するには次の 2 つの方法があります。

- アドホック クエリを作成する
- Hive アプリケーションを作成する

**アドホック クエリを作成、検証、実行するには**

1. **サービス エクスプローラー**から、**[Azure]**、**[HDInsight クラスター]** の順に展開します。
2. クエリを実行するクラスターを右クリックして、**[Hive クエリの作成]** をクリックします。 
3. Hive クエリを入力します。Hive エディターは Intellisense をサポートしています。HDInsight Tools for Visual Studio で、Hive スクリプトの編集時にリモート メタデータの読み込みがサポートされます。たとえば、"SELECT * FROM" と入力すると、Intellisense には推奨されるテーブル名が一覧表示されます。テーブル名を指定すると、列名が Intellisense に一覧表示されます。 

	![Hadoop ツール: HDInsight Visual Studio Tools Intellisense][13]

	![Hadoop ツール: HDInsight Visual Studio Tools Intellisense][14]

	> [AZURE.NOTE]
4. (任意) **[Validate Script]** をクリックして、スクリプトの構文エラーを確認します。

	![Hadoop ツール: HDinsight Tools for Visual Studio ローカル検証][10]

4. **[送信]** または** [(高度な) 送信]** をクリックします。高度な送信オプションの場合は、スクリプトの**ジョブ名**、**引数**、**追加の構成**、**ステータス ディレクトリ**を構成します。

	![HDinsight Hadoop の Hive クエリ][9]

	ジョブを送信した後で、**[Hive ジョブの概要]** ウィンドウを確認できます。

	![HDInsight Hadoop の Hive クエリの概要][8]
5. **[更新]** ボタンを使用して、ジョブのステータスが **[完了]** に変更されるまで、ステータスを更新します。
6. ウィンドウ下部のリンクをクリックして次の **[ジョブ クエリ]**、**[ジョブ出力]**、**[ジョブのログ]** または **[Yarn ログ]** を表示します。



**Hive ソリューションを作成し、実行するには**

1. **[ファイル]** メニューの **[新規作成]** をポイントし、**[プロジェクト]** をクリックします。
2. 左側のウィンドウから **[HDInsight]** を選択し、中央のウィンドウで **[Hive アプリケーション]** を選択して、プロパティを入力し、**[OK]** をクリックしします。

	![Hadoop ツール: HDinsight Visual Studio ツール新しい Hive プロジェクト][11]
3. **ソリューション エクスプローラー**で、**Script.hql** をダブルクリックして開きます。 
4. Hive スクリプトを検証するには、**[Validate Script]** ボタンをクリックするか、Hive エディターでスクリプトを右クリックしてコンテキスト メニューから **[Validate Script]** をクリックします。

 
###Hive ジョブの表示
Hive ジョブのジョブ クエリ、ジョブ出力、ジョブのログ、Yarn ログを表示できます。詳細については、先のスクリーンショットをご覧ください。

最新版のツールでは、YARN ログを収集して表示することで、Hive ジョブの内容を確認できます。YARN ログは、パフォーマンス問題の検証に役立ちます。HDInsight での YARN ログの収集に関する詳細については、「[プログラムで HDInsight アプリケーション ログにアクセスする][hdinsight.access.application.logs]」をご覧ください。

**Hive ジョブの表示**

1. **サーバー エクスプローラー**から、**[Azure]**、**[HDInsight]** の順に展開します。 
2. HDInsight クラスターを右クリックし、**[Hive ジョブの表示]** をクリックします。クラスター上で実行した Hive ジョブの一覧が表示されます。 
3. ジョブの一覧でジョブをクリックして選択し、**[Hive ジョブの概要]** ウィンドウを使用して **[ジョブ クエリ]**、**[ジョブ出力]**、**[ジョブのログ]**、または **[Yarn ログ]** を開きます。

	![Hadoop ツール: HDinsight Visual Studio ツールで新しい Hive ジョブを表示する][12]
## 次のステップ
この記事では、Hadoop ツール パッケージを使用して Visual Studio から HDInsight クラスターに接続し、Hive クエリを実行する方法を説明しました。詳細については、次を参照してください。

- [HDInsight での Hadoop Hive の使用][hdinsight.hive]
- [HDInsight で Hadoop を使用する][hdinsight.get.started]
- [HDInsight での Hadoop ジョブの送信][hdinsight.submit.jobs]
- [HDInsight での Hadoop を使用した Twitter データの分析][hdinsight.analyze.twitter.data]


<!--Anchors-->
[Installation]: #installation
[Connect to your Azure subscription]: #connect-to-your-azure-subscription
[Navigate the linked resources]: #navigate-the-linked-resources
[Run Hive queries]: #run-hive-queries
[Next steps]: #next-steps

<!--Image references-->
[1]: ./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.wpi.png
[2]: ./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.linked.resources.png
[5]: ./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.server.explorer.png
[6]: ./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.hive.schema.png
[7]: ./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.create.hive.table.png
[8]: ./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.run.hive.job.summary.png
[9]: ./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.submit.jobs.advanced.png
[10]: ./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.validate.hive.script.png
[11]: ./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.new.hive.project.png
[12]: ./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.view.hive.jobs.png
[13]: ./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.intellisense.table.names.png
[14]: ./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.intellisense.column.names.png

<!--Link references-->
[hdinsight-provision]: ../hdinsight/hdinsight-provision-clusters.md
[hdinsight.introduction]: ../hdinsight-introduction.md
[hdinsight.get.started]: ../hdinsight-get-started.md
[hdinsight.hive]: ../hdinsight/hdinsight-use-hive.md
[hdinsight.submit.jobs]: ../hdinsight/hdinsight-submit-hadoop-jobs-programmatically.md
[hdinsight.analyze.twitter.data]: ../hdinsight/hdinsight-analyze-twitter-data.md
[hdinsight.storm.visual.studio.tools]: ../hdinsight/hdinsight-storm-develop-csharp-visual-studio-topology.md
[hdinsight.access.application.logs]: ../hdinsight/hdinsight-hadoop-access-yarn-app-logs.md

[apache.hive]: http://hive.apache.org

<!--HONumber=54--> 