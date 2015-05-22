<properties services="virtual-machines" title="Setting up PowerShell for Resource Manager templates" authors="JoeDavies-MSFT" solutions="" manager="timlt" editor="tysonn" />

<tags
   ms.service="virtual-machines"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm=""
   ms.workload="infrastructure"
   ms.date="04/27/2015"
   ms.author="josephd" />

## リソース マネージャー テンプレート向けの PowerShell の設定

リソース マネージャーで Azure PowerShell を使用する前に、適切なバージョンの Windows PowerShell と Azure PowerShell を用意する必要があります。

### 手順 1. PowerShell のバージョンを確認する

Windows PowerShell Version 3.0 または 4.0 があることを確認します。Windows PowerShell のバージョンを確認するには、Windows PowerShell コマンド プロンプトで次のコマンドを入力します。

	$PSVersionTable

次の種類の情報が表示されます。

	Name                           Value
	----                           -----
	PSVersion                      3.0
	WSManStackVersion              3.0
	SerializationVersion           1.1.0.1
	CLRVersion                     4.0.30319.18444
	BuildVersion                   6.2.9200.16481
	PSCompatibleVersions           {1.0, 2.0, 3.0}
	PSRemotingProtocolVersion      2.2


**PSVersion** の値が 3.0 または 4.0 であることを確認します。3.0 または 4.0 でない場合は、[Windows Management Framework 3.0](http://www.microsoft.com/download/details.aspx?id=34595) または [Windows Management Framework 4.0](http://www.microsoft.com/download/details.aspx?id=40855) のダウンロード ページを参照してください。

また、Azure PowerShell Version 0.9.0 以降も必要です。Azure PowerShell をまだインストールおよび構成していない場合は、[こちら](powershell-install-configure.md)で手順を参照してください。

インストールした Azure PowerShell のバージョンは、Azure PowerShell コマンド プロンプトで次のコマンドを使用して確認できます。

	Get-Module azure | format-table version

次の種類の情報が表示されます。

	Version
	-------
	0.9.0

0\.9.0 以降でない場合は、コントロール パネルの \[プログラムと機能\] を使用して Azure PowerShell を削除してから、最新バージョンをインストールする必要があります。詳細については、[Azure PowerShell のインストールと構成の方法](powershell-install-configure.md)に関するページを参照してください。

### 手順 2. Azure アカウントとサブスクリプションを設定する

Azure サブスクリプションを持っていない場合は、[MSDN サブスクライバーの特典](http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)を有効にするか、[無料評価版](http://azure.microsoft.com/pricing/free-trial/)にサインアップしてください。

Azure PowerShell コマンド プロンプトを開き、次のコマンドで Azure にログオンします。

	Add-AzureAccount

Azure サブスクリプションが複数ある場合は、次のコマンドで、Azure サブスクリプションの一覧を表示できます。

	Get-AzureSubscription

次の種類の情報が表示されます。

	SubscriptionId            : fd22919d-eaca-4f2b-841a-e4ac6770g92e
	SubscriptionName          : Visual Studio Ultimate with MSDN
	Environment               : AzureCloud
	SupportedModes            : AzureServiceManagement,AzureResourceManager
	DefaultAccount            : johndoe@contoso.com
	Accounts                  : {johndoe@contoso.com}
	IsDefault                 : True
	IsCurrent                 : True
	CurrentStorageAccountName : 
	TenantId                  : 32fa88b4-86f1-419f-93ab-2d7ce016dba7

Azure PowerShell コマンド プロンプトで次のコマンドを実行して、現在の Azure サブスクリプションを設定します。引用符内のすべての文字 \(< and > を含む\) を正しい名前に置き換えます。

	$subscr="<SubscriptionName from the display of Get-AzureSubscription>"
	Select-AzureSubscription -SubscriptionName $subscr –Current	

Azure サブスクリプションとアカウントの詳細については、[サブスクリプションへの接続方法](powershell-install-configure.md#Connect)に関するページを参照してください。

### 手順 3. Azure リソース マネージャー モジュールに切り替える

Azure リソース マネージャー モジュールを使用するには、既定の Azure コマンド セットから Azure リソース マネージャーのコマンド セットに切り替える必要があります。次のコマンドを実行します。

	Switch-AzureMode AzureResourceManager

> [AZURE.NOTE]**Switch-AzureMode AzureServiceManagement** コマンドを使用して、既定のコマンド セットに戻すことができます。


<!--HONumber=52-->