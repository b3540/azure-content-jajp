<properties 
   pageTitle="SQL Database のセキュリティの概要" 
   description="認証、承認、接続のセキュリティ、暗号化、コンプライアンスなどに関するクラウドと SQL Server オンプレミスの違いなど、Azure SQL Database と SQL Server のセキュリティの詳細について説明します。" 
   services="sql-database" 
   documentationCenter="" 
   authors="tmullaney" 
   manager="jeffreyg" 
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services" 
   ms.date="04/02/2015"
   ms.author="thmullan;jackr"/>


# SQL Database の保護

## 概要

この記事は、Azure SQL Database を使用してアプリケーションのデータ層をセキュリティで保護するための基本事項について説明します。特にこの記事は、[SQL Database の使用に関するチュートリアル](sql-database-get-started.md)で作成したデータベースでリソースを使用して、アクセスの制限、データの保護、アクティビティの監視を実行するのに役立ちます。SQL のすべてのエディションで使用できるセキュリティ機能の概要については、[SQL Server Database Engine と Azure SQL Database のセキュリティ センター](https://msdn.microsoft.com/library/bb510589)を参照してください。

## 接続のセキュリティ

接続のセキュリティとは、ファイアウォール ルールと接続の暗号化を使用して、データベースへの接続を制限し、保護する方法のことです。

ファイアウォール ルールはサーバーとデータベースの両方で使用され、明示的にホワイト リストに登録されていない IP アドレスからの接続試行を拒否します。新しいデータベースへの接続を試行するために、アプリケーションまたはクライアント コンピューターのパブリック IP アドレスを許可するには、まず Azure の管理ポータル、REST API、または PowerShell を使用して、サーバーレベルのファイアウォール ルールを作成する必要があります。ベスト プラクティスとして、可能な限りサーバーのファイアウォールにより許可される IP アドレスの範囲を制限する必要があります。詳細については、「[Azure SQL Database ファイアウォール](https://msdn.microsoft.com/library/ee621782)」を参照してください。

データがデータベースとの間で "送受信中" である間は、常に Azure SQL Database への接続をすべて (SSL/TLS を使用して) 暗号化する必要があります。アプリケーションの接続文字列で、接続を暗号化するパラメーターを指定する必要があります。また、サーバーの証明書を信頼*しないでください* (Azure の管理ポータルから接続文字列をコピーすると、この操作は自動的に実行されます)。この操作を実行しないと、サーバーの ID が確認されず、"man-in-the-middle" 攻撃を受けやすくなります。たとえば ADO.NET ドライバーの場合、これらの接続文字列のパラメーターは、**Encrypt=True** と **TrustServerCertificate=False** です。詳細については、[Azure SQL Database の接続の暗号化と証明書の検証](https://msdn.microsoft.com/library/azure/ff394108#encryption)に関するトピックを参照してください。


## 認証

認証とは、データベースへの接続時に ID を証明する方法のことです。現在 SQL Database では、ユーザー名とパスワードによる SQL 認証をサポートしています。

データベースの論理サーバーを作成したときに、ユーザー名とパスワードによる "サーバー管理" ログインを指定したとします。これらの資格情報を使用すると、データベース所有者、つまり "dbo" として、そのサーバーにある任意のデータベースを認証できます。

ただし、アプリケーションで別のアカウントを使用して認証することをお勧めします。この方法により、アプリケーションに付与されるアクセス許可を制限し、アプリケーション コードが SQL インジェクション攻撃に対して脆弱な場合に、悪意のあるアクティビティのリスクを軽減できます。[包含データベース ユーザー](https://msdn.microsoft.com/library/ff929188)を作成する方法をお勧めします。この方法により、ユーザー名とパスワードを使用して、アプリケーションから単一のデータベースに直接認証できます。サーバー管理ログインでユーザー データベースに接続しているときに次の T-SQL を実行すると、包含データベース ユーザーを作成できます。

```
CREATE USER ApplicationUser WITH PASSWORD = 'strong_password';
```

アプリケーションの接続文字列では、サーバー管理ログインではなく、このユーザー名とパスワードを指定してデータベースに接続します。

SQL Database の認証の詳細については、[Azure SQL Database におけるデータベースとログインの管理](https://msdn.microsoft.com/library/ee336235)に関するページを参照してください。


## 承認
承認とは、Azure SQL Database で許可される操作を示すものであり、ユーザー アカウントのロールのメンバーシップとアクセス許可によって制御されます。ベスト プラクティスとして、必要最低限の特権をユーザーに付与することをお勧めします。Azure SQL Database では、T-SQL のロールを使用して承認を容易に管理できます。

```
ALTER ROLE db_datareader ADD MEMBER ApplicationUser; -- allows ApplicationUser to read data
ALTER ROLE db_datawriter ADD MEMBER ApplicationUser; -- allows ApplicationUser to write data
```

接続しているサーバー管理者のアカウントは db_owner のメンバーであり、データベース内ですべての操作を実行する権限を持ちます。スキーマのアップグレードやその他の管理操作をデプロイするために、このアカウントを保存します。アクセス許可が限定された "ApplicationUser" アカウントを使用して、アプリケーションで必要な最小限の特権により、アプリケーションをデータベースに接続します。

ユーザーが Azure SQL Database で実行できる操作をさらに制限する方法がいくつかあります。

* db_datareader と db_datawriter 以外の[データベース ロール](https://msdn.microsoft.com/library/ms189121)を使用して、より権限の大きなアプリケーション ユーザー アカウント、またはより権限の小さな管理アカウントを作成できます。
* 詳細な[アクセス許可](https://msdn.microsoft.com/library/ms191291)により、個々の列、テーブル、ビュー、プロシージャ、その他のデータベース内のオブジェクトで実行できる操作を制御できます。
* [権限借用](https://msdn.microsoft.com/library/vstudio/bb669087)と[モジュール署名](https://msdn.microsoft.com/library/bb669102)を使用すると、安全にアクセス許可を一時的に昇格できます。
* [行レベル セキュリティ](https://msdn.microsoft.com/library/dn765131)により、ユーザーが確認できる行をフィルター処理できます。
* [データのマスキング](sql-database-dynamic-data-masking-get-started.md)を使用すると、機密データの公開を制限できます。
* [ストアド プロシージャ](https://msdn.microsoft.com/library/ms190782)を使用すると、データベースで実行できるアクションを制限できます。

Azure の管理ポータルまたは Azure リソース マネージャー API を使用したデータベースと論理サーバーの管理は、ポータル ユーザー アカウントのロールの割り当てによって制御されます。このトピックの詳細については、「[Azure プレビュー ポータルでの役割ベースのアクセス制御](../role-based-access-control-configure.md)」を参照してください。


## 暗号化

Azure SQL Database では、[透過的なデータ暗号化](http://go.microsoft.com/fwlink/?LinkId=526242)を使用して、データが "静止" 状態のとき、またはデータベース ファイルやバックアップに格納されているときに、そのデータを暗号化することによりデータを保護できます。データベースを暗号化するには、データベースの所有者として接続し、次のコマンドを実行します。

```
CREATE DATABASE ENCRYPTION KEY 
   WITH ALGORITHM = AES_256 
   ENCRYPTION BY SERVER CERTIFICATE ##MS_TdeCertificate##;
   
ALTER DATABASE [AdventureWorks] SET ENCRYPTION ON;
```

その他の方法で機密データを暗号化するには、次を検討してください。

* [セルレベルの暗号化](https://msdn.microsoft.com/library/ms179331.aspx)により、暗号化キーが異なるデータの特定の列またはセルを暗号化できます。
* ハードウェア セキュリティ モジュールか、暗号化キー階層のサーバーの中央管理が必要な場合は、[Azure VM で Azure Key Vault と SQL Server](http://blogs.technet.com/b/kv/archive/2015/01/12/using-the-key-vault-for-sql-server-encryption.aspx) を併用することを検討してください。


## 監査

データベースの監査イベントと追跡イベントは、規制遵守の維持や、疑わしいアクティビティの特定に役立ちます。SQL Database の監査により、Azure Storage アカウントの監査ログにデータベースのイベントを記録できます。また SQL Database の監査を Microsoft Power BI と統合することにより、詳細なレポートと分析が容易になります。詳細については、「[SQL Database 監査の使用](sql-database-auditing-get-started.md)」を参照してください。

## コンプライアンス

アプリケーションがさまざまなセキュリティ コンプライアンスの要件を満たすのに役立つ上記の機能以外にも、Azure SQL Database は定期的な監査に参加し、さまざまなコンプライアンス基準に認定されています。詳細については、「[Microsoft Azure のトラスト センター](http://azure.microsoft.com/support/trust-center/)」を参照してください。ここから最新の[SQL Database コンプライアンス証明書](http://azure.microsoft.com/support/trust-center/services/)の一覧を入手できます。

<!---HONumber=58--> 