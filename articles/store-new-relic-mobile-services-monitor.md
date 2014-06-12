<properties linkid="mobile-services-monitor-new-relic" urlDisplayName="New Relic を使用したモバイル サービスの監視" pageTitle="ソース管理へのサーバー スクリプトの保存 - Azure モバイル サービス" metaKeywords="" description="New Relic アドオンを使用してモバイル サービスを監視する方法について説明します。" metaCanonical="" disqusComments="1" umbracoNaviHide="0" documentationCenter="Mobile" title="New Relic を使用したモバイル サービスの監視" authors="" />
# New Relic を使用したモバイル サービスの監視

このトピックでは、Azure のモバイル サービスを扱えるように New Relic サード パーティ アドオンを構成して、モバイル サービスの監視機能を拡張する方法について説明します。

このチュートリアルでは、以下の各手順について説明します。

1. [Azure ストアを使用して New Relic にサインアップする]。
2. [New Relic モジュールをインストールする]。
3. [モバイル サービスに対する New Relic 開発者分析を有効にする]。
4. [New Relic ダッシュボードでモバイル サービスを監視する]。

このチュートリアルを完了するには、「[モバイル サービスの使用]」または「[データの使用]」チュートリアルを完了して、モバイル サービスを既に作成してある必要があります。

##<a name="sign-up"></a>Azure ストアを使用して New Relic にサインアップする

最初に、New Relic サービスを購入します。このチュートリアルでは、Azure ストアからこのサービスを購入する方法を紹介します。モバイル サービスでは、Azure ストア以外で購入した New Relic サブスクリプションもサポートされます。

1. [Azure 管理ポータル](https://manage.windowsazure.com)にログインします。

2. 管理ポータルの下のウィンドウの **[新規]** をクリックします。

3. **[ストア]** をクリックします。

4. **[サービスの選択]** ダイアログで、**[New Relic]** をクリックし、**[次へ]** をクリックします。

5. **[個人用サービス]** ダイアログで、希望の New Relic プランを選択します。

7. Azure の設定における New Relic サービスの表示名を入力するか、既定値
   **NewRelic** を使用します。この名前は、Azure ストアの
   サブスクライブ項目の一覧で一意になっている必要があります。

8. リージョンの値を選択します (たとえば **[米国西部]**)。

9. **[次へ]** をクリックします。

10. **[購入の確認]** ダイアログで、プランと料金情報を確認し、法律条項を確認します。条項に同意したら、**[購入]** をクリックします。

11. **[購入]** をクリックすると、New Relic アカウントの作成プロセスが開始されます。その状態は、Azure 管理ポータルで監視できます。

##<a name="install-module"></a>New Relic モジュールをインストールする

New Relic サービスにサインアップしたら、New Relic の Node.js モジュールをモバイル サービスにインストールする必要があります。このモジュールをアップロードするには、モバイル サービスに対するソース管理を有効にしておく必要があります。

1. まだそうしていない場合は、チュートリアル「[ソース管理へのサーバー スクリプトの保存]」の手順に従ってモバイル サービスのソース管理を有効にし、リポジトリを複製して、<a href="http://nodejs.org/" target="_blank">ノード パッケージ マネージャー (NPM)</a> をインストールします。

2. コマンド プロンプトで、ローカル Git リポジトリの `.\service` フォルダーに移動し、次のコマンドを実行します。

		npm install newrelic

	NPM により、[New Relic モジュール][newrelic]が `\newrelic` サブディレクトリにインストールされます。

3. **GitBash** (Windows) や **Bash** (UNIX シェル) などの Git コマンド ライン ツールを開き、Git コマンド プロンプトで次のコマンドを入力します。

		$ git add .
		$ git commit -m "added newrelic module"
		$ git push origin master
		
	これにより、新しい `newrelic` モジュールがモバイル サービスにアップロードされます。

次に、[管理ポータル][Azure Management Portal]でモバイル サービスに対する New Relic 監視を有効にします。

##<a name="enable-service"></a>モバイル サービスに対する New Relic 開発者分析を有効にする。

1. [管理ポータル][Azure Management Portal]で、モバイル サービスを選択し、**[構成]** タブをクリックします。

	![][0]

2. 下へスクロールして **[開発者分析]** を表示し、New Relic サブスクリプションをどのように購入したかに応じて、次のどちらかの手順を実行します。

	+ Purchased in the Azure store:

		**[アドオン]** をクリックし、**[アドオンの選択]** で New Relic アドオンを選択して、**[保存]** をクリックします。

		![][1]

	+ New Relic から直接購入した場合:

		**[カスタム]** をクリックし、**[プロバイダー]** で New Relic を選択して、キーを入力します。次に、**[保存]** をクリックします。

		![][2]

		このキーは New Relic ダッシュボードで取得できます。

3. 登録が完了すると、**[アプリケーション設定]** に新しい値が表示されます。

	![][3] 

##<a name="monitor"></a>New Relic ダッシュボードでモバイル サービスを監視する

1. クライアント アプリケーションを実行して、モバイル サービスに対する読み取り要求、作成要求、更新要求、および削除要求を生成します。

2. データが処理されるまで数分待った後で、New Relic ダッシュボードに移動します。

	New Relic サブスクリプションをアドオンとして購入した場合、[管理ポータル][Azure Management Portal]でそれを選択して、**[管理]** をクリックします。

3. [New Relic] で **[アプリケーション]** をクリックし、対象のモバイル サービスをクリックします。

	![][4]

4. **[Web トランザクション]** をクリックして、モバイル サービスに対して最近行われた要求を表示します。

	![][5]

##<a name="next-steps"> </a>次のステップ

+ 料金情報については、[Azure ストアの New Relic のページ]を参照してください。
+ New Relic の使用方法については、New Relic ドキュメントの「[Applications Overview (アプリケーションの概要)]」を参照してください。

<!-- Anchors. -->
[Azure ストアを使用して New Relic にサインアップする]: #sign-up
[New Relic モジュールをインストールする]: #install-module
[モバイル サービスに対する New Relic 開発者分析を有効にする]: #enable-service
[New Relic ダッシュボードでモバイル サービスを監視する]: #monitor
[次のステップ]: #next-steps

<!-- Images. -->
[0]: ./media/store-new-relic-mobile-services-monitor/mobile-configure-tab.png
[1]: ./media/store-new-relic-mobile-services-monitor/mobile-configure-new-relic-monitoring.png
[2]: ./media/store-new-relic-mobile-services-monitor/mobile-configure-new-relic-monitoring-custom.png
[3]: ./media/store-new-relic-mobile-services-monitor/mobile-configure-new-relic-monitoring-complete.png
[4]: ./media/store-new-relic-mobile-services-monitor/mobile-new-relic-dashboard.png
[5]: ./media/store-new-relic-mobile-services-monitor/mobile-new-relic-dashboard-2.png

<!-- URLs. -->
[ソース管理]: http://msdn.microsoft.com/ja-jp/library/windowsazure/c25aaede-c1f0-4004-8b78-113708761643
[モバイル サービスのサーバー スクリプトの操作]: /ja-jp/develop/mobile/how-to-guides/work-with-server-scripts.md

[Azure の管理ポータル]: https://manage.windowsazure.com/
[Node.js API ドキュメント: Modules (モジュール)]: http://nodejs.org/api/modules.html
[ソース管理へのサーバー スクリプトの保存]: /ja-jp/develop/mobile/tutorials/store-scripts-in-source-control/
[newrelic]: https://npmjs.org/package/newrelic
[Azure ストアの New Relic のページ]: /ja-jp/gallery/store/new-relic/new-relic/
[Applications Overview (アプリケーションの概要)]: https://docs.newrelic.com/docs/applications-dashboards/applications-overview
[モバイル サービスの使用]: /ja-jp/develop/mobile/tutorials/get-started/
[データの使用]: /ja-jp/develop/mobile/tutorials/get-started-with-data-dotnet

