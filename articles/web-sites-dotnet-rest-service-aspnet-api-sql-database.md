<properties linkid="develop-dotnet-rest-service-using-web-api" urlDisplayName="Web API を使用する REST サービス" pageTitle="Web API を使用する REST サービス - Azure チュートリアル" metaKeywords="Azure チュートリアル Web サイト, ASP.NET API Web サイト, Azure VS" description="Visual Studio により ASP.NET Web API を使用するアプリケーションを Azure の Web サイトに展開する方法を示すチュートリアル。" metaCanonical="" services="web-sites" documentationCenter=".NET" title="ASP.NET Web API と SQL データベースを使用した REST サービス" authors="riande" solutions="" manager="" editor="" />




# ASP.NET Web API と SQL データベースを使用した REST サービス

***執筆: [Rick Anderson](https://twitter.com/RickAndMSFT) および Tom Dykstra。更新: 2014 年 3 月***

このチュートリアルでは、Visual Studio 2013 または Visual Studio 2013 for Web Express の Web の発行ウィザードを使用して、ASP.NET Web アプリケーションを Azure の Web サイトに展開する方法を示します (Visual Studio 2012 を使用する場合は、[このチュートリアルの前のバージョン](/ja-jp/develop/net/tutorials/get-started-vs2012/)を参照)。

Azure アカウントは無料で開くことができます。また、まだ Visual Studio 2013 を持っていない場合は、SDK によって Visual Studio 2013 for Web Express が自動的にインストールされます。これで、Azure 向けの開発を完全に無料で始めることができます。

このチュートリアルは、Azure を使用した経験がない読者を対象に作成されています。このチュートリアルでは、クラウドで動作する単純な Web アプリケーションを作成します。
 
学習内容: 

* Azure SDK をインストールして、Azure 向け開発用にコンピューターを準備する方法
* Visual Studio の ASP.NET MVC 5 プロジェクトを作成して Azure の Web サイトに発行する方法
*  ASP.NET Web API を使用して REST ベースの API 呼び出しを可能にする方法
*  SQL データベースを使用して Azure にデータを保存する方法
* Azure にアプリケーションの更新を発行する方法

ASP.NET MVC 5 に基づく、データベース アクセスに ADO.NET Entity Framework を使用する、簡単な連絡先リスト Web アプリケーションをビルドします。次の図に、完成したアプリケーションを示します。

![Web サイトのスクリーンショット][intro001]
このチュートリアルの内容: 

* [開発環境を設定する][setupdbenv]
* [Azure 環境を設定する][setupwindowsazureenv]
* [ASP.NET MVC 5 アプリケーションを作成する][createapplication]
* [Azure にアプリケーションを展開する][deployapp1]
* [アプリケーションにデータベースを追加する][adddb]
* [データのコントローラーとビューを追加する][addcontroller]
* [Web API を使用する REST ベースのインターフェイスを追加する][addwebapi]
* [XSRF 保護を追加する][]
* [Azure および SQL データベースにアプリケーションの更新を発行する][deploy2]

<a name="bkmk_setupdevenv"></a>
<!-- the next line produces the "Set up the development environment" section as see at http://www.windowsazure.com/ja-jp/documentation/articles/web-sites-dotnet-get-started/ -->
[WACOM.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

<h2><a name="bkmk_setupwindowsazure"></a>Azure 環境を設定する</h2>

次のステップでは、Azure の Web サイトと SQL データベースを作成することで Azure 環境をセットアップします。

### Azure で Web サイトと SQL データベースを作成する

次のステップでは、アプリケーションで使用する Azure の Web サイトと SQL データベースを作成します。

Azure の Web サイトは、共有ホスティング環境で実行されます。つまり、他の Azure クライアントと共有する仮想マシン (VM) 上で実行されます。共有ホスティング環境は、低コストでクラウドの利用を開始できる方法です。後で Web トラフィックが増加したら、アプリケーションの規模を変更して専用 VM 上で実行するように設定してニーズを満たすことができます。もっと複雑なアーキテクチャが必要な場合は、Azure のクラウド サービスに移行できます。クラウド サービスは専用 VM 上で実行され、ユーザーのニーズに応じて構成できます。

SQL データベースは、SQL Server テクノロジに基づいて構築されたクラウドベースのリレーショナル データベース サービスです。SQL Server で動作するツールおよびアプリケーションは、SQL データベースでも動作します。

1. [Azure 管理ポータル](https://manage.windowsazure.com)で、左側のタブにある **[Web サイト]** をクリックし、**[新規]** をクリックします。

2. **[カスタム作成]** をクリックします。

	![管理ポータルの [データベースとともに作成] リンク](./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rr6.PNG)

	**新しい Web Site - カスタム作成**ウィザードが開きます。

3. ウィザードの **[新しい Web サイトを作成する]** 手順で、アプリケーションの一意の URL として使用する文字列を **[URL]** ボックスに入力します。URL 全体は、ここで入力した内容に、テキスト ボックスの下に表示されるサフィックスを追加して構成されます。図には "contactmgr11" と表示されていますが、その URL は既に取得されている可能性が高いため、別の URL の選択が必要になります。

1. **[リージョン]** ボックスの一覧で、現在の所在地に最も近いリージョンを選択します。

1. **[データベース]** ボックスの一覧の **[無料の 20 MB SQL データベースの作成]** を選択します。

	![新しい Web サイト - データベースとともに作成ウィザードの新しい Web サイトを作成する手順](./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rrCWS.png)

6. ボックスの下部にある右矢印をクリックします。

	ウィザードの **[データベースの設定]** 手順に進みます。

7. **[名前]** ボックスに「*ContactDB*」と入力します。

8. **[サーバー]** ボックスで、**[新しい SQL データベース サーバー]** を選択します。または、以前に SQL Server データベースを作成した場合は、ボックスの一覧からその SQL Server を選択できます。

9. ボックスの下部にある右矢印をクリックします。

10. 管理者の**ログイン名**と**パスワード**を入力します。**[新しい SQL データベース サーバー]** を選択した場合は、既存の名前とパスワードではなく、このデータベースへのアクセス時に使用する新しい名前とパスワードを入力してください。以前に作成した SQL Server を選択した場合は、その SQL Server の作成時に設定したパスワードを入力します。このチュートリアルでは、**[データベースの詳細設定を構成します]** チェック ボックスをオンにしません。**詳細設定**では、DB サイズ (既定値は 1 GB ですが 150 GB まで拡張可能) と照合順序を指定できます。

11. 終了したら、ダイアログ ボックスの下部にあるチェック マークをクリックします。

	![新しい Web Site -  データベースとともに作成ウィザードのデータベースの設定手順][setup007]

	 次の画像では、既存の SQL Server を選択した場合のログインを示しています。
	
	![新しい Web Site -  データベースとともに作成ウィザードのデータベースの設定手順][rxPrevDB]

	管理ポータルが [Web サイト] ページに戻り、**[状態]** 列にサイトが作成中であることが示されます。しばらくすると (通常は 1 分未満)、サイトの作成に成功したことが **[状態]** 列に示されます。左側にあるナビゲーション バーでは、アカウントで所有するサイト数が **[Web サイト]** アイコンの横に表示され、データベース数が **[SQL データベース]** アイコンの横に表示されます。

<!-- [Web Sites page of Management Portal, web site created][setup009] -->

<h2><a name="bkmk_createmvc4app"></a>ASP.NET MVC 5 アプリケーションを作成する</h2>

Azure の Web サイトを作成しましたが、まだその中にコンテンツがありません。次の手順では、Azure に発行する Visual Studio Web アプリケーション プロジェクトを作成します。

### プロジェクトを作成する

1. Visual Studio 2013 を起動します。
1. **[ファイル]** メニューの **[新しいプロジェクト]** をクリックします。
3. **[新しいプロジェクト]** ダイアログ ボックスで、**[Visual C#]** を展開して **[Web]** を選択し、**[ASP.NET MVC 5 Web アプリケーション]** を選択します。既定の **[.NET Framework 4.5]** をそのまま使用します。アプリケーションに「**ContactManager**」という名前を付けて、**[OK]** をクリックします。
	![[新しいプロジェクト] ダイアログ ボックス](./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rr4.PNG)]
1. **[新しい ASP.NET プロジェクト]** ダイアログ ボックスで、**[MVC]** テンプレートを選択し、**[Web API]** チェック ボックスをオンにして、**[認証の変更]** をクリックします。

	![[新しい ASP.NET プロジェクト] ダイアログ ボックス](./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rt3.PNG)

1. **[認証の変更]** ダイアログ ボックスで、**[認証なし]** をクリックし、**[OK]** をクリックします。

	![[認証なし]](./media/web-sites-dotnet-get-started-vs2013/GS13noauth.png)

	作成中のサンプル アプリケーションには、ユーザーのログインが必要な機能は実装されません。認証と承認の機能を実装する方法については、このチュートリアルの最後にある「[次のステップ](#nextsteps)」セクションを参照してください。

1. **[新しい ASP.NET プロジェクト]** ダイアログ ボックスで **[OK]** をクリックします。

	![[新しい ASP.NET プロジェクト] ダイアログ ボックス](./media/web-sites-dotnet-get-started-vs2013/GS13newaspnetprojdb.png)

### ページのヘッダーとフッターを設定する


1. **ソリューション エクスプローラー**で、*Views\Shared* フォルダーを展開し、*_Layout.cshtml* ファイルを開きます。

	![_ソリューション エクスプローラーに表示されている Layout.cshtml ファイル][newapp004]

1. *_Layout.cshtml* ファイルの内容を次のコードに置き換えます。


		<!DOCTYPE html>
		<html lang="en">
		<head>
		    <meta charset="utf-8" />
		    <title>@ViewBag.Title - Contact Manager</title>
		    <link href="~/favicon.ico" rel="shortcut icon" type="image/x-icon" />
		    <meta name="viewport" content="width=device-width" />
		    @Styles.Render("~/Content/css")
		    @Scripts.Render("~/bundles/modernizr")
		</head>
		<body>
		    <header>
		        <div class="content-wrapper">
		            <div class="float-left">
		                <p class="site-title">@Html.ActionLink("Contact Manager", "Index", "Home")</p>
		            </div>
		        </div>
		    </header>
		    <div id="body">
		        @RenderSection("featured", required: false)
		        <section class="content-wrapper main-content clear-fix">
		            @RenderBody()
		        </section>
		    </div>
		    <footer>
		        <div class="content-wrapper">
		            <div class="float-left">
		                <p>&copy; @DateTime.Now.Year - Contact Manager</p>
		            </div>
		        </div>
		    </footer>
		    @Scripts.Render("~/bundles/jquery")
		    @RenderSection("scripts", required: false)
		</body>
		</html>
			
前に示しているマークアップにより、アプリケーション名が "My ASP.NET App" から "Contact Manager" に変更され、**Home**、**About**、**Contact** へのリンクが削除されます。

### ローカルでアプリケーションを実行する

1. Ctrl + F5 キーを押して、アプリケーションを実行します。
アプリケーションのホーム ページが既定のブラウザーに表示されます。
	![To Do List のホーム ページ](./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rr5.PNG)

これで、Azure に展開するアプリケーションを作成するために必要な操作が完了しました。データベース機能は後で追加します。

<h2><a name="bkmk_deploytowindowsazure1"></a>Azure にアプリケーションを展開する</h2>

1. Visual Studio の**ソリューション エクスプローラー**で、プロジェクトを右クリックし、コンテキスト メニューの **[発行]** をクリックします。

	![プロジェクトのコンテキスト メニューの [発行]][PublishVSSolution]

	**Web の発行**ウィザードが開きます。

2. **Web の発行**ウィザードの **[プロファイル]** タブで、**[インポート]** をクリックします。

	![発行設定のインポート][ImportPublishSettings]

	**[発行プロファイルのインポート]** ダイアログ ボックスが表示されます。

 3.	Azure の Web サイトで [インポート] を選択します。まだサインインしていない場合は、まずサインインする必要があります。**[サインイン]** をクリックします。サブスクリプションに関連付けられているユーザーを入力し、サインインの手順に従います。

	![サインイン](./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rr7.png)

	ドロップダウン リストから Web サイトを選択し、**[OK]** をクリックします。

	![発行プロファイルのインポート](./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rr8.png)

8. **[接続]** タブの **[接続の検証]** をクリックし、設定が正しいことを確認します。

	![接続の検証][ValidateConnection]

9. 接続が検証されると、**[接続の検証]** ボタンの横に緑色のチェック マークが表示されます。

	![[接続] タブの [接続成功] アイコンと [次へ] ボタン][firsdeploy007]

1. **[次へ]** をクリックします。

	![[設定] タブ](./media/web-sites-dotnet-get-started-vs2013/GS13SettingsTab.png)

	このタブでは、既定の設定をそのまま使用できます。リリース ビルド構成を展開しているため、展開先サーバーでファイルを削除したり、アプリケーションをプリコンパイルしたり、App_Data フォルダーでファイルを除外したりする必要はありません。Azure のライブ サイトでデバッグする場合は、デバッグ構成をリリースではなく展開する必要があります。このチュートリアルの末尾にある「[次のステップ](#nextsteps)」を参照してください。

12. **[プレビュー]** タブで、**[プレビューの開始]** をクリックします。

	このタブに、サーバーにコピーされるファイルの一覧が表示されます。プレビューの表示は、アプリケーションの発行に必要ではありませんが、知っておくと便利な機能です。この場合、表示されるファイルの一覧で操作を行う必要はありません。次に発行するときは、変更されたファイルだけがプレビュー一覧に示されます。

	![[プレビュー] タブの [プレビューの開始] ボタン][firsdeploy009]

12. **[発行]** をクリックします。

	Azure サーバーにファイルをコピーする処理が開始されます。**出力**ウィンドウでは、実行された展開操作が表示され、展開が問題なく完了したことが報告されます。

14. 自動的に既定のブラウザーが開き、展開先のサイトの URL にアクセスします。

	これで、作成したアプリケーションはクラウドで実行されています。
	
	![Azure で実行されている連絡先リストのホーム ページ][rxz2]

<h2><a name="bkmk_addadatabase"></a>アプリケーションにデータベースを追加する</h2>

次に、MVC アプリケーションを更新して、連絡先を表示および更新してデータをデータベースに保存する機能を追加します。アプリケーションでは、データベースの作成およびデータベース内のデータの読み取りと更新に Entity Framework を使用します。

### 連絡先のデータ モデル クラスを追加する

まず、コードで単純なデータ モデルを作成します。

1. **ソリューション エクスプローラー**で、Models フォルダーを右クリックし、**[追加]**、**[クラス]** の順にクリックします。

	![Models フォルダーのコンテキスト メニューの [クラスの追加]][adddb001]

2. **[新しい項目の追加]** ダイアログ ボックスで、新しいクラス ファイルに「*Contact.cs*」という名前を付け、**[追加]** をクリックします。

	![[新しい項目の追加] ダイアログ ボックス][adddb002]

3. Contacts.cs ファイルの内容を次のコードに置き換えます。

		using System.Globalization;
		namespace ContactManager.Models
		{
    		public class Contact
   			{
        		public int ContactId { get; set; }
				public string Name { get; set; }
				public string Address { get; set; }
	        	public string City { get; set; }
				public string State { get; set; }
				public string Zip { get; set; }
				public string Email { get; set; }
				public string Twitter { get; set; }
				public string Self
        		{
            		get { return string.Format(CultureInfo.CurrentCulture,
				         "api/contacts/{0}", this.ContactId); }
            		set { }
        		}
    		}
		}

**Contacts** クラスでは、各連絡先について保存するデータと、データベースが必要とする主キー (ContactID) を定義します。データ モデルの詳細については、このチュートリアルの末尾にある「[次のステップ](#nextsteps)」を参照してください。

### アプリケーション ユーザーが連絡先を操作できる Web ページを作成する

ASP.NET MVC では、スキャフォールディング機能によって、作成、読み取り、更新、および削除 (CRUD) の各操作を実行するコードを自動的に生成できます。

<h2><a name="bkmk_addcontroller"></a>データのコントローラーとビューを追加する</h2>

1. **ソリューション エクスプローラー**で、Controllers フォルダーを展開します。

3. プロジェクトをビルドします **(Ctrl + Shift + B)**。(スキャフォールディング機能の使用前にプロジェクトをビルドする必要があります。)

4. Controllers フォルダーを右クリックし、**[追加]**、**[コントローラー]** の順にクリックします。

	![Controllers フォルダーのコンテキスト メニューの [コントローラーの追加]][addcode001]

1. **[スキャフォールディングの追加]** ダイアログ ボックスで、**[Entity Framework を使用した、ビューのある MVC コントローラー]** を選択し、**[追加]** をクリックします。

 ![コントローラーの追加](./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rrAC.PNG)

6. コントローラー名を **HomeController** に設定します。モデル クラスとして **[Contact]** を選択します。**[新しいデータ コンテキスト]** ボタンをクリックし、**[新しいデータ コンテキストの種類]** で既定の "ContactManager.Models.ContactManagerContext" をそのまま使用します。**[追加]** をクリックします。

	![[コントローラーの追加] ダイアログ ボックス](./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rr9.PNG)

	ダイアログ ボックスで、"HomeController という名前のファイルは既に存在します。ファイルを置き換えますか?" とたずねられます。**[はい]** をクリックします。新しいプロジェクトで作成した home コントローラーで上書きされます。連絡先リストに新しい home コントローラーが使用されるようになります。

	Visual Studio によって、**Contact** オブジェクトの CRUD データベース操作に対応したコントローラー メソッドとビューが作成されます。

## Migrations の有効化、データベースの作成、サンプル データとデータ初期化子の追加##

次の作業では、作成したデータ モデルに基づいてデータベースを作成するために、[Code First Migrations](http://curah.microsoft.com/55220) 機能を有効にします。

1. **[ツール]** メニューの **[ライブラリ パッケージ マネージャー]**、**[パッケージ マネージャー コンソール]** の順に選択します。

	![[ツール] メニューの [パッケージ マネージャー コンソール]][addcode008]

2. **[パッケージ マネージャー コンソール]** ウィンドウで、次のコマンドを入力します。

		enable-migrations 
  
	**enable-migrations** コマンドによって *Migrations* フォルダーが作成され、そのフォルダーに *Configuration.cs* ファイルが保存されます。このファイルを編集して Migration を構成できます。

2. **[パッケージ マネージャー コンソール]** ウィンドウで、次のコマンドを入力します。

		add-migration Initial

	**add-migration Initial** コマンドによって、**&lt;date_stamp&gt;Initial** という名前のクラスが生成されて、データベースの作成に使用されます。最初のパラメーター (*Initial*) は任意であり、このファイルの名前の作成に使用されます。新しいクラス ファイルは**ソリューション エクスプローラー**で表示できます。

	**Initial** クラスでは、**Up** メソッドを使用して Contacts テーブルを作成し、**Down** メソッドを使用してそのテーブルを削除します (前の状態に戻します)。

3.  *Migrations\Configuration.cs* ファイルを開きます。

4.  次の名前空間を追加します。

    	 using ContactManager.Models;

5. *Seed* メソッドを次のコードに置き換えます。
		
        protected override void Seed(ContactManager.Models.ContactManagerContext context)
        {
            context.Contacts.AddOrUpdate(p => p.Name,
               new Contact
               {
                   Name = "Debra Garcia",
                   Address = "1234 Main St",
                   City = "Redmond",
                   State = "WA",
                   Zip = "10999",
                   Email = "debra@example.com",
                   Twitter = "debra_example"
               },
                new Contact
                {
                    Name = "Thorsten Weinrich",
                    Address = "5678 1st Ave W",
                    City = "Redmond",
                    State = "WA",
                    Zip = "10999",
                    Email = "thorsten@example.com",
                    Twitter = "thorsten_example"
                },
                new Contact
                {
                    Name = "Yuhong Li",
                    Address = "9012 State st",
                    City = "Redmond",
                    State = "WA",
                    Zip = "10999",
                    Email = "yuhong@example.com",
                    Twitter = "yuhong_example"
                },
                new Contact
                {
                    Name = "Jon Orton",
                    Address = "3456 Maple St",
                    City = "Redmond",
                    State = "WA",
                    Zip = "10999",
                    Email = "jon@example.com",
                    Twitter = "jon_example"
                },
                new Contact
                {
                    Name = "Diliana Alexieva-Bosseva",
                    Address = "7890 2nd Ave E",
                    City = "Redmond",
                    State = "WA",
                    Zip = "10999",
                    Email = "diliana@example.com",
                    Twitter = "diliana_example"
                }
                );
        }

	このコードでは、連絡先情報を使用してデータベースを初期化します。Seed メソッドによるデータベースへのデータの登録の詳細については、「[Debugging Entity Framework (EF) DBs (Entity Framework (EF) DB のデバッグ)](http://blogs.msdn.com/b/rickandy/archive/2013/02/12/seeding-and-debugging-entity-framework-ef-dbs.aspx)」を参照してください。


1. **[パッケージ マネージャー コンソール]** で、次のコマンドを入力します。

		update-database

	![パッケージ マネージャー コンソールのコマンド][addcode009]

	**update-database** によって、データベースを作成する最初の Migration が実行されます。既定では、データベースは SQL Server Express LocalDB データベースとして作成されます 

1. Ctrl + F5 キーを押して、アプリケーションを実行します。

アプリケーションでは、登録されたデータが表示され、編集、詳細、削除のリンクが示されます。

![データの MVC ビュー][rxz3]

<h2><a name="bkmk_addview"></a>ビューの編集</h2>

1. *Views\Home\Index.cshtml* ファイルを開きます。次の手順では、生成されたマークアップを、[jQuery](http://jquery.com/) と [Knockout.js](http://knockoutjs.com/) を使用するコードに置き換えます。この新しいコードは、Web API と JSON を使用して連絡先リストを取得し、knockout.js を使用して連絡先データを UI にバインドします。詳細については、このチュートリアルの末尾にある「[次のステップ](#nextsteps)」を参照してください。


2. ファイルの内容を次のコードに置き換えます。

		@model IEnumerable<ContactManager.Models.Contact>
		@{
		    ViewBag.Title = "Home";
		}
		@section Scripts {
		    @Scripts.Render("~/bundles/knockout")
		    <script type="text/javascript">
		        function ContactsViewModel() {
		            var self = this;
		            self.contacts = ko.observableArray([]);
		            self.addContact = function () {
		                $.post("api/contacts",
		                    $("#addContact").serialize(),
		                    function (value) {
		                        self.contacts.push(value);
		                    },
		                    "json");
		            }
		            self.removeContact = function (contact) {
		                $.ajax({
		                    type: "DELETE",
		                    url: contact.Self,
		                    success: function () {
		                        self.contacts.remove(contact);
		                    }
		                });
		            }

		            $.getJSON("api/contacts", function (data) {
		                self.contacts(data);
		            });
		        }
		        ko.applyBindings(new ContactsViewModel());	
		</script>
		}
		<ul id="contacts" data-bind="foreach: contacts">
		    <li class="ui-widget-content ui-corner-all">
		        <h1 data-bind="text: Name" class="ui-widget-header"></h1>
		        <div><span data-bind="text: $data.Address || 'Address?'"></span></div>
		        <div>
		            <span data-bind="text: $data.City || 'City?'"></span>,
		            <span data-bind="text: $data.State || 'State?'"></span>
		            <span data-bind="text: $data.Zip || 'Zip?'"></span>
		        </div>
		        <div data-bind="if: $data.Email"><a data-bind="attr: { href: 'mailto:' + Email }, text: Email"></a></div>
		        <div data-bind="ifnot: $data.Email"><span>Email?</span></div>
		        <div data-bind="if: $data.Twitter"><a data-bind="attr: { href: 'http://twitter.com/' + Twitter }, text: '@@' + Twitter"></a></div>
		        <div data-bind="ifnot: $data.Twitter"><span>Twitter?</span></div>
		        <p><a data-bind="attr: { href: Self }, click: $root.removeContact" class="removeContact ui-state-default ui-corner-all">Remove</a></p>
		    </li>
		</ul>
		<form id="addContact" data-bind="submit: addContact">
		    <fieldset>
		        <legend>Add New Contact</legend>
		        <ol>
		            <li>
		                <label for="Name">Name</label>
		                <input type="text" name="Name" />
		            </li>
		            <li>
		                <label for="Address">Address</label>
		                <input type="text" name="Address" >
		            </li>
		            <li>
		                <label for="City">City</label>
		                <input type="text" name="City" />
		            </li>
		            <li>
		                <label for="State">State</label>
		                <input type="text" name="State" />
		            </li>
		            <li>
		                <label for="Zip">Zip</label>
		                <input type="text" name="Zip" />
		            </li>
		            <li>
		                <label for="Email">E-mail</label>
		                <input type="text" name="Email" />
		            </li>
		            <li>
		                <label for="Twitter">Twitter</label>
		                <input type="text" name="Twitter" />
		            </li>
		        </ol>
		        <input type="submit" value="Add" />
		    </fieldset>
		</form>

3. Content フォルダーを右クリックし、**[追加]** をクリックして、**[新しい項目]** をクリックします。

	![Content フォルダーのコンテキスト メニューの [スタイル シートの追加]][addcode005]

4. **[新しい項目の追加]** ダイアログ ボックスで、右上の検索ボックスに「**スタイル**」と入力し、[**スタイル シート**] を選択します。
	![[新しい項目の追加] ダイアログ ボックス][rxStyle]

5. ファイルに *Contacts.css* という名前を付け、**[追加]** をクリックします。このファイルの内容を次のコードに置き換えます。
    
        .column {
            float: left;
            width: 50%;
            padding: 0;
            margin: 5px 0;
        }
        form ol {
            list-style-type: none;
            padding: 0;
            margin: 0;
        }
        form li {
            padding: 1px;
            margin: 3px;
        }
        form input[type="text"] {
            width: 100%;
        }
        #addContact {
            width: 300px;
            float: left;
            width:30%;
        }
        #contacts {
            list-style-type: none;
            margin: 0;
            padding: 0;
            float:left;
            width: 70%;
        }
        #contacts li {
            margin: 3px 3px 3px 0;
            padding: 1px;
            float: left;
            width: 300px;
            text-align: center;
            background-image: none;
            background-color: #F5F5F5;
        }
        #contacts li h1
        {
            padding: 0;
            margin: 0;
            background-image: none;
            background-color: Orange;
            color: White;
            font-family: Trebuchet MS, Tahoma, Verdana, Arial, sans-serif;
        }
        .removeContact, .viewImage
        {
            padding: 3px;
            text-decoration: none;
        }

	このスタイル シートは Contact Manager アプリケーションで使用されるレイアウト、色、スタイルに適用されます。

6. *App_Start\BundleConfig.cs* ファイルを開きます。


7. 次のコードを追加して [Knockout](http://knockoutjs.com/index.html "KO") プラグインを登録します。

		bundles.Add(new ScriptBundle("~/bundles/knockout").Include(
		            "~/Scripts/knockout-{version}.js"));
	このサンプルでは Knockout を使用して、画面テンプレートを処理する動的な JavaScript コードを簡略化します。

8. contents/css エントリを変更して *contacts.css* スタイル シートを登録します。次の行を変更します。

                 bundles.Add(new StyleBundle("~/Content/css").Include(
                   "~/Content/bootstrap.css",
                   "~/Content/site.css"));
変更後:

        bundles.Add(new StyleBundle("~/Content/css").Include(
                   "~/Content/bootstrap.css",
                   "~/Content/contacts.css",
                   "~/Content/site.css"));

1. パッケージ マネージャー コンソールで、次のコマンドを実行して Knockout をインストールします。

	Install-Package knockoutjs

<h2><a name="bkmk_addwebapi"></a>Web API を使用する REST ベースのインターフェイスに対応したコントローラーを追加する</h2>

1. **ソリューション エクスプローラー**で、Controllers フォルダーを右クリックし、**[追加]**、**[コントローラー]** の順にクリックします。

1. **[スキャフォールディングの追加]** ダイアログ ボックスで、「**Entity Framework を使用した、読み取り/書き込み操作のある Web API 2 コントローラー**」と入力し、**[追加]** をクリックします。

	![API コントローラーの追加](./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rt1.PNG)

4. **[コントローラーの追加]** ダイアログ ボックスで、コントローラー名として「ContactsController」と入力します。**[モデル クラス]** で [Contact (ContactManager.Models)] を選択します。**[データ コンテキスト クラス]** で既定値をそのまま使用します。

6. **[追加]** をクリックします。

### ローカルでアプリケーションを実行する

1. Ctrl + F5 キーを押して、アプリケーションを実行します。

	![Index ページ][intro001]

2. 連絡先を入力し、**[Add]** をクリックします。アプリケーションはホーム ページに戻り、入力した連絡先が表示されます。

	![連絡先が表示された Index ページ][addwebapi004]

3. ブラウザーで **/api/contacts** を URL に追加します。

	追加後の URL は http://localhost:1234/api/contacts のようになります。追加した REST ベースの Web API によって、保存されている連絡先が返されます。Firefox と Chrome では、それらのデータが XML 形式で表示されます。

	![連絡先が表示された Index ページ][rxFFchrome]
	

	IE では、連絡先を開くか保存するように求められます。

	![Web API の保存確認のダイアログ ボックス][addwebapi006]
	
	
	返された連絡先はメモ帳やブラウザーで開くことができます。
	
	この出力は、モバイルの Web ページやアプリケーションのような別のアプリケーションで使用できます。

	![Web API の保存確認のダイアログ ボックス][addwebapi007]

	**セキュリティ警告**: この時点で、アプリケーションは安全ではなく CSRF 攻撃に対して脆弱です。このチュートリアルの後半では、この脆弱性を排除します。詳細については、「[クロスサイト リクエスト フォージェリ (CSRF) 攻撃の防止][prevent-csrf-attacks]」を参照してください。

<h2><a name="xsrf"></a><span class="short-header">XSRF</span>XSRF 保護を追加する</h2>

クロスサイト リクエスト フォージェリ (XSRF または CSRF) は、Web でホストされるアプリケーションに対する攻撃であり、それによって悪意のある Web サイトが、クライアント ブラウザーとそのブラウザーが信頼する Web サイトの間のやり取りに影響を及ぼすことができます。これらの攻撃が可能になるのは、要求ごとに Web ブラウザーが自動的に認証トークンを Web サイトに送信するからです。標準的な例は、ASP.NET のフォーム認証チケットなどの認証クッキーです。ただし、永続的な認証メカニズム (Windows 認証や基本認証など) を使用する Web サイトも、これらの攻撃の対象になることがあります。

XSRF 攻撃はフィッシング攻撃とは異なります。フィッシング攻撃には攻撃対象とのやり取りが必要です。フィッシング攻撃では、悪意のある Web サイトがターゲット Web サイトを模擬し、攻撃対象は重要な情報を攻撃者に提供するようにだまされます。XSRF 攻撃では、多くの場合に攻撃対象とのやり取りは必要ありません。むしろ攻撃者が利用するのは、ブラウザーがすべての関連クッキーを模擬 Web サイトに自動的に送信することです。

詳細については、「[Open Web Application Security Project (OWASP)](https://www.owasp.org/index.php/Main_Page)」および「[XSRF (CSRF)](https://www.owasp.org/index.php/Cross-Site_Request_Forgery_(CSRF)」を参照してください。

1. **ソリューション エクスプローラー**で、**[ContactManager]** プロジェクトを右クリックし、**[追加]**、**[クラス]** の順にクリックします。

2. ファイルに「*ValidateHttpAntiForgeryTokenAttribute.cs*」という名前を付け、次のコードを追加します。

        using System;
        using System.Collections.Generic;
        using System.Linq;
        using System.Net;
        using System.Net.Http;
        using System.Web.Helpers;
        using System.Web.Http.Controllers;
        using System.Web.Http.Filters;
        using System.Web.Mvc;
        namespace ContactManager.Filters
        {
            public class ValidateHttpAntiForgeryTokenAttribute : AuthorizationFilterAttribute
            {
                public override void OnAuthorization(HttpActionContext actionContext)
                {
                    HttpRequestMessage request = actionContext.ControllerContext.Request;
                    try
                    {
                        if (IsAjaxRequest(request))
                        {
                            ValidateRequestHeader(request);
                        }
                        else
                        {
                            AntiForgery.Validate();
                        }
                    }
                    catch (HttpAntiForgeryException e)
                    {
                        actionContext.Response = request.CreateErrorResponse(HttpStatusCode.Forbidden, e);
                    }
                }
                private bool IsAjaxRequest(HttpRequestMessage request)
                {
                    IEnumerable<string> xRequestedWithHeaders;
                    if (request.Headers.TryGetValues("X-Requested-With", out xRequestedWithHeaders))
                    {
                        string headerValue = xRequestedWithHeaders.FirstOrDefault();
                        if (!String.IsNullOrEmpty(headerValue))
                        {
                            return String.Equals(headerValue, "XMLHttpRequest", StringComparison.OrdinalIgnoreCase);
                        }
                    }
                    return false;
                }
                private void ValidateRequestHeader(HttpRequestMessage request)
                {
                    string cookieToken = String.Empty;
                    string formToken = String.Empty;
					IEnumerable<string> tokenHeaders;
                    if (request.Headers.TryGetValues("RequestVerificationToken", out tokenHeaders))
                    {
                        string tokenValue = tokenHeaders.FirstOrDefault();
                        if (!String.IsNullOrEmpty(tokenValue))
                        {
                            string[] tokens = tokenValue.Split(':');
                            if (tokens.Length == 2)
                            {
                                cookieToken = tokens[0].Trim();
                                formToken = tokens[1].Trim();
                            }
                        }
                    }
                    AntiForgery.Validate(cookieToken, formToken);
                }
            }
        }

1. 次の *using* ステートメントを contracts コントローラーに追加します。これにより **[[ValidateHttpAntiForgeryToken]]** 属性にアクセスできるようになります。

	using ContactManager.Filters;

1. **[ValidateHttpAntiForgeryToken]** 属性を **ContactsController** の Post メソッドに追加して、XSRF の脅威から保護します。そのコードを "PutContact"、"PostContact"、**DeleteContact** の各アクション メソッドに追加します。

	[ValidateHttpAntiForgeryToken]
        public IHttpActionResult PutContact(int id, Contact contact)
        {

1. *Views\Home\Index.cshtml* ファイルの *[Scripts]* セクションを更新して、XSRF トークンを取得するコードを含めます。

         @section Scripts {
            @Scripts.Render("~/bundles/knockout")
            <script type="text/javascript">
                @functions{
                   public string TokenHeaderValue()
                   {
                      string cookieToken, formToken;
                      AntiForgery.GetTokens(null, out cookieToken, out formToken);
                      return cookieToken + ":" + formToken;                
                   }
                }

               function ContactsViewModel() {
                  var self = this;
                  self.contacts = ko.observableArray([]);
                  self.addContact = function () {

                     $.ajax({
                        type: "post",
                        url: "api/contacts",
                        data: $("#addContact").serialize(),
                        dataType: "json",
                        success: function (value) {
                           self.contacts.push(value);
                        },
                        headers: {
                           'RequestVerificationToken': '@TokenHeaderValue()'
                        }
                     });

                  }
                  self.removeContact = function (contact) {
                     $.ajax({
                        type: "DELETE",
                        url: contact.Self,
                        success: function () {
                           self.contacts.remove(contact);
                        },
                        headers: {
                           'RequestVerificationToken': '@TokenHeaderValue()'
                        }

                     });
                  }

                  $.getJSON("api/contacts", function (data) {
                     self.contacts(data);
                  });
               }
               ko.applyBindings(new ContactsViewModel());
            </script>


<h2><a name="bkmk_deploydatabaseupdate"></a>Azure および SQL データベースにアプリケーションの更新を発行する</h2>

アプリケーションを発行するには、前に説明した手順を繰り返します。

1. **ソリューション エクスプローラー**で、プロジェクトを右クリックして **[発行]** をクリックします。

	![発行][rxP]

5. **[設定]** タブをクリックします。
	

1. **[ContactsManagerContext(ContactsManagerContext)]** の下で、**v** アイコンをクリックして、*リモート接続文字列*を連絡先データベースの接続文字列に変更します。**[ContactDB]** をクリックします。

	![設定](./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rt5.png)

7. **[Code First Migrations の実行 (アプリケーションの起動時に実行)]** チェック ボックスをオンにします。

1. **[次へ]** をクリックし、**[プレビュー]** をクリックします。Visual Studio で、追加または更新されるファイルの一覧が表示されます。

8. **[発行]** をクリックします。
展開が完了すると、ブラウザーでアプリケーションのホーム ページが開かれます。

	![連絡先が表示されていない Index ページ][intro001]

	Visual Studio の発行プロセスにより、展開された *Web.config* ファイル内の接続文字列は SQL データベースを指すよう自動的に構成されました。また、Code First Migrations は、展開後にアプリケーションが初めてデータベースに接続するときに、データベースを最新バージョンに自動的にアップグレードするよう構成されました。

	この構成の結果、前の手順で作成した **Initial** クラスのコードが実行されて、Code First によってデータベースが作成されました。この処理は、展開後にアプリケーションが初めてデータベースにアクセスしようとしたときに行われました。

9. アプリケーションをローカルで実行したときと同様に連絡先を入力して、データベースの展開が成功したことを確認します。

入力した項目が保存され、Contact Manager のページに表示されることを確認すると、その項目がデータベースに保存されたことがわかります。

![連絡先が表示された Index ページ][addwebapi004]

これで、データの保存先に SQL データベースを使用して、アプリケーションがクラウドで実行されるようになりました。Azure 上でアプリケーションのテストを終えたら、そのアプリケーションを削除します。アプリケーションはパブリックであり、アクセスを制限するメカニズムを備えていません。

<h2><a name="nextsteps"></a>次のステップ</h2>

実際のアプリケーションでは認証と権限承認が必要になるため、その目的でメンバーシップ データベースを使用します。「[Deploy a Secure ASP.NET MVC application with OAuth, Membership and SQL Database (OAuth、メンバーシップ、SQL データベースを使用するセキュリティで保護された ASP.NET MVC アプリケーションの展開)](http://www.windowsazure.com/ja-jp/develop/net/tutorials/web-site-with-sql-database/)」は、このチュートリアルに基づいており、メンバーシップ データベースを使用する Web アプリケーションを展開する方法について説明しています。

Azure アプリケーションにデータを保存するには、Azure ストレージを使用する方法もあります。Azure ストレージには、非リレーショナル データを BLOB 形式とテーブル形式で保存できます。Web API、ASP.NET MVC、および Window Azure の詳細については、次の Web ページを参照してください。
 

* [MVC を使用した Entity Framework の概要に関するページ][EFCodeFirstMVCTutorial]
* [ASP.NET MVC 5 の入門ページ](http://www.asp.net/mvc/tutorials/mvc-5/introduction/getting-started)
* [ASP.NET Web API の入門ページ](http://www.asp.net/web-api/overview/getting-started-with-aspnet-web-api/tutorial-your-first-web-api)
* [Debugging WAWS (WAWS のデバッグ)](http://www.windowsazure.com/ja-jp/documentation/articles/web-sites-dotnet-troubleshoot-visual-studio/)

このチュートリアルとサンプル アプリケーションは、Tom Dykstra と Barry Dorrans (Twitter [@blowdart](https://twitter.com/blowdart)) の協力の下、[Rick Anderson](http://blogs.msdn.com/b/rickandy/) (Twitter [@RickAndMSFT](https://twitter.com/RickAndMSFT)) が執筆しました。

役に立った内容や改善点など、皆様からのご意見をお寄せください。このチュートリアルに関してだけでなく、ここで紹介した製品に関するご意見やご要望もお待ちしております。お寄せいただいたご意見は、今後の改善に役立たせていただきます。特に、メンバーシップ データベースの構成と展開の自動化に関するご意見をお待ちしております。

<!-- bookmarks -->
[Add an OAuth Provider]: #addOauth
[Add Roles to the Membership Database]:#mbrDB
[Create a Data Deployment Script]:#ppd
[Update the Membership Database]:#ppd2
[setupdbenv]: #bkmk_setupdevenv
[setupwindowsazureenv]: #bkmk_setupwindowsazure
[createapplication]: #bkmk_createmvc4app
[deployapp1]: #bkmk_deploytowindowsazure1
[adddb]: #bkmk_addadatabase
[addcontroller]: #bkmk_addcontroller
[addwebapi]: #bkmk_addwebapi
[deploy2]: #bkmk_deploydatabaseupdate

<!-- links -->
[EFCodeFirstMVCTutorial]: http://www.asp.net/mvc/tutorials/getting-started-with-ef-using-mvc/creating-an-entity-framework-data-model-for-an-asp-net-mvc-application
[dbcontext-link]: http://msdn.microsoft.com/ja-jp/library/system.data.entity.dbcontext(v=VS.103).aspx


<!-- images-->
[rxE]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rxE.png
[rxP]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rxP.png
[rx22]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/
[rxb2]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rxb2.png
[rxz]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rxz.png
[rxzz]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rxzz.png
[rxz2]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rxz2.png
[rxz3]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rxz3.png
[rxStyle]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rxStyle.png
[rxz4]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rxz4.png
[rxz44]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rxz44.png
[rxNewCtx]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rxNewCtx.png
[rxPrevDB]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rxPrevDB.png
[rxOverwrite]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rxOverwrite.png
[rxPWS]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rxPWS.png
[rxNewCtx]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rxNewCtx.png
[rxAddApiController]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rxAddApiController.png
[rxFFchrome]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rxFFchrome.png
[intro001]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/dntutmobil-intro-finished-web-app.png
[rxCreateWSwithDB]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rxCreateWSwithDB.png
[setup007]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/dntutmobile-setup-azure-site-004.png
[setup009]: ../Media/dntutmobile-setup-azure-site-006.png
[newapp002]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/dntutmobile-createapp-002.png
[newapp004]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/dntutmobile-createapp-004.png
[firsdeploy007]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/dntutmobile-deploy1-publish-005.png
[firsdeploy009]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/dntutmobile-deploy1-publish-007.png
[adddb001]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/dntutmobile-adddatabase-001.png
[adddb002]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/dntutmobile-adddatabase-002.png
[addcode001]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/dntutmobile-controller-add-context-menu.png
[addcode002]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/dntutmobile-controller-add-controller-dialog.png
[addcode004]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/dntutmobile-controller-modify-index-context.png
[addcode005]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/dntutmobile-controller-add-contents-context-menu.png
[addcode007]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/dntutmobile-controller-modify-bundleconfig-context.png
[addcode008]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/dntutmobile-migrations-package-manager-menu.png
[addcode009]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/dntutmobile-migrations-package-manager-console.png
[addwebapi004]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/dntutmobile-webapi-added-contact.png
[addwebapi006]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/dntutmobile-webapi-save-returned-contacts.png
[addwebapi007]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/dntutmobile-webapi-contacts-in-notepad.png
[XSRF 保護を追加する]: #xsrf
[WebPIAzureSdk20NetVS12]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/WebPIAzureSdk20NetVS12.png
[XSRF 保護を追加する]: #xsrf
[ImportPublishSettings]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/ImportPublishSettings.png
[ImportPublishProfile]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/ImportPublishProfile.png
[PublishVSSolution]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/PublishVSSolution.png
[ValidateConnection]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/ValidateConnection.png
[WebPIAzureSdk20NetVS12]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/WebPIAzureSdk20NetVS12.png
[prevent-csrf-attacks]: http://www.asp.net/web-api/overview/security/preventing-cross-site-request-forgery-(csrf)-attacks