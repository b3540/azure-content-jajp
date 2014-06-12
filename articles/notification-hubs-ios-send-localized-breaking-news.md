<properties linkid="develop-notificationhubs-tutorials-send-localized-breaking-news-iOS" urlDisplayName="ローカライズしたニュース速報" pageTitle="通知ハブ ローカライズした iOS 向けニュース速報チュートリアル" metaKeywords="" description="Azure のサービス バス通知ハブを使用して、ローカライズしたニュース速報通知を送信する方法を説明します (iOS)。" metaCanonical="" services="mobile-services,notification-hubs" documentationCenter="" title="通知ハブを使用したローカライズ ニュース速報の iOS への送信" authors="ricksal" solutions="" manager="" editor="" />
#通知ハブを使用した iOS デバイスへのローカライズ ニュース速報の送信

<div class="dev-center-tutorial-selector sublanding"> 
    	<a href="/ja-jp/manage/services/notification-hubs/breaking-news-localized-dotnet" title="Windows ストア C#">Windows ストア C#</a><a href="/ja-jp/manage/services/notification-hubs/breaking-news-localized-ios" title="iOS" class="current">iOS</a>
</div>


このトピックでは、Azure 通知ハブの**テンプレート**機能を使用して、言語およびデバイスごとにローカライズしたニュース速報通知をブロードキャストする方法について説明します。このチュートリアルでは、「[通知ハブを使用したニュース速報の送信]」で作成した Windows ストア アプリケーションを使用します。完了すると、興味のあるニュース速報カテゴリに登録して受信する通知の言語を指定し、選択したカテゴリのその言語のプッシュ通知だけを受信できるようになります。

このチュートリアルでは、このシナリオを有効にするための、次の基本的な手順について説明します。

1. [テンプレートの概念]
2. [アプリケーションのユーザー インターフェイス]
3. [iOS アプリケーションを構築する]
4. [バックエンドから通知を送信する]


このシナリオは次の 2 つに分けられます。

- iOS アプリケーションで、クライアント デバイスが言語を指定し、さまざまなニュース速報カテゴリを購読できるようになります。

- Azure 通知ハブの**タグ**機能と**テンプレート**機能を使用して、バックエンドからブロードキャストされます。



##前提条件##

「[通知ハブを使用したニュース速報の送信]」のチュートリアルを完了し、コードが使用可能な状態になっている必要があります。このチュートリアルでは、そのコードに基づいているためです。

Visual Studio 2012 も必要です。



<h2><a name="concepts"></a><span class="short-header">概念</span>テンプレートの概念</h2>

「[通知ハブを使用したニュース速報の送信]」では、**タグ**を使用してさまざまなニュース カテゴリの通知を購読するアプリケーションを構築しました。
しかし、多くのアプリケーションは複数の市場をターゲットとしており、ローカライズが必要です。これは、通知自体の内容をローカライズし、適切なデバイス セットに配信する必要があることを意味します。
このトピックでは、通知ハブの**テンプレート**機能を使用して、ローカライズしたニュース速報通知を簡単に配信する方法について説明します。

注: ローカライズした通知を送信する 1 つの方法として、各タグの複数のバージョンを作成する方法があります。たとえば、ワールド ニュースで英語、フランス語、標準中国語をサポートするには、3 つのタグ ("world_en"、"world_fr"、"world_ch") が必要です。その後、ワールド ニュースのローカライズ バージョンをこれらの各タグに送信する必要があります。このトピックでは、テンプレートを使用することでタグの増加を抑制し、複数のメッセージを送信しなくてもよいようにします。

大まかに言えば、テンプレートとは、特定のデバイスが通知をどのように受信するかを特定するための手段です。テンプレートは、アプリケーション バックエンドにより送信されるメッセージの一部となっているプロパティを参照することで、正確なペイロードを指定します。このケースでは、サポートされるすべての言語を含む、ロケールにとらわれないメッセージを送信します。

	{
		"News_English": "...",
		"News_French": "...",
		"News_Mandarin": "..."
	}

次に、デバイスが、適切なプロパティを参照するテンプレートを登録するようにします。たとえば、フランス語ニュースを登録する iOS アプリケーションは、次のテンプレートを登録します。

	{
		aps:{
			alert: "$(News_French)"
		}
	}

テンプレートは有用な機能です。詳細については、「[通知ハブの概要]」の記事を参照してください。テンプレート式言語のリファレンスは、「[方法: Azure 通知ハブ (iOS アプリケーション)]」にあります。

<h2><a name="ui"></a><span class="short-header">アプリケーションの UI</span>アプリケーションのユーザー インターフェイス</h2>

ここでは、「[通知ハブを使用したニュース速報の送信]」で作成したニュース速報アプリケーションを変更し、テンプレートを使用してローカライズしたニュース速報を送信します。


MainStoryboard_iPhone.storyboard で、サポートする 3 つの言語 ([English]、[French]、[Mandarin]) を備える Segmented Control を追加します。

![][13]
	
次に、以下に示すように ViewController.h に IBOutlet を追加します。
	
![][14]
	
<h2><a name="building-client"></a><span class="building app">アプリケーション UI</span>iOS アプリケーションを構築する</h2>

クライアント アプリケーションがローカライズしたメッセージを受信できるようにするには、*ネイティブ*登録 (つまり、テンプレートを指定する登録) をテンプレート登録に置き換える必要があります。

1. Notification.h で、*retrieveLocale* メソッドを追加し、以下に示すように store メソッドと subscribe メソッドを変更します。

		- (void) storeCategoriesAndSubscribeWithLocale:(int) locale categories:(NSSet*) categories completion: (void (^)(NSError* error))completion;

		- (void) subscribeWithLocale:(int) locale categories:(NSSet*) categories completion:(void (^)(NSError *))completion;
		
		- (NSSet*) retrieveCategories;
		
		- (int) retrieveLocale;
		
	Notification.m で、ロケール パラメーターを追加してユーザーの既定の設定に格納することで、*storeCategoriesAndSubscribe* メソッドを変更します。
	
		- (void) storeCategoriesAndSubscribeWithLocale:(int) locale categories:(NSSet *)categories completion:(void (^)(NSError *))completion {
		    NSUserDefaults* defaults = [NSUserDefaults standardUserDefaults];
		    [defaults setValue:[categories allObjects] forKey:@"BreakingNewsCategories"];
		    [defaults setInteger:locale forKey:@"BreakingNewsLocale"];
		    [defaults synchronize];
		    
		    [self subscribeWithLocale: locale categories:categories completion:completion];
		}

	次に、*subscribe* メソッドを変更してロケールを追加します。
	
		- (void) subscribeWithLocale: (int) locale categories:(NSSet *)categories completion:(void (^)(NSError *))completion{
		    SBNotificationHub* hub = [[SBNotificationHub alloc] initWithConnectionString:@"<connection string>" notificationHubPath:@"<hub name>"];
		    
		    NSString* localeString;
		    switch (locale) {
		        case 0:
		            localeString = @"English";
		            break;
		        case 1:
		            localeString = @"French";
		            break;
		        case 2:
		            localeString = @"Mandarin";
		            break;
		    }
		    
		    NSString* template = [NSString stringWithFormat:@"{\"aps\":{\"alert\":\"$(News_%@)\"},\"inAppMessage\":\"$(News_%@)\"}", localeString, localeString];
		    
		    [hub registerTemplateWithDeviceToken:self.deviceToken name:@"newsTemplate" jsonBodyTemplate:template expiryTemplate:@"0" tags:categories completion:completion];
		}
		
	ここで、*registerNativeWithDeviceToken* の代わりに *registerTemplateWithDeviceToken* メソッドをどのように使用しているかに注目してください。テンプレートを登録するときは、json テンプレートに加えて、テンプレートの名前も指定する必要があります (アプリケーションにより複数のテンプレートが登録されることがあるため)。それらのニュースの通知を確実に受信できるようにするため、カテゴリをタグとして登録してください。
	
	最後に、ユーザーの既定の設定からロケールを取得するメソッドを追加します。
	
		- (int) retrieveLocale {
		    NSUserDefaults* defaults = [NSUserDefaults standardUserDefaults];
		    
		    int locale = [defaults integerForKey:@"BreakingNewsLocale"];
		    
		    return locale < 0?0:locale;
		}
		
3. ここまでで、Notifications クラスを変更したので、ViewController が新しい UISegmentControl を使用することを確認する必要があります。*viewDidLoad* メソッドに次の行を追加し、現在選択されているロケールが表示されるようにします。

		self.Locale.selectedSegmentIndex = [notifications retrieveLocale];
		
	次に、*subscribe* メソッドで、*storeCategoriesAndSubscribe* への呼び出しを次のように変更します。
	
		[notifications storeCategoriesAndSubscribeWithLocale: self.Locale.selectedSegmentIndex categories:[NSSet setWithArray:categories] completion: ^(NSError* error) {
	        if (!error) {
	            UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Notification" message:
	                                  @"Subscribed!" delegate:nil cancelButtonTitle:
	                                  @"OK" otherButtonTitles:nil, nil];
	            [alert show];
	        } else {
	            NSLog(@"Error subscribing: %@", error);
	        }
	    }];
	    
4. 最後に、アプリケーションの起動時に登録を正しく更新できるように、 *AppDelegate.m で didRegisterForRemoteNotificationsWithDeviceToken* メソッドを更新します。通知の *subscribe* メソッドへの呼び出しを、次のように変更します。

		NSSet* categories = [notifications retrieveCategories];
	    int locale = [notifications retrieveLocale];
	    [notifications subscribeWithLocale: locale categories:categories completion:^(NSError* error) {
	        if (error != nil) {
	            NSLog(@"Error registering for notifications: %@", error);
	        }
	    }];

<h2><a name="send"></a><span class="short-header">ローカライズした通知を送信する</span>バックエンドからローカライズした通知を送信する</h2>

[WACOM.INCLUDE [notification-hubs-localized-back-end](../includes/notification-hubs-localized-back-end.md)]


## 次のステップ

テンプレートの使用方法の詳細については、以下を参照してください。

- [通知ハブによるユーザーへの通知: ASP.NET]
- [通知ハブによるモバイル サービスのユーザーへの通知: モバイル サービス]
- [通知ハブの概要]

テンプレート式言語のリファレンスは、「[方法: Azure 通知ハブ (iOS アプリ)]」にあります。



		
<!-- Anchors. -->
[テンプレートの概念]: #concepts
[アプリケーションのユーザー インターフェイス]: #ui
[iOS アプリケーションを構築する]: #building-client
[バックエンドから通知を送信する]: #send
[次のステップ]: #next-steps

<!-- Images. -->









[13]: ./media/notification-hubs-ios-send-localized-breaking-news/ios_localized1.png
[14]: ./media/notification-hubs-ios-send-localized-breaking-news/ios_localized2.png





<!-- URLs. -->
[方法: Azure 通知ハブ (iOS アプリケーション)]: http://msdn.microsoft.com/ja-jp/library/jj927168.aspx
[通知ハブを使用したニュース速報の送信]: /ja-jp/manage/services/notification-hubs/breaking-news-ios
[モバイル サービス]: /ja-jp/develop/mobile/tutorials/get-started
[通知ハブによるユーザーへの通知: ASP.NET]: /ja-jp/manage/services/notification-hubs/notify-users-aspnet
[通知ハブによるモバイル サービスのユーザーへの通知: モバイル サービス]: /ja-jp/manage/services/notification-hubs/notify-users
[アプリケーションの提出に関するページ: ]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[マイ アプリケーション]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Windows 向け Live SDK]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Get started with Mobile Services]: /ja-jp/develop/mobile/tutorials/get-started/#create-new-service
[データの使用]: /ja-jp/develop/mobile/tutorials/get-started-with-data-ios
[認証の使用]: /ja-jp/develop/mobile/tutorials/get-started-with-users-ios
[プッシュ通知の使用]: /ja-jp/develop/mobile/tutorials/get-started-with-push-ios
[アプリケーション ユーザーへのプッシュ通知]: /ja-jp/develop/mobile/tutorials/push-notifications-to-users-ios
[スクリプトを使用したユーザーの承認]: /ja-jp/develop/mobile/tutorials/authorize-users-in-scripts-ios
[JavaScript と HTML]: /ja-jp/develop/mobile/tutorials/get-started-with-push-js.md

[Azure 管理ポータル]: https://manage.windowsazure.com/
[モバイル サービスの Windows 開発者プレビュー登録の手順]: ../HowTo/mobile-services-windows-developer-preview-registration.md
[wns オブジェクトに関するページ]: http://go.microsoft.com/fwlink/p/?LinkId=260591
[通知ハブの概要]: http://msdn.microsoft.com/ja-jp/library/jj927170.aspx
[方法: Azure 通知ハブ (iOS アプリ)]: http://msdn.microsoft.com/ja-jp/library/jj927168.aspx
