<properties linkid="mobile-services-how-to-xamarin-client" urlDisplayName="Xamarin" pageTitle="Xamarin コンポーネント クライアントの使用方法 - Azure モバイル サービスの機能ガイド" metaKeywords="Azure モバイル サービス, Xamarin, iOS, Android, .NET クライアント" description="Azure モバイル サービス向け Xamarin コンポーネント クライアントを使用する方法について説明します。" metaCanonical="" disqusComments="1" umbracoNaviHide="0" title="Azure モバイル サービス向け Xamarin クライアントを使用する方法" authors="" />

# Azure モバイル サービス向け Xamarin コンポーネント クライアントを使用する方法

<div class="dev-center-tutorial-selector sublanding"> 
  <a href="/ja-jp/develop/mobile/how-to-guides/work-with-net-client-library/" title=".NET Framework">.NET Framework</a>
  	<a href="/ja-jp/develop/mobile/how-to-guides/work-with-html-js-client/" title="HTML/JavaScript">HTML/JavaScript</a><a href="/ja-jp/develop/mobile/how-to-guides/work-with-ios-client-library/" title="iOS">iOS</a><a href="/ja-jp/develop/mobile/how-to-guides/work-with-android-client-library/" title="Android">Android</a><a href="/ja-jp/develop/mobile/how-to-guides/work-with-xamarin-client-library/" title="Xamarin" class="current">Xamarin</a>
</div>


このガイドでは、iOS および Android 用の Xamarin アプリで Azure モバイル サービス向け Xamarin コンポーネント クライアントを使用する一般的なシナリオを実行する方法について説明します。紹介するシナリオは、データの照会、挿入、更新、および削除、ユーザーの認証、エラー処理などです。モバイル サービスを初めて使用する場合は、まずモバイル サービスのクイック スタートに関するチュートリアル ([Xamarin.iOS][Xamarin.iOS quickstart tutorial]/[Xamarin.Android][Xamarin.Android quickstart tutorial]) と .NET でのデータの使用に関するチュートリアル ([Xamarin.iOS][Xamarin.iOS data tutorial]/[Xamarin.Android][Xamarin.Android data tutorial]) を完了することをお勧めします。このクイック スタート チュートリアルでは、[Xamarin][Xamarin download] と[モバイル サービス SDK] が必要です。また、このチュートリアルでは、アカウントの構成と初めてのモバイル サービスの作成がサポートされます。


## 目次

- [モバイル サービスとは]
- [概念]
- [方法: モバイル サービス クライアントを作成する]
- [方法: テーブル参照を作成する]
- [方法: モバイル サービスのデータを照会する]
	- [返されるデータをフィルター処理する]
    - [返されるデータを並べ替える]
	- [ページにデータを返す]
	- [特定の列を選択する]
	- [ID でデータを検索する]
- [方法: モバイル サービスにデータを挿入する]
- [方法: モバイル サービスのデータを変更する]
- [方法: モバイル サービスのデータを削除する]
- [方法: ユーザーを認証する]
- [方法: エラーを処理する]
- [方法: 型指定のないデータを処理する]
- [方法: 単体テストを設計する]
- [次のステップ]
	
[WACOM.INCLUDE [mobile-services-concepts](../includes/mobile-services-concepts.md)]

<h2><a name="setup"></a><span class="short-header">セットアップ</span>セットアップと前提条件</h2>

前提条件として、モバイル サービスとテーブルを作成してあるとします。詳細については、「[テーブルの作成](http://go.microsoft.com/fwlink/?LinkId=298592)」を参照してください。このトピックで使用するコードでは、テーブルの名前は `TodoItem` であり、`id`、`Text`、`Complete` という列があります。

これに対応する型指定されたクライアント側 .NET 型を次に示します。


	public class TodoItem
	{
		public string id { get; set; }

		[JsonProperty(PropertyName = "text")]
		public string Text { get; set; }

		[JsonProperty(PropertyName = "complete")]
		public bool Complete { get; set; }
	}
	
動的スキーマを有効にすると、挿入または更新の要求に含まれるオブジェクトに基づいて、Azure のモバイル サービスで自動的に新しい列が生成されます。詳細については、「[動的スキーマ](http://go.microsoft.com/fwlink/?LinkId=296271)」を参照してください。

<h2><a name="create-client"></a><span class="short-header">モバイル サービス クライアントの作成</span>方法: モバイル サービス クライアントの作成</h2>

次のコードは、モバイル サービスにアクセスするために使用される `MobileServiceClient` オブジェクトを生成します。
			
	MobileServiceClient client = new MobileServiceClient( 
		"AppUrl", 
		"AppKey" 
	); 

前のコードの `AppUrl` と `AppKey` を、モバイル サービスの URL とアプリケーション キーにそれぞれ置き換えます。どちらも Azure 管理ポータルで確認できます。モバイル サービスを選択し、[ダッシュボード] をクリックしてください。

<h2><a name="instantiating"></a><span class="short-header">テーブル参照の作成</span>方法: テーブル参照を作成する</h2>

モバイル サービスのテーブル データにアクセスするコードとそのデータを変更するコードは、必ず `MobileServiceTable` オブジェクトで関数を呼び出します。`MobileServiceClient` のインスタンスで [GetTable](http://msdn.microsoft.com/ja-jp/library/windowsazure/jj554275.aspx) 関数を呼び出して、テーブルへの参照を取得します。

    IMobileServiceTable<TodoItem> todoTable = 
		client.GetTable<TodoItem>();

これは型指定されたシリアル化のモデルです。後で示す<a href="#untyped">型指定のないシリアル化のモデルに関する説明</a>を参照してください。
			
<h2><a name="querying"></a><span class="short-header">データの照会</span>方法: モバイル サービスのデータを照会する</h2>

このセクションでは、モバイル サービスにクエリを発行する方法について説明します。サブセクションでは、並べ替え、フィルター処理、ページングなど、さまざまな処理について説明します。
			
### <a name="filtering"></a>方法: 返されるデータをフィルター処理する

次のコードは、クエリに `Where` 句を含めることによってデータをフィルター処理する方法を示しています。このコードは、`todoTable` から `Complete` プロパティが `false` になっているすべての項目を返します。`Where` 関数は、行のフィルタリング述語をテーブルに対するクエリに適用します。
	

	// This query filters out completed TodoItems and 
	// items without a timestamp. 
	List<TodoItem> items = await todoTable
	   .Where(todoItem => todoItem.Complete == false)
	   .ToListAsync();

ブラウザー開発者ツールや Fiddler などのメッセージ検査ソフトウェアを使用して、モバイル サービスに送信された要求の URI を表示できます。次の要求 URI を確認すると、クエリ文字列自体に変更を加えていることがわかります。

	GET /tables/todoitem?$filter=(complete+eq+false) HTTP/1.1				   
通常、この要求は、サーバー側で次のような SQL クエリに変換されます。
			
	SELECT * 
	FROM TodoItem 			
	WHERE ISNULL(complete, 0) = 0
			
`Where` メソッドに渡される関数には、任意の数の条件を設定できます。たとえば、次のコードがあるとします。

	// This query filters out completed TodoItems where Text isn't null
	List<TodoItem> items = await todoTable
	   .Where(todoItem => todoItem.Complete == false
		   && todoItem.Text != null)
	   .ToListAsync();

Would be roughly translated (for the same request shown before) as
			
	SELECT * 
	FROM TodoItem 
	WHERE ISNULL(complete, 0) = 0
	      AND ISNULL(text, 0) = 0

前の `where` ステートメントは、null 以外の `Text` を持ち、`Complete` のステータスが false に設定されている項目を検索します。

代わりに、次のように複数の行で記述することもできます。

	List<TodoItem> items = await todoTable
	   .Where(todoItem => todoItem.Complete == false)
	   .Where(todoItem => todoItem.Text != null)
	   .ToListAsync();

この 2 つの方法は等価であり、区別しないで使用できます。複数の述語を 1 つのクエリに連結する前者の方法が、よりコンパクトでありお勧めの方法です。

`where` 句は、モバイル サービスの OData サブセットに変換される操作をサポートします。これには、関係演算子 (==、! =、&lt;、&lt;=、&gt;、&gt;=)、算術演算子 (+、-、/、*、%)、数の精度 (Math.Floor、Math.Ceiling)、文字列関数 (Length、Substring、Replace、IndexOf、StartsWith、EndsWith)、日付プロパティ (Year、Month、Day、Hour、Minute、Second)、オブジェクトのアクセス プロパティ、これらすべてを組み合わせた式が含まれます。

### <a name="sorting"></a>方法: 返されるデータを並べ替える

次のコードでは、クエリに `OrderBy` 関数または `OrderByDescending` 関数を含めることによってデータを並べ替える方法を示します。次のコードは、`todoTable` から、`Text` フィールドの値に基づいて昇順に並べ替えられた項目を返します。既定では、サーバーは最初の 50 個の要素のみを返します。

<div class="dev-callout"><strong>注</strong> <p>すべての要素が返されるのを防ぐために、サーバー側で設定されたページ サイズが既定で使用されます。これにより、大きなデータ セットの既定の要求がサービスに悪影響を与えないようにします。</p> </div>

次のセクションで説明する `Take` を呼び出すことにより、返される項目の数を増やすことができます。

	// Sort items in ascending order by Text field
	MobileServiceTableQuery<TodoItem> query = todoTable
					.OrderBy(todoItem => todoItem.Text)       
 	List<TodoItem> items = await query.ToListAsync();

	// Sort items in descending order by Text field
	MobileServiceTableQuery<TodoItem> query = todoTable
					.OrderByDescending(todoItem => todoItem.Text)       
 	List<TodoItem> items = await query.ToListAsync();			

### <a name="paging"></a>方法: ページにデータを返す

次のコードでは、クエリで `Take` 句と `Skip` 句を使用して、返されたデータにページングを実装する方法を示しています。次のクエリを実行すると、テーブルの最初の上位 3 つの項目が返されます。

	// Define a filtered query that returns the top 3 items.
	MobileServiceTableQuery<TodoItem> query = todoTable
					.Take(3);                              
	List<TodoItem> items = await query.ToListAsync();

次の変更されたクエリは、最初の 3 つの結果をスキップし、その後の 3 つを返します。ページ サイズが 3 つの項目である場合、これは実質的にデータの 2 番目の "ページ" になります。

	// Define a filtered query that skips the top 3 items and returns the next 3 items.
	MobileServiceTableQuery<TodoItem> query = todoTable
					.Skip(3)
					.Take(3);                              
	List<TodoItem> items = await query.ToListAsync();
			
また、[IncludeTotalCount](http://msdn.microsoft.com/ja-jp/library/windowsazure/jj730933.aspx) メソッドを使用すると、指定された take paging/limit 句を無視して、返される<i>すべての</i>レコードの合計数を取得することができます。

	query = query.IncludeTotalCount();

これは、ハードコーディングされたページング値を `Take` メソッドおよび `Skip` メソッドに渡す、簡略化したシナリオです。実際のアプリケーションでは、ユーザーが前後のページに移動できるように、ページャー コントロールまたは同等の UI と共に上記と同様のクエリを使用することができます。

### <a name="selecting"></a>方法: 特定の列を選択する

クエリに `Select` 句を追加し、結果に含める一連のプロパティを指定できます。たとえば、次のコードでは、1 つのフィールドだけを選択する方法と、複数のフィールドを選択し、フォーマットする方法も示しています。

	// Select one field -- just the Text
	MobileServiceTableQuery<TodoItem> query = todoTable
					.Select(todoItem => todoItem.Text);
	List<string> items = await query.ToListAsync();
	
	// Select multiple fields -- both Complete and Text info
	MobileServiceTableQuery<TodoItem> query = todoTable
					.Select(todoItem => string.Format("{0} -- {1}", todoItem.Text.PadRight(30), todoItem.Complete ? "Now complete!" : "Incomplete!"));
	List<string> items = await query.ToListAsync();
			
これまでに説明した関数はいずれも付加的なものであるため、この後も呼び出すようにし、クエリに活用することにします。次の例も参照してください。

	MobileServiceTableQuery<TodoItem> query = todoTable
					.Where(todoItem => todoItem.Complete == false)
					.Select(todoItem => todoItem.Text)
					.Skip(3).
					.Take(3);
	List<string> items = await query.ToListAsync();
	
### <a name="lookingup"></a>方法: ID でデータを検索する

`LookupAsync` 関数を使うと、データベースから特定の ID を持つオブジェクトを検索することができます。

	// This query filters out the item with the ID of 25
	TodoItem item25 = await todoTable.LookupAsync(25);

<h2><a name="inserting"></a><span class="short-header">データの挿入</span>方法: モバイル サービスにデータを挿入する</h2>

<div class="dev-callout"><strong>注</strong> <p>型に対して挿入、検索、削除、更新の各操作を実行する場合は、どのような場合でも <strong>Id</strong> と呼ばれるメンバーを作成する必要があります。そのため、<strong>TodoItem</strong> クラスの例には、名前 <strong>ID</strong> のメンバーがあります。ID 値は、挿入操作時は、既定値以外に設定することはできません。それに対し、更新操作と削除操作では、ID 値を常に既定値以外に設定し、使用する必要があります。</p></div>

次のコードは、テーブルに新しい行を挿入する方法を示しています。パラメーターには、挿入するデータが .NET オブジェクトとして含まれます。

	await todoTable.InsertAsync(todoItem);

await `todoTable.InsertAsync` の呼び出しが戻ると、サーバーのオブジェクトの ID にクライアントの `todoItem` オブジェクトが設定されます。

型指定のないデータを挿入するために、次に示すように Json.NET を活用することもできます。繰り返しになりますが、オブジェクトを挿入する場合は ID を指定することはできません。

	JObject jo = new JObject(); 
	jo.Add("Text", "Hello World"); 
	jo.Add("Complete", false);
	var inserted = await table.InsertAsync(jo);

"Id" フィールドを設定して項目を取得しようとすると、サービスから `MobileServiceInvalidOperationException` が返されます。

<h2><a name="modifying"></a><span class="short-header">データの変更</span>方法: モバイル サービスのデータを変更する</h2>

次のコードは、同じ ID を持つ既存のインスタンスを新しい情報で更新する方法を示しています。パラメーターには、更新するデータが .NET オブジェクトとして含まれます。

	await todoTable.UpdateAsync(todoItem);


型指定のないデータを挿入するために、次のような Json.NET を活用することもできます。更新を行う場合は、更新対象のインスタンスをモバイル サービスが識別できるように、ID を指定する必要があることに注意してしてください。ID は、`InsertAsync` の呼び出しの結果から取得できます。

	JObject jo = new JObject(); 
	jo.Add("Id", 52);
	jo.Add("Text", "Hello World"); 
	jo.Add("Complete", false);
	var inserted = await table.UpdateAsync(jo);
			
"Id" フィールドを設定せずに項目を更新しようとすると、サービスが更新対象を識別できないため、サービスから `MobileServiceInvalidOperationException` が返されます。同様に、"Id" フィールドを設定せずに型指定されていない項目を更新しようとした場合も、サービスから `MobileServiceInvalidOperationException` が返されます。
			
			
<h2><a name="deleting"></a><span class="short-header">データの削除</span>方法: モバイル サービスのデータを削除する</h2>

次のコードは、既存のインスタンスを削除する方法を示しています。インスタンスは、`todoItem` に設定した "Id" フィールドで識別されます。

	await todoTable.DeleteAsync(todoItem);

型指定のないデータを削除するために、次のような Json.NET を活用することもできます。削除要求を行う場合は、削除対象のインスタンスをモバイル サービスが識別できるように、ID を指定する必要があることに注意してしてください。削除要求は ID のみを必要とします。他のプロパティはサービスに渡されません。そのいずれかが渡された場合、サービスに無視されます。`DeleteAsync` の呼び出しの結果も通常は `null` です。渡す ID は、`InsertAsync` の呼び出しの結果から取得できます。

	JObject jo = new JObject(); 
	jo.Add("Id", 52);
	await table.DeleteAsync(jo);
			
"Id" フィールドを設定せずに項目を削除しようとすると、サービスが削除対象を識別できないため、サービスから `MobileServiceInvalidOperationException` が返されます。同様に、"Id" フィールドを設定せずに型指定されていない項目を削除しようとした場合も、サービスから `MobileServiceInvalidOperationException` が返されます。
		


<h2><a name="authentication"></a><span class="short-header">認証</span>方法: ユーザーを認証する</h2>

モバイル サービスは、Facebook、Google、Microsoft アカウント、Twitter、Azure Active Directory などのさまざまな外部 ID プロバイダーを使用したアプリケーション ユーザーの認証と承認をサポートします。テーブルのアクセス許可を設定することにより、特定の操作へのアクセスを認証されたユーザーのみに制限できます。さらに、認証されたユーザーの ID を使用することにより、サーバー スクリプトで承認ルールを実装することもできます。詳細については、チュートリアル「認証の使用」([Xamarin.iOS][Xamarin.iOS authentication]/[Xamarin.Android][Xamarin.Android authentication]) を参照してください。

_サーバー フロー_と_クライアント フロー_という 2 つの認証フローがサポートされます。サーバー フローには、プロバイダーの Web 認証のインターフェイスを利用する、最も簡単な認証方法が用意されています。クライアント フローでは、プロバイダー固有とデバイス固有の SDK を利用することから、デバイス固有の機能との統合がさらに進みます。

<h3>サーバー フロー</h3>
モバイル サービスによって Windows ストア アプリまたは Windows Phone アプリの認証プロセスが管理されるようにするには、アプリケーションを ID プロバイダーに登録する必要があります。その後、モバイル サービス内で、プロバイダーから提供されたアプリケーション ID とシークレットを構成する必要があります。詳細については、チュートリアル「認証の使用」([Xamarin.iOS][Xamarin.iOS authentication]/[Xamarin.Android][Xamarin.Android authentication]) を参照してください。

ID プロバイダーを登録したら、[MobileServiceAuthenticationProvider] にプロバイダーの値を指定して [LoginAsync メソッド]を呼び出します。たとえば、次のコードは、Facebook を使用してサーバー フローのログインを開始します。

	private MobileServiceUser user;
	private async System.Threading.Tasks.Task Authenticate()
	{
		while (user == null)
		{
			string message;
			try
			{
				user = await client
					.LoginAsync(MobileServiceAuthenticationProvider.Facebook);
				message = 
					string.Format("You are now logged in - {0}", user.UserId);
			}
			catch (InvalidOperationException)
			{
				message = "You must log in. Login Required";
			}

			var dialog = new MessageDialog(message);
			dialog.Commands.Add(new UICommand("OK"));
			await dialog.ShowAsync();
		}
	}

Facebook 以外の ID プロバイダーを使用している場合は、上の [MobileServiceAuthenticationProvider] の値をプロバイダーに対応する値に変更してください。

この場合、モバイル サービスは、選択されたプロバイダーのログイン ページを表示し、ID プロバイダーでのログインが成功した後でモバイル サービス認証トークンを生成することで、OAuth 2.0 認証フローを管理します。[LoginAsync メソッド]は [MobileServiceUser] を返します。これによって、認証されたユーザーの [userId] と、JSON Web トークン (JWT) としての [MobileServiceAuthenticationToken] が提供されます。このトークンはキャッシュして、期限が切れるまで再利用することができます。詳細については、「[認証トークンをキャッシュする]」を参照してください。

<h3>クライアント フロー</h3>

アプリケーションは個別に ID プロバイダーにアクセスして、返されたトークンを認証のためにモバイル サービスに提供することもできます。このクライアント フローでは、ユーザーにシングル サインイン エクスペリエンスを提供したり、ID プロバイダーから追加のユーザー データを取得したりすることができます。

最も単純な形式では、この Facebook や Google のスニペットに示すようにクライアント フローを使用できます。

	var token = new JObject();
	// Replace access_token_value with actual value of your access token obtained 
	// using the Facebook or Google SDK.
	token.Add("access_token", "access_token_value");
			
	private MobileServiceUser user;
	private async System.Threading.Tasks.Task Authenticate()
	{
		while (user == null)
		{
			string message;
			try
			{
				// Change MobileServiceAuthenticationProvider.Facebook 
				// to MobileServiceAuthenticationProvider.Google if using Google auth.
				user = await client
					.LoginAsync(MobileServiceAuthenticationProvider.Facebook, token);
				message = 
					string.Format("You are now logged in - {0}", user.UserId);
			}
			catch (InvalidOperationException)
			{
				message = "You must log in. Login Required";
			}

			var dialog = new MessageDialog(message);
			dialog.Commands.Add(new UICommand("OK"));
			await dialog.ShowAsync();
		}
	}

<h3><a name="caching"></a>認証トークンをキャッシュする</h3>
場合によっては、最初のユーザー認証の後の login メソッドの呼び出しを避けることができます。ローカルのセキュリティで保護された格納場所 ([Xamarin.Auth][Xamarin.Auth component] など) を使用して、ユーザーが初めてログインするときに使用した現在のユーザー ID をキャッシュし、それ以降はユーザー ID がキャッシュに保存されているかどうかを確認します。キャッシュが空の場合は、ユーザーにログイン プロセスを実行してもらう必要があります。

	using Xamarin.Auth;
	var accountStore = AccountStore.Create(); // Xamarin.iOS
	//var accountStore = AccountStore.Create(this); // Xamarin.Android

	// After logging in
	var account = new Account (user.UserId, new Dictionary<string,string> {{"token",user.MobileServiceAuthenticationToken}});
	accountStore.Save(account, "Facebook");

	// Log in 
	var accounts = accountStore.FindAccountsForService ("Facebook").ToArray();
	if (accounts.Count != 0)
	{
		user = new MobileServiceUser (accounts[0].Username);
		user.MobileServiceAuthenticationToken = accounts[0].Properties["token"];
	}
	else
	{
		// Regular login flow
		user = new MobileServiceuser( await client
			.LoginAsync(MobileServiceAuthenticationProvider.Facebook, token);
		var token = new JObject();
		// Replace access_token_value with actual value of your access token
		token.Add("access_token", "access_token_value");
	}
			
	 // Log out
	client.Logout();
	accountStore.Delete(account, "Facebook");


<h2><a name="errors"></a><span class="short-header">エラー処理</span>方法: エラーを処理する</h2>

モバイル サービスには、エラーの検出、検証、回避のためのさまざまな方法があります。

たとえば、サーバー スクリプトは、モバイル サービスに登録され、挿入や更新が行われるデータでの広範な操作 (検証やデータの修正を含む) の実行に使用できます。次のようにデータを検証および変更するサーバー スクリプトを定義し、登録するとします。

	function insert(item, user, request) 
	{
	   if (item.text.length > 10) {
		  request.respond(statusCodes.BAD_REQUEST, { error: "Text cannot exceed 10 characters" });
	   } else {
		  request.execute();
	   }
	}

サーバー側スクリプトは、モバイル サービスに送信された文字列データの長さを検証し、長すぎる文字列 (この場合は 10 文字を超える) を拒否します。

モバイル サービスはサーバー側でデータを検証してエラー応答を送信するため、検証からのエラー応答を処理できるように .NET アプリケーションを更新することができます。

	private async void InsertTodoItem(TodoItem todoItem)
	{
		// This code inserts a new TodoItem into the database. When the operation completes
		// and Mobile Services has assigned an Id, the item is added to the CollectionView
		try
		{
			await todoTable.InsertAsync(todoItem);
			items.Add(todoItem);
		}
		catch (MobileServiceInvalidOperationException e)
		{
			// Handle error
		}
	}

<h2><a name="untyped"></a><span class="short-header">型指定されていないデータの操作</span>方法: 型指定されていないデータを操作する</h2>

Xamarin コンポーネント クライアントは厳密に型指定されたシナリオ向けに設計されます。ただし、大まかに型指定されたエクスペリエンスの方が便利であることもあります。たとえば、オープン スキーマでオブジェクトを処理する場合です。このシナリオには次のように対応できます。クエリでは、LINQ を使用せずに、ワイヤ形式を使用します。

	// Get an untyped table reference
	IMobileServiceTable untypedTodoTable = client.GetTable("TodoItem");			

	// Lookup untyped data using OData
	JToken untypedItems = await untypedTodoTable.ReadAsync("$filter=complete eq 0&$orderby=text");

プロパティ バッグのように使用できる JSON 値が返されます。JToken と Json.NET の詳細については、[Json.NET](http://json.codeplex.com/) を参照してください。

<h2><a name="unit-testing"></a><span class="short-header">テストの設計</span>方法: 単体テストを設計する</h2>

`MobileServiceClient.GetTable` とクエリによって返される値はインターフェイスです。そのためテスト目的で簡単にモックを作成できるので、テスト ロジックを実装する `MyMockTable : IMobileServiceTable<TodoItem>` を作成できます。

<h2><a name="nextsteps"></a>次のステップ</h2>

使用方法の概念リファレンス トピックを完了した後は、モバイル サービスで重要なタスクを実行する方法について詳しく確認してください。

* モバイル サービスの使用 ([Xamarin.iOS][Get started with Mobile Services iOS]/[Xamarin.Android][Get started with Mobile Services Android])
  <br/>モバイル サービスを使用する方法の基本について説明します。

* データの使用 ([Xamarin.iOS][Get started with data iOS]/[Xamarin.Android][Get started with data Android])
  <br/>モバイル サービスを使用してデータの格納およびクエリを実行する方法について説明します。

* 認証の使用 ([Xamarin.iOS][Get started with authentication iOS]/[Xamarin.Android][Get started with authentication Android])
  <br/>ID プロバイダーを使用してアプリケーションのユーザーを認証する方法について説明します。

* スクリプトを使用したデータの検証および変更 ([Xamarin.iOS][Validate and modify data with scripts iOS]/[Xamarin.Android][Validate and modify data with scripts Android])
  <br/>モバイル サービスでサーバー スクリプトを使用して、アプリケーションから送信されたデータを検証および変更する方法について説明します。

* ページングを使用したクエリの改善 ([Xamarin.iOS][Refine queries with paging iOS]/[Xamarin.Android][Refine queries with paging Android])
  <br/>クエリ内でページングを使用して、単一の要求で渡されるデータの量を制御する方法について説明します。

* スクリプトを使用したユーザーの認証 ([Xamarin.iOS][Authorize users with scripts iOS]/[Xamarin.Android][Authorize users with scripts Android])
  <br/>認証されたユーザーに基づいてモバイル サービスによって提供されるユーザー ID 値を受け取り、それを使用して、モバイル サービスから返されたデータをフィルター処理する方法について説明します。

<!-- Anchors. -->
[モバイル サービスとは]: #what-is
[概念]: #concepts
[方法: モバイル サービス クライアントを作成する]: #create-client
[方法: テーブル参照を作成する]: #instantiating
[方法: モバイル サービスのデータを照会する]: #querying
[返されるデータをフィルター処理する]: #filtering
[返されるデータを並べ替える]: #sorting
[ページにデータを返す]: #paging
[特定の列を選択する]: #selecting
[ID でデータを検索する]: #lookingup
[方法: モバイル サービスでユーザー インターフェイスにデータをバインドする]: #binding
[方法: モバイル サービスにデータを挿入する]: #inserting
[方法: モバイル サービスのデータを変更する]: #modifying
[方法: モバイル サービスのデータを削除する]: #deleting
[方法: ユーザーを認証する]: #authentication
[方法: エラーを処理する]: #errors
[方法: 単体テストを設計する]: #unit-testing 
[方法: モバイル サービスのデータを照会する]: #querying
[方法: クライアントをカスタマイズする]: #customizing
[方法: 型指定のないデータを処理する]: #untyped
[要求ヘッダーをカスタマイズする]: #headers
[シリアル化をカスタマイズする]: #serialization
[次のステップ]: #nextsteps
[認証トークンをキャッシュする]: #caching

<!-- URLs. -->
[Get started with Mobile Services iOS]: /ja-jp/develop/mobile/tutorials/get-started-xamarin-ios
[Get started with Mobile Services Android]: /ja-jp/develop/mobile/tutorials/get-started-xamarin-android
[Xamarin download]: http://xamarin.com/download/
[Mobile Services SDK]: http://go.microsoft.com/fwlink/?LinkId=257545
[Xamarin.iOS quickstart tutorial]: /ja-jp/develop/mobile/tutorials/get-started-xamarin-ios/
[Xamarin.Android quickstart tutorial]: /ja-jp/develop/mobile/tutorials/get-started-xamarin-android/
[Xamarin.iOS data tutorial]: /ja-jp/develop/mobile/tutorials/get-started-with-data-xamarin-ios/
[Xamarin.Android data tutorial]: /ja-jp/develop/mobile/tutorials/get-started-with-data-xamarin-android/
[Xamarin.iOS authentication]: /ja-jp/develop/mobile/tutorials/get-started-with-users-xamarin-ios/
[Xamarin.Android authentication]: /ja-jp/develop/mobile/tutorials/get-started-with-users-xamarin-android/
[Mobile Services SDK]: http://go.microsoft.com/fwlink/?LinkId=257545
[Xamarin.Auth component]: https://components.xamarin.com/view/xamarin.auth

[Mobile Services SDK]: http://nuget.org/packages/WindowsAzure.MobileServices/
[Get started with data iOS]: /ja-jp/develop/mobile/tutorials/get-started-with-data-xamarin-ios
[Get started with data Android]: /ja-jp/develop/mobile/tutorials/get-started-with-data-xamarin-android
[Get started with authentication iOS]: /ja-jp/develop/mobile/tutorials/get-started-with-users-xamarin-ios
[Get started with authentication Android]: /ja-jp/develop/mobile/tutorials/get-started-with-users-xamarin-android
[Validate and modify data with scripts ios]: /ja-jp/develop/mobile/tutorials/validate-modify-and-augment-data-xamarin-ios
[Validate and modify data with scripts android]: /ja-jp/develop/mobile/tutorials/validate-modify-and-augment-data-xamarin-android
[Refine queries with paging iOS]: /ja-jp/develop/mobile/tutorials/add-paging-to-data-xamarin-ios
[Refine queries with paging Android]: /ja-jp/develop/mobile/tutorials/add-paging-to-data-xamarin-android
[Authorize users with scripts iOS]: /ja-jp/develop/mobile/tutorials/authorize-users-in-scripts-xamarin-ios
[Authorize users with scripts Android]: /ja-jp/develop/mobile/tutorials/authorize-users-in-scripts-xamarin-android
[LoginAsync method]: http://msdn.microsoft.com/ja-jp/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceclientextensions.loginasync.aspx
[MobileServiceAuthenticationProvider]: http://msdn.microsoft.com/ja-jp/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceauthenticationprovider.aspx
[MobileServiceUser]: http://msdn.microsoft.com/ja-jp/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser.aspx
[UserID]: http://msdn.microsoft.com/ja-jp/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser.userid.aspx
[MobileServiceAuthenticationToken]: http://msdn.microsoft.com/ja-jp/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser.mobileserviceauthenticationtoken.aspx
