<properties linkid="develop-mobile-tutorials-get-started-with-data-dotnet" urlDisplayName="データの使用" pageTitle="データの使用 - Azure モバイル サービス" metaKeywords="" description="Azure モバイル サービスでデータを使用する方法について説明します。" metaCanonical="" disqusComments="1" umbracoNaviHide="1" title="Visual Studio 2012 を使用したモバイル サービスでのデータの使用" documentationCenter="Mobile" authors="" />

# Visual Studio 2012 を使用したモバイル サービスでのデータの使用


<div class="dev-center-tutorial-selector sublanding">
<a href="/ja-jp/develop/mobile/tutorials/get-started-with-data-dotnet-vs2012" title="Windows ストア C#" class="current">Windows ストア C#</a><a href="/ja-jp/develop/mobile/tutorials/get-started-with-data-js-vs2012" title="Windows ストア JavaScript">Windows ストア JavaScript</a><a href="/ja-jp/develop/mobile/tutorials/get-started-with-data-wp8" title="Windows Phone">Windows Phone</a><a href="/ja-jp/develop/mobile/tutorials/get-started-with-data-ios" title="iOS">iOS</a><a href="/ja-jp/develop/mobile/tutorials/get-started-with-data-android" title="Android">Android</a><a href="/ja-jp/develop/mobile/tutorials/get-started-with-data-html" title="HTML">HTML</a><a href="/ja-jp/develop/mobile/tutorials/get-started-with-data-xamarin-ios" title="Xamarin.iOS">Xamarin.iOS</a><a href="/ja-jp/develop/mobile/tutorials/get-started-with-data-xamarin-android" title="Xamarin.Android">Xamarin.Android</a></div>
<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">
<p>このトピックでは、Azure のモバイル サービスを使用して Windows ストア アプリのデータを活用する方法について説明します。このチュートリアルでは、メモリにデータを格納するアプリケーションの Visual Studio 2012 プロジェクトをダウンロードした後、新しいモバイル サービスを作成してそれをアプリケーションに統合します。その後、Azure 管理ポータルにログインして、アプリケーションの実行中にデータに加えられた変更を表示します。このチュートリアルのビデオ バージョンを表示するには、右側のクリップをクリックします。</p>
</div>
<div class="dev-onpage-video-wrapper"><a href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Windows-Store-app-Getting-Started-with-Data-Connecting-your-app-to-Windows-Azure-Mobile-Services" target="_blank" class="label">チュートリアルを見る</a> <a style="background-image: url('/media/devcenter/mobile/videos/get-started-with-data-windows-store-180x120.png') !important;" href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Windows-Store-app-Getting-Started-with-Data-Connecting-your-app-to-Windows-Azure-Mobile-Services" target="_blank" class="dev-onpage-video"><span class="icon">ビデオを再生する</span></a> <span class="time">15:33</span></div>
</div>

<div class="dev-callout"><b>注</b>
<p>このチュートリアルでは、Visual Studio 2012 で作成された Windows ストア アプリにモバイル サービス機能を追加します。Visual Studio 2013 には、Windows ストア アプリをモバイル サービスに簡単に接続できる新機能が含まれています。詳細については、「<a href="/ja-jp/develop/mobile/tutorials/get-started-with-data-dotnet/">モバイル サービスでのデータの使用</a>」を参照してください。</p>
</div>

このチュートリアルでは、次の基本的な手順について説明します。

1. [Windows ストア アプリ プロジェクトのダウンロード][Get the Windows Store app]
2. [モバイル サービスの作成]
3. [ストレージのデータ テーブルの追加]
4. [モバイル サービスを使用するためのアプリケーションの更新]
5. [モバイル サービスに対するアプリケーションのテスト]

<div class="dev-callout"><strong>注</strong><p> このチュートリアルを完了するには、Azure アカウントが必要です。アカウントがない場合は、無料の試用アカウントを数分で作成することができます。詳細については、<a href="http://www.windowsazure.com/ja-jp/pricing/free-trial/?WT.mc_id=AE564AB28" target="_blank">Azure の無料評価版サイト</a>を参照してください。</p></div>

<h2><a name="download-app"></a><span class="short-header">プロジェクトのダウンロード</span>GetStartedWithData プロジェクトのダウンロード</h2>

このチュートリアルは、Windows ストア アプリケーションである [GetStartedWithData アプリケーション][Developer Code Samples site]に基づいています。このアプリケーションの UI は、追加された項目がメモリにローカルに格納される点を除き、モバイル サービスのクイック スタートで生成したアプリケーションと同じです。

1. C# バージョンの GetStartedWithData サンプル アプリケーションを[デベロッパー サンプル コード集のサイト]からダウンロードします。

   	![][10]

2. Visual Studio 2012 Express for Windows 8 で、ダウンロードしたプロジェクトを開き、MainPage.xaml.cs ファイルを確認します。

   	追加された **TodoItem** オブジェクトはメモリ内の **ObservableCollection&lt;TodoItem&gt;** に格納されます。

3. **F5** キーを押してプロジェクトをリビルドし、アプリケーションを開始します。

4. アプリケーションで、**[Insert a TodoItem]** に任意のテキストを入力し、**[Save]** をクリックします。

   	![][0]  

   	保存したテキストが、**[Query and update data]** の下の 2 番目の列に表示されます。

<h2><a name="create-service"></a><span class="short-header">モバイル サービスの作成</span>管理ポータルでの新しいモバイル サービスの作成</h2>

[WACOM.INCLUDE [mobile-services-create-new-service-data](../includes/mobile-services-create-new-service-data.md)]

<h2><a name="add-table"></a><span class="short-header">新しいテーブルの追加</span>モバイル サービスへの新しいテーブルの追加</h2>

[WACOM.INCLUDE [mobile-services-create-new-service-data-2](../includes/mobile-services-create-new-service-data-2.md)]

<h2><a name="update-app"></a><span class="short-header">アプリケーションの更新</span>モバイル サービスをデータ アクセスに使用するためのアプリケーションの更新</h2>

モバイル サービスの準備が整ったら、ローカル コレクションの代わりにモバイル サービスに項目を格納するようにアプリケーションを更新します。

1. Visual Studio の**ソリューション エクスプローラー**で、プロジェクト名を右クリックし、**[NuGet パッケージの管理]** をクリックします。

2. 左側のウィンドウで、**[オンライン]** カテゴリ、**[プレリリースを含める]** の順にクリックし、`WindowsAzure.MobileServices` を探します。**Azure モバイル サービス** パッケージで **[インストール]** をクリックし、使用許諾契約に同意します。

  	![][7]

  	これにより、モバイル サービス クライアント ライブラリがプロジェクトに追加されます。

3. 管理ポータルで、**[モバイル サービス]** をクリックし、先ほど作成したモバイル サービスをクリックします。

4. **[ダッシュボード]** タブをクリックし、**サイトの URL** をメモに記録します。次に、**[キーの管理]** をクリックし、**アプリケーション キー**をメモに記録します。

   	![][8]

  	これらの値は、アプリケーション コードからモバイル サービスにアクセスするときに必要になります。

5. Visual Studio で App.xaml.cs ファイルを開き、次の `using` ステートメントを追加またはコメント解除します。

       	using Microsoft.WindowsAzure.MobileServices;

6. この同じファイルで、**MobileService** 変数を定義するコードをコメント解除します。また、**MobileServiceClient** コンストラクターに、モバイル サービスで入手した URL とアプリケーション キーを (この順序で) 指定します。

		//public static MobileServiceClient MobileService = new MobileServiceClient( 
        //    "AppUrl", 
        //    "AppKey" 
        //); 

  	これで、モバイル サービスへのアクセスに使用される **MobileServiceClient** の新しいインスタンスが作成されます。

6. MainPage.xaml.cs ファイルで、次の `using` ステートメントを追加またはコメント解除します。

       	using Microsoft.WindowsAzure.MobileServices;
		using Newtonsoft.Json;

7. この同じファイルで、**TodoItem** クラス定義を次のコードに置き換えます。

        public class TodoItem
        {
            public string Id { get; set; }

            [JsonProperty(PropertyName = "text")]
            public string Text { get; set; }

            [JsonProperty(PropertyName = "complete")]
            public bool Complete { get; set; }
        }

7. 既存の **items** コレクションを定義する行をコメント アウトし、次の行をコメント解除します。

        private MobileServiceCollection<TodoItem, TodoItem> items;
        private IMobileServiceTable<TodoItem> todoTable = 
			App.MobileService.GetTable<TodoItem>();

   	このコードは、モバイル サービス対応のバインディング コレクション (**items**) と SQL データベース テーブルのプロキシ クラス **TodoItem** (**todoTable**) を作成します。

7. **InsertTodoItem** メソッド内で、**TodoItem**.**Id** プロパティを設定するコード行を削除し、**async** 修飾子をメソッドに追加して、次のコード行をコメント解除します。

        await todoTable.InsertAsync(todoItem);

  	このコードでは、新しい項目をテーブルに挿入します。

8. **RefreshTodoItems** メソッド内で、**async** 修飾子をメソッドに追加して、次のコード行をコメント解除します。

        items = await todoTable.ToCollectionAsync();

   	これにより、todoTable 内で、モバイル サービスから返されたすべての TodoItem オブジェクトが格納される項目のコレクションへのバインディングが設定されます。

9. **UpdateCheckedTodoItem** メソッド内で、**async** 修飾子をメソッドに追加して、次のコード行をコメント解除します。

         await todoTable.UpdateAsync(item);

   	これにより、項目の更新がモバイル サービスに送信されます。

バックエンド ストレージのモバイル サービスを使用するようにアプリケーションを更新した後は、モバイル サービスに対してアプリケーションをテストします。

<h2><a name="test-app"></a><span class="short-header">アプリケーションのテスト</span>新しいモバイル サービスに対するアプリケーションのテスト</h2>

1. Visual Studio で、F5 キーを押してアプリケーションを実行します。

2. ここでも、**[Insert a TodoItem]** にテキストを入力し、**[Save]** をクリックします。

   	これにより、新しい項目が挿入としてモバイル サービスに送信されます。

3. [管理ポータル]で、**[モバイル サービス]** をクリックし、対象のモバイル サービスをクリックします。

4. **[データ]** タブをクリックし、**[参照]** をクリックします。

   	![][9]
  
   	**TodoItem** テーブルに、モバイル サービスによって生成された ID 値を持つデータが含まれ、アプリケーションの TodoItem クラスに対応してその列が自動的にテーブルに追加されていることに注目してください。

5. アプリケーションで、リストの項目の 1 つをチェックします。ポータルの [参照] タブに戻り、**[最新の情報に更新]** をクリックします。

  	"complete" 値が **false** から **true** に変更されます。

6. MainPage.xaml.cs プロジェクト ファイルで、既存の **RefreshTodoItems** メソッドを、完了済み項目をフィルター処理で除外する次のコード行で置き換えます。

        private async void RefreshTodoItems()
        {                       
            // This query filters out completed TodoItems. 
            items = await todoTable
               .Where(todoItem => todoItem.Complete == false)
               .ToCollectionAsync();

            ListItems.ItemsSource = items;            
        }

7. アプリケーションで、リストの項目をもう 1 つチェックし、**[最新の情報に更新]** をクリックします。

   	リスト内のチェックされた項目が非表示になります。最新の情報への更新のたびにモバイル サービスへの往復が発生し、その結果、フィルター処理されたデータが返されます。

これで、**データの使用**に関するチュートリアルはおしまいです。

## <a name="next-steps"> </a>次のステップ

このチュートリアルでは、Windows ストア アプリでモバイル サービスのデータを操作できるようにするための基本について説明しました。次は、このチュートリアルで作成した GetStartedWithData アプリケーションに基づく次のいずれかのチュートリアルを行うことをお勧めします。

* [サーバー スクリプトを使用したモバイル サービスのデータの検証および変更]
  <br/>モバイル サービスでサーバー スクリプトを使用して、アプリケーションから送信されたデータを検証および変更する方法について説明します。

* [ページングを使用したモバイル サービス クエリの改善]
  <br/>クエリ内でページングを使用して、単一の要求で渡されるデータの量を制御する方法について説明します。

データ シリーズを完了した後は、次に示す他のチュートリアルのいずれかを行うことをお勧めします。

* [認証の使用]
  <br/>アプリケーションのユーザーを認証する方法について説明します。

* [プッシュ通知の使用]
  <br/>アプリケーションにごく基本的なプッシュ通知を送信する方法について説明します。

* [モバイル サービス .NET の使用方法の概念リファレンス]
  <br/>.NET でモバイル サービスを使用する方法について説明します
  
<!-- Anchors. -->

[Windows ストア アプリの入手]: #download-app
[モバイル サービスの作成]: #create-service
[ストレージのデータ テーブルの追加]: #add-table
[モバイル サービスを使用するためのアプリケーションの更新]: #update-app
[モバイル サービスに対するアプリケーションのテスト]: #test-app
[次のステップ]:#next-steps

<!-- Images. -->
[0]: ./media/mobile-services-windows-store-dotnet-get-started-data-vs2012/mobile-quickstart-startup.png




[7]: ./media/mobile-services-windows-store-dotnet-get-started-data-vs2012/mobile-add-nuget-package-dotnet.png
[8]: ./media/mobile-services-windows-store-dotnet-get-started-data-vs2012/mobile-dashboard-tab.png
[9]: ./media/mobile-services-windows-store-dotnet-get-started-data-vs2012/mobile-todoitem-data-browse.png
[10]: ./media/mobile-services-windows-store-dotnet-get-started-data-vs2012/mobile-data-sample-download-dotnet.png


<!-- URLs. -->
[サーバー スクリプトを使用したモバイル サービスのデータの検証および変更]: /ja-jp/develop/mobile/tutorials/validate-modify-and-augment-data-dotnet
[ページングを使用したモバイル サービス クエリの改善]: /ja-jp/develop/mobile/tutorials/add-paging-to-data-dotnet
[モバイル サービスの使用]: /ja-jp/develop/mobile/tutorials/get-started
[データの使用]: /ja-jp/develop/mobile/tutorials/get-started-with-data-dotnet-vs2012
[認証の使用]: /ja-jp/develop/mobile/tutorials/get-started-with-users-dotnet
[プッシュ通知の使用]: /ja-jp/develop/mobile/tutorials/get-started-with-push-dotnet-vs2012/
[JavaScript と HTML]: /ja-jp/develop/mobile/tutorials/get-started-with-data-js-vs2012/

[Azure 管理ポータル]: https://manage.windowsazure.com/
[管理ポータル]: https://manage.windowsazure.com/
[モバイル サービス SDK]: http://go.microsoft.com/fwlink/?LinkId=257545
[デベロッパー サンプル コード集のサイト]:  http://go.microsoft.com/fwlink/?LinkId=262308
[モバイル サービス .NET の使用方法の概念リファレンス]: /ja-jp/develop/mobile/how-to-guides/work-with-net-client-library
