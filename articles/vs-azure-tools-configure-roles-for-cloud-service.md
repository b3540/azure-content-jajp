<properties
   pageTitle="Visual Studio で Azure クラウド サービスのロールを構成する方法 | Microsoft Azure"
   description="Visual Studio を使用し、Azure クラウド サービスのロールをセットアップして構成する方法について説明します。"
   services="visual-studio-online"
   documentationCenter="na"
   authors="kempb"
   manager="douge"
   editor="tglee" />
<tags
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="09/08/2015"
   ms.author="kempb" />

# Visual Studio で Azure クラウド サービスのロールを構成する

Azure クラウド サービスには、worker ロールまたは Web ロールを割り当てることができます。それぞれのロールについて、そのセットアップ方法を定義すると共に、実行方法を構成する必要があります。クラウド サービスのロールの詳細については、[Azure Cloud Services の概要](https://channel9.msdn.com/Series/Windows-Azure-Cloud-Services-Tutorials/Introduction-to-Windows-Azure-Cloud-Services)を紹介した動画をご覧ください。クラウド サービスに関する情報は、次のファイルに格納されます。

- **ServiceDefinition.csdef**

    サービス定義ファイルは、必要なロール、エンドポイント、仮想マシンのサイズを含む、クラウド サービスのランタイム設定を定義します。このファイルに格納されたデータはいずれも、ロールの実行中には変更できません。

- **ServiceConfiguration.cscfg**

    サービス構成ファイルでは、実行されるロールのインスタンス数とロールに定義されている設定の値を構成します。このファイルに格納されたデータは、ロールの実行中に変更できます。

これらの設定の値をロールの実行方法に応じて使い分けることができるように、サービス構成は複数使用することができます。デプロイ環境ごとに異なるサービス構成を使用できます。たとえば、ローカル サービス構成で、ローカルの Azure ストレージ エミュレーターを使用するようにストレージ アカウントの接続文字列を設定する一方、クラウドで Azure ストレージを使用するためのサービス構成を別途作成することができます。

Visual Studio で新しい Azure クラウド サービスを作成すると、既定で 2 つのサービス構成が作成されます。これらの構成は、Azure プロジェクトに追加されます。構成には次の名前が付けられます。

- ServiceConfiguration.Cloud.cscfg

- ServiceConfiguration.Local.cscfg

## Azure クラウド サービスを構成する

次の図に示すように、Azure クラウド サービスの構成は、Visual Studio のソリューション エクスプローラーから行うことができます。

    ![](./media/vs-azure-tools-configure-roles-for-cloud-service/IC713462.png)

### Azure クラウド サービスを構成するには

1. **ソリューション エクスプローラー**で Azure プロジェクトの各ロールを構成するには、Azure プロジェクトのロールのショートカット メニューを開き、**[プロパティ]** をクリックします。

    ロールの名前のページが、Visual Studio エディターに表示されます。ページには、**[構成]** タブのフィールドが表示されます。

1. **[サービス構成]** ボックスの一覧で、編集するサービス構成の名前を選択します。

    このロールのすべてのサービス構成を変更する場合は、**[すべての構成]** を選択できます。

    >[AZURE.IMPORTANT]設定の対象が "すべての構成" に限定されるプロパティが一部存在します。特定のサービス構成を選択した場合、それらのプロパティは無効になります。これらのプロパティを編集するには、[すべての構成] を選択する必要があります。

    タブを選択し、そのビューで任意の有効なプロパティを更新できるようになります。

## ロール インスタンス数の変更

クラウド サービスのパフォーマンスを高めるには、実行するロールのインスタンスの数を、ユーザー数や特定のロールに必要な負荷に応じて変更します。Azure でクラウド サービスを実行すると、ロールのインスタンスごとに仮想マシンが作成されます。このことが、そのクラウド サービスのデプロイの料金に影響します。課金の詳細については、[Azure の課金内容の確認](billing-understand-your-bill.md)に関するページを参照してください。

### ロールのインスタンス数を変更するには

1. **[構成]** タブをクリックします。

1. **[サービスの構成]** ボックスの一覧で、更新するサービス構成を選択します。

    >[AZURE.NOTE]インスタンス数は、特定のサービス構成を対象に設定することも、すべてのサービス構成を対象に設定することもできます。

1. **[インスタンス数]** ボックスに、このロールで開始するインスタンスの数を入力します。

    >[AZURE.NOTE]Azure にクラウド サービスを発行すると、それぞれのインスタンスが個別の仮想マシンで実行されます。

1. 以上の変更をサービス構成ファイルに保存するには、ツール バーの **[保存]** ボタンをクリックします。

## ストレージ アカウント用の接続文字列の管理

接続文字列は、サービス構成で追加、削除、変更することができます。接続文字列は、サービス構成ごとに使い分けることができます。たとえば、`UseDevelopmentStorage=true` という値を持つローカル サービス構成には、ローカル接続文字列を使用します。それに加えて、Azure のストレージ アカウントを使用するクラウド サービス構成も必要になることが考えられます。

>[AZURE.CAUTION]ストレージ アカウント接続文字列の Azure ストレージ アカウント キー情報を入力したとき、その情報はローカルのサービス構成ファイルに格納されます。ただしこの情報は現在、暗号化されたテキストとして保存されません。

サービス構成ごとに値を使い分ければ、クラウド サービスを Azure に発行する段階でコードに変更を加えたり、複数の異なる接続文字列をクラウド サービスで使用したりせずに済みます。コード内の接続文字列には同じ名前を使用してください。クラウド サービスをビルドまたは発行するときに選択したサービス構成によって異なる値が適用されます。

### ストレージ アカウント用の接続文字列を管理するには

1. **[設定]** タブをクリックします。

1. **[サービスの構成]** ボックスの一覧で、更新するサービス構成を選択します。

    >[AZURE.NOTE]特定のサービス構成を対象に接続文字列を更新することもできますが、接続文字列を追加または削除する必要がある場合は、[すべての構成] を選択してください。

1. 接続文字列を追加するには、**[設定の追加]** ボタンをクリックします。新しいエントリが一覧に追加されます。

1. **[名前]** ボックスに、接続文字列に使用する名前を入力します。

1. **[種類]** ボックスの一覧の **[接続文字列]** を選択します。

1. 接続文字列の値を変更するには、省略記号 (...) ボタンをクリックします。**[ストレージ接続文字列の作成]** ダイアログ ボックスが表示されます。

1. ローカルのストレージ アカウント エミュレーターを使用するには、**[Microsoft Azure ストレージ エミュレーター]** オプション ボタンをオンにし、**[OK]** をクリックします。

1. Azure でストレージ アカウントを使用するには、**[自分のサブスクリプション]** オプション ボタンをオンにし、目的のストレージ アカウントを選択します。

1. カスタム資格情報を使用するには、**[手動で入力された資格情報]** ボタンをクリックします。ストレージ アカウント名を入力し、プライマリ キーまたはセカンダリ キーを入力します。**[ストレージ接続文字列の作成]** ダイアログ ボックスでストレージ アカウントを作成する方法およびストレージ アカウントの情報を入力する方法については、「[Visual Studio からのクラウド サービスの発行に必要なサービスの設定](vs-azure-tools-setting-up-services-required-to-publish-a-cloud-service-from-visual-studio.md)」を参照してください。

1. 接続文字列を削除するには、接続文字列を選択し、**[設定の削除]** ボタンをクリックします。

1. 以上の変更をサービス構成ファイルに保存するには、ツール バーの **[保存]** アイコンをクリックします。

1. サービス構成ファイル内の接続文字列にアクセスするには、構成設定の値を取得する必要があります。次のコードは、BLOB ストレージが作成される場所と、ユーザーが Azure クラウド サービスの Web ロールで Default.aspx ページから **Button1** を選択したときにサービス構成ファイルから接続文字列 `MyConnectionString` を使用してアップロードされたデータの例を示しています。Default.aspx.cs に次の using ステートメントを追加します。

    ```
    using Microsoft.WindowsAzure;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.ServiceRuntime;
    ```

1. デザイン ビューで Default.aspx.cs を開き、ツールボックスからボタンを追加します。`Button1_Click` メソッドに次のコードを追加します。このコードでは、`GetConfigurationSettingValue` を使用して、サービス構成ファイルから接続文字列の値を取得します。次に、接続文字列 `MyConnectionString` で参照されるストレージ アカウントに BLOB が作成され、最後にプログラムによってテキストが BLOB に追加されます。

    ```
    protected void Button1_Click(object sender, EventArgs e)
    {
        // Setup the connection to Azure Storage
        var storageAccount = CloudStorageAccount.Parse(RoleEnvironment.GetConfigurationSettingValue("MyConnectionString"));
        var blobClient = storageAccount.CreateCloudBlobClient();
        // Get and create the container
        var blobContainer = blobClient.GetContainerReference("quicklap");
        blobContainer.CreateIfNotExists();
        // upload a text blob
        var blob = blobContainer.GetBlockBlobReference(Guid.NewGuid().ToString());
        blob.UploadText("Hello Azure");

    }
    ```

## Azure クラウド サービスで使用するカスタム設定を追加する

特定のサービス構成に使用する文字列の名前と値は、サービス構成ファイルのカスタム設定で追加できます。コードの中でカスタム設定の値を読み取ってその値を基にロジックを制御することで、クラウド サービスの機能を構成することができます。これらのサービス構成の値を変更するために、サービス パッケージをリビルドする必要はありません。クラウド サービスの実行中に値を変更することもできます。設定が変更されたことは、その通知をコードで受け取ることによって確認できます。[RoleEnvironment.Changing イベント](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.changing.aspx)に関するページを参照してください。

カスタム設定は、目的のサービス構成を対象に追加、削除、変更することができます。文字列の値はサービス構成ごとに使い分けることができます。

サービス構成ごとに値を使い分ければ、クラウド サービスを Azure に発行する段階でコードに変更を加えたり、複数の異なる文字列をクラウド サービスで使用したりせずに済みます。コード内の文字列には同じ名前を使用してください。クラウド サービスをビルドまたは発行するときに選択したサービス構成によって異なる値が適用されます。

### Azure クラウド サービスで使用するカスタム設定を追加するには

1. **[設定]** タブをクリックします。

1. **[サービスの構成]** ボックスの一覧で、更新するサービス構成を選択します。

    >[AZURE.NOTE]特定のサービス構成を対象に文字列を更新することもできますが、文字列を追加または削除する必要がある場合は、**[すべての構成]** を選択してください。

1. 文字列を追加するには、**[設定の追加]** ボタンをクリックします。新しいエントリが一覧に追加されます。

1. 文字列に使用する名前を **[名前]** ボックスに入力します。

1. **[種類]** ボックスの一覧の **[文字列]** を選択します。

1. 文字列の値を追加または変更するには、**[値]** ボックスに新しい値を入力します。

1. 文字列を削除するには、文字列を選択し、**[設定の削除]** ボタンをクリックします。

1. 以上の変更をサービス構成ファイルに保存するには、ツール バーの **[保存]** ボタンをクリックします。

1. サービス構成ファイル内の文字列にアクセスするには、構成設定の値を取得する必要があります。

    前の手順と同様、次の using ステートメントが Default.aspx.cs に追加されていることを確認してください。

    ```
    using Microsoft.WindowsAzure;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.ServiceRuntime;
    ```

1. 接続文字列にアクセスするときと同様、`Button1_Click` メソッドに次のコードを追加して、この文字列にアクセスします。その後、使用するサービス構成ファイルの設定文字列の値に基づいて特定のコードを実行できます。

    ```
    var settingValue = RoleEnvironment.GetConfigurationSettingValue("MySetting");
    if (settingValue == “ThisValue”)
    {
    // Perform these lines of code
    }
    ```

## 各ロール インスタンスのローカル ストレージの管理

ローカル ファイル システム ストレージは、ロールのインスタンスごとに追加できます。ここには、他のロールからはアクセスする必要のないローカル データを格納することができます。テーブル、BLOB、SQL Database ストレージに保存する必要がないデータもすべてそこに格納できます。たとえば、繰り返し使用する可能性のあるデータは、このローカル ストレージにキャッシュすることが考えられます。そこに保存されたデータは、ロールの他のインスタンスからはアクセスできません。ローカル ストレージ リソースの詳細については、「[ローカル ストレージ リソースを構成する](../cloud-services/cloud-services-configure-local-storage-resources.md)」を参照してください。

ローカル ストレージの設定はすべてのサービス構成に適用されます。ローカル ストレージの追加、削除、変更は、すべてのサービス構成を対象にのみ実行できます。

### 各ロール インスタンスのローカル ストレージを管理するには

1. **[ローカル ストレージ]** タブをクリックします。

1. **[サービスの構成]** ボックスの一覧の **[すべての構成]** を選択します。

1. ローカル ストレージのエントリを追加するには、**[ローカル ストレージの追加]** ボタンをクリックします。新しいエントリが一覧に追加されます。

1. このローカル ストレージに使用する名前を **[名前]** ボックスに入力します。

1. **[サイズ]** ボックスに、このローカル ストレージに必要なサイズを MB 単位で入力します。

1. このロール用の仮想マシンをリサイクルするときにこのローカル ストレージのデータを削除するには、**[ロールのリサイクル時に消去]** チェック ボックスをオンにします。

1. 既存のローカル ストレージのエントリを編集するには、更新する必要のある行を選択します。そのうえで前の手順の説明に従って、フィールドを編集してください。

1. ローカル ストレージのエントリを削除するには、一覧でストレージのエントリを選択し、**[ローカル ストレージの削除]** ボタンをクリックします。

1. 以上の変更をサービス構成ファイルに保存するには、ツール バーの **[保存]** アイコンをクリックします。

1. サービス構成ファイルに追加したローカル ストレージにアクセスするには、ローカル リソースの構成設定の値を取得する必要があります。この値にアクセスし、**MyStorageTest.txt** というファイルを作成して、テスト データの行をそのファイルに書き込むには、以下のコード行を使用します。前の手順で使用した `Button_Click` メソッドにこのコードを追加してください。

1. 次の using ステートメントが Default.aspx.cs に追加されていることを確認する必要があります。

    ```
    using System.IO;
    using System.Text;
    ```

1. `Button1_Click` メソッドに次のコードを追加します。これによって、ファイルがローカル ストレージに作成され、テスト データがファイルに書き込まれます。

    ```
    // Retrieve an object that points to the local storage resource
    LocalResource localResource = RoleEnvironment.GetLocalResource("LocalStorage1");

    //Define the file name and path
    string[] paths = { localResource.RootPath, "MyStorageTest.txt" };
    String filePath = Path.Combine(paths);

    using (FileStream writeStream = File.Create(filePath))
    {
          Byte[] textToWrite = new UTF8Encoding(true).GetBytes("Testing Web role storage");
          writeStream.Write(textToWrite, 0, textToWrite.Length);
    }
    ```

1. (省略可能) クラウド サービスをローカルで実行するときに作成したこのファイルを表示するには、次の手順に従います。

  1. Web ロールを実行し、**[Button1]** をクリックして、`Button1_Click` 内のコードが呼び出されることを確認します。

  1. 通知領域で、Azure アイコンのショートカット メニューを開き、**[コンピューティング エミュレーター UI の表示]** をクリックします。**[Azure コンピューティング エミュレーター]** ダイアログ ボックスが表示されます。

  1. Web ロールを選択します。

  1. メニュー バーで **[ツール]**、**[ローカル ストアを開く]** の順にクリックします。Windows エクスプローラー ウィンドウが表示されます。

  1. メニュー バーの **[検索]** ボックスに「**MyStorageTest.txt**」と入力し、**Enter** キーを押して検索を開始します。

    このファイルは検索結果に表示されます。

  1. ファイルの内容を表示するには、ファイルのショートカット メニューを開き、**[開く]** をクリックします。

## クラウド サービスの診断データを収集する

Azure クラウド サービスの診断データを収集することができます。このデータは、ストレージ アカウントに追加されます。接続文字列は、サービス構成ごとに使い分けることができます。たとえば、UseDevelopmentStorage=true という値を持つローカル サービス構成には、ローカル ストレージ アカウントを使用します。それに加えて、Azure のストレージ アカウントを使用するクラウド サービス構成も必要になることが考えられます。Azure 診断の詳細については、「Azure 診断を使用したログ データの収集」を参照してください。

>[AZURE.NOTE]ローカル サービス構成は、あらかじめローカル リソースを使用するように構成されています。クラウド サービス構成を使用して Azure クラウド サービスを発行する場合、接続文字列を指定しない限り、発行時に指定した接続文字列が診断の接続文字列にも使用されます。Visual Studio を使用してクラウド サービスをパッケージ化する場合、サービス構成内の接続文字列は変更されません。

### クラウド サービスの診断データを収集するには

1. **[構成]** タブをクリックします。

1. **[サービスの構成]** ボックスの一覧で、更新するサービス構成を選択するか、**[すべての構成]** を選択します。

    >[AZURE.NOTE]ストレージ アカウントは特定のサービス構成を対象に更新できますが、診断を有効または無効にする場合は、[すべての構成] を選択する必要があります。

1. 診断を有効にするには、**[診断を有効にする]** チェック ボックスをオンにします。

1. ストレージ アカウントの値を変更するには、省略記号 (...) ボタンをクリックします。

    **[ストレージ接続文字列の作成]** ダイアログ ボックスが表示されます。

1. ローカル接続文字列を使用するには、[Azure ストレージ エミュレーター] オプションをオンにし、**[OK]** をクリックします。

1. Azure サブスクリプションに関連付けられているストレージ アカウントを使用するには、**[自分のサブスクリプション]** をオンにします。

1. ローカル接続文字列のストレージ アカウントを使用するには、**[手動で入力された資格情報]** をクリックします。

    [ストレージ接続文字列の作成] ダイアログ ボックスでストレージ アカウントを作成する方法およびストレージ アカウントの情報を入力する方法の詳細については、「[Visual Studio からのクラウド サービスの発行に必要なサービスの設定](vs-azure-tools-setting-up-services-required-to-publish-a-cloud-service-from-visual-studio.md)」を参照してください。

1. 使用するストレージ アカウントを **[アカウント名]** で選択します。

    ストレージ アカウントの資格情報を手動で入力する場合は、**[アカウント キー]** にプライマリ キーをコピーするか入力します。このキーは、Microsoft Azure 管理ポータルからコピーできます。このキーをコピーするには、Microsoft Azure 管理ポータルの **[ストレージ アカウント]** ビューで次の手順を実行します。

  1. クラウド サービスに使用するストレージ アカウントを選択します。

  1. 画面下部にある **[アクセス キーの管理]** ボタンをクリックします。**[アクセス キーの管理]** ダイアログ ボックスが表示されます。

  1. アクセス キーをコピーするには、**[クリップボードにコピー]** ボタンをクリックします。コピーしたキーを **[アカウント キー]** フィールドに貼り付けてください。

1. Azure にクラウド サービスを発行するときに指定するストレージ アカウントを診断 (およびキャッシュ) 用の接続文字列として使用するには、**[Azure への発行時に診断とキャッシュのための開発ストレージの接続文字列を Azure ストレージ アカウントの資格情報で更新する]** チェック ボックスをオンにします。

1. 以上の変更をサービス構成ファイルに保存するには、ツール バーの **[保存]** ボタンをクリックします。

## 各ロールに対して使用する仮想マシンのサイズを変更する

仮想マシンのサイズはロールごとに設定できます。サイズは、すべてのサービス構成を対象にのみ設定できます。小さいマシン サイズを選択すると、割り当てられる CPU コア数、メモリ、ローカル ディスクのストレージが少なくなります。同様に、割り当てられる帯域幅も小さくなります。これらのサイズと割り当てられるリソースの詳細については、「[クラウド サービスのサイズを構成する](https://msdn.microsoft.com/library/azure/ee814754)」を参照してください。

Azure の各仮想マシンに必要なリソースは、Azure でクラウド サービスを実行するコストに影響します。Azure の課金の詳細については、[Azure の課金内容の確認](billing-understand-your-bill.md)に関するページを参照してください。

### 仮想マシンのサイズを変更するには

1. **[構成]** タブをクリックします。

1. **[サービスの構成]** ボックスの一覧の **[すべての構成]** を選択します。

1. このロール用の仮想マシンのサイズを選択するには、**[VM サイズ]** ボックスの一覧から適切なサイズを選択します。

1. 以上の変更をサービス構成ファイルに保存するには、ツール バーの **[保存]** ボタンをクリックします。

## ロールのエンドポイントと証明書の管理

ネットワークのエンドポイントの構成は、プロトコルとポート番号、さらに (HTTPS の場合) SSL 証明書の情報を指定して行います。2012 年 6 月よりも前のリリースでは、HTTP、HTTPS、TCP がサポートされます。2012 年 6 月のリリースでは、これらのプロトコルに加え、UDP がサポートされます。コンピューティング エミュレーターの入力エンドポイントに UDP を使用することはできません。このプロトコルは、内部のエンドポイントに対してのみ使用することができます。

Azure クラウド サービスのセキュリティを高めるには、HTTPS プロトコルを使用するエンドポイントを作成してください。たとえば、商品購入用するクラウド サービスであれば、SSL を使用して、顧客の情報を確実に保護することができます。

内部または外部で使用できるエンドポイントを追加することもできます。外部エンドポイントは入力エンドポイントと呼ばれます。入力エンドポイントには、ユーザーがクラウド サービスを利用するためのアクセス ポイントを追加する働きがあります。WCF サービスを提供する場合、そのサービスへのアクセスに使用する Web ロールの内部エンドポイントを公開することができます。

>[AZURE.IMPORTANT]エンドポイントは、すべてのサービス構成を対象としてのみ更新することができます。

HTTPS エンドポイントを追加する場合、SSL 証明書を使用する必要があります。すべてのサービス構成を対象としてロールに証明書を関連付け、ご利用のエンドポイントにそれらの証明書を使用してください。

>[AZURE.IMPORTANT]これらの証明書はサービスに同梱するものではありません。Azure Platform 管理ポータルから Azure に対し、個別に証明書をアップロードする必要があります。

サービス構成に関連付けた管理証明書が適用されるのは、クラウド サービスを Azure で実行したときだけです。クラウド サービスがローカル開発環境で実行されているときは、Azure コンピューティング エミュレーターが管理する標準証明書が使用されます。

### 証明書をロールに追加するには

1. **[証明書]** タブをクリックします。

1. **[サービスの構成]** ボックスの一覧の **[すべての構成]** を選択します。

    >[AZURE.NOTE]証明書を追加または削除するには、[すべての構成] を選択する必要があります。名前とサムプリントについては、必要に応じて、特定のサービス構成を対象に更新できます。

1. このロールの証明書を追加するには、**[証明書の追加]** ボタンをクリックします。新しいエントリが一覧に追加されます。

1. **[名前]** ボックスに、証明書の名前を入力します。

1. **[ストアの場所]** ボックスの一覧から、追加する証明書の場所を選択します。

1. **[ストアの名前]** ボックスの一覧から、証明書の選択に使用するストアを選択します。

1. 証明書を追加するには、省略記号 (...) ボタンをクリックします。**[Windows セキュリティ]** ダイアログ ボックスが表示されます。

1. 使用する証明書を一覧から選択し、**[OK]** をクリックします。

    >[AZURE.NOTE]証明書ストアから証明書を追加すると、すべての中間証明書は自動的に構成設定に追加されます。SSL に対してサービスを適切に構成するために、これらの中間証明書も Azure にアップロードする必要があります。

1. 証明書を削除するには、証明書を選択し、**[証明書の削除]** ボタンをクリックします。

1. 以上の変更をサービス構成ファイルに保存するには、ツール バーの **[保存]** アイコンをクリックします。

### ロール用のエンドポイントを管理するには

1. **[エンドポイント]** タブをクリックします。

1. **[サービスの構成]** ボックスの一覧の **[すべての構成]** を選択します。

1. エンドポイントを追加するには、**[エンドポイントの追加]** ボタンをクリックします。新しいエントリが一覧に追加されます。

1. このエンドポイントに使用する名前を **[名前]** ボックスに入力します。

1. 必要なエンドポイントの種類を **[種類]** ボックスの一覧から選択します。

1. 必要なエンドポイントのプロトコルを **[プロトコル]** ボックスの一覧から選択します。

1. 入力エンドポイントの場合、**[パブリック ポート]** ボックスに、使用するパブリック ポートを入力します。

1. **[プライベート ポート]** ボックスに、使用するプライベート ポートを入力します。

1. エンドポイントに https プロトコルが必要な場合は、**[SSL 証明書の名前]** ボックスの一覧から、使用する証明書を選択します。

    >[AZURE.NOTE]この一覧には、**[証明書]** タブでこのロールに追加した証明書が表示されます。

1. 以上の変更をサービス構成ファイルに保存するには、ツール バーの **[保存]** ボタンをクリックします。

## 次のステップ
Visual Studio における Azure プロジェクトの詳細については、[Azure プロジェクトの構成](vs-azure-tools-configuring-an-azure-project.md)に関するページを参照してください。クラウド サービスのスキーマの詳細については、[スキーマ リファレンス](https://msdn.microsoft.com/library/azure/dd179398)を参照してください。

<!---HONumber=Sept15_HO4-->