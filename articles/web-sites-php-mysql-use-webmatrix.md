<properties linkid="develop-php-website-with-mysql-and-webmatrix" urlDisplayName="WebMatrix を使用した Web サイト" pageTitle="MySQL と WebMatrix を使用した PHP Web サイト - Azure チュートリアル" metaKeywords="" description="無料の WebMatrix IDE を使用して、MySQL にデータを保存する PHP Web サイトを作成および展開する方法を示すチュートリアル。" metaCanonical="" services="web-sites" documentationCenter="PHP" title="WebMatrix を使用して PHP-MySQL Azure の Web サイトを作成および展開する" authors=""  solutions="" writer="" manager="" editor="mollybos"  />





#WebMatrix を使用して PHP-MySQL Azure の Web サイトを作成および展開する

このチュートリアルでは、WebMatrix を使用して PHP-MySQL アプリケーションを作成し、Azure の Web サイトに展開する方法を示します。WebMatrix は、Microsoft から提供されている無料の Web 開発ツールで、Web サイトの開発に必要なものがすべて用意されています。WebMatrix では PHP がサポートされており、PHP 開発用 Intellisense が含まれています。

このチュートリアルは、アプリケーションをローカルでテストできるように、コンピューターに [MySQL][install-mysql] がインストールされていることを前提としています。ただし、MySQL をインストールせずにチュートリアルを完了することもできます。その場合は、アプリケーションを直接 Azure の Web サイトに展開します。

このチュートリアルを完了すると、Azure で動作する PHP/MySQL Web サイトが完成します。
 
学習内容: 

* 管理ポータルを使用して Azure の Web サイトおよび MySQL データベースを作成する方法。Azure の Web サイトでは PHP が既定で有効になっているため、特に何もしなくても PHP コードを実行できます。
* WebMatrix を使用して PHP アプリケーションを作成する方法。
* WebMatrix を使用して Azure にアプリケーションを発行および再発行する方法。
 
このチュートリアルでは、タスク一覧用の単純な Web アプリケーション (Tasklist) を PHP で作成します。このアプリケーションは Azure の Web サイトでホストします。実行中のアプリケーションのスクリーンショットは次のようになります。

![Azure PHP Web サイト][running-app]

[WACOM.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

##前提条件

1. Tasklist アプリケーション ファイルを[ダウンロード][tasklist-mysql-download]します。Tasklist アプリケーションは、タスク一覧のアイテムの追加、完了済みとしてのマーク付け、および削除を行うための、単純な PHP アプリケーションです。タスク一覧のアイテムは MySQL データベースに保存されます。アプリケーションは、次のファイルで構成されます。

	* **additem.php**: 一覧にアイテムを追加します。
	* **createtable.php**: アプリケーション用の MySQL テーブルを作成します。このファイルは 1 度しか呼び出されません。
	* **deleteitem.php**: アイテムを削除します。
	* **getitems.php**: データベース内のアイテムをすべて取得します。
	* **index.php**: タスクを表示し、一覧にアイテムを追加するためのフォームを提供します。
	* **markitemcomplete.php**: アイテムのステータスを "完了済み" に変更します。
	* **taskmodel.php**: データベース内のアイテムの追加、取得、更新、および削除を行う関数が含まれています。

1. `tasklist` というローカルの MySQL データベースを作成します。そのためには、WebMatrix の Database ワークスペースから作成するか (このチュートリアルでこの後にインストールします)、MySQL コマンド プロンプトで次のコマンドを実行します。

		mysql> create database tasklist;

	この手順は、アプリケーションをローカルでテストする場合にのみ必要です。

<h2><a id="CreateWebsite"></a>Azure の Web サイトと MySQL データベースの作成</h2>

1. [管理ポータル][preview-portal]にログインします。
1. ポータルの左下にある **[新規]** アイコンをクリックします。

	![新しい Azure の Web サイトの作成][NewWebSite1]

1. **[Web サイト]** をクリックし、**[カスタム作成]** をクリックします。

	![新しい Web サイトのカスタム作成][NewWebSite2]

	> [WACOM.NOTE]
	> Web サイトを作成した後は、その Web サイトに MySQL データベースを作成できません。Web サイトと MySQL データベースは、次に示す手順に従って作成する必要があります。

1. **[URL]** ボックスに値を入力し、**[データベース]** ボックスの一覧の **[新しい MySQL データベースを作成します]** を選択して、**[リージョン]** ボックスの一覧で Web サイトのデータ センターを選択します。ダイアログの下部にある矢印をクリックします。

	![Web サイトの詳細の入力][NewWebSite3]

5. データベースの **[名前]** ボックスに値を入力し、**[リージョン]** ボックスの一覧でデータベースのデータ センターを選択して、法律条項に同意することを示すチェック ボックスをオンにします。ダイアログの下部にあるチェックマークをクリックします。

	![新しい MySQL データベースの作成][NewWebSite4]

	Web サイトが作成されると、"**Web サイト '[サイト名]' の作成に成功しました**" というテキストが表示されます。

	次に、MySQL の接続情報を取得する必要があります。


6. Web サイトの一覧に表示されている Web サイトの名前をクリックして、Web サイトのクイック スタート ページを開きます。

	![Web サイトのダッシュボードを開く][NewWebSite5]

7. **[構成]** タブをクリックします。

	![[構成] タブ][NewWebSite6]

8. 下方向へ **[接続文字列]** セクションまでスクロールします。`Database`、`Data Source`、`User Id`、および `Password` の値は、それぞれデータベース名、サーバー名、ユーザー名、およびユーザー パスワードです。データベース接続情報は後で必要になるため、メモしておいてください。

	![接続文字列][ConnectionString]

##WebMatrix をインストールしてアプリケーションを作成する

WebMatrix は、[管理ポータル][preview-portal]からインストールできます。

1. ログインした後、Web サイトのクイック スタート ページに移動して、ページの下部にある WebMatrix アイコンをクリックします。

	![WebMatrix のインストール][InstallWebMatrix]

	表示される手順に従って WebMatrix をインストールします。

2. WebMatrix がインストールされると、サイトを WebMatrix プロジェクトとして開く試行が行われます。ライブ サイトを直接編集するか、ローカル コピーをダウンロードするかを選択できます。このチュートリアルではローカル コピーを編集します。

3. サイトをダウンロードするかどうかを確認するメッセージが表示されたら、**[はい、テンプレート ギャラリーからインストールします]** を選択します。

	![Web サイトをダウンロードする][download-site]

4. 使用できるテンプレートから、**[PHP]** を選択します。

	![テンプレートからサイトを作成する][site-from-template]

5. **[空のサイト]** テンプレートを選択します。サイトの名前を指定し、**[次へ]** をクリックします。

	![サイト名の指定][site-from-template-2]

	サイトが WebMatrix で開き、既定のファイルが配置されます。

	次に示す数ステップで、Tasklist アプリケーションを作成します。これには、ダウンロードしておいたファイルを追加し、いくつかの変更を行います。ただし、独自の既存ファイルを追加することも、新しいファイルを作成することもできます。

6. **[既存を追加]** をクリックしてアプリケーション ファイルを追加します。

	![WebMatrix - 既存ファイルを追加する][edit_addexisting]

	表示されたダイアログで、ダウンロードしておいたファイルの場所に移動し、すべて選択して、[開く] をクリックします。確認のメッセージが表示されたら、`index.php` ファイルの置き換えを選択します。

7. 次に、ローカル MySQL データベースの接続情報を `taskmodel.php` ファイルに追加する必要があります。`taskmodel.php` ファイルをダブルクリックして開き、`connect` 関数内のデータベース接続情報を更新します (**注**: アプリケーションをローカルでテストせず、直接 Azure の Web サイトに発行する場合は、「[アプリケーションの発行](#Publish)」に進んでください)。

		// DB connection info
		$host = "localhost";
		$user = "your user name";
		$pwd = "your password";
		$db = "tasklist";

	`taskmodel.php` ファイルを保存します。

8. アプリケーションを実行するには、`items` テーブルを作成する必要があります。`createtable.php` ファイルを右クリックし、**[ブラウザーで起動]** を選択します。これにより、`createtable.php` がブラウザーで起動し、`tasklist` データベースに `items` テーブルを作成するコードが実行されます。

	![WebMatrix - ブラウザーで createtable.php を起動する][edit_run]

9. これで、アプリケーションのテストをローカルで行うことができます。`index.php` ファイルを右クリックし、**[ブラウザーで起動]** を選択します。アイテムの追加、完了済みとしてのマーク付け、および削除を行うことにより、アプリケーションをテストします。


<h2><a id="Publish"></a>アプリケーションの発行</h2>

アプリケーションを Azure の Web サイトに発行する前に、`taskmodel.php` ファイル内のデータベース接続情報を、先ほど (「[Azure の Web サイトと MySQL データベースの作成](#CreateWebsite)」セクションで) 取得した接続情報に更新する必要があります。

1. `taskmodel.php` ファイルをダブルクリックして開き、`connect` 関数内のデータベース接続情報を更新します 

		// DB connection info
		$host = "value of Data Source";
		$user = "value of User Id";
		$pwd = "value of Password";
		$db = "value of Database";
	
	`taskmodel.php` ファイルを保存します。

2. WebMatrix で **[発行]** をクリックし、**[発行のプレビュー]** ダイアログの **[続行]** をクリックします。

	![WebMatrix - 発行][edit_publish]

3. `items` テーブルを作成するには、http://[your web site name].azurewebsites.net/createtable.php に移動します。

4. 最後に、http://[Web サイト名].azurewebsites.net/index.php に移動してアプリケーションを使用します。
	
##アプリケーションを変更して再発行する

アプリケーションを変更するには、前にダウンロードしたサイトのローカル コピーを編集して、再発行する方法と、リモート モードでサイトを直接編集する方法があります。ここでは、`index.php` ファイルの見出しに簡単な変更を加え、それをライブ サイトへ直接保存します。

1. WebMatrix で目的のサイトの [リモート] タブをクリックし、**[リモート ビューを開く]** を選択します。リモート サイトが開き、直接編集できるようになります。
	 ![WebMatrix - リモート ビューを開く][OpenRemoteView]
 
2. `index.php` ファイルをダブルクリックして開きます。
	![WebMatrix - インデックス ファイルを開く][Remote_editIndex]

3. **title** タグと **h1** タグの **My ToDo List** を **My Task List** に変更し、このファイルを保存します。


4. ファイルを保存したら [実行] ボタンをクリックし、ライブ サイトで変更内容を確認します。
	![WebMatrix - リモートでサイトを実行][Remote_run]


# 次のステップ

これで WebMatrix から Web サイトを作成して Azure に展開する方法はわかりました。WebMatrix の詳細については、次のリソースを参照してください。

* [Azure 用 WebMatrix に関するページ](http://go.microsoft.com/fwlink/?LinkID=253622&clcid=0x409)

* [WebMatrix の Web サイト](http://www.microsoft.com/click/services/Redirect2.ashx?CR_CC=200106398)





[install-mysql]: http://dev.mysql.com/doc/refman/5.6/en/installing.html
[running-app]: ./media/web-sites-php-mysql-use-webmatrix/tasklist_app_windows.png
[tasklist-mysql-download]: http://go.microsoft.com/fwlink/?LinkId=252506
[NewWebSite1]: ./media/web-sites-php-mysql-use-webmatrix/NewWebSite1.jpg
[NewWebSite2]: ./media/web-sites-php-mysql-use-webmatrix/NewWebSite2.png
[NewWebSite3]: ./media/web-sites-php-mysql-use-webmatrix/NewWebSite3.png
[NewWebSite4]: ./media/web-sites-php-mysql-use-webmatrix/NewWebSite4.png
[NewWebSite5]: ./media/web-sites-php-mysql-use-webmatrix/NewWebSite5.png
[NewWebSite6]: ./media/web-sites-php-mysql-use-webmatrix/NewWebSite6.png
[ConnectionString]: ./media/web-sites-php-mysql-use-webmatrix/ConnectionString.png
[InstallWebMatrix]: ./media/web-sites-php-mysql-use-webmatrix/InstallWebMatrix.png
[download-site]: ./media/web-sites-php-mysql-use-webmatrix/download-site-1.png
[site-from-template]: ./media/web-sites-php-mysql-use-webmatrix/site-from-template.png
[site-from-template-2]: ./media/web-sites-php-mysql-use-webmatrix/site-from-template-2.png
[edit_addexisting]: ./media/web-sites-php-mysql-use-webmatrix/edit_addexisting.png
[edit_run]: ./media/web-sites-php-mysql-use-webmatrix/edit_run.png
[edit_publish]: ./media/web-sites-php-mysql-use-webmatrix/edit_publish.png
[OpenRemoteView]: ./media/web-sites-php-mysql-use-webmatrix/OpenRemoteView.png
[Remote_editIndex]: ./media/web-sites-php-mysql-use-webmatrix/Remote_editIndex.png
[Remote_run]: ./media/web-sites-php-mysql-use-webmatrix/Remote_run.png













[preview-portal]: https://manage.windowsazure.com













