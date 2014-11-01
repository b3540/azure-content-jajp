<properties title="Generate movie recommendations using Mahout" pageTitle="Generate movie recommendations using Mahout with Microsoft Azure HDInsight (Hadoop)" description="Learn how to use the Apache Mahout machine learning library to generate movie recommendations with HDInsight (Hadoop)" metaKeywords="Azure hdinsight mahout, Azure hdinsight machine learning, azure hadoop mahout, azure hadoop machine learning" services="hdinsight" solutions="" documentationCenter="big-data" authors="larryfr" videoId="" scriptId="" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="larryfr"></tags>

# HDInsight (Hadoop) で Apache Mahout を使用して映画のリコメンデーションを生成する

[Apache Mahout][Apache Mahout] 機械学習ライブラリを使用して Microsoft Azure HDInsight (Hadoop) で映画のリコメンデーションを生成する方法について説明します。

> [WACOM.NOTE] この記事の情報を使用するには HDInsight クラスターが必要です。作成の詳細については、「[Get started with Hadoop in HDInsight (Azure の HDInsight の概要)][Get started with Hadoop in HDInsight (Azure の HDInsight の概要)]」を参照してください。

> [WACOM.NOTE] Mahout は HDInsight 3.1 クラスターと共に提供されます。以前のバージョンの HDInsight を使用している場合は、次に進む前に「[Install Mahout (Mahout のインストール)][Install Mahout (Mahout のインストール)]」を参照してください。

## <a name="learn"></a>学習内容

Mahout は、Apache Hadoop の[機械学習][機械学習]ライブラリの 1 つです。Mahout には、フィルター処理、分類、クラスタリングなどデータを処理するためのアルゴリズムが含まれています。この記事では、リコメンデーション エンジンを使用し、友人たちが鑑賞した映画に基づいて映画のリコメンデーションを生成します。また、デシジョン フォレストで分類を実行する方法についても説明します。学習内容は以下のとおりです。

-   Mahout ジョブを PowerShell から実行する方法

-   Mahout ジョブを Hadoop コマンド ラインから実行する方法

-   Mahout を HDInsight 2.0 および 3.0 クラスターにインストールする方法

## この記事の内容

-   [PowerShell を使用してリコメンデーションを生成する][PowerShell を使用してリコメンデーションを生成する]
-   [Hadoop コマンド ラインを使用してデータを分類する][Hadoop コマンド ラインを使用してデータを分類する]
-   [トラブルシューティング][トラブルシューティング]

## <a name="recommendations"></a>PowerShell を使用してリコメンデーションを生成する

> [WACOM.NOTE] このセクションで使用されるジョブは PowerShell で動作しますが、Mahout で提供されるクラスの多くは現在 PowerShell で動作しないため、Hadoop コマンド ラインを使用して実行する必要があります。PowerShell で動作しないクラスの一覧については、「[トラブルシューティング][トラブルシューティング]」セクションを参照してください。
>
> Hadoop コマンド ラインを使用して Mahout ジョブを実行する例については、「[Hadoop コマンド ラインを使用してデータを分類する][Hadoop コマンド ラインを使用してデータを分類する]」を参照してください。

Mahout で提供される機能の 1 つがリコメンデーション エンジンです。データは、`userID`、`itemId`、`prefValue` (項目に対するユーザーの嗜好) の形式で受け付けられます。Mahout では、共起分析を実行して、*ある項目を嗜好するユーザーが他の項目も嗜好する*ということを判断できます。次に Mahout は、項目の嗜好が似ているユーザーたちを特定します。これはリコメンデーションの作成に使用できます。

映画を使用した非常にシンプルな例を次に示します。

-   **共起** - Joe、Alice、および Bob は全員、好きな映画として「*Star Wars (スター ウォーズ)*」、「*The Empire Strikes Back (帝国の逆襲)*」、および「*Return of the Jedi (ジェダイの帰還)*」を挙げました。Mahout では、これらの映画のいずれかを好きなユーザーが他の 2 作品も好きであると判断します。

-   **共起** - Bob と Alice は「*The Phantom Menace (ファントム メナス)*」、「*Attack of the Clones (クローンの攻撃)*」、および「*Revenge of the Sith (シスの復讐)*」も好きな映画として選びました。Mahout では、この例の最初の 3 作品を好きなユーザーがこれらの 3 作品も好きであると判断します。

-   **類似性のリコメンデーション** - Joe は、この例の最初の 3 作品を好きな映画として選びました。Mahout では、嗜好が似ている他のユーザーが好きな映画の中で、Joe がまだ観ていない映画を調べます (好み/順位)。この場合、Mahout では、「*The Phantom Menace (ファントム メナス)*」、「*Attack of the Clones (クローンの攻撃)*」、および「*Revenge of the Sith (シスの復讐)*」を推薦します。

### データを読み込む

GroupLens Research では利便性を高めるために、Mahout と互換性のある形式で[映画の順位データ][映画の順位データ]を提供します。

1.  [MovieLens 100k][MovieLens 100k] アーカイブをダウンロードします。これには、1,700 本の映画に対する 1,000 人のユーザーによる評価 100,000 件が含まれています。

2.  アーカイブを展開します。これには、名前の前に **u.** が付いた多数のデータ ファイルを格納した **ml-100k** ディレクトリが含まれています。Mahout により分析されるファイルは **u.data** です。このファイルのデータ構造は、`userID`、`movieID`、`userRating`、および `timestamp` です。次にデータの例を示します。

        196 242 3   881250949
        186 302 3   891717742
        22  377 1   878887116
        244 51  2   880606923
        166 346 1   886397596

3.  **u.data** ファイルを、HDInsight クラスターの **example/data/u.data** にアップロードします。[Azure PowerShell][Azure PowerShell] を持っている場合は、[HDInsight-Tools][HDInsight-Tools] の PowerShell モジュールを使用してファイルをアップロードできます。ファイルをアップロードするその他の方法については、「[データを HDInsight にアップロードする方法][データを HDInsight にアップロードする方法]」を参照してください。以下は、`Add-HDInsightFile` を使用したファイルのアップロードを示しています。

        PS C:PS C:\> Add-HDInsightFile -LocalPath "path\to\u.data" -DestinationPath "example/data/u.data" -ClusterName "your cluster name"gt; Add-HDInsightFile -LocalPath "path\to\u.data" -DestinationPath "example/data/u.data" -ClusterName "your cluster name"

    これは、使用するクラスターの既定のストレージ内の **example/data/u.data** に **u.data** ファイルをアップロードします。次に、**wasb:///example/data/u.data** URI を使用して、HDInsight ジョブからこのデータにアクセスできます。

### ジョブを実行する

次の PowerShell スクリプトを使用し、前にアップロードした **u.data** ファイルを含む Mahout リコメンデーション エンジンを使用してジョブを実行します。

    # The HDInsight cluster name.
    $clusterName = "the cluster name"

    # The location of the Mahout jar file.
    $jarFile = "C:\apps\dist\mahout-0.9.0.2.1.3.0-1887\examples\target\mahout-examples-0.9.0.2.1.3.0-1887-job.jar"
    # NOTE: The version number portion of the file path
    # may change in future versions of HDInsight.
    # Use the following to find the location and name
    # of the Mahout jar on HDInsight 3.1, and modify
    # the $jarFile= line to point to it.
    #
    # Use-AzureHDInsightCluster -Name $clusterName
    # $jarFile = Invoke-Hive -Query '!${env:COMSPEC} /c dir /b /s ${env:MAHOUT_HOME}\examples\target\*-job.jar'
    #
    # If you are using an earlier version of HDInsight,
    # set $jarFile to the jar file you
    # uploaded.
    # For example,
    # $jarFile = "wasb:///example/jars/mahout-core-0.9-job.jar"

    # The arguments for this job
    # * input - the path to the data uploaded to HDInsight
    # * output - the path to store output data
    # * tempDir - the directory for temp files
    $jobArguments = "-s", "SIMILARITY_COOCCURRENCE",
                    "--input", "wasb:///example/data/u.data",
                    "--output", "wasb:///example/out",
                    "--tempDir", "wasb:///temp/mahout"

    # Create the job definition
    $jobDefinition = New-AzureHDInsightMapReduceJobDefinition `
      -JarFile $jarFile `
      -ClassName "org.apache.mahout.cf.taste.hadoop.item.RecommenderJob" `
      -Arguments $jobArguments

    # Start the job
    $job = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $jobDefinition

    # Wait on the job to complete
    Write-Host "Wait for the job to complete ..." -ForegroundColor Green
    Wait-AzureHDInsightJob -Job $job

    # Write out any error information
    Write-Host "STDERR"
    Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $job.JobId -StandardError

> [WACOM.NOTE] Mahout ジョブは、ジョブの処理中に作成された一時データを削除しません。そのため、このサンプル ジョブでは `--tempDir` パラメーターを指定し、一時ファイルを特定のパスに分離して簡単に削除できるようにしています。
>
> これらのファイルを削除するには、「[データを HDInsight にアップロードする方法][データを HDInsight にアップロードする方法]」で説明されているユーティリティのいずれかを使用できます。または、[HDInsight-Tools][HDInsight-Tools] の PowerShell スクリプト内の `Remove-HDInsightFile` 関数を使用します。
>
> 一時ファイルまたは出力ファイルを削除しなかった場合、ジョブをもう一度実行するとエラーが返されます。

Mahout ジョブは出力を STDOUT に返しません。代わりに、指定された出力ディレクトリに **part-r-00000** として格納します。ファイルをダウンロードして表示するには、[HDInsight-Tools][HDInsight-Tools] の PowerShell モジュールの `Get-HDInsightFile` 関数を使用します。

ファイルの内容の例を次に示します。

    1   [234:5.0,347:5.0,237:5.0,47:5.0,282:5.0,275:5.0,88:5.0,515:5.0,514:5.0,121:5.0]
    2   [282:5.0,210:5.0,237:5.0,234:5.0,347:5.0,121:5.0,258:5.0,515:5.0,462:5.0,79:5.0]
    3   [284:5.0,285:4.828125,508:4.7543354,845:4.75,319:4.705128,124:4.7045455,150:4.6938777,311:4.6769233,248:4.65625,272:4.649266]
    4   [690:5.0,12:5.0,234:5.0,275:5.0,121:5.0,255:5.0,237:5.0,895:5.0,282:5.0,117:5.0]

最初の列は `userID` です。'[' と ']' に含まれる値は、`movieId`:`recommendationScore` です。

### 出力を表示する

生成された出力はアプリケーションで使用できるものですが、人間が判読するのは困難です。以前に **ml-100k** フォルダーに抽出されたその他のファイルの一部を使用して、`movieId` を映画名に解決することができます。そのために使用する Python スクリプトが **ml-100k** フォルダーに含まれていますが (**show\_recommendations.py**)、次の PowerShell スクリプトを使用することもできます。

    <#
    .SYNOPSIS
        Displays recommendations for movies.
    .DESCRIPTION
        Displays recommendations generated by Mahout
        with HDInsight example in a human readable format.
    .EXAMPLE
        .\Show-Recommendation -userId 4
            -userDataFile "u.data"
            -movieFile "u.item"
            -recommendationFile "output.txt"
    #>

    [CmdletBinding(SupportsShouldProcess = $true)]
    param(
        #The user ID
        [Parameter(Mandatory = $true)]
        [String]$userId,

        [Parameter(Mandatory = $true)]
        [String]$userDataFile,

        [Parameter(Mandatory = $true)]
        [String]$movieFile,

        [Parameter(Mandatory = $true)]
        [String]$recommendationFile
    )
    # Read movie ID & description into hash table
    Write-Host "Reading movies descriptions" -ForegroundColor Green
    $movieById = @{}
    foreach($line in Get-Content $movieFile)
    {
        $tokens = $line.Split("|")
        $movieById[$tokens[0]] = $tokens[1]
    }
    # Load movies user has already seen (rated)
    # into a hash table
    Write-Host "Reading rated movies" -ForegroundColor Green
    $ratedMovieIds = @{}
    foreach($line in Get-Content $userDataFile)
    {
        $tokens = $line.Split("`t")
        if($tokens[0] -eq $userId)
        {
            # Resolve the ID to the movie name
            $ratedMovieIds[$movieById[$tokens[1]]] = $tokens[2]
        }
    }
    # Read recommendations generated by Mahout
    Write-Host "Reading recommendations" -ForegroundColor Green
    $recommendations = @{}
    foreach($line in get-content $recommendationFile)
    {
        $tokens = $line.Split("`t")
        if($tokens[0] -eq $userId)
        {
            #Trim leading/treailing [] and split at ,
            $movieIdAndScores = $tokens[1].TrimStart("[").TrimEnd("]").Split(",")
            foreach($movieIdAndScore in $movieIdAndScores)
            {
                #Split at : and store title and score in a hash table
                $idAndScore = $movieIdAndScore.Split(":")
                $recommendations[$movieById[$idAndScore[0]]] = $idAndScore[1]
            }
            break
        }
    }

    Write-Host "Rated movies" -ForegroundColor Green
    Write-Host "---------------------------" -ForegroundColor Green
    $ratedFormat = @{Expression={$_.Name};Label="Movie";Width=40}, `
                   @{Expression={$_.Value};Label="Rating"}
    $ratedMovieIds | format-table $ratedFormat
    Write-Host "---------------------------" -ForegroundColor Green

    write-host "Recommended movies" -ForegroundColor Green
    Write-Host "---------------------------" -ForegroundColor Green
    $recommendationFormat = @{Expression={$_.Name};Label="Movie";Width=40}, `
                            @{Expression={$_.Value};Label="Score"}
    $recommendations | format-table $recommendationFormat

このスクリプトを使用するには、以前に抽出された **ml-100k** フォルダー、および Mahout ジョブで生成された **part-r-00000** 出力ファイルのローカル コピーが必要です。スクリプトを実行する例を次に示します。

    PS C:PS C:\> show-recommendation.ps1 -userId 4 -userDataFile .\ml-100k\u.data -movieFile .\ml-100k\u.item -recommendationFile .\output.txtgt; show-recommendation.ps1 -userId 4 -userDataFile .\ml-100k\u.data -movieFile .\ml-100k\u.item -recommendationFile .\output.txt

> [WACOM.NOTE] サンプル Python スクリプトの **show\_recommendations.py** は同じパラメーターを使用します。

出力は次のようになります。

    Reading movies descriptions
    Reading rated movies
    Reading recommendations
    Rated movies
    ---------------------------
    Movie                                    Rating
    -----                                    ------
    Devil's Own, The (1997)                  1
    Alien: Resurrection (1997)               3
    187 (1997)                               2
    (lines ommitted)

    ---------------------------
    Recommended movies
    ---------------------------

    Movie                                    Score
    -----                                    -----
    Good Will Hunting (1997)                 4.6504064
    Swingers (1996)                          4.6862745
    Wings of the Dove, The (1997)            4.6666665
    People vs. Larry Flynt, The (1996)       4.834559
    Everyone Says I Love You (1996)          4.707071
    Secrets & Lies (1996)                    4.818182
    That Thing You Do! (1996)                4.75
    Grosse Pointe Blank (1997)               4.8235292
    Donnie Brasco (1997)                     4.6792455
    Lone Star (1996)                         4.7099237  

## <a name="classify"></a>Hadoop コマンド ラインを使用してデータを分類する

Mahout で利用可能は分類方法の 1 つは、[ランダム フォレスト][ランダム フォレスト]をビルドすることです。これは複数の手順から成るプロセスです。データの分類に使用されるデシジョン ツリーをトレーニング データで生成する手順も含まれます。Mahout により提供される **org.apache.mahout.classifier.df.tools.Describe** クラスが使用され、現在は Hadoop コマンド ラインを使用して実行する必要があります。

### データを読み込む
現在の Mahout の実装は、University of California の Irvine (UCI) リポジトリ形式との互換性があります [なぜこれが重要なのか、この形式は何か]。

1. ファイルを [][]<http://nsl.cs.unb.ca/NSL-KDD/></a> からダウンロードします。

  * [KDDTrain+.ARFF][KDDTrain+.ARFF] - トレーニング ファイル

  * [KDDTest+.ARFF][KDDTest+.ARFF] - テスト データ

2. 各ファイルを開き、<'@'> で始まる先頭の行を削除して、ファイルを保存します。これらが削除されていない場合、このデータを Mahout で使用するとエラーが返されます。

2. **example/data** をアップロードします。これは、[HDInsight-Tools][HDInsight-Tools] の PowerShell モジュール内の `Add-HDInsightFile` 関数を使用して実行できます。

### ジョブを実行する

1.  このジョブには Hadoop コマンド ラインが必要であるため、最初に [Azure 管理ポータル][Azure 管理ポータル]を介してリモート デスクトップを有効にする必要があります。このポータルで、HDInsight クラスターを選択し、**[構成]** ページの下部にある **[リモートの有効化]** を選択します。

    ![enable remote][enable remote]

    メッセージが表示されたら、リモート セッションに使用するユーザー名とパスワードを入力します。

2.  リモート アクセスが有効になったら、**[接続]** を選択して接続を開始します。これにより、リモート デスクトップ セッションの開始に使用できる **.rdp** ファイルがダウンロードされます。

    ![connect][connect]

3.  接続した後で、**[Hadoop コマンド ライン]** アイコンを使用して Hadoop コマンド ラインを開きます。

    ![hadoop cli][hadoop cli]

4.  次のコマンドを使用し、Mahout を使用してファイル記述子 (**KDDTrain+.info**) を生成します。

        hadoop jar "c:/apps/dist/mahout-0.9.0.2.1.3.0-1887/examples/target/mahout-examples-0.9.0.2.1.3.0-1887-job.jar" org.apache.mahout.classifier.df.tools.Describe -p "wasb:///example/data/KDDTrain+.arff" -f "wasb:///example/data/KDDTrain+.info" -d N 3 C 2 N C 4 N C 8 N 2 C 19 N L

    `N 3 C 2 N C 4 N C 8 N 2 C 19 N L` は、ファイル内のデータの属性を示します。1 は数値属性、2 はカテゴリ属性などです。L はラベルを示します。

5.  次のコマンドを使用してデシジョン ツリーのフォレストをビルドします。

        hadoop jar c:/apps/dist/mahout-0.9.0.2.1.3.0-1887/examples/target/mahout-examples-0.9.0.2.1.3.0-1887-job.jar org.apache.mahout.classifier.df.mapreduce.BuildForest -Dmapred.max.split.size=1874231 -d wasb:///example/data/KDDTrain+.arff -ds wasb:///example/data/KDDTrain+.info -sl 5 -p -t 100 -o nsl-forest

    この操作の出力は、HDInsight クラスターのストレージにある **nsl-forest** ディレクトリに格納されます (\_\_wasb://user/\<username\>/nsl-forest/nsl-forest.seq)。\<username\> は、リモート デスクトップ セッションに使用されるユーザー名です。これは人間が判読可能なファイルです。

6.  次のコマンドを使用して、**KDDTest+.arff** データセットを分類してフォレストをテストします。

        hadoop jar c:/apps/dist/mahout-0.9.0.2.1.3.0-1887/examples/target/mahout-examples-0.9.0.2.1.3.0-1887-job.jar org.apache.mahout.classifier.df.mapreduce.TestForest -i wasb:///example/data/KDDTest+.arff -ds wasb:///example/data/KDDTrain+.info -m nsl-forest -a -mr -o wasb:///example/data/predictions

    このコマンドは、次のような分類プロセスの概要情報を返します。

        14/07/02 14:29:28 INFO mapreduce.TestForest:

        =======================================================
        Summary
        -------------------------------------------------------
        Correctly Classified Instances          :      17560       77.8921%
        Incorrectly Classified Instances        :       4984       22.1079%
        Total Classified Instances              :      22544

        =======================================================
        Confusion Matrix
        -------------------------------------------------------
        a       b       <--Classified as
        9437    274      |  9711        a     = normal
        4710    8123     |  12833       b     = anomaly

        =======================================================
        Statistics
        -------------------------------------------------------
        Kappa                                       0.5728
        Accuracy                                   77.8921%
        Reliability                                53.4921%
        Reliability (standard deviation)            0.4933

  このジョブでは、**wasb:///example/data/predictions/KDDTest+.arff.out** のファイルも生成されますが、これは人間が判読できないファイルです。

> [WACOM.NOTE] Mahout ジョブはファイルを上書きしません。これらのジョブをもう一度実行する場合は、前のジョブで作成したファイルを削除する必要があります。

## <a name="troubleshooting"></a>トラブルシューティング

### <a name="install"></a>Mahout のインストール
Mahout は HDInsight 3.1 クラスターにインストールされますが、次の手順を使用して手動で 3.0 または 2.1 クラスターにインストールできます。

1. 使用する Mahout のバージョンは、使用するクラスターの HDInsight バージョンによって異なります。[Azure PowerShell][Azure PowerShell] で以下を使用することにより、クラスター バージョンを特定できます。

    	PS C:\> Get-AzureHDInsightCluster -Name YourClusterName | Select version


  * **HDInsight 2.1 の場合は**、[Mahout 0.9][Mahout 0.9] を含む jar ファイルをダウンロードできます。

  * **HDInsight 3.0 の場合は**、[Mahout をソースからビルド][Mahout をソースからビルド]して HDInsight により提供される Hadoop バージョンを指定する必要があります。ビルド ページに示された前提条件をインストールし、ソースをダウンロードして、次のコマンドを使用して Mahout jar ファイルを作成します。

			mvn -Dhadoop2.version=2.2.0 -DskipTests clean package

    	ビルドが完了したら、jarファイルをに作成されます __mahout\mrlegacy\target\mahout-mrlegacy-1.0-SNAPSHOT-job.jar__.

    	> [WACOM.NOTE] Once Mahout 1.0 is released, you should be able to use the pre-built packages with HDInsight 3.0.

2. この jar ファイルを、使用しているクラスターの既定のストレージ内の **example/jars** にアップロードします。次の例は、[send-hdinsight][sendhdinsight] スクリプトを使用してファイルをアップロードします。

    	PS C:\> .\Send-HDInsight -LocalPath "path\to\mahout-core-0.9-job.jar" -DestinationPath "example/jars/mahout-core-0.9-job.jar" -ClusterName "your cluster name"

### ファイルを上書きできない

Mahout ジョブは、処理中に作成された一時ファイルをクリーンアップしません。さらに、このジョブは既存の出力ファイルを上書きしません。

Mahout ジョブの実行中のエラーを回避するには、実行と実行の間で一時ファイルと出力ファイルを削除するか、一時ディレクトリと出力ディレクトリに一意の名前を使用します。

### jar ファイルが見つからない

HDInsight 3.1 に Mahout が含まれていますが、パスとファイル名にはクラスターにインストールされている Mahout のバージョン番号が含まれています。このチュートリアルの PowerShell サンプル スクリプトでは、2014 年 7 月時点で有効なパスを使用していますが、バージョン番号は将来の HDInsight の更新で変わります。使用しているクラスターの Mahout jar ファイルへの現在のパスを決定するには、次の PowerShell コマンドを使用して、返されるファイル パスを参照するようにスクリプトを変更します。

    Use-AzureHDInsightCluster -Name $clusterName
    $jarFile = Invoke-Hive -Query '!${env:COMSPEC} /c dir /b /s ${env:MAHOUT_HOME}\examples\target\*-job.jar'

### <a name="nopowershell"></a>PowerShell で動作しないクラス

次のクラスを使用する Mahout ジョブを PowerShell から使用すると、さまざまなエラーが返されます。

-   org.apache.mahout.utils.clustering.ClusterDumper
-   org.apache.mahout.utils.SequenceFileDumper
-   org.apache.mahout.utils.vectors.lucene.Driver
-   org.apache.mahout.utils.vectors.arff.Driver
-   org.apache.mahout.text.WikipediaToSequenceFile
-   org.apache.mahout.clustering.streaming.tools.ResplitSequenceFiles
-   org.apache.mahout.clustering.streaming.tools.ClusterQualitySummarizer
-   org.apache.mahout.classifier.sgd.TrainLogistic
-   org.apache.mahout.classifier.sgd.RunLogistic
-   org.apache.mahout.classifier.sgd.TrainAdaptiveLogistic
-   org.apache.mahout.classifier.sgd.ValidateAdaptiveLogistic
-   org.apache.mahout.classifier.sgd.RunAdaptiveLogistic
-   org.apache.mahout.classifier.sequencelearning.hmm.BaumWelchTrainer
-   org.apache.mahout.classifier.sequencelearning.hmm.ViterbiEvaluator
-   org.apache.mahout.classifier.sequencelearning.hmm.RandomSequenceGenerator
-   org.apache.mahout.classifier.df.tools.Describe

これらのクラスを使用するジョブを実行するには、HDInsight クラスターに接続し、Hadoop コマンド ラインを使用してジョブを実行します。例については、「[Hadoop コマンド ラインを使用してデータを分類する][Hadoop コマンド ラインを使用してデータを分類する]」を参照してください。

  [Apache Mahout]: http://mahout.apache.org
  [Get started with Hadoop in HDInsight (Azure の HDInsight の概要)]: http://azure.microsoft.com/ja-jp/documentation/articles/hdinsight-get-started/
  [Install Mahout (Mahout のインストール)]: #install
  [機械学習]: http://en.wikipedia.org/wiki/Machine_learning
  [PowerShell を使用してリコメンデーションを生成する]: #recommendations
  [Hadoop コマンド ラインを使用してデータを分類する]: #classify
  [トラブルシューティング]: #troubleshooting
  [映画の順位データ]: http://grouplens.org/datasets/movielens/
  [MovieLens 100k]: http://files.grouplens.org/datasets/movielens/ml-100k.zip
  [Azure PowerShell]: http://azure.microsoft.com/ja-jp/documentation/articles/install-configure-powershell/
  [HDInsight-Tools]: https://github.com/Blackmist/hdinsight-tools
  [データを HDInsight にアップロードする方法]: http://azure.microsoft.com/ja-jp/documentation/articles/hdinsight-upload-data/
  [ランダム フォレスト]: http://en.wikipedia.org/wiki/Random_forest
  []: http://nsl.cs.unb.ca/NSL-KDD/
  [KDDTrain+.ARFF]: http://nsl.cs.unb.ca/NSL-KDD/KDDTrain+.arff
  [KDDTest+.ARFF]: http://nsl.cs.unb.ca/NSL-KDD/KDDTest+.arff
  [Azure 管理ポータル]: https://manage.windowsazure.com/
  [enable remote]: ./media/hdinsight-mahout/enableremote.png
  [connect]: ./media/hdinsight-mahout/connect.png
  [hadoop cli]: ./media/hdinsight-mahout/hadoopcli.png
  [Mahout 0.9]: http://repo2.maven.org/maven2/org/apache/mahout/mahout-core/0.9/mahout-core-0.9-job.jar
  [Mahout をソースからビルド]: http://mahout.apache.org/developers/buildingmahout.html