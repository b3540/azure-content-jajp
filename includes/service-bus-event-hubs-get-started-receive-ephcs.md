﻿## EventProcessorHost を使用したメッセージの受信

[EventProcessorHost] は、永続的なチェックポイントの管理によって Event Hub のイベントの受信を簡素化し、並列してそれらの Event Hub から受信する .NET クラスです。[EventProcessorHost] を使用すると、さまざまなノードでホストされている場合でも、複数の受信側間でイベントを分割することができます。この例では、受信側が単一の場合に [EventProcessorHost] を使用する方法を示します。[イベント処理のスケール アウトのサンプル]は、受信側が複数の場合に [EventProcessorHost] を使用する方法を示します。

Event Hub の受信パターンの詳細については、「[Event Hub の概要]」を参照してください。

[EventProcessorHost] を使用するには、[Azure ストレージ アカウント]が必要です。

1. [Azure の管理ポータル]にログオンし、画面下部にある **[新規]** をクリックします。

2. **[データ サービス]**、**[ストレージ]**、**[簡易作成]** の順にクリックし、ストレージ アカウントの名前を入力します。目的のリージョンを選択し、**[ストレージ アカウントの作成]** をクリックします。

   	![][11]

3. 新しく作成したストレージ アカウントをクリックし、**[アクセス キーの管理]** をクリックします。

   	![][12]

	後で使用するために、アクセス キーをコピーします。

4. Visual Studio で、**コンソール アプリケーション** プロジェクト テンプレートを使用して、新しい Visual C# のデスクトップ アプリ プロジェクトを作成します。プロジェクトの名前として「**Receiver**」と入力します。

   	![][14]

5. ソリューション エクスプローラーでソリューションを右クリックし、**[NuGet パッケージの管理]** をクリックします。 

	**[NuGet パッケージの管理]** ダイアログ ボックスが表示されます。

6. `Microsoft Azure Service Bus Event Hub - EventProcessorHost` を検索し、**[インストール]** をクリックして、使用条件に同意します。 

	![][13]

	これによって、<a href="https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost">Azure Service Bus Event Hub - EventProcessorHost NuGet パッケージ</a>への参照がすべての依存関係と共にダウンロード、インストール、追加されます。

7. **SimpleEventProcessor** と呼ばれる新しいクラスを作成し、ファイルの先頭に次のステートメントを追加します。

		using Microsoft.ServiceBus.Messaging;
		using System.Diagnostics;
		using System.Threading.Tasks;

	次に、クラスの本文として次のコードを挿入します。

		class SimpleEventProcessor : IEventProcessor
	    {
	        Stopwatch checkpointStopWatch;
	        
	        async Task IEventProcessor.CloseAsync(PartitionContext context, CloseReason reason)
	        {
	            Console.WriteLine(string.Format("Processor Shuting Down.  Partition '{0}', Reason: '{1}'.", context.Lease.PartitionId, reason.ToString()));
	            if (reason == CloseReason.Shutdown)
	            {
	                await context.CheckpointAsync();
	            }
	        }
	
	        Task IEventProcessor.OpenAsync(PartitionContext context)
	        {
	            Console.WriteLine(string.Format("SimpleEventProcessor initialize.  Partition: '{0}', Offset: '{1}'", context.Lease.PartitionId, context.Lease.Offset));
	            this.checkpointStopWatch = new Stopwatch();
	            this.checkpointStopWatch.Start();
	            return Task.FromResult<object>(null);
	        }
	
	        async Task IEventProcessor.ProcessEventsAsync(PartitionContext context, IEnumerable<EventData> messages)
	        {
	            foreach (EventData eventData in messages)
	            {
	                string data = Encoding.UTF8.GetString(eventData.GetBytes());
	                
	                Console.WriteLine(string.Format("Message received.  Partition: '{0}', Data: '{2}'",
	                    context.Lease.PartitionId, data));
	            }
	
	            //Call checkpoint every 5 minutes, so that worker can resume processing from the 5 minutes back if it restarts.
	            if (this.checkpointStopWatch.Elapsed > TimeSpan.FromMinutes(5))
	            {
	                await context.CheckpointAsync();
	                lock (this)
	                {
	                    this.checkpointStopWatch.Reset();
	                }
	            }
	        }
	    }

	このクラスは、**EventProcessorHost** から呼び出されて、Event Hub から受信したイベントを処理します。`SimpleEventProcessor` クラスは、ストップウォッチを使用して **EventProcessorHost** コンテキストで定期的にチェックポイント メソッドを呼び出します。これにより、受信側を再起動すると、処理の作業の 5 分以内に機能が失われます。

8. **Program** クラスには、上部に次の `using` ステートメントを追加します。

		using Microsoft.ServiceBus.Messaging;
		using System.Threading.Tasks;
	
	次に、**Main** メソッドに、Event Hub 名と接続文字列、前のセクションでコピーしたストレージ アカウントとキーを代入する次のコードを追加します。

		string eventHubConnectionString = "{event hub connection string}";
        string eventHubName = "{event hub name}";
        string storageAccountName = "{storage account name}";
        string storageAccountKey = "{storage account key}";
        string storageConnectionString = string.Format("DefaultEndpointsProtocol=https;AccountName={0};AccountKey={1}",
                    storageAccountName, storageAccountKey);

        string eventProcessorHostName = Guid.NewGuid().ToString();
        EventProcessorHost eventProcessorHost = new EventProcessorHost(eventProcessorHostName, eventHubName, EventHubConsumerGroup.DefaultGroupName, eventHubConnectionString, storageConnectionString);
        eventProcessorHost.RegisterEventProcessorAsync<SimpleEventProcessor>().Wait();
            
        Console.WriteLine("Receiving. Press enter key to stop worker.");
        Console.ReadLine();

> [AZURE.NOTE] このチュートリアルでは、[EventProcessorHost] の単一のインスタンスを使用します。スループットを向上させるには、[EventProcessorHost] の複数のインスタンスを実行することをお勧めします ([イベント処理のスケール アウトのサンプル]を参照してください)。このような場合、受信したイベントの負荷を分散するために、さまざまなインスタンスが自動的に連携します。複数の受信側でぞれぞれ*すべて*のイベントを処理する場合、**ConsumerGroup** 概念を使用する必要があります。さまざまなコンピューターからイベントを受信する場合、デプロイしたコンピューター (またはロール) に基づいて [EventProcessorHost] インスタンスの名前を指定するのに便利です。これらのトピックの詳細については、「[Event Hubs の概要]」および [Event Hubs のプログラミング ガイド]を参照してください。

<!-- Links -->
[Event Hubs の概要]: http://msdn.microsoft.com/ja-jp/library/azure/dn821413.aspx
[イベント処理のスケール アウトのサンプル]: https://code.msdn.microsoft.com/windowsazure/Service-Bus-Event-Hub-45f43fc3
[Azure ストレージ アカウント]: http://azure.microsoft.com/ja-jp/documentation/articles/storage-create-storage-account/
[EventProcessorHost]: http://msdn.microsoft.com/ja-jp/library/azure/microsoft.servicebus.messaging.eventprocessorhost(v=azure.95).aspx 

<!-- Images -->

[11]: ./media/service-bus-event-hubs-getstarted/create-eph-csharp2.png
[12]: ./media/service-bus-event-hubs-getstarted/create-eph-csharp3.png
[13]: ./media/service-bus-event-hubs-getstarted/create-eph-csharp1.png
[14]: ./media/service-bus-event-hubs-getstarted/create-sender-csharp1.png

[Event Hubs のプログラミング ガイド]: http://msdn.microsoft.com/ja-jp/library/azure/dn789972.aspx