<properties linkid="manage-services-hdinsight-use-sqoop" urlDisplayName="HDInsight での Sqoop の使用サンプル" pageTitle="HDInsight での Sqoop の使用 | Azure" metaKeywords="" description="コンピューターから Azure PowerShell を使用して、HDInsight クラスターと Azure SQL データベース間で Sqoop インポートとエクスポートを実行する方法について説明します。" umbracoNaviHide="0" disqusComments="1" editor="cgronlun" manager="paulettm" services="hdinsight" documentationCenter="" title="HDInsight での Sqoop の使用" authors="jgao" />

# HDInsight での Sqoop の使用
 
コンピューターから Azure PowerShell と HDInsight .NET SDK を使用して、HDInsight クラスターと Azure SQL データベース間で Sqoop インポートとエクスポートを実行する方法について説明します。

**所要時間:** 30 分


##この記事の内容

- [Sqoop とは](#whatissqoop)
- [前提条件](#prerequisites)
- [チュートリアルのシナリオについて](#scenario)
- [チュートリアルを準備する](#prepare)
- [PowerShell を使用して Sqoop エクスポートを実行する](#export)
- [HDInsight SDK を使用して Sqoop エクスポートを実行する](#export-sdk)
- [PowerShell を使用して Sqoop インポートを実行する](#import)
- [次のステップ](#nextsteps)


## <a id="whatissqoop"></a> Sqoop とは

Hadoop はログやファイルなどの非構造化データおよび半構造化データを処理する場合に自然な選択ですが、リレーショナル データベースに格納された構造化データを処理する必要が生じることもあります。

[Sqoop][sqoop-user-guide-1.4.4] は、Hadoop クラスターとリレーショナル データベース間でデータを転送するためのツールです。このツールを使用して、SQL、MySQL、Oracle などのリレーショナル データベース管理システム (RDBMS) から Hadoop 分散ファイル システム (HDFS) へデータをインポートしたり、MapReduce または Hive を使用して Hadoop のデータを変換し、そのデータを RDBMS へ取り込んだりできます。このチュートリアルでは、リレーショナル データベースとして SQL データベースを使用します。

HDInsight クラスターでサポートされている Sqoop のバージョンについては、「[HDInsight で提供されるクラスター バージョンの新機能][hdinsight-versions]」を参照してください。




##<a id="prerequisites"></a>前提条件

このチュートリアルを読み始める前に、次の項目を用意する必要があります。

- **コンピューター**。Azure PowerShell がインストールされ構成されている必要があります。手順については、[Azure PowerShell のインストールおよび構成に関するページ][powershell-install]を参照してください。PowerShell スクリプトを実行するには、Azure PowerShell を管理者として実行し、実行ポリシーを *RemoteSigned* に設定する必要があります。「[Run Windows PowerShell scripts (Windows PowerShell スクリプトの実行)][powershell-script]」を参照してください。

- **Azure HDInsight クラスター**。クラスターのプロビジョニングの手順については、「[Azure HDInsight の概要][hdinsight-get-started]」または「[HDInsight クラスターのプロビジョニング][hdinsight-provision]」を参照してください。このチュートリアルを読み進めるには、次のデータが必要です。

	<table border="1">
	<tr><th>クラスターのプロパティ</th><th>PowerShell 変数名</th><th>値</th><th>説明</th></tr>
	<tr><td>HDInsight クラスター名</td><td>$clusterName</td><td></td><td>HDInsight クラスター名です。</td></tr>
	<tr><td>Azure ストレージ アカウント名</td><td>$storageAccountName</td><td></td><td>HDInsight クラスターで使用できる Azure ストレージ アカウント。このチュートリアルでは、クラスターのプロビジョニング プロセス中に指定された既定のストレージ アカウントを使用します。</td></tr>
	<tr><td>Azure BLOB コンテナー名</td><td>$containerName</td><td></td><td>この例では、既定の HDInsight クラスター ファイル システムで使用される Azure BLOB ストレージ コンテナーを使用します。既定では、HDInsight クラスターと同じ名前です。</td></tr>
	</table>

- **Azure SQL データベース**。コンピューターから SQL データベース サーバーに対するアクセスを許可するようにファイアウォール ルールを構成する必要があります。SQL データベースの作成方法とファイアウォールの構成方法については、「[Azure SQL データベースの概要][sqldatabase-get-started]」を参照してください。この記事には、このチュートリアルに必要な SQL データベース テーブルを作成する PowerShell スクリプトが紹介されています。

	<table border="1">
	<tr><th>SQL データベースのプロパティ</th><th>PowerShell 変数名</th><th>値</th><th>説明</th></tr>
	<tr><td>SQL データベース サーバー名</td><td>$sqlDatabaseServer</td><td></td><td>Sqoop によるデータのエクスポート先またはインポート元になる SQL データベース サーバー。</td></tr>
	<tr><td>SQL データベースのログイン名</td><td>$sqlDatabaseLogin</td><td></td><td>SQL データベースのログイン名。</td></tr>
	<tr><td>SQL データベースのログイン パスワード</td><td>$sqlDatabasePassword</td><td></td><td>SQL データベースのログイン パスワード。</td></tr>
	<tr><td>SQL データベース名</td><td>$sqlDatabaseName</td><td></td><td>Sqoop によるデータのエクスポート先またはインポート元になる Azure SQL データベース。</td></tr>
	</table>

	> [WACOM.NOTE]既定では、Azure SQL データベースは Azure HDInsight のような Azure サービスからの接続を許可します。このファイアウォール設定が無効になっている場合は、Azure 管理ポータルから有効にする必要があります。SQL データベースの作成方法とファイアウォール ルールの構成方法については、「[SQL データベースの作成と構成][sqldatabase-create-configue]」を参照してください。


> [WACOM.NOTE] テーブルに値を入力します。そうしておくと、このチュートリアルを読み進める際に役に立ちます。

## <a id="scenario"></a>シナリオについて
HDInsight クラスターにはサンプル データがいくつか付属しています。そのうち次の 2 つを使用します。

- log4j ログ ファイル (*/example/data/sample.log*)。次のようなログがファイルから抽出されます。

		2012-02-03 18:35:34 SampleClass6 [INFO] everything normal for id 577725851
		2012-02-03 18:35:34 SampleClass4 [FATAL] system problem at id 1991281254
		2012-02-03 18:35:34 SampleClass3 [DEBUG] detail for id 1304807656
		...

- *hivesampletable* という名前の Hive テーブル。*/hive/warehouse/hivesampletable* にあるデータ ファイルを参照しています。テーブルはモバイル デバイスのデータを含んでいます。Hive テーブルのスキーマは次のとおりです。

	<table border="1">
	<tr><th>フィールド</th><th>データ型</th></tr>
	<tr><td>clientid</td><td>string</td></tr>
	<tr><td>querytime</td><td>string</td></tr>
	<tr><td>market</td><td>string</td></tr>
	<tr><td>deviceplatform</td><td>string</td></tr>
	<tr><td>devicemake</td><td>string</td></tr>
	<tr><td>devicemodel</td><td>string</td></tr>
	<tr><td>state</td><td>string</td></tr>
	<tr><td>country</td><td>string</td></tr>
	<tr><td>querydwelltime</td><td>double</td></tr>
	<tr><td>sessionid</td><td>bigint</td></tr>
	<tr><td>sessionpagevieworder</td><td>bigint</td></tr>
	</table>

まず、sample.log と hivesampletable の両方を SQL データベースにエクスポートし、次に、次のパスを使用して、モバイル デバイスのデータを含むテーブルを HDInsight をインポートします。

	/tutorials/usesqoop/importeddata

###HDInsight ストレージについて

HDInsight はデータ ストレージとして Azure BLOB ストレージを使用します。これは *WASB* または *Azure ストレージ - BLOB* と呼ばれています。WASB は、HDFS を Azure BLOB ストレージ上で Microsoft が実装したものです。詳細については、「[HDInsight での Azure BLOB ストレージの使用][hdinsight-storage]」を参照してください。

HDInsight クラスターをプロビジョニングするときに、HDFS と同じように、Azure ストレージ アカウントと、そのアカウントに対応する特定の BLOB ストレージ コンテナーを、既定のファイル システムとして指定します。プロビジョニング プロセスを実行するときに、このストレージ アカウントに加えて、同じ Azure サブスクリプションまたは別の Azure サブスクリプションに属する付加的なストレージ アカウントを追加することもできます。付加的なストレージ アカウントを追加する方法の詳細については、「[HDInsight クラスターのプロビジョニング][hdinsight-provision]」を参照してください。このチュートリアルで使用する PowerShell スクリプトを簡単にするために、ファイルはすべて、*/tutorials/usesqoop* にある既定のファイル システム コンテナーに格納されています。既定で、このコンテナーの名前は、HDInsight クラスター名と同じです。
WASB の構文は次のとおりです。

	wasb[s]://<ContainerName>@<StorageAccountName>.blob.core.windows.net/<path>/<filename>

> [WACOM.NOTE] HDInsight クラスター Version 3.0 では *wasb://* 構文だけがサポートされています。以前の *asv://* 構文は HDInsight 2.1 および 1.6 クラスターでサポートされていますが、HDInsight 3.0 クラスターではサポートされておらず、今後のバージョンでもサポートされる予定はありません。

> [WACOM.NOTE] WASB のパスは、仮想パスです。詳細については、「[HDInsight での Azure BLOB ストレージの使用][hdinsight-storage]」を参照してください。

既定のファイル システム コンテナーに格納されているファイルは、次の URI のどれを使用しても HDInsight からアクセスできます (例として sample.log を使用しています)。

	wasb://mycontainer@mystorageaccount.blob.core.windows.net/example/data/sample.log
	wasb:///example/data/sample.log
	/example/data/sample.log

ストレージ アカウントから直接ファイルにアクセスする場合、ファイルの BLOB 名は次のようになります。

	example/data/sample.log


##<a id="prepare"></a>チュートリアルを準備する

このチュートリアルの後の方で Sqoop エクスポートによって使用される 2 つの SQL データベース テーブルを作成します。また、Sqoop による処理の前に、sample.log ファイルの前処理をする必要もあります。

**SQL データベース テーブルを作成するには**

1. Windows PowerShell ISE を開きます (Windows 8 のスタート画面で、「**PowerShell_ISE**」と入力し、**[Windows PowerShell ISE]** をクリックします。「[Start Windows PowerShell on Windows 8 and Windows (Windows 8 と Windows での Windows PowerShell の起動)][powershell-start])」を参照してください。

2. 次のスクリプトをスクリプト ウィンドウにコピーし、最初の 4 個の変数を設定します。
		
		#SQL database variables
		$sqlDatabaseServer = "<SQLDatabaseServerName>" 
		$sqlDatabaseLogin = "<SQLDatabaseUsername>"
		$sqlDatabasePassword = "<SQLDatabasePassword>"
		$sqlDatabaseName = "<SQLDatabaseName>" 

		$sqlDatabaseConnectionString = "Data Source=$sqlDatabaseServer.database.windows.net;Initial Catalog=$sqlDatabaseName;User ID=$sqlDatabaseLogin;Password=$sqlDatabasePassword;Encrypt=true;Trusted_Connection=false;"

	変数の詳細については、このチュートリアルの「[前提条件](#prerequisites)」セクションを参照してください。

3. スクリプト ウィンドウの末尾に、次のスクリプトを追加します。これは 2 つのテーブルとクラスター化インデックスを定義する SQL ステートメントです。SQL データベースにはクラスター化インデックスが必要です。

		# SQL query strings for creating tables and clustered indexes
		$cmdCreateLog4jTable = "CREATE TABLE [dbo].[log4jlogs](
		    [t1] [nvarchar](50), 
		    [t2] [nvarchar](50), 
		    [t3] [nvarchar](50), 
		    [t4] [nvarchar](50), 
		    [t5] [nvarchar](50), 
		    [t6] [nvarchar](50), 
		    [t7] [nvarchar](50))"
		
		$cmdCreateLog4jClusteredIndex = "CREATE CLUSTERED INDEX log4jlogs_clustered_index on log4jlogs(t1)"
		
		$cmdCreateMobileTable = " CREATE TABLE [dbo].[mobiledata](
		[clientid] [nvarchar](50), 
		[querytime] [nvarchar](50), 
		[market] [nvarchar](50), 
		[deviceplatform] [nvarchar](50), 
		[devicemake] [nvarchar](50), 
		[devicemodel] [nvarchar](50), 
		[state] [nvarchar](50), 
		[country] [nvarchar](50), 
		[querydwelltime] [float],
		[sessionid] [bigint],
		[sessionpagevieworder][bigint])"
		
		$cmdCreateMobileDataClusteredIndex = "CREATE CLUSTERED INDEX mobiledata_clustered_index on mobiledata(clientid)"

4. SQL コマンドを実行するために、スクリプト ウィンドウの末尾に次のスクリプトを追加します。

		Write-Host "Connect to the SQL Database ..." -ForegroundColor Green
		$conn = New-Object System.Data.SqlClient.SqlConnection
		$conn.ConnectionString = $sqlDatabaseConnectionString
		$conn.Open()
		
		Write-Host "Create log4j table and clustered index ..." -ForegroundColor Green
		$cmd = New-Object System.Data.SqlClient.SqlCommand
		$cmd.Connection = $conn
		$cmd.CommandText = $cmdCreateLog4jTable
		$ret = $cmd.ExecuteNonQuery()
		$cmd.CommandText = $cmdCreateLog4jClusteredIndex
		$cmd.ExecuteNonQuery()
		
		Write-Host "Create log4j table and clustered index ..." -ForegroundColor Green
		$cmd.CommandText = $cmdCreateMobileTable
		$cmd.ExecuteNonQuery()
		$cmd.CommandText = $cmdCreateMobileDataClusteredIndex
		$cmd.ExecuteNonQuery()
		
		Write-Host "Close connection ..." -ForegroundColor Green		
		$conn.close()
		
		Write-Host "Done" -ForegroundColor Green
	
5. **[スクリプトの実行]** をクリックするか、**F5** キーを押して、スクリプトを実行します。
6. [Azure 管理ポータル][azure-management-portal]を使用して、テーブルとクラスター化インデックスを確認します。

このチュートリアルでは、log4j ログ ファイル (区切り記号で区切られたファイル) と Hive テーブルを SQL データベースにエクスポートします。区切られたファイルは */example/data/sample.log* です。チュートリアルの前の方で、log4j ログのサンプルをいくつか示しました。ログ ファイルには空白行がいくつかあり、その他の行は次のような内容です。

	java.lang.Exception: 2012-02-03 20:11:35 SampleClass2 [FATAL] unrecoverable system problem at id 609774657
		at com.osa.mocklogger.MockLogger$2.run(MockLogger.java:83)

行から *java.lang.Exception:* を削除すると、レコードが正しく解析されます。

空の文字列がある場合、または、SQL データベース テーブルで定義されているフィールドの数よりも要素の数が少ない行がある場合、Sqoop エクスポートは失敗します。log4jlogs テーブルには 7 個の文字列型のフィールドがあります。

**sample.log ファイルを前処理するには**

1. Windows PowerShell ISE を開きます。
2. 下のウィンドウで、次のコマンドを実行して、Azure サブスクリプションに接続します。

		Add-AzureAccount

	Azure アカウント資格情報の入力を求められます。サブスクリプション接続を追加するこの方法はタイム アウトし、12 時間後には、再度ログインする必要があります。

	> [WACOM.NOTE] Azure サブスクリプションが複数あり、使用するサブスクリプションが既定のサブスクリプションではない場合は、<strong>Select-AzureSubscription</strong> コマンドレットを使用して現在のサブスクリプションを選択します。

3. 次のスクリプトをスクリプト ウィンドウにコピーし、最初の 2 個の変数を設定します。
		
		$storageAccountName = "<WindowsAzureStorageAccountName>"
		$containerName = "<BlobContainerName>"
		
		$sourceBlobName = "example/data/sample.log"
		$destBlobName = "tutorials/usesqoop/data/sample.log"

	変数の詳細については、このチュートリアルの「[前提条件](#prerequisites)」セクションを参照してください。
 
4. スクリプト ウィンドウの末尾に、次のスクリプトを追加します。

		# Define the connection string
		$storageAccountKey = get-azurestoragekey $storageAccountName | %{$_.Primary}
		$storageConnectionString = "DefaultEndpointsProtocol=https;AccountName=$storageAccountName;AccountKey=$storageAccountKey"
		
		# Create block blob objects referencing the source and destination blob.
		$storageAccount = [Microsoft.WindowsAzure.Storage.CloudStorageAccount]::Parse($storageConnectionString)
		$storageClient = $storageAccount.CreateCloudBlobClient();
		$storageContainer = $storageClient.GetContainerReference($containerName)
		$sourceBlob = $storageContainer.GetBlockBlobReference($sourceBlobName)
		$destBlob = $storageContainer.GetBlockBlobReference($destBlobName)
		
		# Define a MemoryStream and a StreamReader for reading from the source file
		$stream = New-Object System.IO.MemoryStream
		$stream = $sourceBlob.OpenRead()
		$sReader = New-Object System.IO.StreamReader($stream)
		
		# Define a MemoryStream and a StreamWriter for writing into the destination file
		$memStream = New-Object System.IO.MemoryStream
		$writeStream = New-Object System.IO.StreamWriter $memStream
		
		# process the source blob
		$exString = "java.lang.Exception:"
		while(-Not $sReader.EndOfStream){
		    $line = $sReader.ReadLine()
		    $split = $line.Split(" ")
		
		    # remove the "java.lang.Exception" from the first element of the array
		    # for example: java.lang.Exception: 2012-02-03 19:11:02 SampleClass8 [WARN] problem finding id 153454612
		    if ($split[0] -eq $exString){
		        #create a new ArrayList to remove $split[0]
		        $newArray = [System.Collections.ArrayList] $split
		        $newArray.Remove($exString)
		
		        # update $split and $line
		        $split = $newArray
		        $line = $newArray -join(" ")
		    }
		
		    # remove the lines that has less than 7 elements
		    if ($split.count -ge 7){
		        write-host $line
		        $writeStream.WriteLine($line)
		    }
		}
		
		# Write to the destination blob
		$writeStream.Flush()
		$memStream.Seek(0, "Begin")
		$destBlob.UploadFromStream($memStream)

5. **[スクリプトの実行]** をクリックするか、**F5** キーを押して、スクリプトを実行します。
6. 変更されたデータ ファイルを確認するには、Azure 管理ポータル、Azure ストレージ エクスプ ローラー ツール、または Azure PowerShell を使用できます。「[Azure HDInsight の概要][hdinsight-get-started]」に、PowerShell を使用してファイルをダウンロードしファイルの内容を表示するコード例が示されています。










































































##<a id="export"></a>PowerShell を使用して Sqoop エクスポートを実行する

このセクションでは、Azure PowerShell を使用して Sqoop エクスポート コマンドを実行し、Hive テーブルとデータ ファイルの両方を Azure SQL データベースにエクスポートします。次のセクションでは、HDInsight の .NET サンプルを示します。

**log4j ログ ファイルをエクスポートするには**

1. Windows PowerShell ISE を開きます。
2. 下のウィンドウで、次のコマンドを実行して、Azure サブスクリプションに接続します。

		Add-AzureAccount

	Azure アカウント資格情報の入力を求められます。

3. 次のスクリプトをスクリプト ウィンドウにコピーし、最初の 7 個の変数を設定します。

		# Define the cluster variables
		$clusterName = "<HDInsightClusterName>"
		$storageAccountName = "<WindowsAzureStorageAccount>"
		$containerName = "<BlobStorageContainerName>"
		
		# Define the SQL database variables
		$sqlDatabaseServerName = "<SQLDatabaseServerName>"
		$sqlDatabaseLogin = "<SQLDatabaseUsername>"
		$sqlDatabasePassword = "SQLDatabasePassword>"
		$databaseName = "<SQLDatabaseName>"

		$tableName_log4j = "log4jlogs"
		
		$connectionString = "jdbc:sqlserver://$sqlDatabaseServerName.database.windows.net;user=$sqlDatabaseLogin@$sqlDatabaseServerName;password=$sqlDatabasePassword;database=$databaseName"
		
		$exportDir_log4j = "/tutorials/usesqoop/data"
	
	変数の詳細については、このチュートリアルの「[前提条件](#prerequisites)」セクションを参照してください。

	$exportDir_log4j に sample.log ファイルの名前が指定されていないことに注意してください。Sqoop は、そのフォルダーの下にあるすべてのファイルからデータをエクスポートします。

4. スクリプト ウィンドウの末尾に、次のスクリプトを追加します。

		# Submit a Sqoop job
		$sqoopDef = New-AzureHDInsightSqoopJobDefinition -Command "export --connect $connectionString --table $tableName_log4j --export-dir $exportDir_log4j --input-fields-terminated-by \0x20 -m 1"
		$sqoopJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $sqoopDef #-Debug -Verbose
		Wait-AzureHDInsightJob -WaitTimeoutInSeconds 3600 -Job $sqoopJob
		
		Write-Host "Standard Error" -BackgroundColor Green
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $sqoopJob.JobId -StandardError
		Write-Host "Standard Output" -BackgroundColor Green
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $sqoopJob.JobId -StandardOutput

	フィールドの区切り記号は **\0x20** (スペース) です。区切り記号は、sample.log ファイルを前処理する PowerShell スクリプトで定義されています。**-m 1** については、[Sqoop ユーザー ガイド][sqoop-user-guide-1.4.4]を参照してください。

5. **[スクリプトの実行]** をクリックするか、**F5** キーを押して、スクリプトを実行します。
6. [Azure 管理ポータル][azure-management-portal]を使用して、エクスポートされたデータを確認します。

**Hivesampletable Hive テーブルをエクスポートするには**

1. Windows PowerShell ISE を開きます。
2. 下のウィンドウで、次のコマンドを実行して、Azure サブスクリプションに接続します。

		Add-AzureAccount

	Azure アカウント資格情報の入力を求められます。

3. 次のスクリプトをスクリプト ウィンドウにコピーし、最初の 7 個の変数を設定します。

		# Define the cluster variables
		$clusterName = "<HDInsightClusterName>"
		$storageAccountName = "<WindowsAzureStorageAccount>"
		$containerName = "<BlobStorageContainerName>"
		
		# Define the SQL database variables
		$sqlDatabaseServerName = "<SQLDatabaseServerName>"
		$sqlDatabaseLogin = "<SQLDatabaseUsername>"
		$sqlDatabasePassword = "SQLDatabasePassword>"
		$databaseName = "SQLDatabaseName"

		$tableName_mobile = "mobiledata"
		
		$connectionString = "jdbc:sqlserver://$sqlDatabaseServerName.database.windows.net;user=$sqlDatabaseLogin@$sqlDatabaseServerName;password=$sqlDatabasePassword;database=$databaseName"
		
		$exportDir_mobile = "/hive/warehouse/hivesampletable"
	
	変数の詳細については、このチュートリアルの「[前提条件](#prerequisites)」セクションを参照してください。

4. スクリプト ウィンドウの末尾に、次のスクリプトを追加します。
		
		$sqoopDef = New-AzureHDInsightSqoopJobDefinition -Command "export --connect $connectionString --table $tableName_mobile --export-dir $exportDir_mobile --fields-terminated-by \t -m 1"
		
		
		$sqoopJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $sqoopDef #-Debug -Verbose
		Wait-AzureHDInsightJob -WaitTimeoutInSeconds 3600 -Job $sqoopJob
		
		Write-Host "Standard Error" -BackgroundColor Green
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $sqoopJob.JobId -StandardError
		Write-Host "Standard Output" -BackgroundColor Green
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $sqoopJob.JobId -StandardOutput

5. **[スクリプトの実行]** をクリックするか、**F5** キーを押して、スクリプトを実行します。
6. [Azure 管理ポータル][azure-management-portal]を使用して、エクスポートされたデータを確認します。



##<a id="export-sdk"></a>HDInsight .NET SDK を使用して Sqoop エクスポートを実行する

以下は、HDInsight .NET SDK を使用して Sqoop エクスポートを実行する C# サンプルです。HDInsight .NET SDK の使用方法に関する一般的情報については、「[プログラムによる Hadoop ジョブの送信][hdinsight-submit-jobs]」を参照してください。


	using System;
	using System.Collections.Generic;
	using System.Linq;
	using System.Text;
	using System.Threading.Tasks;
	using System.IO;
	using System.Threading;
	using System.Security.Cryptography.X509Certificates;
	using Microsoft.WindowsAzure.Management.HDInsight;
	using Microsoft.Hadoop.Client;
	
	namespace sqoopSDKSample
	{
	    class Program
	    {
	        static void Main(string[] args)
	        {
	            // Set the variables
	            string subscriptionID = "<WindowsAzureSubscriptionID>";
	            string clusterName = "<HDInsightClusterName>";
	            string certFriendlyName = "<WindowsAzureCertificateFriendlyName>";
	            string sqlDatabaseServerName = "<SQLDatabaseServerName>";
	            string sqlDatabaseLogin = "<SQLDatabaseLogin>" + "@" + sqlDatabaseServerName;
	            string sqlDatabaseLoginPassword = "<SQLDatabaseLoginPassword>";
	            string sqlDatabaseDatabaseName = "hdisqoop";
	            string sqlDatabaseTableName = "log4jlogs";
	
	            cmdExport = @"export";
	            cmdExport = cmdExport + @" --connect jdbc:sqlserver://" + sqlDatabaseServerName + ".database.windows.net;user=" + sqlDatabaseLogin + ";password=" + sqlDatabaseLoginPassword + ";database=" + sqlDatabaseDatabaseName;
	            cmdExport = cmdExport + @" --table " + sqlDatabaseTableName;
	            cmdExport = cmdExport + @" --export-dir /tutorials/usesqoop/data";
	            cmdExport = cmdExport + @" --input-fields-terminated-by \0x20 -m 1";
	
	            SqoopJobCreateParameters sqoopJobDefinition = new SqoopJobCreateParameters()
	            {
	                Command = cmdExport,
	                StatusFolder = "/tutorials/usesqoop/jobStatus"
	            };
	
	            // Get the certificate object from certificate store using the friendly name to identify it
	            X509Store store = new X509Store();
	            store.Open(OpenFlags.ReadOnly);
	            X509Certificate2 cert = store.Certificates.Cast<X509Certificate2>().First(item => item.FriendlyName == certFriendlyName);
	            JobSubmissionCertificateCredential creds = new JobSubmissionCertificateCredential(new Guid(subscriptionID), cert, clusterName);
	
	            // Submit the Hive job
	            var jobClient = JobSubmissionClientFactory.Connect(creds);
	            JobCreationResults jobResults = jobClient.CreateSqoopJob(sqoopJobDefinition);
	
	            // Wait for the job to complete
	            WaitForJobCompletion(jobResults, jobClient);
	
	            // Print the Hive job output
	            System.IO.Stream stream = jobClient.GetJobErrorLogs(jobResults.JobId);
	
	            StreamReader reader = new StreamReader(stream);
	            Console.WriteLine(reader.ReadToEnd());
	
	            Console.WriteLine("Press ENTER to continue.");
	            Console.ReadLine();
	        }
	
	        private static void WaitForJobCompletion(JobCreationResults jobResults, IJobSubmissionClient client)
	        {
	            JobDetails jobInProgress = client.GetJob(jobResults.JobId);
	            while (jobInProgress.StatusCode != JobStatusCode.Completed && jobInProgress.StatusCode != JobStatusCode.Failed)
	            {
	                jobInProgress = client.GetJob(jobInProgress.JobId);
	                Thread.Sleep(TimeSpan.FromSeconds(10));
	            }
	        }
	    }
	}

スクリプト ファイルを実行するには、

	Command = cmdExport,

 を以下に置き換えます。

	File = "/tutorials/usesqoop/sqoopexport.txt",

スクリプト ファイルは、WASB に配置する必要があります。




##<a id="import"></a>PowerShell を使用して Sqoop インポートを実行する

このセクションでは、log4j ログ (SQL データベースにエクスポートしたデータ) を HDInsight にインポートします。

1. Windows PowerShell ISE を開きます。
2. 下のウィンドウで、次のコマンドを実行して、Azure サブスクリプションに接続します。

		Add-AzureAccount

	Azure アカウント資格情報の入力を求められます。

3. 次のスクリプトをスクリプト ウィンドウにコピーし、最初の 7 個の変数を設定します。

		# Define the cluster variables
		$clusterName = "<HDInsightClusterName>"
		$storageAccountName = "<WindowsAzureStorageAccount>"
		$containerName = "<BlobStorageContainerName>"
		
		# Define the SQL database variables
		$sqlDatabaseServerName = "<SQLDatabaseServerName>"
		$sqlDatabaseLogin = "<SQLDatabaseUsername>"
		$sqlDatabasePassword = "SQLDatabasePassword>"
		$databaseName = "SQLDatabaseName"

		$tableName_log4j = "log4jlogs"
		
		$connectionString = "jdbc:sqlserver://$sqlDatabaseServerName.database.windows.net;user=$sqlDatabaseLogin@$sqlDatabaseServerName;password=$sqlDatabasePassword;database=$databaseName"
		
		$tableName_mobile = "mobiledata"
		$targetDir_mobile = "/tutorials/usesqoop/importeddata/"
	
	変数の詳細については、このチュートリアルの「[前提条件](#prerequisites)」セクションを参照してください。

4. スクリプト ウィンドウの末尾に、次のスクリプトを追加します。
	
		$sqoopDef = New-AzureHDInsightSqoopJobDefinition -Command "import --connect $connectionString --table $tableName_mobile --target-dir $targetDir_mobile --fields-terminated-by \t --lines-terminated-by \n -m 1"
		
		$sqoopJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $sqoopDef #-Debug -Verbose
		Wait-AzureHDInsightJob -WaitTimeoutInSeconds 3600 -Job $sqoopJob
		
		Write-Host "Standard Error" -BackgroundColor Green
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $sqoopJob.JobId -StandardError
		Write-Host "Standard Output" -BackgroundColor Green
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $sqoopJob.JobId -StandardOutput

5. **[スクリプトの実行]** をクリックするか、**F5** キーを押して、スクリプトを実行します。
6. 変更されたデータ ファイルを確認するには、Azure 管理ポータル、Azure ストレージ エクスプ ローラー ツール、または Azure PowerShell を使用できます。「[Azure HDInsight の概要][hdinsight-get-started]」に、PowerShell を使用してファイルをダウンロードしファイルの内容を表示するコード例が示されています。

##<a id="nextsteps"></a>次のステップ

ここでは Sqoop の使用方法を説明しました。詳細については、次を参照してください。

- [HDInsight での Oozie の使用][hdinsight-oozie]: Oozie ワークフローで Sqoop アクションを使用します。
- [HDInsight を使用したフライト遅延データの分析][hdinsight-analyze-flight-delay-data]: Hive を使用してフライト遅延データを分析し、Sqoop を使用して SQL データベースにデータをエクスポートします。
- [HDInsight へのデータのアップロード][hdinsight-upload-data]: HDInsight/Azure BLOB ストレージにデータをアップロードするその他の方法を説明します。


 

[azure-management-portal]: https://manage.windowsazure.com/

[hdinsight-versions]:  ../hdinsight-component-versioning/
[hdinsight-provision]: ../hdinsight-provision-clusters/
[hdinsight-get-started]: ../hdinsight-get-started/
[hdinsight-storage]: ../hdinsight-use-blob-storage/
[hdinsight-analyze-flight-delay-data]: ../hdinsight-analyze-flight-delay-data/
[hdinsight-oozie]: ../hdinsight-use-oozie/
[hdinsight-upload-data]: ../hdinsight-upload-data/
[hdinsight-submit-jobs]: ../hdinsight-submit-hadoop-jobs-programmatically/

[sqldatabase-get-started]: ../sql-database-get-started/
[sqldatabase-create-configue]: ../sql-database-create-configure/

[powershell-start]: http://technet.microsoft.com/ja-jp/library/hh847889.aspx
[powershell-install]: ../install-configure-powershell
[powershell-script]: http://technet.microsoft.com/ja-jp/library/ee176949.aspx

[sqoop-user-guide-1.4.4]: https://sqoop.apache.org/docs/1.4.4/SqoopUserGuide.html

