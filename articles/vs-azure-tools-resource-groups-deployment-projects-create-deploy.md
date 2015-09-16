<properties 
   pageTitle="Azure リソース グループ デプロイ プロジェクトの作成とデプロイ"
	description="Azure リソース グループ デプロイ プロジェクトの作成とデプロイ"
	services="visual-studio-online"
	documentationCenter="na"
	authors="kempb"
	manager="douge"
	editor="tlee"/>
<tags 
   ms.service="azure-resource-manager"
	ms.devlang="multiple"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="na"
	ms.date="08/24/2015"
	ms.author="kempb"/>

# Azure リソース グループ デプロイ プロジェクトの作成とデプロイ

**Azure リソース グループ** デプロイ プロジェクト テンプレートは、Azure SDK 2.6 がインストールされている場合に Visual Studio で使用できます。Azure リソース グループ プロジェクトを使用すると、関連する複数の Azure リソースをグループ化し、1 回のデプロイ操作で発行することができます。Azure リソース グループ プロジェクトでは、**Azure リソース マネージャー**と呼ばれるテクノロジを使用して作業を行います。**Azure リソース マネージャー**は、Azure リソース グループを定義できる REST API サービスです。Azure リソース グループには、通常は一緒に使用され、同じようなライフサイクルを持つ複数の Azure リソースが含まれます。リソース グループを使用すると、個々のリソースごとに異なる関数を呼び出すのではなく、単一の関数呼び出しでグループ内のすべてのリソースを操作できます。Azure リソース グループの詳細については、[リソース グループを使用した Azure のリソースの管理](./azure-portal/azure-preview-portal-using-resource-groups/)に関するページを参照してください。

Azure リソース グループ プロジェクトには、リソース グループにデプロイされる要素を定義する Azure リソース マネージャー JSON テンプレートが含まれています。詳細については、「[Azure リソース マネージャー テンプレートの言語](https://msdn.microsoft.com/library/azure/dn835138.aspx)」を参照してください。

Azure リソース マネージャーには、使用可能なさまざまなリソース プロバイダーが用意されています。これらを使用すると、Ubuntu Server や Windows Server 2012 R2 などのリソースをデプロイすることができます。このトピックでは、**Web Apps** リソースを使用します。これにより、基本的な空の Web サイトが Azure にデプロイされます。

## Azure リソース グループ プロジェクトの作成

この手順では、**Web アプリ** テンプレートを使用して Azure リソース グループ プロジェクトを作成する方法について説明します。

### Azure リソース グループ プロジェクトを作成するには

1. Visual Studio で、**[ファイル]**、**[新しいプロジェクト]** の順に選択し、**[C#]** または **[Visual Basic]** を選択します。次に **[Cloud]** を選択し、**[Azure リソース グループ]** プロジェクトを選択します。

    ![Cloud Deployment Project](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/IC796668.png)

1. Azure リソース マネージャーにデプロイするテンプレートを選択します。この例では、**[Web アプリ]** テンプレートを選択します。

    ![Choose a template](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/IC796669.png)

    リソースは、後でリソース グループにさらに追加することもできます。

    >[AZURE.NOTE]使用可能なテンプレートの一覧はオンラインで取得されるため、変更される可能性があります。

    Visual Studio では、Web アプリ用の Azure リソース グループ デプロイ プロジェクトが作成されます。

1. デプロイ プロジェクト内のノードを展開して、作成された内容を確認します。

    この例では [Web アプリ] テンプレートを選択したため、次のファイルが表示されます。

|ファイル名|説明|
|---|---|
|Deploy-AzureResourceGroup.ps1|Azure リソース マネージャーにデプロイするために PowerShell コマンドを呼び出す PowerShell スクリプト。

**注** この PowerShell スクリプトは、テンプレートをデプロイするために Visual Studio によって使用されます。このスクリプトに変更を加えると、Visual Studio でのデプロイにも影響するため、注意してください。| |WebSite.json|Azure リソース マネージャーにデプロイするすべての詳細を指定する構成ファイル。| |WebSite.param.dev.json|構成ファイルで必要な特定の値を含むパラメーター ファイル。| |AzCopy.exe|ローカル ストレージのドロップ パスからストレージ アカウントのコンテナーにファイルをコピーするために PowerShell スクリプトで使用されるツール。このツールは、テンプレートと共にコードをデプロイするようデプロイ プロジェクトを構成する場合にのみ使用します。|

すべての Azure リソース グループ デプロイ プロジェクトには、上記の 4 つの基本的なファイルが含まれます。他のプロジェクトには、他の機能をサポートするために追加のファイルが含まれることがあります。

## Azure リソース グループ プロジェクトのカスタマイズ

デプロイする Azure リソースが記述されている JSON テンプレート ファイルを変更することで、デプロイ プロジェクトをカスタマイズできます。JSON とは JavaScript Object Notation の略であり、操作が簡単なシリアル化されたデータ形式です。

Azure リソース グループ プロジェクトでは、ソリューション エクスプローラーの **Templates** ノードの下に、Azure リソース マネージャー テンプレート ファイルとパラメーター ファイルという、変更可能な 2 つのテンプレート ファイルがあります。

- **Azure リソース マネージャー テンプレート ファイル** (拡張子は .json) では、サイトの名前や場所など、デプロイ プロジェクトに必要なパラメーターだけでなく、必要なリソースが含まれるファイルを指定します。また、トリガーの名前、タグ、ルールなど、Azure リソース グループ内のコンポーネントとそのプロパティの依存関係も指定します。このファイルを変更して独自の機能を追加できます。たとえば、テンプレートにデータベースを追加できます。指定する必要があるパラメーターを確認するには、各リソース プロバイダーのドキュメントを参照してください。詳細については、「[リソース プロバイダー](https://msdn.microsoft.com/library/azure/dn790572.aspx)」を参照してください。

- **パラメーター ファイル** (拡張子は .param.*.json) には、構成ファイルで指定された、各リソース プロバイダーに必要なパラメーターの値が含まれます。この例では、Web アプリの構成ファイル (WebSite.json) で siteName と siteLocation のパラメーターを定義します。デプロイ時に、テンプレート ファイルのパラメーターの値を指定するように求められ、これらの値はパラメーター ファイルに格納されます。パラメーター ファイルは、直接編集することもできます。

JSON ファイルは、Visual Studio エディターで編集できます。[PowerShell Tools for Visual Studio](https://visualstudiogallery.msdn.microsoft.com/c9eb3ba8-0c59-4944-9a62-6eee37294597) をインストールすると、構文の強調表示、かっこの一致、および IntelliSense も使用できるようになり、PowerShell スクリプトの読み取りと編集がより簡単になります。PowerShell Tools がまだインストールされていない場合は、インストールするためのリンクがエディターの上部に表示されます。

![Install PowerShell Tools](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/IC796671.png)

JSON ファイルでは、各ファイルの上部で参照されているスキーマが使用されます。スキーマをより深く理解する場合は、スキーマをダウンロードして分析できます。スキーマでは、許可される要素、フィールドの種類と形式、列挙値に使用できる値などが定義されています。

さまざまな構成へのデプロイや設定の変更を頻繁に行う場合は、*param* ファイルのコピーをそれぞれに作成できます。すべての環境に同じテンプレートを使用してみてください。

## Azure リソース グループへの Azure リソース グループ プロジェクトのデプロイ

Azure リソース グループ プロジェクトをデプロイする場合は、Azure のリソース (Web アプリやデータベースなど) の単なる論理グループである Azure リソース グループにプロジェクトをデプロイします。

1. デプロイ プロジェクト ノードのショートカット メニューで、**[デプロイ]**、**[新しい配置]** の順に選択します。

    ![Deploy, New Deployment menu item](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/IC796672.png)

    **[リソース グループに配置する]** ダイアログ ボックスが表示されます。

    ![Deploy To Resource Group Dialog Box](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/IC796673.png)

1. **[リソース グループ]** ボックスの一覧で、既存のリソース グループを選択するか、新しいリソース グループを作成します。リソース グループを作成するには、**[リソース グループ]** ボックスの一覧を開き、**[<Create New...>]** を選択します。

    **[リソース グループの作成]** ダイアログ ボックスが表示されます。

    ![Create Resource Group Dialog Box](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/IC796674.png)

    >[AZURE.NOTE]通常、新しいデプロイ プロジェクトを開始する場合は、デプロイ先の新しいリソース グループを作成します。

1. リソース グループの名前と場所を入力し、**[作成]** をクリックします。

    リソース グループの場所は、リソースの場所と同じである必要はありません。グループ内のリソースは、複数のリージョンにまたがっている場合があります。

1. このデプロイに使用するテンプレート構成ファイルとパラメーター ファイルを選択するか、既定値をそのまま使用します。

    **[パラメーターの編集]** をクリックすると、リソースのプロパティを編集できます。デプロイ時に必要なパラメーターがない場合は、パラメーターを指定できるように **[パラメーターの編集]** ダイアログ ボックスが表示されます。値が指定されていないパラメーターには、その横にある **[値]** ボックスに **<null>** と表示されます。この例 (Web アプリ リソース) の場合、必要なパラメーターには、サイト名、ホスティング プラン、サイトの場所があります(取り消した場合、パラメーター ファイルのこれらのパラメーター値は既定で null に設定されます)。 他のパラメーターには既定値が設定されています。

    ![Edit Parameters Dialog Box](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/IC796675.png)

1. **[パラメーターの編集]** ダイアログ ボックスで、サイト名、サイトの場所、ホスティング プラン名を入力し、その他のプロパティの値を確認します。終了したら、**[保存]** を選択します。

    - *siteName* パラメーターは、Web ページの URL の最初の部分です。たとえば、mywebsitename.azurewebsites.net という URL の場合、サイト名は mywebsitename になります。

    - *hostingPlanName* パラメーターは、ホスティング プランを指定します。この例では、"Free" を使用できます。ホスティング プランの詳細については、[Azure Websites ホスティング プランの詳細な概要](http://azure.microsoft.com/documentation/articles/azure-web-sites-web-hosting-plans-in-depth-overview/)に関するページを参照してください。

    - *siteLocation* パラメーターは、"米国西部" など、サイトがホストされる Azure リージョンを表します。使用可能なリージョンの一覧については、「[Azure のリージョン](http://azure.microsoft.com/regions/)」を参照してください。

1. **[デプロイ]** をクリックして、プロジェクトを Azure にデプロイします。

    デプロイの進行状況は、**出力**ウィンドウで確認できます。デプロイは、構成に応じて、完了までに数分かかる場合があります。

    >[AZURE.NOTE]Microsoft Azure PowerShell コマンドレットのインストールを求められる場合があります。これらのコマンドレットは、Azure リソース グループをデプロイするために必要であるため、インストールする必要があります。

1. ブラウザーで、[Azure ポータル](https://portal.azure.com/)を開きます。これは新しい変更であるため、新しい通知メッセージが **[通知]** タブに表示されます。通知メッセージを選択すると、新しい Azure リソース グループの詳細が表示されます。使用可能なすべてのリソース グループの一覧を表示するには、**[参照]** タブをクリックし、**[リソース グループ]** を選択します。

    ![The provisioned Azure Resource Group](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/IC796676.png)

1. 変更を加え、プロジェクトを再デプロイする場合は、Azure リソース グループ プロジェクトのショートカット メニューから直接既存のリソース グループを選択できます。ショートカット メニューの **[デプロイ]** を選択してから、デプロイ先のリソース グループを選択します。

    ![Azure resource group deployed](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/IC796677.png)

    Azure リソース グループをデプロイすると、そのプロジェクトのみがデプロイされます。ソリューションにコード プロジェクトまたはその他のプロジェクトがある場合は、個別にデプロイする必要があります。

## Azure SDK 2.6 での Azure SDK 2.5 クラウド デプロイ プロジェクトの使用

Azure SDK 2.5 で作成されたクラウド デプロイ プロジェクトを使用している場合は、Azure SDK 2.6 にアップグレードして、Azure リソース テンプレートを編集してデプロイするための新機能を使用できるようにする必要があります。Azure SDK 2.5 で作成したテンプレートを再利用する最も簡単な方法では、Azure SDK 2.6 バージョンのプロジェクトを作成し、そのプロジェクトにテンプレートを移動してからいくつかの変更を行います。

### Azure SDK 2.6 で Azure SDK 2.5 クラウド デプロイ プロジェクトを使用するには

1. 新しい Azure SDK 2.6 の Azure リソース グループ プロジェクトをソリューションに追加します。

    1. Azure SDK 2.5 クラウド デプロイ プロジェクトを含むソリューションを開きます。

    1. **[ファイル]** メニューの **[新規作成]**、**[プロジェクト]** の順に選択します。

    1. **[新しいプロジェクト]** ダイアログ ボックスで、**[Visual C#]** の **[Cloud]** または **[Visual Basic]** の **[Cloud]** の下で **[Azure リソース グループ]** プロジェクトを見つけます。
    
         プロジェクト テンプレートの名前は、"**クラウド デプロイ**" から "Azure リソース グループ" に変更されました。

    1. プロジェクトに名前を付けます。

    1. ソリューションのドロップダウン ボックスを **[ソリューションに追加]** に変更します。

    1. 次に、テンプレートを選択するように求められます。Azure SDK 2.5 のクラウド デプロイ プロジェクトから既存のテンプレートを移動するため、任意のテンプレートを選択できます。ここでは、一覧の下部にある空のテンプレートを選択します。

    1. **[OK]** をクリックします。
    
        新しいプロジェクトがソリューションに追加されます。

1. テンプレート ファイルとパラメーター ファイルを、Azure SDK 2.5 のクラウド デプロイ プロジェクトから Azure SDK 2.6 のリソース グループ プロジェクトにコピーします。

    1. ソリューション エクスプローラーで、Azure SDK 2.5 のデプロイ プロジェクトでコピーするテンプレート ファイルとパラメーター ファイルを探して選択し、コピーします。
    
    2. コピーしたファイルを、新しい Azure SDK 2.6 リソース グループ プロジェクトの **Templates** フォルダーに貼り付けます。

1. ソリューション エクスプローラーで、Azure SDK 2.5 のデプロイ プロジェクトでコピーするテンプレート ファイルとパラメーター ファイルを探して選択し、コピーします。

1. コピーしたファイルを、新しい Azure SDK 2.6 リソース グループ プロジェクトの Templates フォルダーに貼り付けます。

1. テンプレートと共に Web アプリケーションもデプロイする場合は、新しい Azure SDK 2.6 リソース グループ プロジェクトから Web アプリケーションへの参照を作成する必要があります。

    1. ソリューション エクスプローラーで、新しい Azure SDK 2.6 リソース グループ プロジェクトの **[参照設定]** ノードのコンテキスト メニューにある **[参照の追加]** を選択します。

    1. プロジェクトの一覧で Web アプリケーションの横にあるボックスをオンにして、**[OK]** をクリックします。

1. ソリューション エクスプローラーで、新しい Azure SDK 2.6 リソース グループ プロジェクトの [参照設定] ノードのコンテキスト メニューにある [参照の追加] を選択します。

1. プロジェクトの一覧で Web アプリケーションの横にあるボックスをオンにして、[OK] をクリックします。

1. *dropLocation* と *dropLocationSasToken* というすべてのパラメーターの名前を *\_artifactsLocation* と *\_artifactsLocationSasToken* に変更します。

1. 使用する予定がない場合は、Azure SDK 2.6 プロジェクトの作成時に自動的に追加された空のテンプレート ファイルとパラメーター ファイルを削除することができます。

    1. DeployTemplate.json ファイルを削除します。

    1. DeploymentTemplate.param.dev.json ファイルを削除します。

1. Azure SDK 2.5 プロジェクトの Publish-AzureResourceGroup.ps1 スクリプトに変更を加えた場合は、Azure SDK 2.6 プロジェクトの Deploy-AzureResourceGroup.ps1 スクリプトにそれらの変更を移行する必要があります。

    これで、Azure SDK 2.6 の Azure リソース グループ プロジェクトでデプロイ コマンドを使用してテンプレートをデプロイし、Azure SDK 2.6 でテンプレートを編集するための新機能を利用できるようになりました。2.6 のプロジェクトが思いどおりに動作している場合は、ソリューションから Azure SDK 2.5 プロジェクトを削除できます。

## プロジェクトの更新が必要な理由

テンプレートで行われたいくつかの変更が Azure SDK 2.6 でデプロイされると、Azure SDK 2.5 のデプロイ スクリプトとテンプレートに互換性がなくなります。最も重要となる大きな変更が、デプロイの起動です。Azure SDK 2.5 では、テンプレートをアップロードしてデプロイを開始するために Azure REST API を使用したコードをコンパイルしてきました。多くの開発者からは、プロジェクトに含まれている PowerShell スクリプトを Visual Studio で起動するだけにしてほしいというフィードバックがありました。そのため、Azure SDK 2.6 では、プロジェクトに含まれている PowerShell スクリプトをデプロイ コマンドで起動することで、テンプレートをデプロイできるようにしました。これで、Azure PowerShell を使用してコマンド ラインからデプロイするか、デプロイ コマンドを使用して VS からデプロイするかに関係なく、デプロイをカスタマイズして、これらのカスタマイズをいつでも実行できるようになりました。Visual Studio からデプロイするには、Azure SDK 2.6 がインストールされている場合に、Azure SDK 2.6 のデプロイ PowerShell スクリプトを使用する必要があります。

また、Microsoft 内の TFS 自動ビルドや他のプロジェクトでの命名規則により的確に合うように、いくつかの変数名とビルド タスクが調整されました。PowerShell スクリプトの起動に必要な変数と値を収集する Visual Studio のコードによって、これらの新しい名前が検索されます。

## 次のステップ

Visual Studio で Azure リソース グループにリソースを追加する方法については、「[Azure リソース グループへのリソースの追加](https://msdn.microsoft.com/library/azure/mt125415.aspx)」を参照してください。

<!---HONumber=September15_HO1-->