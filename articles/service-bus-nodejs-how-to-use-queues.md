<properties linkid="dev-nodejs-how-to-service-bus-queues" urlDisplayName="サービス バス キュー" pageTitle="サービス バス キューの使用方法 (Node.js) - Azure" metaKeywords="Azure サービス バス キュー, Azure キュー, Azure メッセージング, Azure キュー Node.js" description="Azure でのサービス バス キューの使用方法を学習します。コード サンプルは Node.js で記述されています。" metaCanonical="" services="service-bus" documentationCenter="Node.js" title="サービス バス キューの使用方法" authors="" solutions="" manager="" editor="" />





# サービス バス キューの使用方法

このガイドでは、サービス バス キューの使用方法について説明します。サンプルは JavaScript で記述され、
Node.js Azure モジュールを利用しています。紹介するシナリオは、
**キューの作成、メッセージの送受信**、および
**キューの削除**です。キューの詳細については、「[次のステップ]」の
セクションを参照してください。

## 目次

-   [サービス バス キューとは][]
-   [サービス名前空間の作成][]
-   [名前空間の既定の管理資格情報の取得][]
-   [Node.js アプリケーションの作成](#create-app)
-   [サービス バスを使用するようにアプリケーションを構成する](#configure-app)
-   [How to: キューを作成する](#create-queue)
-   [How to: メッセージをキューに送信する](#send-messages)
-   [How to: キューからメッセージを受信する](#receive-messages)
-   [How to: アプリケーションのクラッシュと読み取り不能のメッセージを処理する](#handle-crashes)
-   [次のステップ](#next-steps)

[WACOM.INCLUDE [howto-service-bus-queues](../includes/howto-service-bus-queues.md)]

## <a name="create-app"> </a>Node.js アプリケーションの作成

空の Node.js アプリケーションを作成します。Node.js アプリケーションを作成する手順については、[Node.js アプリケーションの作成と Azure Web サイトへのデプロイ]、[Node.js クラウド サービスへのデプロイ] (Windows PowerShell を使用)、または [WebMatrix による Web サイトの作成とデプロイ]に関するページを参照してください。

## <a name="configure-app"> </a>サービス バスを使用するようにアプリケーションを構成する

Azure のサービス バスを使用するには、Node.js azure パッケージを
ダウンロードして使用する必要があります。このパッケージには、サービス バス REST サービスと通信
するための便利なライブラリのセットが含まれています。

### ノード パッケージ マネージャー (NPM) を使用してパッケージを取得する

1.  **Windows PowerShell for Node.js** コマンド ウィンドウを使用して、
    サンプル アプリケーションを作成した **c:\\node\\sbqueues\\WebRole1**
    フォルダーに移動します。

2.  コマンド ウィンドウに「**npm install azure**」と入力すると、
    次のような出力が生成されます。

        azure@0.7.5 node_modules\azure
		d ateformat@1.0.2-1.2.3
		 xmlbuilder@0.4.2
		 node-uuid@1.2.0
		 mime@1.2.9
		 underscore@1.4.4
		 validator@1.1.1
		 tunnel@0.0.2
		 wns@0.5.3
		 xml2js@0.2.7 (sax@0.5.2)
		 request@2.21.0 (json-stringify-safe@4.0.0, forever-agent@0.5.0, aws-sign@0.3.0, tunnel-agent@0.3.0, oauth-sign@0.3.0, qs@0.6.5, cookie-jar@0.3.0, node-uuid@1.4.0, http-signature@0.9.11, form-data@0.0.8, hawk@0.13.1)

3.  手動で **ls** コマンドを実行して、**node\_modules** 
       フォルダーが作成されたことを確認することもできます。このフォルダーで **azure** 
    パッケージを検索します。このパッケージには、サービス バス キューにアクセス
    するために必要なライブラリが含まれています。

### モジュールのインポート

メモ帳などのテキスト エディターを使用して、アプリケーションの 
**server.js** ファイルの先頭に次の内容を追加します。

    var azure = require('azure');

### Azure のサービス バス接続の設定

azure モジュールは、Azure サービス バスに接続するために必要な情報として、環境変数 AZURE\_SERVICEBUS\_NAMESPACE および AZURE\_SERVICEBUS\_ACCESS\_KEY を読み取ります。これらの環境変数が設定されていない場合、**createServiceBusService** を呼び出すときにアカウント
情報を指定する必要があります。

Azure クラウド サービスの構成ファイルで環境変数を設定する例については、[ストレージを使用する Node.js クラウド サービスに関するトピック]を参照してください。

Azure Web サイトの管理ポータルで環境変数を設定する例については、[ストレージを使用する Node.js Web アプリケーションに関するトピック]を参照してください。

## <a name="create-queue"> </a>キューの作成方法

**ServiceBusService** オブジェクトを使用して、キューを操作できます。次の
コードでは、**ServiceBusService** オブジェクトを作成します。**server.js** ファイルの
先頭付近の、azure モジュールをインポートするステートメントの後に、このコードを追加します。

    var serviceBusService = azure.createServiceBusService();

**ServiceBusService** オブジェクトで **createQueueIfNotExists** を
呼び出すことによって、指定されたキューが返されるか (存在する場合)、
指定された名前で新しいキューが作成されます。次のコードでは、
**createQueueIfNotExists** を使用して、myqueue という名前のキューを
作成、またはキューに接続します。

    serviceBusService.createQueueIfNotExists('myqueue', function(error){
        if(!error){
            // Queue exists
        }
    });

**createServiceBusService** は追加のオプションもサポートしています。
これにより、メッセージの有効期間や最大キュー サイズなどの既定のキューの
設定をオーバーライドできます。次の例では、キューの最大サイズを 5 GB に、
メッセージの既定の有効期間を 1 分に設定する方法を示しています。

    var queueOptions = {
          MaxSizeInMegabytes: '5120',
          DefaultMessageTimeToLive: 'PT1M'
        };

    serviceBusService.createQueueIfNotExists('myqueue', queueOptions, function(error){
        if(!error){
            // Queue exists
        }
    });

###フィルター

オプションのフィルター操作は、**ServiceBusService** を使用して行われる操作に適用できます。フィルター操作には、ログ、自動的な再試行などが含まれる場合があります。フィルターは、次のようなシグネチャを実装するオブジェクトです。

		function handle (requestOptions, next)

要求オプションに対するプリプロセスを行った後で、このメソッドは "next" を呼び出して、次のシグネチャのコールバックを渡す必要があります。

		function (returnObject, finalCallback, next)

このコールバックで、returnObject (サーバーへの要求からの応答) の処理の後に、コールバックは next を呼び出すか (他のフィルターの処理を続けるために next が存在する場合)、単に finalCallback を呼び出す必要があります (サービス呼び出しを終了する場合)。

再試行のロジックを実装する 2 つのフィルター (**ExponentialRetryPolicyFilter** と **LinearRetryPolicyFilter**) が、Azure SDK for Node.js に含まれています。 次のコードは、**ExponentialRetryPolicyFilter** を使う **ServiceBusService** オブジェクトを作成します。

	var retryOperations = new azure.ExponentialRetryPolicyFilter();
	var serviceBusService = azure.createServiceBusService().withFilter(retryOperations);

## <a name="send-messages"> </a>メッセージをキューに送信する方法

メッセージをサービス バス キューに送信するには、アプリケーションで 
**ServiceBusService** オブジェクトの **sendQueueMessage** メソッドを呼び出します。
サービス バス キューに送信された (およびキューから受信された) メッセージは 
**BrokeredMessage** オブジェクトであり、このオブジェクトには、
(**Label** や **TimeToLive** などの) 標準的なプロパティ、
アプリケーションに特有のカスタム プロパティの保持に使用するディクショナリ、
および任意のアプリケーション データの本体が備わっています。アプリケーションでは、メッセージとして文字列値を
渡すことによってメッセージの本文を設定でき、必須の標準プロパティは
既定値に設定されます。

次の例では、**sendQueueMessage** を使用して、myqueue という名前の
キューにテスト メッセージを送信する方法を示しています。

    var message = {
        body: 'Test message',
        customProperties: {
            testproperty: 'TestValue'
        };
    serviceBusService.sendQueueMessage('myqueue', message, function(error){
        if(!error){
            // message sent
        }
    });

サービス バス キューでは、最大 256 KB までのメッセージをサポートして
います (標準とカスタムのアプリケーション プロパティが含まれるヘッダー
の最大サイズは 64 KB です)。キューで保持されるメッセージ数には上限が
ありませんが、キュー 1 つあたりが保持できるメッセージの合計サイズには
上限があります。このキューのサイズはキューの作成時に定義します。
上限は 5 GB です。

## <a name="receive-messages"> </a>キューからメッセージを受信する方法

キューからメッセージを受信するには、**ServiceBusService** オブジェクトの 
**receiveQueueMessage** メソッドを使用します。既定では、メッセージは読み取られるときに
キューから削除されますが、省略可能な **isPeekLock** パラメーターを 
**true** に設定することによって、キューからメッセージを削除せずに、
メッセージを読み取って (ピークして) ロックすることができます。

受信操作の中で行われるメッセージの読み取りと削除の既定の動作は、
最もシンプルなモデルであり、障害発生時にアプリケーション側でメッセージを
処理しないことを許容できるシナリオに
最適です。このことを理解するために、コンシューマーが受信要求を
発行した後で、メッセージを処理する前にクラッシュしたというシナリオを
考えてみましょう。サービス バスはメッセージを読み取り済みとしてマークするため、アプリケーションが
再起動してメッセージの読み取りを再開すると、クラッシュ前に読み取られていた
メッセージは見落とされることになります。

**isPeekLock** パラメーターが **true** に設定されている場合、受信処理が
2 段階の動作になり、メッセージが失われることが許容できないアプリケーションに
対応することができます。サービス バスは要求を受け取ると、
次に読み取られるメッセージを検索して、他のコンシューマーが受信できないよう
ロックしてから、アプリケーションにメッセージを返します。
アプリケーションはメッセージの処理を終えた後 (または後で処理するために
確実に保存した後)、**deleteMessage** メソッドを呼び出し、削除する
メッセージをパラメーターとして指定して、受信処理の第 2 段階を完了
します。**deleteMessage** メソッドによって、
メッセージが読み取り中としてマークされ、キューから削除されます。

次の例では、**receiveQueueMessage** を使用したメッセージの
受信および処理の方法を示しています。この例では、最初にメッセージを受信して
削除し、次に true に設定した **isPeekLock** を使用してメッセージを受信した後、
**deleteMessage** を使用してメッセージを削除します。

    serviceBusService.receiveQueueMessage('taskqueue', function(error, receivedMessage){
        if(!error){
            // Message received and deleted
        }
    });
    serviceBusService.receiveQueueMessage(queueName, { isPeekLock: true }, function(error, lockedMessage){
        if(!error){
            // Message received and locked
            serviceBusService.deleteMessage(lockedMessage, function (deleteError){
                if(!deleteError){
                    // Message deleted
                }
            }
        }
    });

## <a name="handle-crashes"> </a>アプリケーションのクラッシュと読み取り不能のメッセージを処理する方法

サービス バスには、アプリケーションにエラーが発生した場合や、メッセージの
処理に問題がある場合に復旧を支援する機能が備わっています。受信側の
アプリケーションが何らかの理由によってメッセージを処理できない場合には、
**ServiceBusService** オブジェクトの **unlockMessage** メソッドを
呼び出すことができます。このメソッドが呼び出されると、サービス バスによって
キュー内のメッセージのロックが解除され、メッセージが再度受信できる状態に
変わります。メッセージを受信するアプリケーションは、以前と同じものでも、
別のものでもかまいません。

キュー内でロックされているメッセージにはタイムアウトも設定されています。
アプリケーションがクラッシュした場合など、ロックがタイムアウトに
なる前にアプリケーションがメッセージの処理に失敗した場合には、
メッセージのロックが自動的に解除され、再度受信できる状態に
変わります。

メッセージが処理された後、**deleteMessage** メソッドが呼び出される前に
アプリケーションがクラッシュした場合は、アプリケーションが再起動する際に
メッセージが再配信されます。一般的に、
この動作は **"1 回以上の処理"** と呼ばれます。つまり、すべての
メッセージが 1 回以上処理されますが、特定の状況では、同じメッセージが
再配信される可能性があります。重複処理が許されないシナリオの場合、重複メッセージの配信を
扱うロジックをアプリケーションに追加する必要があります。通常、この問題はメッセージの 
**MessageId** プロパティを使用して対処します。このプロパティは配信が
試行された後も同じ値を保持します。

## <a name="next-steps"> </a>次のステップ

これで、サービス バス キューの基本を学習できました。さらに詳細な情報が
必要な場合は、次のリンク先を参照してください。

-   MSDN リファレンス: [キュー、トピック、およびサブスクリプション][]
-   GitHub の [Azure SDK for Node] リポジトリ。

  [Azure ノードの追加]: https://github.com/WindowsAzure/azure-sdk-for-node
  [次のステップ]: #next-steps
  [サービス バス キューとは]: #what-are-service-bus-queues
  [サービス名前空間の作成]: #create-a-service-namespace
  [名前空間の既定の管理資格情報の取得]: #obtain-default-credentials
  [Node.js アプリケーションの作成]: #create-app
  [サービス バスを使用するようにアプリケーションを構成する]: #configure-app
  [How to: キューを作成する]: #create-queue
  [How to: メッセージをキューに送信する]: #send-messages
  [How to: キューからメッセージを受信する]: #receive-messages
  [How to: アプリケーションのクラッシュと読み取り不能のメッセージを処理する]: #handle-crashes
  [キューの概念]: ../../dotNet/Media/sb-queues-08.png
  [Azure 管理ポータル]: http://manage.windowsazure.com
  
  
  
  
  
  [Node.js クラウド サービスへのデプロイ]: /ja-jp/documentation/articles/cloud-services-nodejs-develop-deploy-app/
  [キュー、トピック、およびサブスクリプション]: http://msdn.microsoft.com/ja-jp/library/windowsazure/hh367516.aspx
  [WebMatrix による Web サイトの作成とデプロイ]: /ja-jp/develop/nodejs/tutorials/web-site-with-webmatrix/
[以前の管理ポータル]: ../../Shared/Media/previous-portal.png
  [Node.js アプリケーションの作成と Azure Web サイトへのデプロイ]: /ja-jp/develop/nodejs/tutorials/create-a-website-(mac)/
  [ストレージを使用する Node.js クラウド サービスに関するトピック]: /ja-jp/develop/nodejs/tutorials/web-app-with-storage/
  [ストレージを使用する Node.js Web アプリケーションに関するトピック]: /ja-jp/develop/nodejs/tutorials/web-site-with-storage/
