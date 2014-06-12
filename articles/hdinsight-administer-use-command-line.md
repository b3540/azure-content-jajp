<properties linkid="manage-services-hdinsight-administer-hdinsight-using-command-line" urlDisplayName="HDInsight の管理" pageTitle="クロス プラットフォーム コマンド ライン インターフェイスを使用した HDInsight の管理 | Azure" metaKeywords="hdinsight, hdinsight の管理, azure での hdinsight の管理 " description="クロス プラットフォーム コマンド ライン インターフェイスを使用して、Node.js をサポートする Windows、Mac、Linux などのプラットフォームで HDInsight クラスターを管理する方法について説明します。" services="hdinsight" umbracoNaviHide="0" disqusComments="1" editor="cgronlun" manager="paulettm" title="クロスプラットフォーム コマンド ライン インターフェイスを使用した HDInsight の管理" authors="jgao" />

# クロスプラットフォーム コマンド ライン インターフェイスを使用した HDInsight の管理

この記事では、クロスプラットフォーム コマンド ライン インターフェイスを使用して HDInsight クラスターを管理する方法について説明します。コマンド ライン ツールは Node.js で実装されます。Windows、Mac、Linux など、Node.js をサポートするいずれのプラットフォームでも使用できます。

コマンド ライン ツールはオープン ソースです。ソース コードは GitHub (<a href= "https://github.com/WindowsAzure/azure-sdk-tools-xplat">https://github.com/WindowsAzure/azure-sdk-tools-xplat</a>) で管理されています。

この記事では、Windows からコマンド ライン インターフェイスを使用する方法だけを取り上げます。コマンド ライン インターフェイスの使用方法の一般的ガイドについては、「[Mac および Linux 用 Azure コマンド ライン ツールの使用方法][azure-command-line-tools]」を参照してください。包括的なリファレンス ドキュメントについては、「[Mac および Linux 用 Azure コマンド ライン ツール][azure-command-line-tool]」を参照してください。


**前提条件:**

この記事を読み始める前に、次の項目を用意する必要があります。

- **Azure サブスクリプション**。Azure はサブスクリプション方式のプラットフォームです。サブスクリプションの入手方法の詳細については、[購入オプション][azure-purchase-options]、[メンバー プラン][azure-member-offers]、または[無料評価版][azure-free-trial]に関するページを参照してください。

##この記事の内容

* [インストール](#installation)
* [Azure アカウントの発行設定のダウンロードとインポート](#importsettings)
* [クラスターのプロビジョニング](#provision)
* [構成ファイルを使用したクラスターのプロビジョニング](#provisionconfigfile)
* [クラスターの一覧と表示](#listshow)
* [クラスターの削除](#delete)
* [次のステップ](#nextsteps)

##<a id="installation"></a> インストール
コマンド ライン インターフェイスは *Node.js パッケージ マネージャー (NPM)* または Windows インストーラーを使用してインストールできます。

**NPM を使用してコマンド ライン インターフェイスをインストールするには**

1.	ブラウザーで **www.nodejs.org** を開きます。
2.	**[INSTALL]** をクリックし、指示に従います。設定は既定の設定を使います。
3.	コンピューターから**コマンド プロンプト** (または *Azure コマンド プロンプト*、または *VS2012 の開発者コマンド プロンプト*) を開きます。
4.	コマンド プロンプト ウィンドウで次のコマンドを実行します。

		npm install -g azure-cli

	> [WACOM.NOTE] NPM コマンドが見つからないというエラーが表示される場合、次のパスが PATH 環境変数の中にあることを確認します: <i>C:\Program Files (x86)\nodejs;C:\Users\[ユーザー名]\AppData\Roaming\npm</i> または <i>C:\Program Files\nodejs;C:\Users\[ユーザー名]\AppData\Roaming\npm</i>


5.	次のコマンドを実行してインストールを確認します。

		azure hdinsight -h

	*-h* スイッチを使うと、さまざまなレベルでヘルプ情報を表示できます。次に例を示します。
		
		azure -h
		azure hdinsight -h
		azure hdinsight cluster -h
		azure hdinsight cluster create -h

**Windows インストーラーを使用してコマンド ライン インターフェイスをインストールするには**

1.	ブラウザーで **http://www.windowsazure.com/ja-jp/downloads/** を開きます。
2.	下へスクロールして、**[コマンド ライン ツール]** セクションの **[クロスプラットフォーム コマンド ライン インターフェイス]** をクリックし、Web プラットフォーム インストーラー ウィザードの指示に従います。

##<a id="importsettings"></a> Azure アカウントの発行設定のダウンロードとインポート

コマンド ライン インターフェイスを使用する前に、自分のコンピューターと Azure との接続を構成する必要があります。Azure のサブスクリプション情報は、コマンド ライン インターフェイスがアカウントにアクセスする際に使用されます。この情報は、Azure から発行設定ファイルとして入手できます。発行設定ファイルは永続的なローカル構成設定としてインポートすることができます。インポートすると、コマンド ライン インターフェイスの以降の操作にはこのファイルが使用されます。発行設定ファイルのインポートは 1 回だけ行う必要があります。

> [WACOM.NOTE] 発行設定ファイルには、機密情報が含まれます。このファイルを削除するか、追加の作業を行ってファイルのある user フォルダーを暗号化することをお勧めします。Windows の場合、フォルダー プロパティを変更するか、または BitLocker を使用します。


**発行設定ファイルをダウンロードしてインポートするには**

1.	**コマンド プロンプト**を開きます。
2.	次のコマンドを実行して、発行設定ファイルをダウンロードします。

		azure account download
 
	![HDI.CLIAccountDownloadImport][image-cli-account-download-import]

	URL も含めて、ファイルのダウンロード方法が表示されます。

3.	**Internet Explorer** を開き、コマンド プロンプト ウィンドウに表示された URL にアクセスします。
4.	**[保存]** をクリックして、ファイルをコンピューターに保存します。
5.	コマンド プロンプト ウィンドウで次のコマンドを実行して、発行設定ファイルをインポートします。

		azure account import <file>

	先のスクリーンショットで、発行設定ファイルはコンピューターの C:\HDInsight フォルダーに保存されています。


##<a id="provision"></a> HDInsight クラスターのプロビジョニング
HDInsight は、既定のファイル システムとして Azure BLOB ストレージ コンテナーを使用します。HDInsight クラスターを作成するには Azure ストレージ アカウントが必要です。

発行設定ファイルをインポートした後、次のコマンドを使ってストレージ アカウントを作成できます。

	azure account storage create [options] <StorageAccountName>


> [WACOM.NOTE] ストレージ アカウントは、クラスターと同じデータ センターに併置する必要があります。現在、HDInsight クラスターのプロビジョニングができるのは次のデータ センターだけです。

><ul>
<li>東南アジア</li>
<li>北ヨーロッパ</li>
<li>西ヨーロッパ</li>
<li>米国東部</li>
<li>米国西部</li>
</ul>


Azure 管理ポータルを使った Azure ストレージ アカウントの作成については、「[ストレージ アカウントの作成方法][azure-create-storageaccount]」を参照してください。

既にストレージ アカウントを持っていて、アカウント名とアカウント キーがわからない場合は、次のコマンドを使ってその情報を取得できます。

	-- lists storage accounts
	azure account storage list
	-- Shows a storage account
	azure account storage show <StorageAccountName>
	-- Lists the keys for a storage account
	azure account storage keys list <StorageAccountName>

管理ポータルを使用して情報を取得する方法の詳細については、「[ストレージ アカウントの管理方法][azure-manage-storageaccount]」の「*方法: ストレージ アクセス キーの表示、コピーおよび再生成*」を参照してください。


*azure hdinsight cluster create* コマンドは、コンテナーが存在しない場合、コンテナーを作成します。コンテナーを事前に作成する場合は、次のコマンドを使用できます。

	azure storage container create --account-name <StorageAccountName> --account-key <StorageAccountKey> [ContainerName]
		
ストレージ アカウントを用意して、BLOB コンテナーを準備したら、クラスターを作成する準備は整いました。次のコマンドを実行できます。

	azure hdinsight cluster create --clusterName <ClusterName> --storageAccountName <StorageAccountName> --storageAccountKey <storageAccountKey> --storageContainer <StorageContainer> --nodes <NumberOfNodes> --location <DataCenterLocation> --username <HDInsightClusterUsername> --clusterPassword <HDInsightClusterPassword>

![HDI.CLIClusterCreation][image-cli-clustercreation]

















##<a id="provisionconfigfile"></a> 構成ファイルを使用した HDInsight クラスターのプロビジョニング
通常は、HDInsight クラスターをプロビジョニングして、そのクラスター上でジョブを実行した後、クラスターを削除してコストを削減します。コマンド ライン インターフェイスを使うと、構成をファイルに保存して、クラスターをプロビジョニングするたびにその構成を再利用することができます。
 
	azure hdinsight cluster config create <file>
	 
	azure hdinsight cluster config set <file> --clusterName <ClusterName> --nodes <NumberOfNodes> --location "<DataCenterLocation>" --storageAccountName "<StorageAccountName>.blob.core.windows.net" --storageAccountKey "<StorageAccountKey>" --storageContainer "<BlobContainerName>" --username "<Username>" --clusterPassword "<UserPassword>"
	 
	azure hdinsight cluster config storage add <file> --storageAccountName "<StorageAccountName>.blob.core.windows.net"
	       --storageAccountKey "<StorageAccountKey>"
	 
	azure hdinsight cluster config metastore set <file> --type "hive" --server "<SQLDatabaseName>.database.windows.net"
	       --database "<HiveDatabaseName>" --user "<Username>" --metastorePassword "<UserPassword>"
	 
	azure hdinsight cluster config metastore set <file> --type "oozie" --server "<SQLDatabaseName>.database.windows.net"
	       --database "<OozieDatabaseName>" --user "<SQLUsername>" --metastorePassword "<SQLPassword>"
	 
	azure hdinsight cluster create --config <file>
		 


![HDI.CLIClusterCreationConfig][image-cli-clustercreation-config]


##<a id="listshow"></a> クラスターの一覧と詳細の表示
クラスターの一覧と詳細を表示するには、次のコマンドを使用します。
	
	azure hdinsight cluster list
	azure hdinsight cluster show <ClusterName>
	
![HDI.CLIListCluster][image-cli-clusterlisting]


##<a id="delete"></a> クラスターの削除
クラスターを削除するには、次のコマンドを使用します。

	azure hdinsight cluster delete <ClusterName>




##<a id="nextsteps"></a> 次のステップ
この記事では、さまざまな HDInsight クラスター管理タスクを実行する方法について説明しました。詳細については、次の記事を参照してください。

* [管理ポータルを使用した HDInsight クラスターの管理][hdinsight-admin]
* [PowerShell を使用した HDInsight の管理][hdinsight-admin-powershell]
* [Azure HDInsight の概要][hdinsight-getting-started]
* [Mac および Linux 用 Azure コマンド ライン ツールの使用方法][azure-command-line-tools]
* [Mac および Linux 用 Azure コマンド ライン ツール][azure-command-line-tool]


[azure-command-line-tools]: /ja-jp/develop/nodejs/how-to-guides/command-line-tools/
[azure-command-line-tool]: /ja-jp/manage/linux/other-resources/command-line-tools/
[azure-create-storageaccount]: /ja-jp/manage/services/storage/how-to-create-a-storage-account/ 
[azure-manage-storageaccount]: /ja-jp/manage/services/storage/how-to-manage-a-storage-account/
[azure-purchase-options]: https://www.windowsazure.com/ja-jp/pricing/purchase-options/
[azure-member-offers]: https://www.windowsazure.com/ja-jp/pricing/member-offers/
[azure-free-trial]: https://www.windowsazure.com/ja-jp/pricing/free-trial/


[hdinsight-admin]: /ja-jp/manage/services/hdinsight/howto-administer-hdinsight/

[hdinsight-admin-powershell]: /ja-jp/manage/services/hdinsight/administer-hdinsight-using-powershell/
[hdinsight-getting-started]: /ja-jp/manage/services/hdinsight/get-started-hdinsight/

[image-cli-account-download-import]: ./media/hdinsight-administer-use-command-line/HDI.CLIAccountDownloadImport.png 
[image-cli-clustercreation]: ./media/hdinsight-administer-use-command-line/HDI.CLIClusterCreation.png
[image-cli-clustercreation-config]: ./media/hdinsight-administer-use-command-line/HDI.CLIClusterCreationConfig.png
[image-cli-clusterlisting]: ./media/hdinsight-administer-use-command-line/HDI.CLIListClusters.png "List and show clusters"

