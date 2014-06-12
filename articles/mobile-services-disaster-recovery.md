<properties linkid="mobile-services-recovery-disaster" urlDisplayName="障害発生時のモバイル サービスの復旧" pageTitle="障害発生時のモバイル サービスの復旧 - Azure モバイル サービス" metaKeywords="" description="障害発生時にモバイル サービスを復旧する方法について説明します" metaCanonical="" services="" documentationCenter="Mobile" title="障害発生時のモバイル サービスの復旧" authors="yavorg" solutions="" manager="" editor="" />

# 障害発生時のモバイル サービスの復旧

Azure のモバイル サービスを使用して、アプリケーションを展開した場合、組み込み機能を使用することで、サーバーの障害、ネットワークの中断、データの消失、広範囲に及ぶ設備の損失など、可用性の問題が発生したときにビジネス継続性を維持できます。Azure のモバイル サービスを使用して、アプリケーションを展開することで、従来の内部設置型ソリューションを展開する場合に設計、実装、および管理する必要があるさまざまなフォールト トレランス機能やインフラストラクチャ機能を利用することができます。Azure により、わずかな料金で潜在的な障害の大部分が軽減されます。

<h2><a name="prepare"></a><span class="short-header">準備</span>考えられる災害に対応する準備</h2>

可用性の問題が発生した場合に簡単に復旧するには、あらかじめ対応する準備を整えておきます。

+ **Azure のモバイル サービスの SQL データベースのデータをバックアップする**
	<br/>モバイル サービス アプリケーションのデータは Azure SQL データベースに格納されています。「[Azure SQL データベースにおけるビジネス継続性]」の説明に従ってバックアップすることをお勧めします。
+ **モバイル サービス スクリプトをバックアップする**
	<br/>[Team Foundation Service] や [GitHub] などのソース管理システムにモバイル サービス スクリプトを保存し、モバイル サービス自体でのコピーのみに頼らないようにすることをお勧めします。スクリプトは、モバイル サービスの[ソース管理機能]または [Azure コマンド ライン ツール]を使用して、Azure ポータルからダウンロードできます。ポータルで "プレビュー" というラベルが付いている機能には、細心の注意を払ってください。それらのスクリプトの復旧は保証されておらず、独自のソース管理の元のスクリプトから復旧することが必要になる場合があります。
+ **セカンダリ モバイル サービスを占有に設定する**
	<br/>モバイル サービスで可用性の問題が発生した場合、Azure の代替リージョンに再展開することが必要になる場合があります。(たとえば、リージョン全体のデータの損失などのまれな状況で) 容量の可用性を確保するためには、代替リージョンにセカンダリ モバイル サービスを作成し、そのモードをプライマリ サービスのモードと同じかそれ以上に設定することをお勧めします。(プライマリ サービスが共有モードの場合、セカンダリ サービスは共有と占有のどちらかに設定できます。ただし、プライマリが占有の場合、セカンダリも占有である必要があります)。


<h2><a name="watch"></a><span class="short-header">監視</span>問題の兆候の監視</h2>

次の状況は、復旧操作が必要となる可能性がある問題を示しています。

+ モバイル サービスに接続されているアプリケーションが長時間にわたってモバイル サービスと通信できない。
+ [Azure ポータル]で、モバイル サービスの状態が "**異常**" と表示されている。
+ Azure ポータルで、モバイル サービスのどのタブの上部にも "**異常**" というバナーが表示され、管理操作を実行するとエラー メッセージが生成される。
+ [Azure サービス ダッシュボード]に可用性の問題が示されている。

<h2><a name="recover"></a><span class="short-header">復旧</span>災害からの復旧</h2>

問題が発生した場合は、サービス ダッシュボードを使用して、ガイダンスと最新情報を取得します。
 
サービス ダッシュボードが表示されたら、次の手順を実行して、モバイル サービスを Azure の代替リージョンで "実行中" 状態に復元します。あらかじめセカンダリ サービスを作成している場合は、その容量を使用して、プライマリ サービスが復元されます。プライマリ サービスの URL とアプリケーション キーは復旧後も変更されないため、プライマリ サービスを参照するアプリケーションを更新する必要はありません。

停電後にモバイル サービスを復旧するには、以下の手順を実行します。

1. Azure ポータルで、サービスの状態が "**異常**" と報告されていることを確認します。

2. セカンダリ モバイル サービスを既に占有に設定している場合は、この手順をスキップできます。

   セカンダリ モバイル サービスをまだ占有に設定していない場合は、Azure の別のリージョンにセカンダリ モバイル サービスを 1 つ作成します。モードをプライマリ サービスのモードと同じか、それよりも高く設定します (プライマリ サービスが共有モードの場合、セカンダリ サービスは共有と占有のどちらかに設定できます。ただし、プライマリが占有の場合、セカンダリも占有である必要があります)。

3. 「[コマンド ライン ツールを使用したモバイル サービスの自動化]」の説明に従って、サブスクリプションを操作できるように Azure コマンド ライン ツールを構成します。

4. これで、セカンダリ サービスを使用して、プライマリ サービスを復旧できます。

    <div class="dev-callout"><b>重要</b>
	<p>この手順のコマンドを実行すると、セカンダリ サービスの容量を使用してプライマリ サービスを復旧できるように、セカンダリ サービスは削除されます。スクリプトと設定を保持する場合は、コマンドを実行する前に、バックアップすることをお勧めします。</p>
    </div>

   準備ができたら、次のコマンドを実行します。

		azure mobile recover PrimaryService SecondaryService
		info:    Executing command mobile recover
		Warning: this action will use the capacity from the mobile service 'SecondaryService' and delete it. Do you want to recover the mobile service 'PrimaryService'? (y/n): y
		+ Performing recovery
		+ Cleaning up
		info:    Recovery complete
		info:    mobile recover command OK

    <div class="dev-callout"><b>注</b>
	<p>コマンドが完了してからポータルに変更が反映されるまでに数分かかる場合があります。</p>
    </div>

5. すべてのスクリプトをソース管理内の元のスクリプトと比較して、正しく復旧されていることを確認します。ほとんどの場合、スクリプトはデータが失われることなく自動的に復旧されます。ただし、相違がある場合は、そのスクリプトを手動で復旧できます。

6. 復旧したサービスが Azure SQL データベースと通信していることを確認します。復旧コマンドでは、モバイル サービスが復旧されますが、元のデータベースへの接続が保持されます。Azure のプライマリ リージョンの問題がデータベースにも影響を与えている場合は、復旧したサービスが正常に実行されないことがあります。特定のリージョンのデータベースの状態は、Azure サービス ダッシュボードを使用して確認できます。元のデータベースが動作していない場合は、次の手順でデータベースを復旧できます。
	+ 「[Azure SQL データベースにおけるビジネス継続性]」の説明に従って、モバイル サービスを復旧した Azure のリージョンに対して Azure SQL データベースを復旧します。
	+ Azure ポータルで、モバイル サービスの **[構成]** タブにある [データベースの変更] をクリックし、新しく復旧したデータベースを選択します。

これで、モバイル サービスが Azure の新しいリージョンに復旧され、元の URL を使用して、ストア アプリからトラフィックを受け取っている状態になります。

<!-- Anchors. -->

<!-- Images. -->

<!-- URLs. -->
[Azure SQL データベースにおけるビジネス継続性]: http://msdn.microsoft.com/ja-jp/library/windowsazure/hh852669.aspx
[Team Foundation Service]: http://tfs.visualstudio.com/

[ソース管理機能]: http://www.windowsazure.com/ja-jp/develop/mobile/tutorials/store-scripts-in-source-control/
[Azure コマンド ライン ツール]: http://www.windowsazure.com/ja-jp/develop/mobile/tutorials/command-line-administration/
[Azure ポータル]: http://manage.windowsazure.com/
[Azure サービス ダッシュボード]: http://www.windowsazure.com/ja-jp/support/service-dashboard/
[コマンド ライン ツールを使用したモバイル サービスの自動化]: http://www.windowsazure.com/ja-jp/develop/mobile/tutorials/command-line-administration/
