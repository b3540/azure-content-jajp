<properties linkid="dev-ruby-how-to-service-bus-queues" urlDisplayName="サービス バス キュー" pageTitle="サービス バス キューの使用方法 (Ruby) - Azure" metaKeywords="Azure サービス バス キュー, Azure キュー, Azure メッセージング, Azure キュー Ruby" description="Azure でのサービス バス キューの使用方法を学習します。コード サンプルは Ruby で記述されています。" metaCanonical="" services="service-bus" documentationCenter="Ruby" title="サービス バス キューの使用方法" authors="guayan" solutions="" manager="" editor="" />




# サービス バス キューの使用方法

このガイドでは、サービス バス キューの使用方法について説明します。サンプルは
Ruby で記述され、Azure gem を利用しています。紹介するシナリオは、
**キューの作成、メッセージの送受信**、および
**キューの削除**です。キューの詳細については、「[次のステップ](#next-steps)」のセクションを参照してください。

## 目次

* [サービス バス キューとは](#what-are-service-bus-queues)
* [サービス名前空間の作成](#create-a-service-namespace)
* [名前空間の既定の管理資格情報の取得](#obtain-default-credentials)
* [Ruby アプリケーションの作成](#create-a-ruby-application)
* [サービス バスを使用するようにアプリケーションを構成する](#configure-your-application-to-use-service-bus)
* [Azure のサービス バス接続の設定](#setup-a-windows-azure-service-bus-connection)
* [キューの作成方法](#how-to-create-a-queue)
* [メッセージをキューに送信する方法](#how-to-send-messages-to-a-queue)
* [キューからメッセージを受信する方法](#how-to-receive-messages-from-a-queue)
* [アプリケーションのクラッシュと読み取り不能のメッセージを処理する方法](#how-to-handle-application-crashes-and-unreadable-messages)
* [次のステップ](#next-steps)

[WACOM.INCLUDE [howto-service-bus-queues](../includes/howto-service-bus-queues.md)]

## <a id="create-a-ruby-application"></a>Ruby アプリケーションの作成

Ruby アプリケーションを作成します。手順については、[Azure での Ruby アプリケーションの作成に関するページ](/ja-jp/develop/ruby/tutorials/web-app-with-linux-vm/)を参照してください。

## <a id="configure-your-application-to-use-service-bus"></a> サービス バス を使用するようにアプリケーションを構成する

Azure サービス バスを使用するには、Ruby azure パッケージをダウンロードして使用する必要があります。このパッケージには、ストレージ REST サービスと通信するための便利なライブラリのセットが含まれています。

### RubyGems を使用してパッケージを取得する

1. **PowerShell** (Windows)、**ターミナル** (Mac)、**Bash** (Unix) などのコマンド ライン インターフェイスを使用します。

2. コマンド ウィンドウに「gem install azure」と入力して、gem と依存関係をインストールします。

### パッケージをインポートする

任意のテキスト エディターを使用して、ストレージを使用する Ruby ファイルの先頭に次のコードを追加します。

    require "azure"

## <a id="setup-a-windows-azure-service-bus-connection"></a>Azure のサービス バス接続の設定

azure モジュールは、Azure サービス バス に接続するために必要な情報として、環境変数 **AZURE\_SERVICEBUS\_NAMESPACE** および **AZURE\_SERVICEBUS\_ACCESS_KEY** を読み取ります。これらの環境変数が設定されていない場合は、**Azure::ServiceBusService** を使用する前に、次のコードを使用して名前空間の情報を指定する必要があります。

    Azure.config.sb_namespace = "<your azure service bus namespace>"
    Azure.config.sb_access_key = "<your azure service bus access key>"

## <a id="how-to-create-a-queue"></a>方法: キューを作成する

**Azure::ServiceBusService** オブジェクトを使用して、キューを操作できます。キューを作成するには、**create_queue()** メソッドを使用します。次の例では、キューを作成し、既に存在している場合はエラーを出力します。

    azure_service_bus_service = Azure::ServiceBusService.new
    begin
      queue = azure_service_bus_service.create_queue("test-queue")
    rescue
      puts $!
    end

追加のオプションを **Azure::ServiceBus::Queue** オブジェクトに渡すこともできます。これにより、メッセージの有効期間やキューの最大サイズなどの既定のキューの設定をオーバーライドできます。次の例は、キューの最大サイズを 5 GB に、有効期間を 1 分に設定する方法を示しています。

    queue = Azure::ServiceBus::Queue.new("test-queue")
    queue.max_size_in_megabytes = 5120
    queue.default_message_time_to_live = "PT1M"

    queue = azure_service_bus_service.create_queue(queue)

## <a id="how-to-send-messages-to-a-queue"></a>メッセージをキューに送信する方法

メッセージを サービス バス キューに送信するために、アプリケーションで **Azure::ServiceBusService** オブジェクトの **send\_queue\_message()** メソッドを呼び出します。サービス バス キューに送信された (およびサービス バス キューから受信された) メッセージは **Azure::ServiceBus::BrokeredMessage** オブジェクトであり、このオブジェクトには、標準的なプロパティ (**label**、**time\_to\_live** など)、アプリケーションに特有のカスタム プロパティの保持に使用するディクショナリ、および任意のアプリケーション データの本体が備わっています。アプリケーションでは、メッセージとして文字列値を渡すことによってメッセージの本文を設定でき、必須の標準プロパティは既定値に設定されます。

次の例では、**send\_queue\_message()** を使用して、"test-queue" というキューにテスト メッセージを送信する方法を示しています。

    message = Azure::ServiceBus::BrokeredMessage.new("test queue message")
    message.correlation_id = "test-correlation-id"
    azure_service_bus_service.send_queue_message("test-queue", message)

サービス バス キューでは、最大 256 KB までのメッセージをサポートしています (標準とカスタムのアプリケーション プロパティが含まれるヘッダーの最大サイズは 64 KB です)。キューで保持されるメッセージ数には上限がありませんが、キュー 1 つあたりが保持できるメッセージの合計サイズには上限があります。このキュー サイズは作成時に定義され、上限は 5 GB です。

## <a id="how-to-receive-messages-from-a-queue"></a>キューからメッセージを受信する方法

メッセージは、**Azure::ServiceBusService** オブジェクトの **receive\_queue\_message()** メソッドを使用してキューからを受信します。既定では、メッセージは読み取られて (ピークされて) ロックされますが、キューからは削除されません。ただし、**:peek_lock** オプションを **false** に設定すると、読み取ったメッセージをキューから削除できます。

既定の動作では、読み取りと削除が 2 段階の操作になるため、メッセージが失われることを許容できないアプリケーションにも対応することができます。Service Bus は要求を受け取ると、次に読み取られるメッセージを検索して、他のコンシューマーが受信できないようロックしてから、アプリケーションにメッセージを返します。アプリケーションがメッセージの処理を終えた後 (または後で処理するために確実に保存した後)、**delete\_queue\_message()** メソッドを呼び出し、削除するメッセージをパラメーターとして指定して、受信処理の第 2 段階を完了します。**delete\_queue\_message()** メソッドによって、メッセージが読み取り中としてマークされ、キューから削除されます。

**:peek\_lock** パラメーターを **false** に設定すると、メッセージの読み取りと削除は最もシンプルなモデルになります。これは、問題の発生時にアプリケーション側でメッセージを処理しないことを許容できるシナリオに最適です。このことを理解するために、コンシューマーが受信要求を発行した後で、メッセージを処理する前にクラッシュしたというシナリオを考えてみましょう。サービス バスはメッセージを読み取り済みとしてマークするため、アプリケーションが再起動してメッセージの読み取りを再開すると、クラッシュ前に読み取られていたメッセージは見落とされることになります。

次の例では、**receive\_queue\_message()** を使用したメッセージの受信および処理の方法を示しています。この例では、まず **:peek\_lock** を **false** に設定し、メッセージを受信して削除します。次に、別のメッセージを受信してから、**delete\_queue\_message()** を使用してメッセージを削除します。

    message = azure_service_bus_service.receive_queue_message("test-queue", 
	  { :peek_lock => false })
    message = azure_service_bus_service.receive_queue_message("test-queue")
    azure_service_bus_service.delete_queue_message("test-queue",
	  message.sequence_number, message.lock_token)

## <a id="how-to-handle-application-crashes-and-unreadable-messages"></a>アプリケーションのクラッシュと読み取り不能のメッセージを処理する方法

サービス バスには、アプリケーションにエラーが発生した場合や、メッセージの処理に問題がある場合に復旧を支援する機能が備わっています。受信側のアプリケーションが何らかの理由によってメッセージを処理できない場合には、**Azure::ServiceBusService** オブジェクトの **unlock\_queue\_message()** メソッドを呼び出すことができます。このメソッドが呼び出されると、サービス バスによってキュー内のメッセージのロックが解除され、メッセージが再度受信できる状態に変わります。メッセージを受信するアプリケーションは、以前と同じものでも、別のものでもかまいません。

キュー内でロックされているメッセージには、タイムアウトも設定されています。アプリケーションがクラッシュした場合など、ロックがタイムアウトになる前にアプリケーションがメッセージの処理に失敗した場合は、Service Bus によってメッセージのロックが自動的に解除され、再度受信できる状態に変わります。

メッセージが処理された後、**delete\_queue\_message()** メソッドが呼び出される前にアプリケーションがクラッシュした場合は、アプリケーションが再起動する際にメッセージが再配信されます。一般的に、この動作は **"1 回以上の処理"** と呼ばれます。つまり、すべてのメッセージが 1 回以上処理されますが、特定の状況では、同じメッセージが再配信される可能性があります。重複処理が許されないシナリオの場合、重複メッセージの配信を扱うロジックをアプリケーションに追加する必要があります。通常、この問題はメッセージの **message\_id** プロパティを使用して対処します。このプロパティは配信が試行された後も同じ値を保持します。

## <a id="next-steps"></a>次のステップ

これで、サービス バスキューの基本を学習できました。さらに詳細な情報が必要な場合は、次のリンク先を参照してください。

-   MSDN リファレンスの「[サービス バス キュー、トピック、およびサブスクリプション](http://msdn.microsoft.com/ja-jp/library/windowsazure/hh367516.aspx)」を参照
-   GitHub の [Azure SDK for Ruby](https://github.com/WindowsAzure/azure-sdk-for-ruby) リポジトリを参照

この記事で説明されている Azure サービス バス キューと、「[サービス バス キューの使用方法](/ja-jp/develop/ruby/how-to-guides/queue-service/)」で説明されている Azure サービス バス キューの比較については、「[Azure Queues and Azure Service Bus Queues - Compared and Contrasted (Azure キューと Azure サービス バス キューの比較)](http://msdn.microsoft.com/ja-jp/library/windowsazure/hh767287.aspx)」を参照してください。
