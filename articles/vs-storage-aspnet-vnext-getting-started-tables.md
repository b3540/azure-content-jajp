<properties title="Getting Started with Azure Storage" pageTitle="Getting Started with Azure Storage" metaKeywords="Azure, Getting Started, Storage" description="" services="storage" documentationCenter="" authors="ghogen, kempb" />

<tags ms.service="storage" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/10/2014" ms.author="ghogen, kempb"></tags>

[WACOM.INCLUDE [vs-storage-aspnet-vnext-getting-started-intro](../includes/vs-storage-aspnet-vnext-getting-started-intro.md)]

# Azure Storage の使用

<div class="dev-center-tutorial-selector sublanding"><a href="/ja-jp/documentation/articles/vs-storage-aspnet-vnext-getting-started-blobs" title="BLOB" class="current">BLOB</a><a href="/ja-jp/documentation/articles/vs-storage-aspnet-vnext-getting-started-queues" title="キュー">キュー</a><a href="/ja-jp/documentation/articles/vs-storage-aspnet-vnext-getting-started-tables" title="テーブル">テーブル</a></div>

Azure テーブル ストレージ サービスを使用すると、大量の構造化データを格納できるようになります。このサービスは、Azure クラウドの内部および外部からの認証された呼び出しを受け付ける NoSQL データストアです。Azure のテーブルは、構造化された非リレーショナル データを格納するのに最適です。詳細については、「[How to use Table Storage from .NET (.NET からテーブル ストレージを使用する方法)][How to use Table Storage from .NET (.NET からテーブル ストレージを使用する方法)]」を参照してください。

ASP.NET vNext プロジェクトのテーブルにプログラムを使用してアクセスするには、以下のタスクを実行する必要があります。

1.  Microsoft.WindowsAzure.Storage.dll アセンブリを取得します。それには NuGet を使用します。ソリューション エクスプローラーでプロジェクトを右クリックし、[NuGet パッケージの管理] をクリックします。"WindowsAzure.Storage" をオンライン検索し、[インストール] をクリックして Azure Storage のパッケージと依存関係をインストールします。このアセンブリへの参照をプロジェクトに追加します。
2.  Microsoft.Framework.ConfigurationModel.Json NuGet パッケージをインストールします。
3.  プログラムを使用して Azure Storage にアクセスするすべての C# ファイルの冒頭部分に、次の名前空間宣言コードを追加します。

        using Microsoft.Framework.ConfigurationModel;

4.  次のコードを使用して構成設定を取得します。

        var config = new Configuration();
        config.AddJsonFile("config.json");

### ストレージ接続文字列を取得する

テーブルを使用した操作を行うには、テーブルを使用するストレージ アカウントの接続文字列を取得する必要があります。ストレージ アカウント情報を表すには、**CloudStorageAccount** 型を使用します。ASP.NET vNext プロジェクトを使用する場合、次のコードに示すように、構成オブジェクトの get メソッドを呼び出して Azure サービス構成からストレージ接続文字列とストレージ アカウント情報を取得することができます。

    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
      config.get("MicrosoftAzureStorage:<storageAccountName>_AzureStorageConnectionString"));

[WACOM.INCLUDE [vs-storage-getting-started-tables-include](../includes/vs-storage-getting-started-tables-include.md)]

  [vs-storage-aspnet-vnext-getting-started-intro]: ../includes/vs-storage-aspnet-vnext-getting-started-intro.md
  [BLOB]: /ja-jp/documentation/articles/vs-storage-aspnet-vnext-getting-started-blobs "BLOB"
  [キュー]: /ja-jp/documentation/articles/vs-storage-aspnet-vnext-getting-started-queues "キュー"
  [テーブル]: /ja-jp/documentation/articles/vs-storage-aspnet-vnext-getting-started-tables "テーブル"
  [How to use Table Storage from .NET (.NET からテーブル ストレージを使用する方法)]: http://azure.microsoft.com/ja-jp/documentation/articles/storage-dotnet-how-to-use-tables/#create-table "How to use Table Storage from .NET"
  [vs-storage-getting-started-tables-include]: ../includes/vs-storage-getting-started-tables-include.md