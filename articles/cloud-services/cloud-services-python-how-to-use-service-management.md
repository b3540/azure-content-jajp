<properties
	pageTitle="サービス管理 API の使用方法 (Python) - 機能ガイド"
	description="Python から一般的なサービス管理タスクをプログラムで実行する方法について説明します。"
	services="cloud-services"
	documentationCenter="python"
	authors="huguesv"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="cloud-services"
	ms.workload="tbd"
	ms.tgt_pltfrm="na"
	ms.devlang="python"
	ms.topic="article"
	ms.date="03/11/2015"
	ms.author="huvalo"/>

# Python からサービス管理を使用する方法

このガイドでは、Python から一般的なサービス管理タスクをプログラムで実行する方法について説明します。**Azure SDK for Python** の [ServiceManagementService][download-SDK-Python] クラスは、[管理ポータル][management-portal]で使用できるサービス管理関連の機能 (**クラウド サービス、デプロイメント、データ管理サービスおよび仮想マシンの作成、更新、削除**など) へのプログラムによるアクセスをサポートしています。この機能は、サービス管理へのプログラムによるアクセスが必要なアプリケーションをビルドするために役立つ場合があります。

## <a name="WhatIs"> </a>サービス管理とは
サービス管理 API を使用すると、[管理ポータル][management-portal]を通じて使用できるサービス管理機能の多くにプログラムでアクセスできます。Azure SDK for Python を使用すると、クラウド サービスとストレージ アカウントを管理できます。

サービス管理 API を使用するには、[Azure アカウントを作成する](http://www.windowsazure.com/pricing/free-trial/)必要があります。

## <a name="Concepts"> </a>概念
Azure SDK for Python は、REST API である [Azure サービス管理 API][svc-mgmt-rest-api] をラップします。すべての API 操作は SSL 上で実行され、X.509 v3 証明書を使用して相互認証されます。管理サービスへのアクセスは、Azure で実行されているサービス内から行うことも、HTTPS 要求の送信と HTTPS 応答の受信の機能を持つ任意のアプリケーションからインターネット上で直接行うこともできます。

## <a name="Connect"> </a>方法: サービス管理に接続する
サービス管理エンドポイントに接続するには、Azure サブスクリプション ID、および有効な管理証明書が必要です。サブスクリプション ID は[管理ポータル][management-portal]から入手できます。

> [AZURE.NOTE]Azure SDK for Python v0.8.0 以降、Windows で実行中の OpenSSL で作成した証明書を使用できるようになりました。これには、Python 2.7.4 以降が必要です。.pfx 証明書のサポートは今後削除される可能性があるため、.pfx の代わりに OpenSSL を使用することをお勧めします。

### Windows、Mac または Linux での管理証明書 (OpenSSL)
[OpenSSL](http://www.openssl.org/) を使用して管理証明書を作成できます。実際は 2 つの証明書を作成する必要があります。1 つはサーバー用 (`.cer` ファイル)、もう 1 つはクライアント用 (`.pem` ファイル) です。`.pem` ファイルを作成するには、次のコマンドを実行します。

	`openssl req -x509 -nodes -days 365 -newkey rsa:1024 -keyout mycert.pem -out mycert.pem`

`.cer` 証明書を作成するには、次のコマンドを実行します。

	`openssl x509 -inform pem -in mycert.pem -outform der -out mycert.cer`

Azure 証明書の詳細については、「[証明書の管理](http://msdn.microsoft.com/library/windowsazure/gg981929.aspx)」を参照してください。OpenSSL のパラメーターの詳細については、[http://www.openssl.org/docs/apps/openssl.html](http://www.openssl.org/docs/apps/openssl.html) にあるドキュメントを参照してください。

これらのファイルを作成した後、[管理ポータル][management-portal]の [設定] タブで [アップロード] をクリックして、`.cer` ファイルを Azure にアップロードする必要があります。また、`.pem` ファイルを保存した場所を書き留めておいてください。

サブスクリプション ID を取得し、証明書を作成して、`.cer` ファイルを Azure にアップロードした後、サブスクリプション ID と `.pem` ファイルへのパスを **ServiceManagementService** に渡すことで、Azure の管理エンドポイントに接続できます。

	from azure import *
	from azure.servicemanagement import *

	subscription_id = '<your_subscription_id>'
	certificate_path = '<path_to_.pem_certificate>'

	sms = ServiceManagementService(subscription_id, certificate_path)

この例では、`sms` は **ServiceManagementService** オブジェクトです。**ServiceManagementService** クラスは、Azure サービスを管理するときに使用する主要なクラスです。

### Windows での管理証明書 (MakeCert)

`makecert.exe` を使用して、マシン上で自己署名管理証明書を作成できます。**管理者**として **Visual Studio コマンド プロンプト**を開き、次のコマンドを使用して、*AzureCertificate* を、使用する証明書の名前に置き換えます。

    makecert -sky exchange -r -n "CN=AzureCertificate" -pe -a sha1 -len 2048 -ss My "AzureCertificate.cer"

このコマンドにより、`.cer` ファイルが作成され、**個人用**証明書ストアにインストールされます。詳細については、「[Windows Azure の管理証明書の作成とアップロード](http://msdn.microsoft.com/library/windowsazure/gg551722.aspx)」を参照してください。

証明書を作成した後、[管理ポータル][management-portal]の [設定] タブで [アップロード] をクリックして、`.cer` ファイルを Azure にアップロードする必要があります。

サブスクリプション ID を取得し、証明書を作成して、`.cer` ファイルを Azure にアップロードした後、サブスクリプション ID と**個人用**証明書ストア内の証明書の場所を **ServiceManagementService** に渡すことで、Azure の管理エンドポイントに接続できます (再度、*AzureCertificate* を証明書の名前に置き換えます)。

	from azure import *
	from azure.servicemanagement import *

	subscription_id = '<your_subscription_id>'
	certificate_path = 'CURRENT_USER\\my\\AzureCertificate'

	sms = ServiceManagementService(subscription_id, certificate_path)

この例では、`sms` は **ServiceManagementService** オブジェクトです。**ServiceManagementService** クラスは、Azure サービスを管理するときに使用する主要なクラスです。

## <a name="ListAvailableLocations"> </a>方法: 利用可能な場所を列挙する

ホスティング サービスに利用できる場所を列挙するには、**list_locations** メソッドを使用します。

	from azure import *
	from azure.servicemanagement import *

	sms = ServiceManagementService(subscription_id, certificate_path)

	result = sms.list_locations()
	for location in result:
		print(location.name)

クラウド サービスやストレージ サービスを作成するときは、有効な場所を指定する必要があります。**list_locations** メソッドでは常に、現在利用可能な場所の最新のリストが返されます。この記事の執筆時点で利用可能な場所は次のとおりです。

- 西ヨーロッパ
- 北ヨーロッパ
- 東南アジア
- 東アジア
- 米国中央部
- 米国中北部
- 米国中南部
- 米国西部
- 米国東部
- 東日本
- 西日本
- ブラジル南部
- オーストラリア東部
- オーストラリア南東部

## <a name="CreateCloudService"> </a>方法: クラウド サービスを作成する

アプリケーションを作成して、それを Azure で実行するときは、そのコードと構成をあわせて Azure [クラウド サービス]と呼びます (以前にリリースした Azure では*ホステッド サービス*と呼ばれていました)。**create_hosted_service** メソッドを使用して、新しいホステッド サービスを作成できます。そのためには、このメソッドに、ホステッド サービス名 (Azure 上で一意の名前)、ラベル (Base64 に自動的にエンコードされます)、説明、場所を渡します。

	from azure import *
	from azure.servicemanagement import *

	sms = ServiceManagementService(subscription_id, certificate_path)

	name = 'myhostedservice'
	label = 'myhostedservice'
	desc = 'my hosted service'
	location = 'West US'

	sms.create_hosted_service(name, label, desc, location)

**list_hosted_services** メソッドを使用して、サブスクリプションのすべてのホステッド サービスを列挙できます。

	result = sms.list_hosted_services()

	for hosted_service in result:
		print('Service name: ' + hosted_service.service_name)
		print('Management URL: ' + hosted_service.url)
		print('Location: ' + hosted_service.hosted_service_properties.location)
		print('')

特定のホステッド サービスに関する情報を取得する場合は、ホステッド サービス名を **get_hosted_service_properties** メソッドに渡します。

	hosted_service = sms.get_hosted_service_properties('myhostedservice')

	print('Service name: ' + hosted_service.service_name)
	print('Management URL: ' + hosted_service.url)
	print('Location: ' + hosted_service.hosted_service_properties.location)

クラウド サービスを作成した後、**create_deployment** メソッドを使用してコードをサービスにデプロイできます。

## <a name="DeleteCloudService"> </a>方法: クラウド サービスを削除する

クラウド サービスを削除するには、そのサービス名を **delete_hosted_service** メソッドに渡します。

	sms.delete_hosted_service('myhostedservice')

サービスを削除する前に、そのサービスのすべての展開を最初に削除する必要があることに注意してください(詳細については「[方法: デプロイの削除](#DeleteDeployment)」を参照)。

## <a name="DeleteDeployment"> </a>方法: デプロイの削除

デプロイメントを削除するには、**delete_deployment** メソッドを使用します。次の例では、`v1` という名前のデプロイメントを削除する方法を示しています。

	from azure import *
	from azure.servicemanagement import *

	sms = ServiceManagementService(subscription_id, certificate_path)

	sms.delete_deployment('myhostedservice', 'v1')

## <a name="CreateStorageService"> </a>方法: ストレージ サービスを作成する

[ストレージ サービス] を使用すると、Azure の [BLOB][azure-blobs]、[テーブル][azure-tables]、[キュー][azure-queues] にアクセスできます。ストレージ サービスを作成するには、サービスの名前 (Azure 内で一意の 3 〜 24 文字の小文字)、説明、ラベル (最大 100 文字、Base64 に自動的にエンコードされます)、場所が必要です。次の例では、場所を指定してストレージ サービスを作成する方法を示しています。

	from azure import *
	from azure.servicemanagement import *

	sms = ServiceManagementService(subscription_id, certificate_path)

	name = 'mystorageaccount'
	label = 'mystorageaccount'
	location = 'West US'
	desc = 'My storage account description.'

	result = sms.create_storage_account(name, desc, label, location=location)

	operation_result = sms.get_operation_status(result.request_id)
	print('Operation status: ' + operation_result.status)

上の例では、**create_storage_account** 処理のステータスを取得するため、**create_storage_account** から返された結果を **get_operation_status** メソッドに渡しています。

ストレージ アカウントとそのプロパティを列挙するには、**list_storage_accounts** メソッドを使用します。

	from azure import *
	from azure.servicemanagement import *

	sms = ServiceManagementService(subscription_id, certificate_path)

	result = sms.list_storage_accounts()
	for account in result:
		print('Service name: ' + account.service_name)
		print('Location: ' + account.storage_service_properties.location)
		print('')

## <a name="DeleteStorageService"> </a>方法: ストレージ サービスを削除する

ストレージ サービスを削除するには、そのサービス名を **delete_storage_account** メソッドに渡します。ストレージ サービスを削除すると、サービスに格納されているすべてのデータ (BLOB、テーブル、キュー) が削除されます。

	from azure import *
	from azure.servicemanagement import *

	sms = ServiceManagementService(subscription_id, certificate_path)

	sms.delete_storage_account('mystorageaccount')

## <a name="ListOperatingSystems"> </a>方法: 利用可能なオペレーティング システムを列挙する

ホスティング サービスに利用できるオペレーティング システムを列挙するには、**list_operating_systems** メソッドを使用します。

	from azure import *
	from azure.servicemanagement import *

	sms = ServiceManagementService(subscription_id, certificate_path)

	result = sms.list_operating_systems()

	for os in result:
		print('OS: ' + os.label)
		print('Family: ' + os.family_label)
		print('Active: ' + str(os.is_active))

または、**list_operating_system_families** メソッドを使用することもできます。このメソッドでは、オペレーティング システムがファミリにグループ化されます。

	result = sms.list_operating_system_families()

	for family in result:
		print('Family: ' + family.label)
		for os in family.operating_systems:
			if os.is_active:
				print('OS: ' + os.label)
				print('Version: ' + os.version)
		print('')

## <a name="CreateVMImage"> </a>方法: オペレーティング システム イメージを作成する

オペレーティング システム イメージをイメージ リポジトリに追加するには、**add_os_image** メソッドを使用します。

	from azure import *
	from azure.servicemanagement import *

	sms = ServiceManagementService(subscription_id, certificate_path)

	name = 'mycentos'
	label = 'mycentos'
	os = 'Linux' # Linux or Windows
	media_link = 'url_to_storage_blob_for_source_image_vhd'

	result = sms.add_os_image(label, media_link, name, os)

	operation_result = sms.get_operation_status(result.request_id)
	print('Operation status: ' + operation_result.status)

利用できるオペレーティング システム イメージを列挙するには、**list_os_images** メソッドを使用します。このメソッドでは、すべてのプラットフォーム イメージとユーザー イメージが対象となります。

	result = sms.list_os_images()

	for image in result:
		print('Name: ' + image.name)
		print('Label: ' + image.label)
		print('OS: ' + image.os)
		print('Category: ' + image.category)
		print('Description: ' + image.description)
		print('Location: ' + image.location)
		print('Media link: ' + image.media_link)
		print('')

## <a name="DeleteVMImage"> </a>方法: オペレーティング システム イメージを削除する

ユーザー イメージを削除するには、**delete_os_image** メソッドを使用します。

	from azure import *
	from azure.servicemanagement import *

	sms = ServiceManagementService(subscription_id, certificate_path)

	result = sms.delete_os_image('mycentos')

	operation_result = sms.get_operation_status(result.request_id)
	print('Operation status: ' + operation_result.status)

## <a name="CreateVM"> </a>方法: 仮想マシンを作成する

仮想マシンを作成するには、最初に[クラウド サービス](#CreateCloudService)を作成する必要があります。その後で、**create_virtual_machine_deployment** メソッドを使用して、仮想マシンのデプロイメントを作成します。

	from azure import *
	from azure.servicemanagement import *

	sms = ServiceManagementService(subscription_id, certificate_path)

	name = 'myvm'
	location = 'West US'

	#Set the location
	sms.create_hosted_service(service_name=name,
		label=name,
		location=location)

	# Name of an os image as returned by list_os_images
	image_name = 'OpenLogic__OpenLogic-CentOS-62-20120531-ja-jp-30GB.vhd'

	# Destination storage account container/blob where the VM disk
	# will be created
	media_link = 'url_to_target_storage_blob_for_vm_hd'

	# Linux VM configuration, you can use WindowsConfigurationSet
	# for a Windows VM instead
	linux_config = LinuxConfigurationSet('myhostname', 'myuser', 'mypassword', True)

	os_hd = OSVirtualHardDisk(image_name, media_link)

	sms.create_virtual_machine_deployment(service_name=name,
		deployment_name=name,
		deployment_slot='production',
		label=name,
		role_name=name,
		system_config=linux_config,
		os_virtual_hard_disk=os_hd,
		role_size='Small')

## <a name="DeleteVM"> </a>方法: 仮想マシンを削除する

仮想マシンを削除するには、最初に **delete_deployment** メソッドを使用してデプロイメントを削除します。

	from azure import *
	from azure.servicemanagement import *

	sms = ServiceManagementService(subscription_id, certificate_path)

	sms.delete_deployment(service_name='myvm',
		deployment_name='myvm')

その後で、**delete_hosted_service** メソッドを使用して、クラウド サービスを削除することができます。

	sms.delete_hosted_service(service_name='myvm')

##方法: キャプチャした仮想マシン イメージから仮想マシンを作成する

VM イメージをキャプチャするには、まず、**capture_vm_image** メソッドを呼び出します。

	from azure import *
	from azure.servicemanagement import *

	sms = ServiceManagementService(subscription_id, certificate_path)

	# replace the below three parameters with actual values
	hosted_service_name = 'hs1'
	deployment_name = 'dep1'
	vm_name = 'vm1'

	image_name = vm_name + 'image'
	image = CaptureRoleAsVMImage	('Specialized',
		image_name,
		image_name + 'label',
		image_name + 'description',
		'english',
		'mygroup')

	result = sms.capture_vm_image(
			hosted_service_name,
			deployment_name,
			vm_name,
			image
		)

次に、イメージが正常にキャプチャされたことを確認するには、**list_vm_images** API を使用して、イメージが結果に表示されていることを確認します。

	images = sms.list_vm_images()

キャプチャしたイメージを使用して仮想マシンを作成するために、前の手順と同様に、**create_virtual_machine_deployment** メソッドを使用しますが、今回は vm_image_name を渡します。

	from azure import *
	from azure.servicemanagement import *

	sms = ServiceManagementService(subscription_id, certificate_path)

	name = 'myvm'
	location = 'West US'

	#Set the location
	sms.create_hosted_service(service_name=name,
		label=name,
		location=location)

	sms.create_virtual_machine_deployment(service_name=name,
		deployment_name=name,
		deployment_slot='production',
		label=name,
		role_name=name,
		system_config=linux_config,
		os_virtual_hard_disk=None,
		role_size='Small',
		vm_image_name = image_name)

Linux 仮想マシンをキャプチャする方法についての詳細は、[Linux 仮想マシンをキャプチャする方法](../virtual-machines-linux-capture-image.md)に関するページを参照してください。

Windows 仮想マシンをキャプチャする方法についての詳細は、[Windows 仮想マシンをキャプチャする方法](../virtual-machines-capture-image-windows-server.md)に関するページを参照してください。

## <a name="What's Next"> </a>次のステップ

これで、サービス管理の基本を学習できました。[Azure Python SDK の完全な API のリファレンス ドキュメント](http://azure-sdk-for-python.readthedocs.org/en/documentation/index.html)にアクセスして、複雑なタスクを簡単に実行することにより、Python アプリケーションを管理できます。



[What is Service Management]: #WhatIs
[Concepts]: #Concepts
[How to: Connect to service management]: #Connect
[How to: List available locations]: #ListAvailableLocations
[How to: Create a cloud service]: #CreateCloudService
[How to: Delete a cloud service]: #DeleteCloudService
[How to: Create a deployment]: #CreateDeployment
[How to: Update a deployment]: #UpdateDeployment
[How to: Move deployments between staging and production]: #MoveDeployments
[How to: Delete a deployment]: #DeleteDeployment
[How to: Create a storage service]: #CreateStorageService
[How to: Delete a storage service]: #DeleteStorageService
[How to: List available operating systems]: #ListOperatingSystems
[How to: Create an operating system image]: #CreateVMImage
[How to: Delete an operating system image]: #DeleteVMImage
[How to: Create a virtual machine]: #CreateVM
[How to: Delete a virtual machine]: #DeleteVM
[Next Steps]: #NextSteps
[management-portal]: https://manage.windowsazure.com/
[svc-mgmt-rest-api]: http://msdn.microsoft.com/library/windowsazure/ee460799.aspx


[download-SDK-Python]: https://www.windowsazure.com/develop/python/common-tasks/install-python/
[クラウド サービス]: http://windowsazure.com/documentation/articles/cloud-services-what-is
[service package]: http://msdn.microsoft.com/library/windowsazure/jj155995.aspx
[Azure PowerShell cmdlets]: https://www.windowsazure.com/develop/php/how-to-guides/powershell-cmdlets/
[cspack commandline tool]: http://msdn.microsoft.com/library/windowsazure/gg432988.aspx
[Deploying an Azure Service]: http://msdn.microsoft.com/library/windowsazure/gg433027.aspx
[ストレージ サービス]: https://www.windowsazure.com/manage/services/storage/what-is-a-storage-account/
[azure-blobs]: https://www.windowsazure.com/develop/python/how-to-guides/blob-service/
[azure-tables]: https://www.windowsazure.com/develop/python/how-to-guides/table-service/
[azure-queues]: https://www.windowsazure.com/develop/python/how-to-guides/queue-service/
[Azure Service Configuration Schema (.cscfg)]: http://msdn.microsoft.com/library/windowsazure/ee758710.aspx
[Cloud Services]: http://msdn.microsoft.com/library/windowsazure/jj155995.aspx
[Virtual Machines]: http://msdn.microsoft.com/library/windowsazure/jj156003.aspx

<!--HONumber=54--> 