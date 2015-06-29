<properties 
	pageTitle="SQL Database V12 の新機能 |Microsoft Azure" 
	description="クラウドで Azure SQL Database を使用しているビジネス システムが今すぐ V12 にアップグレードでメリットを得られる理由を説明します。" 
	services="sql-database" 
	documentationCenter="" 
	authors="MightyPen" 
	manager="jeffreyg" 
	editor="jeffreyg"/>


<tags 
	ms.service="sql-database" 
	ms.workload="data-management" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="get-started-article" 
	ms.date="06/10/2015" 
	ms.author="genemi"/>


# SQL Database V12 の新機能


このトピックでは、Azure SQL Database の新しい V12 バージョンを V11 バージョンと比べたときの多くのメリットについて説明します。


V12 には継続的に機能が追加されます。したがって、Azure のサービス更新情報に間する Web ページにアクセスし、次のフィルターを使用することをお勧めします。


- サービスを [[SQL Database]](http://azure.microsoft.com/updates/?service=sql-database) でフィルター処理します。
- SQL Database の機能について、[General Availability] [(GA) のアナウンス](http://azure.microsoft.com/updates/?service=sql-database&update-type=general-availability)でフィルター処理します。


## SQL Server との強化されたアプリケーションの互換性


SQL Database V12 の主要な目的は、Microsoft SQL Server 2014 との互換性を強化することでした。その他の領域では、V12 はプログラミングの重要な領域で SQL Server との対応を実現しました。次に例を示します。


- [共通言語ランタイム (CLR) アセンブリ](http://msdn.microsoft.com/library/ms189524.aspx)
- [ウィンドウ関数](https://msdn.microsoft.com/library/bb934097.aspx)と [OVER](http://msdn.microsoft.com/library/ms189461.aspx) 
- [XML インデックス](https://msdn.microsoft.com/library/bb934097.aspx)と[選択的 XML インデックス](http://msdn.microsoft.com/library/jj670104.aspx)
- [変更の追跡](http://msdn.microsoft.com/library/bb933875.aspx)
- [SELECT...INTO](http://msdn.microsoft.com/library/ms188029.aspx)
- [フルテキスト検索](http://msdn.microsoft.com/library/ms142571.aspx)


SQL Database でまだサポートされていない一部の機能については、[ここ](http://msdn.microsoft.com/library/azure/ee336281.aspx)を参照してください。


## Premium のパフォーマンスの向上、新しいパフォーマンス レベル


V12 では、すべての Premium パフォーマンス レベルに割り当てられているデータベース スループット ユニット (DTU) を追加料金なしで 25% 引き上げました。さらに大きなパフォーマンスの向上は、次のような新機能で実現できます。


- メモリ内[列ストア インデックス](http://msdn.microsoft.com/library/gg492153.aspx)のサポート
- [行によるテーブル パーティション](http://msdn.microsoft.com/library/ms187802.aspx)と、関連する [TRUNCATE TABLE](http://msdn.microsoft.com/library/ms177570.aspx) の機能強化。
- パフォーマンスの監視と調整に役立つ動的管理ビュー [(DMV)](http://msdn.microsoft.com/library/ms188754.aspx) と拡張イベント [(XEvent)](https://msdn.microsoft.com/library/bb630282.aspx) の利用。


## クラウド SaaS ベンダーのサポートの充実


V12 でのみ、新しい Standard パフォーマンス レベルの S3 と、[エラスティック データベース プール](sql-database-elastic-pool.md)のパブリック プレビューをリリースしました。これは、クラウド SaaS ベンダー向けに設計されたソリューションです。エラスティック データベース プールには次のメリットがあります。


- DTU をデータベース間で共有して、多数のデータベースのコストを削減できます。
- [エラスティック データベース ジョブ](sql-database-elastic-jobs-overview.md)を実行して、大規模にデータベースを管理できます。


## セキュリティの強化


セキュリティは、ビジネスをクラウドで遂行しているユーザーにとっての主要な懸案事項です。V12 でリリースされた最新のセキュリティ機能は、次のとおりです。


- [行レベル セキュリティ](http://msdn.microsoft.com/library/dn765131.aspx) (RLS)
- [動的データ マスク](sql-database-dynamic-data-masking-get-started.md)
- [包含データベース](http://msdn.microsoft.com/library/azure/ff394108.aspx)
- GRANT、DENY、REVOKE を使用して管理される[アプリケーション ロール](http://msdn.microsoft.com/library/ms190998.aspx)
- [透過的なデータ暗号化](http://msdn.microsoft.com/library/0bf7e8ff-1416-4923-9c4c-49341e208c62.aspx) (TDE)


## 復旧が必要なときのビジネス継続性の向上


V12 では、目標復旧時点 (PRO) と推定復旧時間 (ERT) が大幅に向上しています。


| ビジネス継続性に関係する機能 | 以前のバージョン | V12 |
| :-- | :-- | :-- |
| geo リストア | • RPO は 24 時間未満。<br/>• ERT は 12 時間未満。 | • RPO は 1 時間未満。<br/>• ERT は 12 時間未満。 |
| Standard geo レプリケーション | • RPO は 30 分未満。<br/>• ERT は 2 時間未満。 | • RPO は 5 秒未満。<br/>• ERT は 30 秒未満。 |
| アクティブ geo レプリケーション | • RPO は 5 分未満。<br/>• ERT は 1 時間未満。 | • RPO は 5 秒未満。<br/>• ERT は 30 秒未満。 |


詳細については、[SQL Database のビジネス継続性](https://msdn.microsoft.com/library/azure/hh852669.aspx)に関するページを参照してください。


## 今すぐアップグレードすることをお勧めするその他の理由


Azure SQL Database を今すぐ V11 から V12 にアップグレードすることをお勧めする理由は多数あります。


- SQL Database V12 には、V11 にない機能が多数あります。
- V12 には新しい機能を追加していくものの、V11 に新機能は追加されません。
- ほとんどの新機能は、Microsoft SQL Server 向けにリリースされる前に、SQL Database V12 でリリースされます。


## V12 を既に使っている場合


以前のバージョンの SQL Database サービスでデータベースまたは論理サーバーが実行されているかどうかは、次の方法で簡単に確認できます。


1. [Azure プレビュー ポータル](http://portal.azure.com/)にアクセスします。
2. **[参照]** をクリックします。
3. **[SQL Server]** をクリックします。
4. サーバーまたはデータベースの隣のアイコンから、現在の状態がわかります。
 - ![Icon for a v12 server](./media/sql-database-v12-whats-new/v12_icon.png) **V12 の論理サーバー**
 - ![Icon for earlier version server](./media/sql-database-v12-whats-new/earlier_icon.png) **以前のバージョンの論理サーバー**


データベースで `SELECT @@version;` ステートメントを実行し、次のような結果を確認することで、バージョンを確認することもできます。


- **12**.0.2000.10 &nbsp; *(バージョン V12)*
- **11**.0.9228.18 &nbsp; *(バージョン V11)*


V12 の論理サーバーでのみ、V12 データベースをホストできます。V12 サーバーは V12 データベースのみをホストできます。


まだ V12 で実行していない場合は、[SQL Database V12 へのインプレース アップグレード](sql-database-v12-upgrade.md)の手順に従って、論理サーバーをアップグレードできます。


## <a name="V12AzureSqlDbPreviewGaTable"></a>プレビューのリージョン


V12 は 2014 年 12 月にリリースされましたが、[プレビュー](http://azure.microsoft.com/support/legal/preview-supplemental-terms/)の段階でした。2015 年 12 月までに、V12 はほとんどの地理的リージョンで一般公開 (GA) に昇格されました。


V12 は、次のリージョンではプレビューとして利用できます。


| Azure リージョン | V12 の現在の<br/>リリース状況 | GA への<br/>昇格日 |
| :--- | :--- | :--- |
| オーストラリア東部 | **プレビュー** | 2015 年第 2 四半期の予定 |
| オーストラリア南東部 | **プレビュー** | 2015 年第 2 四半期の予定 |

 

<!---HONumber=58_postMigration-->