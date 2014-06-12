<properties linkid="develop-php-how-to-guides-service-bus-queues" urlDisplayName="サービス バス キュー" pageTitle="サービス バス キューの使用方法 (PHP) - Azure" metaKeywords="Azure サービス バス キュー, Azure キュー, Azure メッセージング, Azure キュー PHP" description="Azure でのサービス バス キューの使用方法について説明します。コード サンプルは PHP で記述されています。" metaCanonical="" services="service-bus" documentationCenter="PHP" title="サービス バス キューの使用方法" authors="" solutions="" manager="" editor="" />

# サービス バス キューの使用方法

このガイドでは、PHP でサービス バス キューを使用する方法について説明します。サンプルは 
PHP で記述され、[Azure SDK for PHP][download-sdk] を利用しています。紹介するシナリオは、
**キューの作成**、**メッセージの送受信**、および
**キューの削除**です。

## 目次

-   [サービス バス キューとは](#what-are-service-bus-queues)
-   [サービス名前空間の作成](#create-a-service-namespace)
-   [名前空間の既定の管理資格情報の取得](#obtain-default-credentials)
- 	[PHP アプリケーションの作成](#CreateApplication)
-	[Azure クライアント ライブラリの入手](#GetClientLibrary)
-   [サービス バスを使用するようにアプリケーションを構成する](#ConfigureApp)
-   [方法: キューを作成する](#CreateQueue)
-   [方法: キューにメッセージを送信する](#SendMessages)
-   [方法: キューからメッセージを受信する](#ReceiveMessages)
-   [方法: アプリケーションのクラッシュと読み取り不能のメッセージを処理する](#HandleCrashes)
-   [次のステップ](#NextSteps)

[WACOM.INCLUDE [howto-service-bus-queues](../includes/howto-service-bus-queues.md)]

##<a id="CreateApplication"></a>PHP アプリケーションの作成

Azure BLOB サービスにアクセスする PHP アプリケーションを作成するための要件は、コード内から [Azure SDK for PHP][download-sdk] のクラスを参照することのみです。アプリケーションの作成には、メモ帳などの任意の開発ツールを使用できます。

> [WACOM.NOTE]
> PHP のインストールでは、<a href="http://php.net/openssl">OpenSSL 拡張機能</a>をインストールして有効にしておく必要もあります。

このガイドで使用するサービス機能は、PHP アプリケーション内でローカルで呼び出すことも、Azure の Web ロール、worker ロール、または Web サイト上で実行されるコード内で呼び出すこともできます。

##<a id="GetClientLibrary"></a>Azure クライアント ライブラリの入手

[WACOM.INCLUDE [get-client-libraries](../includes/get-client-libraries.md)]

##<a id="ConfigureApp"></a>サービス バスを使用するようにアプリケーションを構成する

Azure のサービス バス キュー API を使用するには、次の要件があります。

1. [require_once][require_once] ステートメントを使用してオートローダー ファイルを参照する
2.  使用する可能性のあるクラスを参照する

次の例では、オートローダー ファイルをインクルードし、**ServicesBuilder** クラスを参照する方法を示しています。

> [WACOM.NOTE] 
> この例 (およびこの記事のその他の例) では、Composer を使用して Azure 向け PHP クライアント ライブラリがインストールされていることを前提としています。ライブラリを手動でまたは PEAR パッケージとしてインストールした場合は、<code>WindowsAzure.php</code> オートローダー ファイルを参照する必要があります。

	require_once 'vendor\autoload.php';
	use WindowsAzure\Common\ServicesBuilder;


この後のコード例では、`require_once` ステートメントが常に記述されていますが、コード例の実行に必要なクラスのみ参照されます。

##<a id="ConnectionString"></a>Azure のサービス バス接続の設定

Azure のサービス バス クライアントをインスタンス化するには、第一に、次の形式の有効な接続文字列が必要です。

	Endpoint=[yourEndpoint];SharedSecretIssuer=[Default Issuer];SharedSecretValue=[Default Key]

ここで Endpoint の一般的な形式は `https://[yourNamespace].servicebus.windows.net` です。

いずれの Azure サービス クライアントを作成するにも、**ServicesBuilder** クラスを使用する必要があります。そのための方法は次のとおりです。

* 接続文字列を直接渡す
* **CloudConfigurationManager (CCM)** を使用して複数の外部ソースに対して接続文字列を確認する
	* 既定では 1 つの外部ソース (環境変数) のみサポートされています。
	* **ConnectionStringSource** クラスを継承して新しいソースを追加できます。

ここで概説している例では、接続文字列を直接渡します。

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;

	$connectionString = "Endpoint=[yourEndpoint];SharedSecretIssuer=[Default Issuer];SharedSecretValue=[Default Key]";

	$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);


##<a id="CreateQueue"></a>方法: キューを作成する

サービス バス キューの管理処理は **ServiceBusRestProxy** クラスを
使用して実行できます。**ServiceBusRestProxy** オブジェクトを
作成するには、**ServicesBuilder::createServiceBusService** ファクトリ メソッドに、管理処理用のトークン アクセス許可をカプセル化した適切な接続文字列を渡します。

次の例では、**ServiceBusRestProxy** をインスタンス化し、**ServiceBusRestProxy->createQueue** を呼び出して、`MySBNamespace` サービス名前空間内で `myqueue` という名前のキューを作成する方法を示しています。

    require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\Common\ServiceException;
	use WindowsAzure\ServiceBus\Models\QueueInfo;

	// Create Service Bus REST proxy.
		$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);
	
	try	{
		$queueInfo = new QueueInfo("myqueue");
		
		// Create queue.
		$serviceBusRestProxy->createQueue($queueInfo);
	}
	catch(ServiceException $e){
		// Handle exception based on error codes and messages.
		// Error codes and messages are here: 
		// http://msdn.microsoft.com/ja-jp/library/windowsazure/dd179357
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}

> [WACOM.NOTE]
> <b>ServiceBusRestProxy</b> オブジェクトの <b>listQueues</b> メソッドを使用すると、指定した名前のキューがサービス名前空間に既に存在するかどうかを確認できます。

##<a id="SendMessages"></a>方法: キューにメッセージを送信する

メッセージをサービス バス キューに送信するには、アプリケーションで **ServiceBusRestProxy->sendQueueMessage** メソッドを呼び出します。次のコードでは、前のコードで `MySBNamespace` サービス名前空間内で作成した `myqueue` キューにメッセージを送信する方法を示しています。

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\Common\ServiceException;
	use WindowsAzure\ServiceBus\models\BrokeredMessage;

	// Create Service Bus REST proxy.
	$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);
		
	try	{
		// Create message.
		$message = new BrokeredMessage();
		$message->setBody("my message");
	
		// Send message.
		$serviceBusRestProxy->sendQueueMessage("myqueue", $message);
	}
	catch(ServiceException $e){
		// Handle exception based on error codes and messages.
		// Error codes and messages are here: 
		// http://msdn.microsoft.com/ja-jp/library/windowsazure/hh780775
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}

サービス バス キューに送信されたメッセージ (およびサービス バス キューから受信したメッセージ) は、
**BrokeredMessage** クラスのインスタンスになります。**BrokeredMessage** オブジェクトには、
一連の標準的なメソッド (**getLabel**、**getTimeToLive**、
**setLabel**、**setTimeToLive** など) と、アプリケーションに固有の
カスタム プロパティの保持に使用するプロパティのセットが用意されており、
任意のアプリケーション データの本体が格納されます。

サービス バス キューでは、最大 256 KB までのメッセージをサポートして
います (標準とカスタムのアプリケーション プロパティが含まれるヘッダー
の最大サイズは 64 KB です)。キューで保持されるメッセージ数には上限が
ありませんが、キュー 1 つあたりが保持できるメッセージの合計サイズには
上限があります。このキュー サイズの上限は 5 GB です。

##<a id="ReceiveMessages"></a>方法: キューからメッセージを受信する

キューからメッセージを受信する主な方法は、**ServiceBusRestProxy->receiveQueueMessage** メソッドを使用することです。メッセージは 2 つの異なるモード (**ReceiveAndDelete** (既定) と **PeekLock**) で受信できます。

**ReceiveAndDelete** モードを使用する場合、受信が 1 回ずつの動作になります。つまり、サービス バスはキュー内のメッセージに対する読み取り要求を受け取ると、メッセージを読み取り中としてマークし、アプリケーションに返します。**ReceiveAndDelete** モードは最もシンプルなモデルであり、問題が発生した際に
アプリケーション側でメッセージを処理しないことを許容できるシナリオに最適です。このことを理解するために、コンシューマーが受信要求を発行した後で、メッセージを処理する前にクラッシュしたというシナリオを考えてみましょう。サービス バスはメッセージを読み取り済みとしてマークするため、アプリケーションが再起動してメッセージの読み取りを再開すると、クラッシュ前に読み取られていたメッセージは見落とされることになります。

**PeekLock** モードでは、メッセージの受信処理が 2 段階の動作になり、メッセージが失われることが許容できないアプリケーションに対応することができます。サービス バス は要求を受け取ると、次に読み取られるメッセージを検索して、他のコンシューマーが受信できないようロックしてから、アプリケーションにメッセージを返します。アプリケーションがメッセージの処理を終えた後 (または後で処理するために確実に保存した後)、受信したメッセージを **ServiceBusRestProxy->deleteMessage** に渡して受信処理の第 2 段階を完了します。サービス バスが **deleteMessage** の呼び出しを確認すると、メッセージが読み取り中としてマークされ、キューから削除されます。

次の例では、**PeekLock** モード (既定ではないモード) を使用したメッセージの受信および処理の方法を示しています。

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\Common\ServiceException;
	use WindowsAzure\ServiceBus\models\ReceiveMessageOptions;

	// Create Service Bus REST proxy.
	$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);
		
	try	{
		// Set the receive mode to PeekLock (default is ReceiveAndDelete).
		$options = new ReceiveMessageOptions();
		$options->setPeekLock();
		
		// Receive message.
		$message = $serviceBusRestProxy->receiveQueueMessage("myqueue", $options);
		echo "Body: ".$message->getBody()."<br />";
		echo "MessageID: ".$message->getMessageId()."<br />";
		
		/*---------------------------
			Process message here.
		----------------------------*/
		
		// Delete message. Not necessary if peek lock is not set.
		echo "Message deleted.<br />";
		$serviceBusRestProxy->deleteMessage($message);
	}
	catch(ServiceException $e){
		// Handle exception based on error codes and messages.
		// Error codes and messages are here:
		// http://msdn.microsoft.com/ja-jp/library/windowsazure/hh780735
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}

##<a id="HandleCrashes"></a>方法: アプリケーションのクラッシュと読み取り不能のメッセージを処理する

サービス バスには、アプリケーションにエラーが発生した場合や、メッセージの
処理に問題がある場合に復旧を支援する機能が備わっています。受信側の
アプリケーションが何らかの理由によってメッセージを処理できない場合には、
受信したメッセージについて (**deleteMessage** メソッドの代わりに) 
**unlockMessage** メソッドを呼び出すことができます。このメソッドが呼び出されると、 サービス バス によって
キュー内のメッセージのロックが解除され、メッセージが再度受信できる状態に
変わります。メッセージを受信するアプリケーションは、以前と同じものでも、
別のものでもかまいません。

キュー内でロックされているメッセージにはタイムアウトも設定されています。
アプリケーションがクラッシュした場合など、ロックがタイムアウトに
なる前にアプリケーションがメッセージの処理に失敗した場合には、
メッセージのロックが自動的に解除され、再度受信できる状態に
変わります。

メッセージが処理された後、**deleteMessage** 要求が発行される前に
アプリケーションがクラッシュした場合は、アプリケーションが再起動する際に
メッセージが再配信されます。一般的に、この動作は 
**"1 回以上の処理"** と呼ばれます。つまり、すべてのメッセージが 1 回以上
処理されますが、特定の状況では、同じメッセージが再配信される
可能性があります。重複処理が許されないシナリオの場合、重複メッセージの
配信を扱うロジックをアプリケーションに追加することをお勧めします。通常、この問題は
メッセージの **getMessageId** メソッドを使用して対処します。このプロパティは
配信が試行された後も同じ値を保持します。

##<a id="NextSteps"></a>次のステップ

これで、サービス バス キューの基本を学習できました。詳細については、MSDN のトピック「[サービス バス キュー、トピックおよびサブスクリプション][]」を参照してください。

[download-sdk (SDK のダウンロード)]: http://go.microsoft.com/fwlink/?LinkId=252473
[サービス バス キューの図]: ../../../DevCenter/Java/Media/SvcBusQueues_01_FlowDiagram.jpg
[Azure 管理ポータル]: http://manage.windowsazure.com/
[[サービス バス] ノードのスクリーンショット]: ../../../DevCenter/Java/Media/SvcBusQueues_02_SvcBusNode.jpg
[[新しい名前空間の作成] のスクリーンショット]: ../../../DevCenter/Java/Media/SvcBusQueues_03_CreateNewSvcNamespace.jpg
[利用可能な名前空間のスクリーンショット]: ../../../DevCenter/Java/Media/SvcBusQueues_04_SvcBusNode_AvailNamespaces.jpg
[名前空間の一覧のスクリーンショット]: ../../../DevCenter/Java/Media/SvcBusQueues_05_NamespaceList.jpg
[[プロパティ] ウィンドウのスクリーンショット]: ../../../DevCenter/Java/Media/SvcBusQueues_06_PropertiesPane.jpg
[[既定のキー] のスクリーンショット]: ../../../DevCenter/Java/Media/SvcBusQueues_07_DefaultKey.jpg
[サービス バス キュー、トピック、およびサブスクリプション]: http://msdn.microsoft.com/ja-jp/library/windowsazure/hh367516.aspx
[require_once]: http://php.net/require_once

