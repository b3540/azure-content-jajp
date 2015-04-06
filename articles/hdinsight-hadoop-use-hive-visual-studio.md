﻿<properties
   pageTitle="HDInsight での Hive の使用 | Azure"
   description="Visual Studio を通じて、HDInsight で Hive を使用する方法を説明します。"
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang=""
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="02/18/2015"
   ms.author="larryfr"/>

#Visual Studio の HDInsight ツールを使用して Hive クエリを実行

[AZURE.INCLUDE [hive-selector](../includes/hdinsight-selector-use-hive.md)]

この記事では、Visual Studio の HDInsight ツールを使用して、Hive クエリを HDInsight クラスターにリモートで送信する方法を説明します。

> [AZURE.NOTE] このドキュメントには、例で使用される HiveQL ステートメントで何が実行されるかに関する詳細は含まれていません。この例で使用される HiveQL の詳細については、「<a href="../hdinsight-use-hive/" target="_blank">HDInsight での Hive と Hadoop の使用</a>」を参照してください。

##<a id="prereq"></a>前提条件

この記事の手順を完了するには、次のものが必要です。

* Azure HDInsight (HDInsight での Hadoop) クラスター (Linux または Windows ベース)

* Visual Studio 2012 <a href="http://www.microsoft.com/download/details.aspx?id=39305" target="_blank">Update 4</a>、Visual Studio 2013 <a href="http://www.microsoft.com/download/details.aspx?id=43721" target="_blank">Update 3</a>、または <a href="http://www.microsoft.com/download/details.aspx?id=43722" target="_blank">Visual Studio Express 2013</a>

##<a id="run"></a>Visual Studio の HDInsight ツールを使用して Hive クエリを実行

1. **Visual Studio** を開き、**[新規]**、**[プロジェクト]**、**[HDInsight]**、**[Hive アプリケーション]** の順に選択します。プロジェクトの名前を指定します。

2. このプロジェクトで作成した **Script.hql** ファイルを開き、次の HiveQL ステートメントを貼り付けます。

        DROP TABLE log4jLogs;
        CREATE EXTERNAL TABLE log4jLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
        ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
        STORED AS TEXTFILE LOCATION 'wasb:///example/data/';
        SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' GROUP BY t4;

    これらのステートメントは次のアクションを実行します。

    * **DROP TABLE** - テーブルが既存の場合にテーブルとデータ ファイルを削除します。
    * **CREATE EXTERNAL TABLE** - Hive に新しく '外部'テーブルを作成します。外部テーブルは、Hive にテーブル定義のみを格納し、データは、元の場所に残します。

        > [AZURE.NOTE] 基盤となるデータを外部ソースによって更新する (データの自動アップロード処理など) 場合や別の MapReduce 操作によって更新する場合に、Hive クエリで最新のデータを使用する場合は、外部テーブルを使用する必要があります。
        >
        > 外部テーブルを削除しても、データは削除**されません**。テーブル定義のみが削除されます。

    * **ROW FORMAT** - Hive にデータの形式を示します。ここでは、各ログのフィールドは、スペースで区切られています。
    * **STORED AS TEXTFILE LOCATION** - Hive に、データの格納先 (example/data directory) と、データはテキストとして格納されていることを示します。
    * **SELECT** - **t4** 列の値が **[ERROR]** であるすべての行の数を指定します。ここでは、この値を含む列が 3 行あるため、**3** という値が返されています。

3. ツール バーで、このクエリに使用する **HDInsight クラスター**を選択し、**[送信]** を選択して、Hive ジョブとしてステートメントを実行します。**Hive ジョブの概要**に実行しているジョブに関する情報が表示されます。**[更新]** リンクを使用して、**ジョブのステータス**が**完了**に変更されるまで、ジョブの情報を更新します。

4. **[ジョブ出力]** リンクを使用して、このジョブの出力を表示します。SELECT ステートメントによって返された値「`[ERROR] 3`」が表示されます。

5. プロジェクトを作成せずに Hive クエリを実行することもできます。**サーバー エクスプローラー**を使用して **[Azure]**、**[HDInsight]** の順に展開し、HDInsight サーバーを右クリックして、**[Hive クエリを記述]** を選択します。

6. 表示された **temp.hql** ドキュメントに次の HiveQL ステートメントを追加します。

        CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
        INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]';

    これらのステートメントは次のアクションを実行します。

    * **CREATE TABLE IF NOT EXISTS** - 既存のテーブルがない場合、テーブルを作成します。 **EXTERNAL** キーワードが使用されていないため、これは内部テーブルであり、Hive のデータ保管先に格納され、完全に Hive によって管理されます。

        > [AZURE.NOTE] **EXTERNAL**テーブルとは異なり、内部デーブルを削除すると、基盤となるデータは削除されます。

    * **STORED AS ORC** - Optimized Row Columnar (ORC) 形式でデータを格納します。 この形式は、Hive にデータを格納するための、非常に効率的で適切な形式です。
    * **INSERT OVERWRITE ... SELECT** - **[ERROR]** を含む **log4jLogs** テーブルの列を選択し、**errorLogs** テーブルにデータを挿入します。

7. ツール バーのドロップダウンで **[送信]** を選択し、ジョブを実行します。**[ジョブ ステータス]** で、ジョブが正常に完了したことを確認します。

8. ジョブが完了し、新しいテーブルが作成されたことを確認するには、**サーバー エクスプローラー**で **[Azure]**、**[HDInsight]**、HDInsight クラスター、**[Hive データベース]**、**[既定]** の順に展開します。**errorLogs** および **log4jLogs** テーブルが表示されます。

##<a id="summary"></a>まとめ

このように、Visual Studio の HDInsight ツールを使用すると、HDInsight クラスターで簡単に Hive クエリを実行し、ジョブ ステータスを監視し、出力を取得できます。

##<a id="nextsteps"></a>次のステップ

HDInsight での Hive に関する全般的な情報

* [HDInsight での Hive と Hadoop の使用](../hdinsight-use-hive/)

HDInsight での Hadoop のその他の使用方法に関する情報

* [HDInsight での Pig と Hadoop の使用](../hdinsight-use-pig/)

* [HDInsight での MapReduce と Hadoop の使用](../hdinsight-use-mapreduce/)

Visual Studio の HDInsight ツールに関する詳細情報

* [Visual Studio の HDInsight ツールの概要](../hdinsight-hadoop-visual-studio-tools-get-started/)


[hdinsight-sdk-documentation]: http://msdnstage.redmond.corp.microsoft.com/library/dn479185.aspx

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[apache-tez]: http://tez.apache.org
[apache-hive]: http://hive.apache.org/
[apache-log4j]: http://en.wikipedia.org/wiki/Log4j
[hive-on-tez-wiki]: https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez
[import-to-excel]: http://azure.microsoft.com/documentation/articles/hdinsight-connect-excel-power-query/


[hdinsight-use-oozie]: ../hdinsight-use-oozie/
[hdinsight-analyze-flight-data]: ../hdinsight-analyze-flight-delay-data/



[hdinsight-storage]: ../hdinsight-use-blob-storage

[hdinsight-provision]: ../hdinsight-provision-clusters/
[hdinsight-submit-jobs]: ../hdinsight-submit-hadoop-jobs-programmatically/
[hdinsight-upload-data]: ../hdinsight-upload-data/
[hdinsight-get-started]: ../hdinsight-get-started/

[Powershell-install-configure]: ../install-configure-powershell/
[powershell-here-strings]: http://technet.microsoft.com/library/ee692792.aspx

[image-hdi-hive-powershell]: ./media/hdinsight-use-hive/HDI.HIVE.PowerShell.png
[img-hdi-hive-powershell-output]: ./media/hdinsight-use-hive/HDI.Hive.PowerShell.Output.png
[image-hdi-hive-architecture]: ./media/hdinsight-use-hive/HDI.Hive.Architecture.png

<!--HONumber=47-->