<properties linkid="develop-php-how-to-guides-service-management" urlDisplayName="サービス管理" pageTitle="Azure サービス管理 API の使用方法 (PHP)" metaKeywords="" description="Azure PHP サービス管理 API を使用して、クラウド サービスおよびその他の Azure アプリケーションを管理する方法について説明します。" metaCanonical="" services="" documentationCenter="PHP" title="PHP からサービス管理を使用する方法" authors="waltpo" solutions="" manager="bjsmith" editor="mollybos" videoId="" scriptId="" />

# PHP からサービス管理を使用する方法

このガイドでは、PHP から一般的なサービス管理タスクをプログラムで実行する方法について説明します。[Azure SDK for PHP][download-SDK-PHP] の [ServiceManagementRestProxy] クラスは、[管理ポータル][management-portal]で使用できるサービス管理関連の機能 (**クラウド サービス、展開、ストレージ サービス、およびアフィニティ グループの作成、更新、削除**など) へのプログラムによるアクセスをサポートしています。この機能は、サービス管理へのプログラムによるアクセスが必要なアプリケーションをビルドするために役立つ場合があります。

##目次

* [サービス管理とは](#WhatIs)
* [概念](#Concepts)
* [PHP アプリケーションの作成](#CreateApplication)
* [Azure クライアント ライブラリの入手](#GetClientLibraries)
* [方法: サービス管理に接続する](#Connect)
* [方法: 利用可能な場所を列挙する](#ListAvailableLocations)
* [方法: クラウド サービスを作成する](#CreateCloudService)
* [方法: クラウド サービスを削除する](#DeleteCloudService)
* [方法: 展開を作成する](#CreateDeployment)
* [方法: 展開を更新する](#UpdateDeployment)
* [方法: ステージング環境と運用環境の間で展開を移動する](#MoveDeployments)
* [方法: 展開を削除する](#DeleteDeployment)
* [方法: ストレージ サービスを作成する](#CreateStorageService)
* [方法: ストレージ サービスを削除する](#DeleteStorageService)
* [方法: アフィニティ グループを作成する](#CreateAffinityGroup)
* [方法: アフィニティ グループを削除する](#DeleteAffinityGroup)

##<a id="WhatIs"></a>サービス管理とは
サービス管理 API を使用すると、[管理ポータル][management-portal]を通じて使用できるサービス管理機能の多くにプログラムでアクセスできます。Azure SDK for PHP を利用して、クラウド サービス、ストレージ アカウント、アフィニティ グループを管理できるようになります。

サービス管理 API を使用するには、[Azure アカウントを作成する][win-azure-account]必要があります。

##<a id="Concepts"></a>概念
Azure SDK for PHP は、REST API である [Azure サービス管理 API][svc-mgmt-rest-api] をラップします。すべての API 操作は SSL 上で実行され、X.509 v3 証明書を使用して相互認証されます。管理サービスへのアクセスは、Azure で実行されているサービス内から行うことも、HTTPS 要求の送信と HTTPS 応答の受信の機能を持つ任意のアプリケーションからインターネット上で直接行うこともできます。

##<a id="CreateApplication"></a>PHP アプリケーションの作成

Azure サービス管理を使用する PHP アプリケーションを作成するための要件は、コード内から Azure SDK for PHP のクラスを参照することのみです。アプリケーションの作成には、メモ帳などの任意の開発ツールを使用できます。

このガイドで使用するサービス機能は、PHP アプリケーション内でローカルで呼び出すことも、Azure の Web ロール、worker ロール、または Web サイト上で実行されるコード内で呼び出すこともできます。

##<a id="GetClientLibraries"></a>Azure クライアント ライブラリの入手

[WACOM.INCLUDE [get-client-libraries](../includes/get-client-libraries.md)]

##<a id="Connect"></a>方法: サービス管理に接続する

サービス管理エンドポイントに接続するには、Azure サブスクリプション ID、および有効な管理証明書へのパスが必要です。[管理ポータル][management-portal]を通じてサブスクリプション ID を取得し、多くの方法で管理証明書を作成できます。このガイドでは、[OpenSSL](http://www.openssl.org/) を使用しています。OpenSSL は [Windows 版をダウンロード](http://www.openssl.org/related/binaries.html)して、コンソールで実行できます。

実際は 2 つの証明書を作成する必要があります。1 つはサーバー用 (`.cer` ファイル)、もう 1 つはクライアント用 (`.pem` ファイル) です。`.pem` ファイルを作成するには、次のコマンドを実行します。

	`openssl req -x509 -nodes -days 365 -newkey rsa:1024 -keyout mycert.pem -out mycert.pem`

`.cer` 証明書を作成するには、次をコマンドを実行します。

	`openssl x509 -inform pem -in mycert.pem -outform der -out mycert.cer`

Azure 証明書の詳細については、「[Azure の証明書の概要](http://msdn.microsoft.com/ja-jp/library/windowsazure/gg981935.aspx)」を参照してください。OpenSSL のパラメーターの詳細については、[http://www.openssl.org/docs/apps/openssl.html](http://www.openssl.org/docs/apps/openssl.html) にあるドキュメントを参照してください。

[Azure コマンド ライン ツール][command-line-tools]を使用して発行設定ファイルをダウンロードおよびインポートした場合、ツールによって作成された `.pem` ファイルを使用できます。独自に作成する必要はありません。ツールによって `.cer` が作成され、Azure にアップロードされて、対応する `.pem` ファイルがコンピューター上の `.azure` ディレクトリ (ユーザー ディレクトリ内) に保存されます。

これらのファイルを作成した後、[管理ポータル][management-portal]を通じて `.cer` ファイルを Azure にアップロードし、`.pem` ファイルを保存した場所を書き留める必要があります。

サブスクリプション ID を取得し、証明書を作成して、`.cer` ファイルを Azure にアップロードした後、接続文字列を作成し、**ServicesBuilder** クラスの **createServiceManagementService** メソッドに渡すことで、Azure 管理のエンドポイントに接続できます。

	require_once 'vendor\autoload.php';
	
	use WindowsAzure\Common\ServicesBuilder;

	$conn_string = "SubscriptionID=<your_subscription_id>;CertificatePath=<path_to_.pem_certificate>";

	$serviceManagementRestProxy = ServicesBuilder::getInstance()->createServiceManagementService($conn_string);

この例では、`$serviceManagementRestProxy` は [ServiceManagementRestProxy] オブジェクトです。**ServiceManagementRestProxy** クラスは、Azure サービスの管理に使用される主要なクラスです。

##<a id="ListAvailableLocations"></a>方法: 利用可能な場所を列挙する

ホスティング サービスに利用可能な場所を列挙するには、**ServiceManagementRestProxy->listLocations** メソッドを使用します。

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\Common\ServiceException;

	try{
		$serviceManagementRestProxy = ServicesBuilder::getInstance()->createServiceManagementService($conn_string);
	
		$result = $serviceManagementRestProxy->listLocations();
	
		$locations = $result->getLocations();

		foreach($locations as $location){
		      echo $location->getName()."<br />";
		}
	}
	catch(ServiceException $e){
		// Handle exception based on error codes and messages.
		// Error codes and messages are here: 
		// http://msdn.microsoft.com/ja-jp/library/windowsazure/ee460801
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}

クラウド サービス、ストレージ サービス、またはアフィニティ グループを作成するときは、有効な場所を指定する必要があります。**listLocations** メソッドでは常に、現在利用可能な場所の最新のリストが返されます。この記事の執筆時点で利用可能な場所は次のとおりです。

- アメリカ合衆国地域
- ヨーロッパ地域
- 西ヨーロッパ
- アジア地域
- 東南アジア
- 東アジア
- 米国中北部
- 北ヨーロッパ
- 米国中南部
- 米国西部
- 米国東部

> [WACOM.NOTE]
> この後のコード例では、場所を文字列としてメソッドに渡しています。ただし、<code>WindowsAzure\ServiceManagement\Models\Locations</code> クラスを使用して、場所を列挙型として渡すこともできます。たとえば、場所を受け取るメソッドに "米国西部" を渡す代わりに、<code>Locations::WEST_US</code> を渡すことができます。

##<a id="CreateCloudService"></a>方法: クラウド サービスを作成する

アプリケーションを作成して、それを Azure で実行するときは、そのコードと構成をあわせて Azure [クラウド サービス]と呼びます (以前にリリースした Azure では*ホステッド サービス*と呼ばれていました)。**createHostedServices** メソッドを使用して、新しいホステッド サービスを作成できます。このメソッドには、ホステッド サービス名 (Azure 上で一意の名前)、ラベル (Base64 エンコード形式のホステッド サービス名)、**CreateServiceOptions** オブジェクトを渡します。[CreateServiceOptions] オブジェクトを使用して、サービスの場所*または*アフィニティ グループを設定できます。

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\ServiceManagement\Models\CreateServiceOptions;
	use WindowsAzure\Common\ServiceException;

	try{
		// Create REST proxy.
		$serviceManagementRestProxy = ServicesBuilder::getInstance()->createServiceManagementService($conn_string);
		
        $name = "myhostedservice";
        $label = base64_encode($name);
        $options = new CreateServiceOptions();
        $options->setLocation('West US');
		// Instead of setLocation, you can use setAffinityGroup
		// to set an affinity group.

        $result = $serviceManagementRestProxy->createHostedService($name, $label, $options);
	}
	catch(ServiceException $e){
		// Handle exception based on error codes and messages.
		// Error codes and messages are here: 
		// http://msdn.microsoft.com/ja-jp/library/windowsazure/ee460801
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}

**listHostedServices** メソッドを使用して、サブスクリプションのすべてのホステッド サービスを列挙できます。このメソッドによって [ListHostedServicesResult] オブジェクトが返されます。**getHostedServices** メソッドを呼び出し、[[HostedServices]] オブジェクトの配列をループ処理して、サービスのプロパティを取得できます。

	$listHostedServicesResult = $serviceManagementRestProxy->listHostedServices();

	$hosted_services = $listHostedServicesResult->getHostedServices();

	foreach($hosted_services as $hosted_service){
		echo "Service name: ".$hosted_service->getName()."<br />";
		echo "Management URL: ".$hosted_service->getUrl()."<br />";
		echo "Affinity group: ".$hosted_service->getAffinityGroup()."<br />";
		echo "Location: ".$hosted_service->getLocation()."<br />";
		echo "------<br />";
		}

特定のホステッド サービスに関する情報を取得する場合は、そのためにホステッド サービス名を **getHostedServiceProperties** メソッドに渡します。

	$getHostedServicePropertiesResult = $serviceManagementRestProxy->getHostedServiceProperties("myhostedservice");
		
	$hosted_service = $getHostedServicePropertiesResult->getHostedService();
		
	echo "Service name: ".$hosted_service->getName()."<br />";
	echo "Management URL: ".$hosted_service->getUrl()."<br />";
	echo "Affinity group: ".$hosted_service->getAffinityGroup()."<br />";
	echo "Location: ".$hosted_service->getLocation()."<br />";

クラウド サービスを作成した後、[createDeployment](#CreateDeployment) メソッドを使用してコードをサービスに展開できます。

##<a id="DeleteCloudService"></a>方法: クラウド サービスを削除する

クラウド サービスを削除するには、そのサービス名を **deleteHostedService** メソッドに渡します。

	$serviceManagementRestProxy->deleteHostedService("myhostedservice");

サービスを削除する前に、そのサービスのすべての展開を最初に削除する必要があることに注意してください (詳細については「[方法: 展開を削除する](#DeleteDeployment)」を参照)。

##<a id="CreateDeployment"></a>方法: 展開を作成する

**createDeployment** メソッドでは、ステージング環境または運用環境に新しい[サービス パッケージ]をアップロードし、新しい展開を作成します。このメソッドのパラメーターは次のとおりです。

* **$name**: ホステッド サービスの名前。
* **$deploymentName**: 展開の名前。
* **$slot**: ステージング スロットまたは運用スロットを示す列挙型。
* **$packageUrl**: 展開パッケージ (.cspgk ファイル) の URL。パッケージ ファイルは、パッケージのアップロード先のホステッド サービスと同じサブスクリプションの Azure BLOB ストレージ アカウントに保存する必要があります。展開パッケージを作成するには、[Azure PowerShell コマンドレット]または [cspack コマンド ライン ツール]を使用します。
* **$configuration**: サービス構成ファイル (.cscfg ファイル)。
* **$label**: Base64 エンコード形式のホステッド サービスの名前。

次の例では、`myhostedservice` という名前のホステッド サービスの運用スロットで新しい展開を作成しています。


	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\ServiceManagement\Models\DeploymentSlot;
	use WindowsAzure\Common\ServiceException;

	try{
		// Create REST proxy.
		$serviceManagementRestProxy = ServicesBuilder::getInstance()->createServiceManagementService($conn_string);
		
        $name = "myhostedservice";
		$deploymentName = "v1";
        $slot = DeploymentSlot::PRODUCTION;
		$packageUrl = "URL_for_.cspkg_file";
		$configuration = base64_encode(file_get_contents('path_to_.cscfg_file'));
		$label = base64_encode($name);

        $result = $serviceManagementRestProxy->createDeployment($name,
														 $deploymentName,
														 $slot,
														 $packageUrl,
														 $configuration,
														 $label);
		
		$status = $serviceManagementRestProxy->getOperationStatus($result);
		echo "Operation status: ".$status->getStatus()."<br />";
	}
	catch(ServiceException $e){
		// Handle exception based on error codes and messages.
		// Error codes and messages are here: 
		// http://msdn.microsoft.com/ja-jp/library/windowsazure/ee460801
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}

この例では、**createDeployment** 処理のステータスを取得するために、**createDeployment** によって返された結果を **getOperationStatus** メソッドに渡していることに注意してください。

展開のプロパティにアクセスするには、**getDeployment** メソッドを使用します。次の例では、展開を取得するために、[GetDeploymentOptions] オブジェクトで展開スロットを指定しますが、代わりに展開名を指定することもできます。この例では、展開のすべてのインスタンスの反復処理もしています。

	$options = new GetDeploymentOptions();
	$options->setSlot(DeploymentSlot::PRODUCTION);
		
	$getDeploymentResult = $serviceManagementRestProxy->getDeployment("myhostedservice", $options);
	$deployment = $getDeploymentResult->getDeployment();

	echo "Name: ".$deployment->getName()."<br />";
	echo "Slot: ".$deployment->getSlot()."<br />";
	echo "Private ID: ".$deployment->getPrivateId()."<br />";
	echo "Status: ".$deployment->getStatus()."<br />";
	echo "Instances: <br />";
	foreach($deployment->getroleInstanceList() as $roleInstance){
		echo "Instance name: ".$roleInstance->getInstanceName()."<br />";
		echo "Instance status: ".$roleInstance->getInstanceStatus()."<br />";
		echo "Instance size: ".$roleInstance->getInstanceSize()."<br />";
	}
	echo "------<br />";

##<a id="UpdateDeployment"></a>方法: 展開を更新する

展開は、**changeDeploymentConfiguration** メソッドまたは **updateDeploymentStatus** メソッドを使用して更新できます。

**changeDeploymentConfiguration** メソッドを使用して、新しいサービス構成 (`.cscfg`) ファイルをアップロードできます。これにより、複数のサービス設定のいずれか (展開内のインスタンスの数など) が変更されます。詳細については、「[Azure サービスの構成スキーマ (.cscfg ファイル)]」を参照してください。次の例では、新しいサービス構成ファイルをアップロードする方法を示しています。

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\ServiceManagement\Models\ChangeDeploymentConfigurationOptions;
	use WindowsAzure\ServiceManagement\Models\DeploymentSlot;
	use WindowsAzure\Common\ServiceException;

	try{
		// Create REST proxy.
		$serviceManagementRestProxy = ServicesBuilder::getInstance()->createServiceManagementService($conn_string);
		
        $name = "myhostedservice";
		$configuration = base64_encode(file_get_contents('path to .cscfg file'));
		$options = new ChangeDeploymentConfigurationOptions();
		$options->setSlot(DeploymentSlot::PRODUCTION);

        $result = $serviceManagementRestProxy->changeDeploymentConfiguration($name, $configuration, $options);
		
		$status = $serviceManagementRestProxy->getOperationStatus($result);
		echo "Operation status: ".$status->getStatus()."<br />";
	}
	catch(ServiceException $e){
		// Handle exception based on error codes and messages.
		// Error codes and messages are here: 
		// http://msdn.microsoft.com/ja-jp/library/windowsazure/ee460801
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}

この例では、**changeDeploymentConfiguration** 処理のステータスを取得するために、**changeDeploymentConfiguration** によって返された結果を **getOperationStatus** メソッドに渡していることに注意してください。

**updateDeploymentStatus** メソッドでは、展開のステータスを RUNNING または SUSPENDED に設定できます。次の例では、`myhostedservice` という名前のホステッド サービスの運用スロットにある展開について、そのステータスを RUNNING に設定する方法を示しています。

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\ServiceManagement\Models\DeploymentStatus;
	use WindowsAzure\ServiceManagement\Models\DeploymentSlot;
	use WindowsAzure\ServiceManagement\Models\GetDeploymentOptions;
	use WindowsAzure\Common\ServiceException;
	
	try{
		// Create REST proxy.
		$serviceManagementRestProxy = ServicesBuilder::getInstance()->createServiceManagementService($conn_string);
		
		$options = new GetDeploymentOptions();
		$options->setSlot(DeploymentSlot::PRODUCTION);
		
        $result = $serviceManagementRestProxy->updateDeploymentStatus("myhostedservice", DeploymentStatus::RUNNING, $options);
	}
	catch(ServiceException $e){
		// Handle exception based on error codes and messages.
		// Error codes and messages are here: 
		// http://msdn.microsoft.com/ja-jp/library/windowsazure/ee460801
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}

##<a id="MoveDeployments"></a>方法: ステージング環境と運用環境の間で展開を移動する

Azure には、2 つの展開環境が用意されています。ステージングと運用です。通常は、サービスをステージング環境にデプロイしてテストし、その後で運用環境にデプロイします。ステージング環境のサービスを運用環境へ昇格する場合、サービスを再デプロイする必要はありません。デプロイをスワップすることで運用環境へ昇格できます。(展開のスワップの詳細については、[Azure における展開管理の概要]を参照してください)。

次の例では、**swapDeployment** メソッドを使用して 2 つの展開 (展開名は `v1` と `v2`) をスワップする方法を示しています。この例では、**swapDeployment** の呼び出し前、展開 `v1` は運用スロットに、`v2` はステージング スロットにあります。**swapDeployment** の呼び出し後、`v2` は運用スロットに、`v1` はステージング スロットにあります。

	require_once 'vendor\autoload.php';	

	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\Common\ServiceException;

	try{
		// Create REST proxy.
		$serviceManagementRestProxy = ServicesBuilder::getInstance()->createServiceManagementService($conn_string);
		
		$result = $serviceManagementRestProxy->swapDeployment("myhostedservice", "v2", "v1");
	}
	catch(ServiceException $e){
		// Handle exception based on error codes and messages.
		// Error codes and messages are here: 
		// http://msdn.microsoft.com/ja-jp/library/windowsazure/ee460801
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}

##<a id="DeleteDeployment"></a>方法: 展開を削除する

展開を削除するには、**deleteDeployment** メソッドを使用します。次の例では、ステージング環境で展開を削除する方法を示しています。そのためには、[GetDeploymentOptions] オブジェクトの **setSlot** メソッドを使用して、展開を **deleteDeployment** に渡します。スロットで展開を指定する代わりに、[[GetDepolymentOptions]] クラスの **setName** メソッドを使用して展開名で展開を指定できます。

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\ServiceManagement\Models\GetDeploymentOptions;
	use WindowsAzure\ServiceManagement\Models\DeploymentSlot;
	use WindowsAzure\Common\ServiceException;

	try{
		// Create REST proxy.
		$serviceManagementRestProxy = ServicesBuilder::getInstance()->createServiceManagementService($conn_string);
		
		$options = new GetDeploymentOptions();
		$options->setSlot(DeploymentSlot::STAGING);
		
		$result = $serviceManagementRestProxy->deleteDeployment("myhostedservice", $options);
	}
	catch(ServiceException $e){
		// Handle exception based on error codes and messages.
		// Error codes and messages are here: 
		// http://msdn.microsoft.com/ja-jp/library/windowsazure/ee460801
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}

##<a id="CreateStorageService"></a>方法: ストレージ サービスを作成する

[ストレージ サービス] を使用すると、Azure の [BLOB][azure-blobs]、[テーブル][azure-tables]、[キュー][azure-queues] にアクセスできます。ストレージ サービスを作成するには、サービスの名前 (Azure 内で一意の 3 〜 24 文字の小文字)、ラベル (Base64 エンコード形式の最大 100 文字のサービス名)、場所 (またはアフィニティ グループ) が必要です。サービスの説明は省略できます。場所、アフィニティ グループ、説明は [CreateServiceOptions] オブジェクトで設定します。このオブジェクトを **createStorageService** メソッドに渡します。次の例では、場所を指定してストレージ サービスを作成する方法を示しています。アフィニティ グループを使用する場合は、まずアフィニティ グループを作成し (「[方法: アフィニティ グループの作成](#CreateAffinityGroup)」を参照)、**CreateServiceOptions->setAffinityGroup** メソッドでアフィニティ グループを設定する必要があります。

	require_once 'vendor\autoload.php';
	 
	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\ServiceManagement\Models\CreateServiceOptions;
	use WindowsAzure\Common\ServiceException;
	 
	 
	try{
		// Create REST proxy.
		$serviceManagementRestProxy = ServicesBuilder::getInstance()->createServiceManagementService($conn_string);
		
        $name = "mystorageaccount";
        $label = base64_encode($name);
        $options = new CreateServiceOptions();
        $options->setLocation('West US');
		$options->setDescription("My storage account description.");

        $result = $serviceManagementRestProxy->createStorageService($name, $label, $options);

		$status = $serviceManagementRestProxy->getOperationStatus($result);
		echo "Operation status: ".$status->getStatus()."<br />";
	}
	catch(ServiceException $e){
		// Handle exception based on error codes and messages.
		// Error codes and messages are here: 
		// http://msdn.microsoft.com/ja-jp/library/windowsazure/ee460801
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}

この例では、**createStorageService** 処理のステータスを取得するために、**createStorageService** によって返された結果を **getOperationStatus** メソッドに渡していることに注意してください。

ストレージ アカウントとそれらのプロパティを列挙するには、**listStorageServices** メソッドを使用します。

	// Create REST proxy.
	$serviceManagementRestProxy = ServicesBuilder::getInstance()->createServiceManagementService($conn_string);
	$getStorageServicesResult = $serviceManagementRestProxy->listStorageServices();
	$storageServices = $getStorageServicesResult->getStorageServices();

	foreach($storageServices as $storageService){
		echo "Service name: ".$storageService->getName()."<br />";
		echo "Service URL: ".$storageService->getUrl()."<br />";
		echo "Affinity group: ".$storageService->getAffinityGroup()."<br />";
		echo "Location: ".$storageService->getLocation()."<br />";
		echo "------<br />";
	}

##<a id="DeleteStorageService"></a>方法: ストレージ サービスを削除する

ストレージ サービスを削除するには、そのサービス名を **deleteStorageService** メソッドに渡します。ストレージ サービスを削除すると、サービスに格納されているすべてのデータ (BLOB、テーブル、キュー) が削除されます。

	require_once 'vendor\autoload.php';
	
	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\Common\ServiceException;

	try{
		// Create REST proxy.
		$serviceManagementRestProxy = ServicesBuilder::getInstance()->createServiceManagementService($conn_string);
		
		$serviceManagementRestProxy->deleteStorageService("mystorageservice");
	}
	catch(ServiceException $e){
		// Handle exception based on error codes and messages.
		// Error codes and messages are here: 
		// http://msdn.microsoft.com/ja-jp/library/windowsazure/ee460801
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}

##<a id="CreateAffinityGroup"></a>方法: アフィニティ グループを作成する

アフィニティ グループは Azure サービスの論理グループであり、Azure で最適なパフォーマンスを得られるようにサービスを配置するために使用されます。たとえば、アフィニティ グループを "米国西部" という場所に作成し、そのアフィニティ グループ内に[クラウド サービス](#CreateCloudService)を作成できます。その後、同じアフィニティ グループ内にストレージ サービスを作成した場合、Azure では、そのサービスは場所 "米国西部" に配置され、同じアフィニティ グループ内のクラウド サービスと連携して最高レベルのパフォーマンスが得られるように、データ センター内で最適化されます。

アフィニティ グループを作成するには、グループの名前、ラベル (Base64 エンコード形式の名前)、場所が必要です。必要に応じて説明を指定できます。

	require_once 'vendor\autoload.php';
	
	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\ServiceManagement\Models\CreateAffinityGroupOptions;
	use WindowsAzure\Common\ServiceException;
	 
	try{
		// Create REST proxy.
		$serviceManagementRestProxy = ServicesBuilder::getInstance()->createServiceManagementService($conn_string);
		
        $name = "myAffinityGroup";
        $label = base64_encode($name);
        $location = "West US";

        $options = new CreateAffinityGroupOptions();
		$options->setDescription = "My affinity group description.";
		
        $serviceManagementRestProxy->createAffinityGroup($name, $label, $location, $options);
	}
	catch(ServiceException $e){
		// Handle exception based on error codes and messages.
		// Error codes and messages are here: 
		// http://msdn.microsoft.com/ja-jp/library/windowsazure/ee460801
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}

アフィニティ グループの作成後、[ストレージ サービスを作成](#CreateStorageService)するときは、場所ではなくグループを指定できます。

アフィニティ グループを列挙し、それらのプロパティを調べることができます。そのためには、**listAffinityGroups** メソッドを呼び出してから、[AffinityGroup] クラスの該当するメソッドを呼び出します。

	$result = $serviceManagementRestProxy->listAffinityGroups();
	
	$groups = $result->getAffinityGroups();

	foreach($groups as $group){
		echo "Name: ".$group->getName()."<br />";
		echo "Description: ".$group->getDescription()."<br />";
		echo "Location: ".$group->getLocation()."<br />";
		echo "------<br />";
	}

##<a id="DeleteAffinityGroup"></a>方法: アフィニティ グループを削除する
	
アフィニティ グループを削除するには、そのグループ名を **deleteAffinityGroup** メソッドに渡します。アフィニティ グループを削除する前に、アフィニティ グループとサービス (削除するアフィニティ グループを使用するサービス) との関連付けを解除する必要があることに注意してください。

	require_once 'vendor\autoload.php';
	
	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\Common\ServiceException;

	try{
		// Create REST proxy.
		$serviceManagementRestProxy = ServicesBuilder::getInstance()->createServiceManagementService($conn_string);
		
		// An affinity group must be disassociated from all services 
		// before it can be deleted.
		$serviceManagementRestProxy->deleteAffinityGroup("myAffinityGroup");
	}
	catch(ServiceException $e){
		// Handle exception based on error codes and messages.
		// Error codes and messages are here: 
		// http://msdn.microsoft.com/ja-jp/library/windowsazure/ee460801
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}

[ServiceManagementRestProxy]: https://github.com/WindowsAzure/azure-sdk-for-php/blob/master/WindowsAzure/ServiceManagement/ServiceManagementRestProxy.php
[management-portal]: https://manage.windowsazure.com/
[svc-mgmt-rest-api]: http://msdn.microsoft.com/ja-jp/library/windowsazure/ee460799.aspx
[win-azure-account]: /ja-jp/pricing/free-trial/
[storage-account]: ../storage-create-storage-account/

[download-SDK-PHP]: ../php-download-sdk/
[command-line-tools]: ../command-line-tools/
[Composer]: http://getcomposer.org/
[ServiceManagementSettings]: https://github.com/WindowsAzure/azure-sdk-for-php/blob/master/WindowsAzure/ServiceManagement/ServiceManagementSettings.php

[クラウド サービス]: ../cloud-services-what-is/
[CreateServiceOptions]: https://github.com/WindowsAzure/azure-sdk-for-php/blob/master/WindowsAzure/ServiceManagement/Models/CreateServiceOptions.php
[ListHostedServicesResult]: https://github.com/WindowsAzure/azure-sdk-for-php/blob/master/WindowsAzure/ServiceManagement/Models/ListHostedServicesResult.php

[サービス パッケージ]: http://msdn.microsoft.com/ja-jp/library/windowsazure/gg433093
[Azure PowerShell コマンドレット]: ../install-configure-powershell/
[CSPack コマンド ライン ツール]: http://msdn.microsoft.com/ja-jp/library/windowsazure/gg432988.aspx
[GetDeploymentOptions]: https://github.com/WindowsAzure/azure-sdk-for-php/blob/master/WindowsAzure/ServiceManagement/Models/GetDeploymentOptions.php
[ListHostedServicesResult]: https://github.com/WindowsAzure/azure-sdk-for-php/blob/master/WindowsAzure/ServiceManagement/Models/GetDeploymentOptions.php

[Azure における展開管理の概要]: http://msdn.microsoft.com/ja-jp/library/windowsazure/hh386336.aspx
[ストレージ サービス]: ../storage-whatis-account/
[azure-blobs]: ../storage-php-how-to-use-blobs/
[azure-tables]: ../storage-php-how-to-use-table-storage/
[azure-queues]: ../storage-php-how-to-use-queues/
[AffinityGroup]: https://github.com/WindowsAzure/azure-sdk-for-php/blob/master/WindowsAzure/ServiceManagement/Models/AffinityGroup.php


[Azure サービスの構成スキーマ (.cscfg ファイル)]: http://msdn.microsoft.com/ja-jp/library/windowsazure/ee758710.aspx
