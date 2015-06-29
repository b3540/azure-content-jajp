<properties 
	pageTitle="Azure Notification Hubs の使用" 
	description="Azure Notification Hubs を使用してプッシュ通知を行う方法について説明します。" 
	services="notification-hubs" 
	documentationCenter="windows" 
	authors="wesmc7777" 
	manager="dwrede" 
	editor="dwrede"/>

<tags 
	ms.service="notification-hubs" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-phone" 
	ms.devlang="dotnet" 
	ms.topic="hero-article" 
	ms.date="03/16/2015" 
	ms.author="wesmc"/>
# 通知ハブの使用

[AZURE.INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

## 概要

このトピックでは、Azure Notification Hubs を使用して Windows Phone 8 または Windows Phone 8.1 Silverlight アプリケーションにプッシュ通知を送信する方法について説明します。Windows Phone 8.1 (Silverlight 以外) を対象としている場合は、[Windows Universal](notification-hubs-windows-store-dotnet-get-started.md) バージョンを参照してください。このチュートリアルでは、Microsoft プッシュ通知サービス (MPNS) を使用してプッシュ通知を受信する空の Windows Phone 8 アプリケーションを作成します。完了すると、通知ハブを使用して、アプリケーションを実行するすべてのデバイスにプッシュ通知をブロードキャストできるようになります。

> [AZURE.NOTE]Notification Hubs Windows Phone SDK では、Windows Phone 8.1 Silverlight アプリで WNS を使用することはできません。Windows Phone 8.1 Silverlight アプリで (MPNS ではなく) WNS を使用するには、REST API を使用する「[Notification Hub - WP Silverlight チュートリアル]」のサンプルの手順に従ってください。

このチュートリアルでは、通知ハブを使用した簡単なブロードキャスト シナリオのデモンストレーションを行います。

## 前提条件

このチュートリアルには、次のものが必要です。

+ [Visual Studio 2012 Express for Windows Phone] 以降のバージョン

このチュートリアルを完了することは、Windows Phone 8 アプリケーションの他のすべての通知ハブ チュートリアルの前提条件です。

> [AZURE.NOTE]このチュートリアルを完了するには、アクティブな Azure アカウントが必要です。アカウントがない場合は、無料の試用アカウントを数分で作成することができます。詳細については、[Azure の無料評価版サイト](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fja-jp%2Fdocumentation%2Farticles%2Fnotification-hubs-windows-phone-get-started%2F)を参照してください。

## 通知ハブを作成する

1. [Azure 管理ポータル]にログオンし、画面の下部にある **[+新規]** をクリックします。

2. **[アプリ サービス]**、**[Service Bus]**、**[Notification Hub]**、**[簡易作成]** の順にクリックします。

   	![][7]

3. 通知ハブの名前を入力して、目的のリージョンを選択し、**[新しい通知ハブを作成する]** をクリックします。

   	![][8]

4. 前の手順で作成した名前空間 (通常は "***通知ハブ名*-ns**") をクリックし、上部にある **[構成]** タブをクリックします。

   	![][9]

5. 上部にある **[Notification Hubs]** タブをクリックし、前の手順で作成した通知ハブをクリックします。

   	![][10]

6. 下部にある **[接続情報]** をクリックします。2 つの接続文字列をメモします。

   	![][12]

7. **[構成]** タブをクリックし、**[Windows Phone 通知設定]** セクションの **[非認証プッシュ通知を有効にする]** チェック ボックスをオンにします。

   	![][15]

これで、Windows Phone 8 アプリケーションを登録して通知を送信するのに必要な接続文字列が取得されました。

> [AZURE.NOTE]このチュートリアルでは、非認証モードで MPNS を使用します。MPNS 非認証モードでは、各チャネルに送信できる通知に制限があります。Notification Hubs は、[MPNS 認証モード](http://msdn.microsoft.com/library/windowsphone/develop/ff941099(v=vs.105).aspx)をサポートします。
<!--Refer to [Notification Hubs How-To for Windows Phone 8] for more information on how to use MPNS authenticated mode.-->

## 通知ハブにアプリケーションを接続する

1. Visual Studio で、新しい Windows Phone 8 アプリケーションを作成します。

   	![][13]

	Visual Studio 2013 Update 2 以降では、代わりに Windows Phone Silverlight アプリケーションを作成します。
	
	![][11]

2. Visual Studio でソリューションを開いて右クリックし、**[NuGet パッケージの管理]** をクリックします。 

	[NuGet パッケージの管理] ダイアログ ボックスが表示されます。

3. `WindowsAzure.Messaging.Managed` を検索し、**[インストール]** をクリックして、使用条件に同意します。

	![][20]

	これによりパッケージのダウンロードとインストールが実行され、<a href="http://nuget.org/packages/WindowsAzure.Messaging.Managed/">WindowsAzure.Messaging.Managed NuGet パッケージ</a>を使用して、Windows の Azure メッセージング ライブラリに参照が追加されます。

4. App.xaml.cs ファイルを開き、次の `using` ステートメントを追加します。

        using Microsoft.Phone.Notification;
        using Microsoft.WindowsAzure.Messaging;

5. App.xaml.cs 内の **Application_Launching** メソッドの先頭に、次のコードを追加します。
	
	    var channel = HttpNotificationChannel.Find("MyPushChannel");
        if (channel == null)
        {
            channel = new HttpNotificationChannel("MyPushChannel");
            channel.Open();
            channel.BindToShellToast();
        }

        channel.ChannelUriUpdated += new EventHandler<NotificationChannelUriEventArgs>(async (o, args) =>
        {
            var hub = new NotificationHub("<hub name>", "<connection string>");
            await hub.RegisterNativeAsync(args.ChannelUri.ToString());
        });

    必ず、ハブの名前と、前のセクションで取得した **DefaultListenSharedAccessSignature** という接続文字列を挿入してください。このコードにより、MPNS からアプリケーションの ChannelURI が取得され、その ChannelURI が通知ハブに登録されます。これにより、アプリケーションが起動するたびに必ず ChannelURI も通知ハブに登録されます。

	>[AZURE.NOTE]このチュートリアルでは、トースト通知がデバイスに送信されます。タイル通知を送信する場合は、チャネルの **BindToShellTile** メソッドを呼び出す必要があります。トースト通知とタイル通知の両方をサポートするには、**BindToShellTile** と **BindToShellToast** の両方を呼び出します。
    
6. ソリューション エクスプローラーで、**[プロパティ]** を展開して WMAppManifest.xml ファイルを開き、**[機能]** タブをクリックして、**ID___CAP___PUSH_NOTIFICATION**  機能がオンであることを確認します。

   	![][14]

   これにより、アプリケーションでプッシュ通知の受信が有効になります。
	
7. F5 キーを押してアプリケーションを実行します。

	登録メッセージが表示されます。

## バックエンドから通知を送信する

通知は、<a href="http://msdn.microsoft.com/library/windowsazure/dn223264.aspx">REST インターフェイス</a>を使用するどのバックエンドからも Notification Hubs を使用して送信できます。このチュートリアルでは、.NET コンソール アプリケーションを使用して通知を送信します。通知ハブと統合した Azure モバイル サービス バックエンドからの通知の送信例については、「**モバイル サービスでのプッシュ通知の使用**」([.NET バックエンド](../mobile-services-javascript-backend-windows-phone-get-started-push.md) | [JavaScript バックエンド](../mobile-services-javascript-backend-windows-phone-get-started-push.md)) を参照してください。REST API を使用した通知の送信方法の例については、「**How to use Notification Hubs from Java/PHP (Java/PHP から Notification Hubs を使用する方法)**」([Java](notification-hubs-java-backend-how-to.md) | [PHP](notification-hubs-php-backend-how-to.md)) を参照してください。

1. ソリューションを右クリックし、**[追加]** と **[新しいプロジェクト]** を選択し、**[Visual C#]** で **[Windows]** と **[コンソール アプリケーション]** をクリックして、**[OK]** をクリックします。 

   	![][6]

	これにより、Visual C# の新しいコンソール アプリケーションがソリューションに追加されます。これは個別のソリューションで実行することもできます。 

4. 右クリックし、**[ツール]**、**[ライブラリ パッケージ マネージャー]**、**[パッケージ マネージャー コンソール]** の順にクリックします。 

	これにより、パッケージ マネージャー コンソールが開きます。

6. コンソール ウィンドウで、**[既定のプロジェクト]** を新しいコンソール アプリケーション プロジェクトに設定し、次に、コンソール ウィンドウで次のコマンドを実行します。

        Install-Package WindowsAzure.ServiceBus
    
	これにより、<a href="http://nuget.org/packages/WindowsAzure.ServiceBus/">WindowsAzure.ServiceBus NuGet パッケージ</a>を使用して Azure のサービス バス SDK に参照を追加します。

5. Program.cs ファイルを開き、次の `using` ステートメントを追加します。

        using Microsoft.ServiceBus.Notifications;

6. **Program** クラスで、次のメソッドを追加します。

        private static async void SendNotificationAsync()
        {
            NotificationHubClient hub = NotificationHubClient
				.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
            string toast = "<?xml version="1.0" encoding="utf-8"?>" +
                "<wp:Notification xmlns:wp="WPNotification">" +
                   "<wp:Toast>" +
                        "<wp:Text1>Hello from a .NET App!</wp:Text1>" +
                   "</wp:Toast> " +
                "</wp:Notification>";
            await hub.SendMpnsNativeNotificationAsync(toast);
        }

	"hub name" プレースホルダーは、ポータルの **[通知ハブ]** タブに表示される通知ハブの名前に置き換えてください。また、接続文字列プレースホルダーを、「通知ハブを構成する」で取得した **DefaultFullSharedAccessSignature** という接続文字列に置き換えます。

	>[AZURE.NOTE]**リッスン** アクセスではなく**フル** アクセスを持つ接続文字列を使用してください。リッスン アクセス文字列には通知を送信するアクセス許可はありません。

4. 次に、Main メソッド内に次の行を追加します。

         SendNotificationAsync();
		 Console.ReadLine();

5. Windows Phone エミュレーターを実行し、アプリを閉じて、コンソール アプリケーション プロジェクトを既定のスタートアップ プロジェクトとして設定します。次に、F5 キーを押して、アプリを実行します。

	トースト通知を受信します。トースト バナーをタップすると、アプリが読み込まれます。

MSDN の[トースト カタログ]および[タイル カタログ]に関するトピックに、使用できるすべてのペイロードが記載されています。

## 次のステップ

この簡単な例では、すべての Windows Phone 8 デバイスに通知をブロードキャストします。特定のユーザーをターゲットとするには、「[通知ハブを使用したユーザーへのプッシュ通知]」のチュートリアルを参照してください。対象グループごとにユーザーを区分する場合は、「[通知ハブを使用したニュース速報の送信]」を参照してください。通知ハブの使用方法の詳細については、「[Windows Azure 通知ハブの概要]」を参照してください。



<!-- Images. -->
[6]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-console-app.png
[7]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-from-portal.png
[8]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-from-portal2.png
[9]: ./media/notification-hubs-windows-phone-get-started/notification-hub-select-from-portal.png
[10]: ./media/notification-hubs-windows-phone-get-started/notification-hub-select-from-portal2.png
[11]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-wp-silverlight-app.png
[12]: ./media/notification-hubs-windows-phone-get-started/notification-hub-connection-strings.png

[13]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-wp-app.png
[14]: ./media/notification-hubs-windows-phone-get-started/mobile-app-enable-push-wp8.png
[15]: ./media/notification-hubs-windows-phone-get-started/notification-hub-pushauth.png
[20]: ./media/notification-hubs-windows-phone-get-started/notification-hub-windows-universal-app-install-package.png
[213]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-console-app.png





<!-- URLs. -->
[Visual Studio 2012 Express for Windows Phone]: https://go.microsoft.com/fwLink/p/?LinkID=268374
[Azure 管理ポータル]: https://manage.windowsazure.com/
[Windows Azure 通知ハブの概要]: http://msdn.microsoft.com/library/jj927170.aspx
[MPNS 認証モード]: http://msdn.microsoft.com/library/windowsphone/develop/ff941099(v=vs.105).aspx
[通知ハブを使用したユーザーへのプッシュ通知]: notification-hubs-aspnet-backend-windows-dotnet-notify-users.md
[通知ハブを使用したニュース速報の送信]: notification-hubs-windows-phone-send-breaking-news.md
[トースト カタログ]: http://msdn.microsoft.com/library/windowsphone/develop/jj662938(v=vs.105).aspx
[タイル カタログ]: http://msdn.microsoft.com/library/windowsphone/develop/hh202948(v=vs.105).aspx
[Notification Hub - WP Silverlight チュートリアル]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/PushToSLPhoneApp


<!--HONumber=52-->
 