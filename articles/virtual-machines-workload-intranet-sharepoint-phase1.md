<properties 
	pageTitle="SharePoint イントラネット ファーム ワークロードのフェーズ 1: Azure の構成" 
	description="イントラネット専用 SharePoint 2013 ファームと SQL Server AlwaysOn 可用性グループを Azure インフラストラクチャ サービスにデプロイする作業のこの第 1 フェーズでは、Azure Virtual Network および他の Azure インフラストラクチャ要素を作成します。" 
	documentationCenter=""
	services="virtual-machines" 
	authors="JoeDavies-MSFT" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/05/2015" 
	ms.author="josephd"/>

# SharePoint イントラネット ファーム ワークロードのフェーズ 1: Azure の構成

イントラネット専用 SharePoint 2013 ファームと SQL Server AlwaysOn 可用性グループを Azure インフラストラクチャ サービスにデプロイする作業の第 1 フェーズでは、Azure のネットワーキングおよびストレージ インフラストラクチャを構築します。[フェーズ 2](virtual-machines-workload-intranet-sharepoint-phase2.md) に進むには、このフェーズを完了する必要があります。全フェーズについては、「[Azure での SharePoint と SQL Server AlwaysOn 可用性グループのデプロイ](virtual-machines-workload-intranet-sharepoint-overview.md)」をご覧ください。

Azure を次の基本的なネットワーク コンポーネントでプロビジョニングする必要があります。

- サブネットを 1 つ含むクロスプレミス仮想ネットワーク
- 3 つの Azure Cloud Services
- VHD ディスク イメージと追加のデータ ディスクを格納するための 1 つの Azure ストレージ アカウント

## 開始する前に

Azure コンポーネントの構成を開始する前に、次の表を作成します。Azure を構成するときに役立つよう、このセクションを印刷して必要な情報を書き込むか、またはこのセクションをドキュメントにコピーして情報を入力してください。

仮想ネットワーク (VNet) の設定を表 V に記入します。

項目 | 構成要素 | 説明 | 値 
--- | --- | --- | --- 
1. | VNet の名前 | Azure Virtual Network に割り当てる名前 (例: SPFarmNet) 。 | __________________
2. | VNet の場所 | 仮想ネットワークを含む Azure データセンター。 | __________________
3. | ローカル ネットワークの名前 | 組織のネットワークに割り当てる名前。 | __________________
4. | VPN デバイスの IP アドレス | インターネット上の VPN デバイスのインターフェイスのパブリック IPv4 アドレス。IT 部門と相談してこのアドレスを決定します。 | __________________
5. | VNet のアドレス空間 | 単一のプライベート アドレス プレフィックスで定義されている、仮想ネットワークのアドレス空間。IT 部門と相談してこのアドレス空間を決定します。 | __________________
6. | 1 番目の最終 DNS サーバー | 仮想ネットワークのサブネットのアドレス空間に対して可能な 4 番目の IP アドレス (表 S を参照)。IT 部門と相談してこれらのアドレスを決定します。 | __________________
7. | 2 番目の最終 DNS サーバー | 仮想ネットワークのサブネットのアドレス空間に対して可能な 5 番目の IP アドレス (表 S を参照)。IT 部門と相談してこれらのアドレスを決定します。 | __________________

**表 V: クロスプレミス仮想ネットワークの構成**

このソリューションのサブネットに関する表 S に記入します。サブネットの表示名、仮想ネットワーク アドレス空間に基づく 1 つの IP アドレス空間、および用途の説明を指定します。アドレス空間は、ネットワーク プレフィックス形式とも呼ばれるクラスレス ドメイン間ルーティング (CIDR) 形式である必要があります。たとえば、10.24.64.0/20 のような形式です。IT 部門と相談して、仮想ネットワーク アドレス空間からこのアドレス空間を決定します。

項目 | サブネット名 | サブネットのアドレス空間 | 目的 
--- | --- | --- | --- 
1. | _______________ | _____________________________ | _________________________

**表 S: 仮想ネットワークのサブネット**

> [AZURE.NOTE]この定義済みアーキテクチャでは、わかりやすくするため単一のサブネットを使用します。一連のトラフィック フィルターをオーバーレイしてサブネットの分離をエミュレートする場合は、Azure の[ネットワーク セキュリティ グループ](https://msdn.microsoft.com/library/azure/dn848316.aspx)を使用できます。

仮想ネットワーク内にドメイン コント ローラーを最初にセットアップするときに使用する 2 つのオンプレミス DNS サーバーについて、表 D に記入します。各 DNS サーバーの表示名および単一の IP アドレスを指定します。この表示名は、DNS サーバーのホスト名またはコンピューター名と一致している必要はありません。記入欄は 2 つですが、さらに追加してもかまいません。IT 部門と相談してこのリストを決定します。

項目 | DNS サーバーの表示名 | DNS サーバーの IP アドレス 
--- | --- | ---
1. | ___________________________ | ___________________________
2. | ___________________________ | ___________________________ 

**表 D: オンプレミス DNS サーバー**

サイト間 VPN 接続を使用してクロスプレミス ネットワークから組織のネットワークにパケットをルーティングするには、組織のオンプレミス ネットワーク上にあるすべての到達可能な場所のアドレス空間 (CIDR 表記) のリストを含むローカル ネットワークで、仮想ネットワークを構成する必要があります。ローカル ネットワークを定義するアドレス空間のリストは、他の仮想ネットワークまたは他のローカル ネットワークに使用されるアドレス空間を含んでいたり、そのようなアドレス空間と重複していてはなりません。つまり、構成される仮想ネットワークおよびローカル ネットワークのアドレス空間は、一意である必要があります。

一連のローカル ネットワーク アドレス空間について、表 L に記入します。記入欄は 3 つですが、通常はさらに必要です。IT 部門と相談してこのアドレス空間のリストを決定します。

項目 | ローカル ネットワークのアドレス空間 
--- | ---
1. | ___________________________________
2. | ___________________________________
3. | ___________________________________

**表 L: ローカル ネットワークのアドレス プレフィックス**

表 V、S、D、L の設定で仮想ネットワークを作成するには、「[構成テーブルを使用してクロスプレミス仮想ネットワークを作成する](virtual-machines-workload-deploy-vnet-config-tables.md)」の手順を使用します。

> [AZURE.NOTE]この手順では、サイト間 VPN 接続を使用する仮想ネットワークを作成します。サイト間接続に ExpressRoute を使用する方法については、「[ExpressRoute の技術概要](http://msdn.microsoft.com/library/dn606309.aspx)」を参照してください。

Azure Virtual Network の作成が済むと、Azure 管理ポータルで次のことが決定されます。

- 仮想ネットワークの Azure VPN ゲートウェイのパブリック IPv4 アドレス
- サイト間 VPN 接続用のインターネット プロトコル セキュリティ (IPsec) 事前共有キー

仮想ネットワーク作成後に Azure 管理ポータルでこれらを確認するには、[**ネットワーク**] をクリックし、仮想ネットワークの名前をクリックして、[**ダッシュボード**] メニュー オプションをクリックします。

次に、仮想ネットワーク ゲートウェイを構成して、セキュリティで保護されたサイト間 VPN 接続を作成します。方法については、「[管理ポータルでの仮想ネットワーク ゲートウェイの構成](http://msdn.microsoft.com/library/jj156210.aspx)」を参照してください。

次に、新しい仮想ネットワークとオンプレミス VPN デバイスの間に、サイト間 VPN 接続を作成します。詳細な手順については、「[管理ポータルでの仮想ネットワーク ゲートウェイの構成](http://msdn.microsoft.com/library/jj156210.aspx)」を参照してください。

次に、仮想ネットワークのアドレス空間がオンプレミス ネットワークから到達できることを確認します。これは、通常、仮想ネットワークのアドレス空間に対応するルートを VPN デバイスに追加した後、組織ネットワークのルーティング インフラストラクチャの他の部分にそのルートをアドバタイズすることによって行います。IT 部門と相談してこの方法を決定します。

次に、「[Azure PowerShell のインストールと構成の方法](install-configure-powershell.md)」の手順に従って、ローカル コンピューターに Azure PowerShell をインストールしますAzure PowerShell コマンド プロンプトを開きます。

まず以下のコマンドで、適切な Azure サブスクリプションを選択します。引用符内のすべての文字 (< and > を含む) を、正しい名前に置き換えます。

	$subscr="<Subscription name>"
	Select-AzureSubscription -SubscriptionName $subscr –Current

**Get-AzureSubscription** コマンドの出力の **SubscriptionName** プロパティで、サブスクリプション名を取得できます。

次に、この SharePoint ファームに必要な 3 つのクラウド サービスを作成します。表 C に記入します。

項目 | 目的 | クラウド サービス名 
--- | --- | ---
1. | ドメイン コントローラー | ___________________________
2. | SQL Server | ___________________________
3. | SharePoint サーバー | ___________________________

**表 C: クラウド サービスの名前**

各クラウド サービスには一意の名前を選ぶ必要があります。*クラウド サービスの名前には、文字、数字、ハイフンのみを含めることができます。フィールドの先頭と末尾の文字は、文字または数字としてください。*

たとえば、最初のクラウド サービスには DCs-*UniqueSequence* (*UniqueSequence* は組織の略称) といった名前を付けます。たとえば、組織の名前が Tailspin Toys であれば、クラウド サービスの名前は DCs-Tailspin などとなります。

名前が一意かどうかは、ローカル コンピューターで次の Azure PowerShell コマンドを使用することで確認できます。

	Test-AzureName -Service <Proposed cloud service name>

このコマンドで "False" が返された場合は、提案した名前は一意です。その後、次のコマンドでクラウド サービスを作成します。

	New-AzureService -Service <Unique cloud service name> -Location "<Table V – Item 2 – Value column>"

実際に作成した各クラウド サービスの名前を表 C に記録してください。

次に、SharePoint ファーム用のストレージ アカウントを作成します。*小文字と数字のみから成る一意の名前を選ぶ必要があります。* ストレージ アカウント名が一意かどうかは、次の Azure PowerShell コマンドで確認できます。

	Test-AzureName -Storage <Proposed storage account name>

このコマンドで "False" が返された場合は、提案した名前は一意です。さらに、ストレージ アカウントを作成し、それを使用するための設定をサブスクリプションに対して行います。以下のコマンドを使用してください。

	$staccount="<Unique storage account name>"
	New-AzureStorageAccount -StorageAccountName $staccount -Location "<Table V – Item 2 – Value column>"
	Set-AzureSubscription -SubscriptionName $subscr -CurrentStorageAccountName $staccount

次に、4 つの可用性セットの名前を定義します。表 A に記入します。

項目 | 目的 | 可用性セットの名前 
--- | --- | --- 
1. | ドメイン コントローラー | ___________________________
2. | SQL Server | ___________________________
3. | SharePoint アプリケーション サーバー | ___________________________
4. | SharePoint フロントエンド Web サーバー | ___________________________

**表 A: 可用性セットの名前**

これらの名前は、フェーズ 2、3、4 で仮想マシンを作成するときに必要になります。

次の図は、このフェーズが問題なく完了したときの構成を示しています。

![](./media/virtual-machines-workload-intranet-sharepoint-phase1/workload-spsqlao_01.png)

## 次の手順

このワークロードを引き続き構成するには、「[フェーズ 2: ドメイン コントローラーの構成](virtual-machines-workload-intranet-sharepoint-phase2.md)」に進んでください。

## その他のリソース

[Azure での SharePoint と SQL Server AlwaysOn 可用性グループのデプロイ](virtual-machines-workload-intranet-sharepoint-overview.md)

[Azure インフラストラクチャ サービスでホストされる SharePoint ファーム](virtual-machines-sharepoint-infrastructure-services.md)

[SharePoint と SQL Server AlwaysOn のインフォグラフィック](http://go.microsoft.com/fwlink/?LinkId=394788)

[SharePoint 2013 用の Microsoft Azure アーキテクチャ](https://technet.microsoft.com/library/dn635309.aspx)

<!--HONumber=54-->