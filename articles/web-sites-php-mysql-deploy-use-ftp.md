<properties linkid="develop-php-website-with-mysql-and-ftp" urlDisplayName="MySQL と FTP を使用した Web サイト" pageTitle="MySQL と FTP を使用した PHP Web サイト - Azure チュートリアル" metaKeywords="" description="MySQL にデータを保存する PHP Web サイトを作成し、Azure への FTP 展開を使用する方法を示すチュートリアル。" metaCanonical="" services="web-sites" documentationCenter="PHP" title="PHP-MySQL Azure の Web サイトを作成して FTP で展開する" authors=""  solutions="" writer="" manager="" editor=""  />


#PHP-MySQL Azure の Web サイトを作成して FTP で展開する

このチュートリアルでは、PHP-MySQL Azure Web サイトを作成する方法と、FTP を使用してそれを展開する方法について説明します。このチュートリアルは、コンピューターに [PHP][install-php]、[MySQL][install-mysql]、Web サーバー、および FTP クライアントがインストールされていることを前提としています。このチュートリアルの手順は、Windows、Mac、Linux など、任意のオペレーティング システムで使用できます。このチュートリアルを完了すると、Azure で動作する PHP/MySQL Web サイトが完成します。
 
学習内容: 

* Azure の管理ポータルを使用して Azure の Web サイトと MySQL データベースを作成する方法。Azure の Web サイトでは PHP が既定で有効になっているため、特に何もしなくても PHP コードを実行できます。
* FTP を使用して Azure にアプリケーションを発行する方法。
 
このチュートリアルでは、登録用の単純な Web アプリケーションを PHP で作成します。このアプリケーションは Azure の Web サイトでホストします。完成したアプリケーションのスクリーンショットは次のようになります。

![Azure PHP Web サイト][running-app]

[WACOM.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

##Azure の Web サイトを作成して FTP 発行を設定する

Azure の Web サイトと MySQL データベースを作成するには、次の手順に従います。

1. [Azure の管理ポータル][management-portal]にログインします。
2. ポータルの左下にある **[新規]** アイコンをクリックします。

	![新しい Azure の Web サイトの作成][new-website]

3. **[Web サイト]** をクリックし、**[カスタム作成]** をクリックします。

	![新しい Web サイトのカスタム作成][custom-create]
	
	**[URL]** ボックスに値を入力し、**[データベース]** ボックスの一覧の **[新しい MySQL データベースを作成します]** を選択して、**[リージョン]** ボックスの一覧で Web サイトのデータ センターを選択します。ダイアログの下部にある矢印をクリックします。

	![Web サイトの詳細の入力][website-details]

4. データベースの **[名前]** ボックスに値を入力し、**[リージョン]** ボックスの一覧でデータベースのデータ センターを選択して、法律条項に同意することを示すチェック ボックスをオンにします。ダイアログの下部にあるチェックマークをクリックします。

	![新しい MySQL データベースの作成][new-mysql-db]

	Web サイトが作成されると、"**Web サイト '[SITENAME]' の作成が正常に完了しました**" というテキストが表示されます。これで、FTP 発行を有効にする準備ができました。

5. Web サイトの一覧に表示されている Web サイトの名前をクリックして、Web サイトの**クイック スタート** ダッシュボードを開きます。

	![Web サイトのダッシュボードを開く][go-to-dashboard]


6. **クイック スタート** ページの下部で、**[展開資格情報のリセット]** をクリックします。

	![デプロイ資格情報のリセット][reset-deployment-credentials]

7. FTP 発行を有効にするには、ユーザー名とパスワードを指定する必要があります。作成するユーザー名とパスワードはメモしておいてください 

	![発行資格情報の作成][portal-git-username-password]

##アプリケーションの作成とローカル テスト

Registration アプリケーションは、名前と電子メール アドレスを入力してイベントに登録するための、単純な PHP アプリケーションです。それまでの登録者情報がテーブルに表示されます。登録情報は MySQL データベースに保存されます。アプリケーションは、次の 2 つのファイルで構成されます。

* **index.php**: 登録用のフォームと登録者情報が含まれたテーブルを表示します。
* **createtable.php**: アプリケーション用の MySQL テーブルを作成します。このファイルは 1 度しか使用されません。

アプリケーションを作成してローカルで実行するには、次の手順に従います。ここに示す手順は、ローカル コンピューターに PHP、MySQL、および Web サーバーがセットアップされており、[MySQL 用 PDO 拡張機能][pdo-mysql]が有効になっていることを前提としています。

1. `registration` という MySQL データベースを作成します。これには、MySQL コマンド プロンプトで次のコマンドを実行します。

		mysql> create database registration;

2. Web サーバーのルート ディレクトリで、`registration` というフォルダーを作成し、その中に 2 つのファイル (`createtable.php` と `index.php`) を作成します。

3. `createtable.php` ファイルをテキスト エディターまたは IDE で開き、次のコードを追加します。このコードは、`registration` データベースに `registration_tbl` テーブルを作成するために使用します。

		<?php
		// DB connection info
		$host = "localhost";
		$user = "user name";
		$pwd = "password";
		$db = "registration";
		try{
			$conn = new PDO( "mysql:host=$host;dbname=$db", $user, $pwd);
			$conn->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );
			$sql = "CREATE TABLE registration_tbl(
						id INT NOT NULL AUTO_INCREMENT, 
						PRIMARY KEY(id),
						name VARCHAR(30),
						email VARCHAR(30),
						date DATE)";
			$conn->query($sql);
		}
		catch(Exception $e){
			die(print_r($e));
		}
		echo "<h3>Table created.</h3>";
		?>

	> [WACOM.NOTE] 
	> <code>$user</code> と <code>$pwd</code> の値は、ローカルの MySQL ユーザー名とパスワードに置き換える必要があります。

4. Web ブラウザーを開いて、[http://localhost/registration/createtable.php][localhost-createtable] にアクセスします。このコードは、データベースに `registration_tbl` テーブルを作成するために使用します。

5. **index.php**index.php ファイルをテキスト エディターまたは IDE で開いて、ページの基本的な HTML コードおよび CSS コードを追加します (PHP コードは後で追加します)。

		<html>
		<head>
		<Title>登録フォーム</Title>
		<style type="text/css">
			body { background-color: #fff; border-top: solid 10px #000;
			    color: #333; font-size: .85em; margin: 20; padding: 20;
			    font-family: "Segoe UI", Verdana, Helvetica, Sans-Serif;
			}
			h1, h2, h3,{ color: #000; margin-bottom: 0; padding-bottom: 0; }
			h1 { font-size: 2em; }
			h2 { font-size: 1.75em; }
			h3 { font-size: 1.2em; }
			table { margin-top: 0.75em; }
			th { font-size: 1.2em; text-align: left; border: none; padding-left: 0; }
			td { padding: 0.25em 2em 0.25em 0em; border: 0 none; }
		</style>
		</head>
		<body>
		<h1>Register here!</h1>
		<p>Fill in your name and email address, then click <strong>Submit</strong> to register.</p>
		<form method="post" action="index.php" enctype="multipart/form-data" >
		      Name  <input type="text" name="name" id="name"/></br>
		      Email <input type="text" name="email" id="email"/></br>
		      <input type="submit" name="submit" value="Submit" />
		</form>
		<?php

		?>
		</body>
		</html>

6. PHP タグ内に、データベースに接続するための PHP コードを追加します。

		// DB connection info
		$host = "localhost";
		$user = "user name";
		$pwd = "password";
		$db = "registration";
		// Connect to database.
		try {
			$conn = new PDO( "mysql:host=$host;dbname=$db", $user, $pwd);
			$conn->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );
		}
		catch(Exception $e){
			die(var_dump($e));
		}

	> [WACOM.NOTE]
	> ここでも、<code>$user</code> と <code>$pwd</code> の値は、ローカルの MySQL ユーザー名とパスワードに置き換える必要があります。

7. データベース接続コードの次に、登録情報をデータベースに挿入するためのコードを追加します。

		if(!empty($_POST)) {
		try {
			$name = $_POST['name'];
			$email = $_POST['email'];
			$date = date("Y-m-d");
			// Insert data
			$sql_insert = "INSERT INTO registration_tbl (name, email, date) 
						   VALUES (?,?,?)";
			$stmt = $conn->prepare($sql_insert);
			$stmt->bindValue(1, $name);
			$stmt->bindValue(2, $email);
			$stmt->bindValue(3, $date);
			$stmt->execute();
		}
		catch(Exception $e) {
			die(var_dump($e));
		}
		echo "<h3>Your're registered!</h3>";
		}

8. 上のコードの次に、データベースからデータを取得するためのコードを追加します。

		$sql_select = "SELECT * FROM registration_tbl";
		$stmt = $conn->query($sql_select);
		$registrants = $stmt->fetchAll(); 
		if(count($registrants) > 0) {
			echo "<h2>People who are registered:</h2>";
			echo "<table>";
			echo "<tr><th>Name</th>";
			echo "<th>Email</th>";
			echo "<th>Date</th></tr>";
			foreach($registrants as $registrant) {
				echo "<tr><td>".$registrant['name']."</td>";
				echo "<td>".$registrant['email']."</td>";
				echo "<td>".$registrant['date']."</td></tr>";
		    }
		 	echo "</table>";
		} else {
			echo "<h3>No one is currently registered.</h3>";
		}

これで、[http://localhost/registration/index.php][localhost-index] に移動してアプリケーションをテストできるようになりました。

##MySQL と FTP の接続情報を取得する

Azure の Web サイトで実行されている MySQL データベースに接続するには、接続情報が必要になります。MySQL の接続情報を取得するには、次の手順に従います。

1. Web サイトのダッシュボードで、ページの右側にある **[接続文字列の表示]** リンクをクリックします。

	![データベース接続情報を取得する][connection-string-info]
	
2. `Database`、`Data Source`、`User Id`、および `Password` の値をメモします。

3. Web サイトのダッシュボードで、ページの右下にある **[発行プロファイルのダウンロード]** リンクをクリックします。

	![発行プロファイルのダウンロード][download-publish-profile]

4. XML エディターで `publishsettings` ファイルを開きます。

3. 次のように `publishMethod="FTP"` が指定されている `<publishProfile >` 要素を確認します。

		<publishProfile publishMethod="FTP" publishUrl="ftp://[mysite].azurewebsites.net/site/wwwroot" ftpPassiveMode="True" userName="[username]" userPWD="[password]" destinationAppUrl="http://[name].antdf0.antares-test.windows-int.net" 
			...
		</publishProfile>
	
`publishUrl` 属性、`userName` 属性、および `userPWD` 属性をメモします。

##アプリケーションを発行する

アプリケーションをローカルでテストした後、FTP を使用してそのアプリケーションを Azure の Web サイトに発行できます。ただし、まずアプリケーション内のデータベース接続情報を更新する必要があります。先ほど (「**MySQL と FTP の接続情報の取得**」セクションで) 取得したデータベース接続情報を使用し、`createdatabase.php` ファイルと `index.php` ファイルの**両方**で、次の情報を適切な値に置き換えます。

	// DB connection info
	$host = "value of Data Source";
	$user = "value of User Id";
	$pwd = "value of Password";
	$db = "value of Database";

これで、FTP を使用してアプリケーションを発行する準備ができました。

1. 目的の FTP クライアントを開きます。

2. 先ほどメモしておいた `publishUrl` 属性の*ホスト名部分*を FTP クライアントに入力します。

3. 先ほどメモしておいた `userName` 属性および `userPWD` 属性をそのまま FTP クライアントに入力します。

4. 接続を確立します。

接続した後、必要に応じて、ファイルをアップロードおよびダウンロードすることができます。ファイルのアップロード先は、必ずルート ディレクトリ (`/site/wwwroot`) にしてください。

`index.php` と `createtable.php` の両方をアップロードした後、**http://[site name].azurewebsites.net/createtable.php** に移動してアプリケーション用の MySQL テーブルを作成し、**http://[サイト名].azurewebsites.net/index.php** に移動してアプリケーションの使用を開始します。
 

[install-php]: http://www.php.net/manual/en/install.php
[install-mysql]: http://dev.mysql.com/doc/refman/5.6/en/installing.html
[pdo-mysql]: http://www.php.net/manual/en/ref.pdo-mysql.php
[localhost-createtable]: http://localhost/tasklist/createtable.php
[localhost-index]: http://localhost/tasklist/index.php
[running-app]: ./media/web-sites-php-web-site-mysql-deploy-use-ftp/running_app_2.png
[new-website]: ./media/web-sites-php-web-site-mysql-deploy-use-ftp/new_website.jpg
[custom-create]: ./media/web-sites-php-web-site-mysql-deploy-use-ftp/custom_create.png
[website-details]: ./media/web-sites-php-web-site-mysql-deploy-use-ftp/website_details.jpg
[new-mysql-db]: ./media/web-sites-php-web-site-mysql-deploy-use-ftp/new_mysql_db.jpg
[go-to-dashboard]: ./media/web-sites-php-web-site-mysql-deploy-use-ftp/go_to_dashboard.png
[reset-deployment-credentials]: ./media/web-sites-php-web-site-mysql-deploy-use-ftp/reset-deployment-credentials.png
[portal-git-username-password]: ./media/web-sites-php-web-site-mysql-deploy-use-ftp/git-deployment-credentials.png


[connection-string-info]: ./media/web-sites-php-web-site-mysql-deploy-use-ftp/connection_string_info.png
[management-portal]: https://manage.windowsazure.com
[download-publish-profile]: ./media/web-sites-php-web-site-mysql-deploy-use-ftp/download_publish_profile_2.png
