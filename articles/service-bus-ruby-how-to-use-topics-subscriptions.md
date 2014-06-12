<properties linkid="dev-ruby-how-to-service-bus-topics" urlDisplayName="サービス バス トピック" pageTitle="サービス バス トピックの使用方法 (Ruby) - Azure" metaKeywords="Azure サービス バス トピックの概要, サービス バス トピックの概要, Azure 発行/サブスクライブ メッセージング, Azure メッセージング トピックおよびサブスクリプション, サービス バス トピック ruby" description="Azure での サービス バス のトピックとサブスクリプションの使用方法について学習します。コード サンプルは Ruby アプリケーション向けに作成されています。" metaCanonical="" services="service-bus" documentationCenter="Ruby" title="サービス バス トピック/サブスクリプションの使用方法" authors="guayan" solutions="" manager="" editor="" />





#  サービス バス トピック/サブスクリプションの使用方法

このガイドでは、Ruby アプリケーションからサービス バス トピックとサブスクリプションを使用する方法について説明します。ここでは、
**トピックとサブスクリプションの作成、サブスクリプション フィルターの作成、トピックへのメッセージの送信**、**サブスクリプションからのメッセージの受信**、**トピックとサブスクリプションの削除**などのシナリオについて説明します。トピックとサブスクリプションの詳細については、「[次の手順](#NextSteps)」を参照してください。

## 目次
* [サービス バス トピックとサブスクリプションとは](#what-are-service-bus-topics)
* [サービス名前空間の作成](#create-a-service-namespace)
* [名前空間の既定の管理資格情報の取得](#obtain-default-credentials)
* [Ruby アプリケーションの作成](#create-a-ruby-application)
* [サービス バスを使用するようにアプリケーションを構成する](#configure-your-application-to-use-service-bus)
* [Azure のサービス バス接続の設定](#setup-a-windows-azure-service-bus-connection)
* [トピックを作成する方法](#how-to-create-a-topic)
* [サブスクリプションの作成方法](#how-to-create-subscriptions)
* [メッセージをトピックに送信する方法](#how-to-send-messages-to-a-topic)
* [サブスクリプションからメッセージを受信する方法](#how-to-receive-messages-from-a-subscription)
* [アプリケーションのクラッシュと読み取り不能のメッセージを処理する方法](#how-to-handle-application-crashes-and-unreadable-messages)
* [方法: トピックとサブスクリプションを削除する](#how-to-delete-topics-and-subscriptions)
* [次のステップ](#NextSteps)

[WACOM.INCLUDE [howto-service-bus-topics](../includes/howto-service-bus-topics.md)]

## <a id="create-a-ruby-application"></a>Ruby アプリケーションを作成する

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

azure モジュールは、Azure サービス バス に接続するために必要な情報として、環境変数 **AZURE\_SERVICEBUS\_NAMESPACE** および **AZURE\_SERVICEBUS\_ACCESS\_KEY** を読み取ります。これらの環境変数が設定されていない場合は、**Azure::ServiceBusService** を使用する前に、次のコードを使用して名前空間の情報を指定する必要があります。

    Azure.config.sb_namespace = "<your azure service bus namespace>"
    Azure.config.sb_access_key = "<your azure service bus access key>"

## <a id="how-to-create-a-topic"></a>トピックの作成方法

**Azure::ServiceBusService** オブジェクトを使用して、トピックを操作できます。次のコードでは、**Azure::ServiceBusService** オブジェクトを作成します。トピックを作成するには、**create\_topic()** メソッドを使用します。次の例では、トピックを作成し、既に存在する場合はエラーを出力します。

	azure_service_bus_service = Azure::ServiceBusService.new
	begin
      topic = azure_service_bus_service.create_queue("test-topic")
    rescue
      puts $!
    end

**Azure::ServiceBus::Topic** オブジェクトに追加のオプションを渡すこともできます。これにより、メッセージの有効期間やキューの最大サイズなどの既定のトピックの設定をオーバーライドできます。次の例は、キューの最大サイズを 5 GB に、有効期間を 1 分に設定する方法を示しています。

	topic = Azure::ServiceBus::Topic.new("test-topic")
    topic.max_size_in_megabytes = 5120
    topic.default_message_time_to_live = "PT1M"

    topic = azure_service_bus_service.create_topic(topic)

## <a id="how-to-create-subscriptions"></a>サブスクリプションの作成方法

トピック サブスクリプションも **Azure::ServiceBusService** オブジェクトで作成します。サブスクリプションを指定し、サブスクリプションの仮想キューに配信するメッセージを制限するフィルターを設定することができます。

**注**: サブスクリプションは永続的であり、サブスクリプション、またはサブスクリプションが関連付けられているトピックが削除されるまで存在し続けます。アプリケーションにサブスクリプションを作成するロジックが含まれている場合は、最初に getSubscription メソッドを使用して、サブスクリプションが既に存在しているかどうかを確認する必要があります。

### 既定の (MatchAll) フィルターを適用したサブスクリプションの作成

**MatchAll** フィルターは、新しいサブスクリプションの作成時にフィルターが指定されていない場合に使用される既定のフィルターです。**MatchAll** フィルターを使用すると、トピックに発行されたすべてのメッセージがサブスクリプションの仮想キューに置かれます。次の例では、"all-messages" という名前のサブスクリプションを作成し、既定の **MatchAll** フィルターを使用します。

	subscription = azure_service_bus_service.create_subscription("test-topic", 
	  "all-messages")

### <a id="how-to-create-subscriptions"></a>フィルターを適用したサブスクリプションの作成

トピックに送信されたメッセージのうち、特定のトピック サブスクリプション内に表示されるメッセージに絞り込めるフィルターを設定することもできます。

サブスクリプションでサポートされているうち最も柔軟性の高いフィルターの種類は、**Azure::ServiceBus::SqlFilter** です。これは、SQL92.  のサブセットです。SQL フィルターは、トピックに対して発行されているメッセージのプロパティを操作します。SQL フィルターで使用できる式の詳細については、[SqlFilter.SqlExpression](http://msdn.microsoft.com/ja-jp/library/windowsazure/microsoft.servicebus.messaging.sqlfilter.sqlexpression.aspx) 構文の説明を参照してください。

フィルターをサブスクリプションに追加するには、**Azure::ServiceBusService** オブジェクトの **create\_rule()** メソッドを使用します。このメソッドによって、新しいフィルターを既存のサブスクリプションに追加できます。

**注**: 既定のフィルターはすべての新しいサブスクリプションに自動的に適用されるので、最初に既定のフィルターを削除する必要があります。削除しない場合、**MatchAll** は指定される他のすべてのフィルターをオーバーライドします。既定のルールを削除するには、**Azure::ServiceBusService** オブジェクトの **delete\_rule()**メソッドを使用します。

次の例では、"high-messages" という名前のサブスクリプションを作成し、**Azure::ServiceBus::SqlFilter** を適用します。このフィルターでは、カスタム プロパティ **message\_number** が 3 を超えるメッセージのみが選択されます。

	subscription = azure_service_bus_service.create_subscription("test-topic", 
	  "high-messages")
	azure_service_bus_service.delete_rule("test-topic", "high-messages", 
	  "$Default")
	
	rule = Azure::ServiceBus::Rule.new("high-messages-rule")
	rule.topic = "test-topic"
	rule.subscription = "high-messages"
	rule.filter = Azure::ServiceBus::SqlFilter.new({ 
	  :sql_expression => "message_number > 3" })
	rule = azure_service_bus_service.create_rule(rule)

同様に、次の例では "low-messages" という名前のサブスクリプションを作成し、**Azure::ServiceBus::SqlFilter** を適用します。このフィルターでは、**message_number** プロパティが 3 以下のメッセージのみが選択されます。

	subscription = azure_service_bus_service.create_subscription("test-topic", 
	  "low-messages")
	azure_service_bus_service.delete_rule("test-topic", "low-messages", 
	  "$Default")
	
	rule = Azure::ServiceBus::Rule.new("low-messages-rule")
	rule.topic = "test-topic"
	rule.subscription = "low-messages"
	rule.filter = Azure::ServiceBus::SqlFilter.new({ 
	  :sql_expression => "message_number <= 3" })
	rule = azure_service_bus_service.create_rule(rule)

メッセージが "test-topic" に送信されると、そのメッセージは "all-messages" トピック サブスクリプションにサブスクライブしている受信者に必ず配信され、さらにメッセージのコンテンツに応じて、"high-messages" および "low-messages" トピック サブスクリプションにサブスクライブしている受信者に対して選択的に配信されます。

## <a id="how-to-send-messages-to-a-topic"></a>メッセージをトピックに送信する方法

メッセージをサービス バス トピックに送信するには、アプリケーションで **Azure::ServiceBusService** オブジェクトの **send\_topic\_message()** メソッドを使用する必要があります。サービス バス トピックに送信されたメッセージは、**Azure::ServiceBus::BrokeredMessage** オブジェクトです。**Azure::ServiceBus::BrokeredMessage** オブジェクトには、一連の標準的なプロパティ (**label**、**time\_to\_live** など)、アプリケーション固有のカスタム プロパティの保持に使用するディクショナリ、および文字列データの本体が含まれています。アプリケーションでは、文字列値を **send\_topic\_message()** メソッドに渡すことによってメッセージの本文を設定でき、必須の標準プロパティは既定値に設定されます。

次の例では、"test-topic" に 5 件のテスト メッセージを送信する方法を示します。各メッセージの **message_number** カスタム プロパティの値がループの反復回数に応じて変化することに注目してください (これによってメッセージを受信するサブスクリプションが決定されます)。

	5.times do |i|
	  message = Azure::ServiceBus::BrokeredMessage.new("test message " + i, 
	    { :message_number => i })
	  azure_service_bus_service.send_topic_message("test-topic", message)
	end

サービス バス トピックでは、最大 256 MB までのメッセージをサポートしています (標準とカスタムのアプリケーション プロパティが含まれるヘッダーの最大サイズは 64 MB です)。トピックで保持されるメッセージ数には上限がありませんが、1 つのトピックで保持できるメッセージの合計サイズには上限があります。このトピックのサイズはトピックの作成時に定義します。上限は 5 GB です。

## <a id="how-to-receive-messages-from-a-subscription"></a>サブスクリプションからメッセージを受信する方法

サブスクリプションからメッセージを受信するには、**Azure::ServiceBusService** オブジェクトの **receive\_subscription\_message()** メソッドを使用します。既定では、メッセージは読み取られて (ピークされて) ロックされますが、サブスクリプションからは削除されません。**peek\_lock** オプションを **false** に設定すると、サブスクリプションからメッセージを読み取って削除できます。

既定の動作では、読み取りと削除が 2 段階の操作になるため、メッセージが失われることを許容できないアプリケーションにも対応することができます。サービス バスは要求を受け取ると、次に読み取られるメッセージを検索して、他のコンシューマーが受信できないようロックしてから、アプリケーションにメッセージを返します。アプリケーションがメッセージの処理を終えた後 (または後で処理するために確実に保存した後)、**delete\_subscription\_message()** メソッドを呼び出し、削除するメッセージをパラメーターとして指定して、受信処理の第 2 段階を完了します。**delete\_subscription\_message()** メソッドによって、メッセージが読み取り中としてマークされ、キューから削除されます。

**:peek\_lock** パラメーターを **false** に設定すると、メッセージの読み取りと削除は最もシンプルなモデルになります。これは、問題の発生時にアプリケーション側でメッセージを処理しないことを許容できるシナリオに最適です。このことを理解するために、コンシューマーが受信要求を発行した後で、メッセージを処理する前にクラッシュしたというシナリオを考えてみましょう。サービス バスはメッセージを読み取り済みとしてマークするため、アプリケーションが再起動してメッセージの読み取りを再開すると、クラッシュ前に読み取られていたメッセージは見落とされることになります。

次の例では、**receive\_subscription\_message()** を使用したメッセージの受信および処理の方法を示しています。この例では、まず **:peek\_lock** を **false** に設定して使用することで、"low-messages" サブスクリプションからメッセージを受信して削除します。次に、"high-messages" から別のメッセージを受信し、**delete\_queue\_message()** を使用してそのメッセージを削除します。

    message = azure_service_bus_service.receive_subscription_message(
	  "test-topic", "low-messages", { :peek_lock => false })
    message = azure_service_bus_service.receive_subscription_message(
	  "test-topic", "high-messages")
    azure_service_bus_service.delete_subscription_message("test-topic", 
	  "high-messages", message.sequence_number, message.lock_token)

## <a id="how-to-handle-application-crashes-and-unreadable-messages"></a>アプリケーションのクラッシュと読み取り不能のメッセージを処理する方法

サービス バスには、アプリケーションにエラーが発生した場合や、メッセージの処理に問題がある場合に復旧を支援する機能が備わっています。受信側のアプリケーションが何らかの理由によってメッセージを処理できない場合には、**Azure::ServiceBusService** オブジェクトの **unlock\_subscription\_message()** メソッドを呼び出すことができます。このメソッドが呼び出されると、サブスクリプション内のメッセージのロックが解除され、メッセージが再度受信できる状態に変わります。このメッセージは、同じコンシューマー側アプリケーションまたは他のコンシューマー側アプリケーションで受信されます。

サブスクリプション内でロックされているメッセージには、タイムアウトも設定されています。アプリケーションがクラッシュした場合など、ロックがタイムアウトになる前にアプリケーションがメッセージの処理に失敗した場合は、サービス バスによってメッセージのロックが自動的に解除され、再度受信できる状態に変わります。

メッセージが処理された後、**delete\_subscription\_message()** メソッドが呼び出される前にアプリケーションがクラッシュした場合は、アプリケーションが再起動する際にメッセージが再配信されます。一般的に、この動作は **"1 回以上の処理"** と呼ばれます。つまり、すべてのメッセージが 1 回以上処理されますが、特定の状況では、同じメッセージが再配信される可能性があります。重複処理が許されないシナリオの場合、重複メッセージの配信を扱うロジックをアプリケーションに追加する必要があります。通常、この問題はメッセージの **message\_id** プロパティを使用して対処します。このプロパティは配信が試行された後も同じ値を保持します。

## <a id="how-to-delete-topics-and-subscriptions"></a>トピックとサブスクリプションを削除する方法

トピックおよびサブスクリプションは永続的であり、[Azure 管理ポータル](https://manage.windowsazure.com)またはプログラムによって明示的に削除する必要があります。次の例では、"test-topic" という名前のトピックを削除する方法を示します。

	azure_service_bus_service.delete_topic("test-topic")

トピックを削除すると、そのトピックに登録されたサブスクリプションもすべて削除されます。サブスクリプションは、個別に削除することもできます。次のコードでは、"high-messages" という名前のサブスクリプションを "test-topic" トピックから削除する方法を示しています。

	azure_service_bus_service.delete_subscription("test-topic", "high-messages")

## <a id="NextSteps"></a>次のステップ

これで、サービス バス トピックの基本を学習できました。さらに詳細な情報が必要な場合は、次のリンク先を参照してください。

-   MSDN リファレンスの「[サービス バス キュー、トピック、およびサブスクリプション](http://msdn.microsoft.com/ja-jp/library/windowsazure/hh367516.aspx)」を参照
-   [SqlFilter](http://msdn.microsoft.com/ja-jp/library/windowsazure/microsoft.servicebus.messaging.sqlfilter.aspx) という API リファレンス
-	GitHub の [Azure SDK for Ruby](https://github.com/WindowsAzure/azure-sdk-for-ruby) リポジトリにアクセス
