<properties umbracoNaviHide="0" pageTitle="ストレージ アカウントの概念 | Azure" metaKeywords="Azure ストレージ, ストレージ サービス, サービス, ストレージ アカウント, アカウント, ストレージ アカウントの作成, アカウントの作成" description="ストレージ アカウントの概念について説明します。" linkid="manage-windows-how-to-guide-storage-accounts" urlDisplayName="ストレージ アカウントの管理方法" headerExpose="" footerExpose="" disqusComments="1" title="ストレージ アカウントの概念" authors="" />


<h1 id="storageaccountconcepts">ストレージ アカウントの概念</h1>


- **Geo 冗長ストレージ (GRS)**   Geo 冗長ストレージでは、同じリージョン内の 2 次拠点にデータをシームレスに複製することにより、最も優れたレベルのストレージの持続性が実現されます。この結果、1 次拠点で大規模な障害が発生した場合でも、フェールオーバーが利用できます。2 次拠点は、1 次拠点から数百 km 離れています。GRS は、ストレージ アカウントで既定で有効になっている *Geo レプリケーション*と呼ばれる機能を使用して実装されますが、この機能を使用することを望まない (たとえば、会社のポリシーで使用が禁止されている) 場合は、無効にできます。詳細については、[Azure のストレージの Geo レプリケーションの概要に関するページ](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/09/15/introducing-geo-replication-for-windows-azure-storage.aspx)を参照してください。

- **ローカル冗長ストレージ (LRS)**   単一拠点内にある、持続性と可用性に優れたローカル冗長ストレージです。ローカル冗長ストレージの場合、アカウント データは同じデータ センター内で 3 回複製されます。Azure 内のすべてのストレージは、ローカル冗長です。持続性を強化するために、Geo レプリケーションを有効にすることもできます。ローカル冗長ストレージは、割引価格で使用できます。料金情報については、[料金に関するページ](http://www.windowsazure.com/ja-jp/pricing/details/)を参照してください。

- **アフィニティ グループ**   *アフィニティ グループ*は、Azure 内でクラウド サービス デプロイとストレージ アカウントを地理的にまとめたグループです。アフィニティ グループは、コンピューティング ワークロードを同じデータ センター内または対象ユーザーの近くに配置することにより、サービス パフォーマンスを向上させることができます。また、送信に対して課金は適用されません。

- **ストレージ アカウント エンドポイント**   BLOB、テーブル、またはキューにアクセスするための最高レベルの名前空間を表すストレージ アカウントの*エンドポイント*です。ストレージ アカウントに対応する既定のエンドポイントでは、次の書式を使用します。

    - BLOB サービス: http://*mystorageaccount*.blob.core.windows.net

    - テーブル サービス: http://*mystorageaccount*.table.core.windows.net

    - キュー サービス: http://*mystorageaccount*.queue.core.windows.net

- **ストレージ アカウント URL**   ストレージ アカウント内にあるオブジェクトにアクセスするための URL を作成するには、ストレージ アカウント内でのオブジェクトの場所をエンドポイントに追加します。たとえば、BLOB アドレスは、http://*mystorageaccount*.blob.core.windows.net/*mycontainer*/*myblob* のような形式になります。

- - **ストレージ アクセス キー**   ストレージ アカウントを作成するときに、Azure によって 2 つの 512 ビット ストレージ アクセス キーが生成されます。これらは、ストレージ アカウントにアクセスするときに認証の目的で使用されます。Azure によって 2 つのストレージ アクセス キーが提供される結果、ストレージ サービスやサービスへのアクセスを中断することなく、これらのキーを再生成できます。

- **最小限のメトリックと詳細メトリックの比較**   ストレージ アカウントの監視設定で、最小限のメトリック、または詳細メトリックを構成できます。*最低限のメトリック*の場合は、Blob サービス、テーブル サービス、キュー サービスに関して集計される、受信/送信、可用性、遅延時間、成功のパーセンテージなどのデータを収集します。*詳細メトリック*の場合は、同じメトリックに関するサービス レベルの集計に加えて、オペレーションレベルの詳細を収集します。詳細メトリックにより、アプリケーションの操作中に発生する問題を詳しく分析できます。使用可能なメトリックの詳細なリストについては、「[Storage Analytics Metrics のテーブル スキーマ](http://msdn.microsoft.com/ja-jp/library/windowsazure/hh343264.aspx)」を参照してください。ストレージ監視の詳細については、「[Storage Analytics Metrics について](http://msdn.microsoft.com/ja-jp/library/windowsazure/hh343258.aspx)」を参照してください。

- **ログ**   ログはストレージ アカウントの構成可能な機能であり、BLOB、テーブル、およびキューへの読み取り、書き込み、削除の各要求をログに記録できます。Azure の管理ポータルでログを構成しますが、管理ポータルでログを表示することはできません。ログは、ストレージ アカウント内の $logs コンテナーに格納され、アクセスできます。詳細については、「[Storage Analytics Overview (Storage Analytics の概要)](http://msdn.microsoft.com/ja-jp/library/windowsazure/hh343268.aspx)」を参照してください。
