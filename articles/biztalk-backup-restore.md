<properties linkid="biztalk-backup-restore" urlDisplayName="BizTalk サービス: バックアップと復元" pageTitle="BizTalk サービス: バックアップと復元 | Azure" metaKeywords="" description="BizTalk サービスには、バックアップ機能と復元機能が備わっています。バックアップを作成すると、Azure BizTalk サービス構成のスナップショットが取得されます。" metaCanonical="" services="" documentationCenter="" title="BizTalk サービス: バックアップと復元" authors=""  solutions="" writer="mandia" manager="paulettm" editor="cgronlun"  />


# BizTalk サービス: バックアップと復元

Azure BizTalk サービスには、バックアップ機能と復元機能が備わっています。バックアップを作成すると、Azure BizTalk サービス構成のスナップショットが取得されます。

次のシナリオで考えてみましょう。


- Azure 管理ポータルを使用して、BizTalk サービス構成を 2 つの方法でバックアップすることができます - 必要に応じてバックアップを作成 (アドホック バックアップ) するか、スケジュールされたバックアップを実行します。

- バックアップ コンテンツは、同じ BizTalk サービスまたは新しい BizTalk サービスに復元できます。同じ名前を使用して BizTalk サービスを復元するには、既存の BizTalk サービスを削除する必要があります。そうしないと、復元に失敗します。

- BizTalk サービスは、同じエディション以上に復元できます。バックアップを実行したときより下位のエディションへの BizTalk サービスの復元はサポートされていません。

	たとえば、基本エディションを使用したバックアップはプレミアム エディションに復元できます。プレミアム エディションを使用したバックアップを標準エディションに復元することはできません。

- コントロール番号の連続性を維持するため、EDI コントロール番号がバックアップされます。前回のバックアップ後にメッセージが処理された場合、このバックアップ コンテンツを復元するとコントロール番号が重複する可能性があります。

- バックアップと復元を、BizTalk サービス開発者エディションの一部として利用することはできません。

このトピックでは、Azure 管理ポータルを使用して BizTalk サービスをバックアップおよび復元する方法について説明しています。また、REST API を使用して BizTalk サービスをバックアップすることもできます。詳細については、[BizTalk サービスの REST API に関するページ](http://msdn.microsoft.com/library/windowsazure/dn232347.aspx)を参照してください。

[バックアップ対象](#budata)

[バックアップの作成](#createbu)

[復元](#restore)


##<a name="budata"></a>バックアップ対象

バックアップが作成されるとき、次の項目がバックアップされます。

<table border="1"> 
<TR bgcolor="FAF9F9">
<th> </th>
<TH>バックアップされる項目</TH>
</TR> 
<TR>
<td colspan="2">
 <strong>Azure BizTalk サービス ポータル</strong></td>
</TR> 
<TR>
<TD>構成とランタイム</TD>
<TD><bl>
<li>パートナーとプロファイルの詳細</li>
<li>パートナー契約</li>
<li>デプロイされているカスタム アセンブリ</li>
<li>デプロイされているブリッジ</li>
<li>証明書</li>
<li>デプロイされている変換</li>
<li>パイプライン</li>
<li>BizTalk サービス ポータルで作成および保存されたテンプレート</li>
<li>X12 ST01 および GS01 マッピング</li>
<li>コントロール番号 (EDI)</li>
<li>AS2 メッセージ MIC 値</li>
</bl></TD>
</TR> 
 
<TR>
<td colspan="2">
 <strong>Azure BizTalk サービス</strong></td>
</TR> 
<TR>
<TD>SSL 証明書</TD>
<TD>
<bl>
<li>SSL 証明書データ</li>
<li>SSL 証明書のパスワード</li>
</bl>
</TD>
</TR> 
<TR>
<TD>BizTalk サービスの設定</TD>
<TD>
<bl>
<li>スケール ユニット数</li>
<li>エディション</li>
<li>製品バージョン</li>
<li>リージョン/データセンター</li>
<li>Access Control サービス (ACS) の名前空間とキー</li>
<li>トラッキング データベースの接続文字列</li>
<li>アーカイブ ストレージ アカウントの接続文字列</li>
<li>監視ストレージ アカウントの接続文字列</li>
</bl></TD>
</TR> 
<TR>
<td colspan="2">
 <strong>その他の項目</strong></td>
</TR> 
<TR>
<TD>トラッキング データベース</TD>
<TD>BizTalk サービスがプロビジョニングされると、Azure SQL データベース サーバーやトラッキング データベース名など、トラッキング データベースの詳細が入力されます。トラッキング データベースは自動的にはバックアップされません。<br/><br/>
<strong>重要</strong><br/>
トラッキング データベースが誤って削除されたため、データベースを復旧する必要がある場合、前のバックアップが存在している必要があります。バックアップが存在しない場合、トラッキング データベースとそのデータは復旧できません。このような状況では、同じデータベース名の新しいトラッキング データベースを作成します。geo レプリケーションが推奨されます。</TD>
</TR> 
</table>

##<a name="createbu"></a>バックアップの作成

バックアップはいつでも取得でき、完全にユーザーによって制御されます。Azure 管理ポータルから、または BizTalk サービスの REST API からバックアップを実行することができます。BizTalk サービスの REST API を使用してバックアップを作成するには、<a HREF="http://go.microsoft.com/fwlink/p/?LinkID=325584">BizTalk サービスのバックアップに関するページ</a>を参照してください。

このセクションでは、管理ポータルを使用してバックアップを実行する方法について説明します。管理ポータルを使用して、アドホック バックアップを作成するか、適切な間隔でバックアップを実行するようにスケジュールすることができます。

##<a name="beforebackup"></a>バックアップを作成する前に

バックアップを作成する前に、次の注意事項に従うことを確認してください。

1. バッチになっているメッセージに対してアドホック バックアップを実行する前に、メッセージのバッチを処理してください。これにより、このバックアップを復元した場合に<i></i>メッセージが失われるのを防ぐことができます。バックアップの実行時に、バッチになっているメッセージが保存されることはありません。スケジュールされたバックアップを使用する場合は、バックアップを開始するときに、メッセージがバッチになっていないことを確認できない可能性があります。
	<div class="dev-callout"><b>メモ</b>
<p>アクティブなメッセージがバッチになっている状態で、それらに対応するバックアップを取得しようとしても、それらのメッセージはバックアップされないため、失われます。</p></div>
2. オプション: BizTalk サービス ポータルで、管理操作をすべて停止します。
4. REST API で使用可能な <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=325584">BizTalk サービスのバックアップ</a> コマンドを使用して、ストレージ アカウントに対してバックアップを作成します。

[アドホック バックアップ](#backupnow)

[バックアップのスケジュール](#backupschedule)

###<a name="backupnow"></a>アドホック バックアップ
1. Azure 管理ポータルで [BizTalk サービス] をクリックし、バックアップする BizTalk サービスの名前をクリックします。
2. [BizTalk サービス] の <b>[ダッシュボード]</b> タブで、ページの下部にある <b>[バックアップ]</b> をクリックします。
3. <b>[BizTalk サービスのバックアップ]</b> ダイアログ ボックスで、バックアップ名を指定します。
4. BLOB ストレージ アカウントを選択し、バックアップを開始するチェックマークをクリックします。


バックアップが完了すると、ストレージ アカウントの下に、指定したバックアップ名を持つコンテナーが作成されます。このコンテナーには、BizTalk サービスのバックアップ構成が含まれています。


###<a name="backupschedule"></a>バックアップのスケジュール

1. Azure 管理ポータルで [BizTalk サービス] をクリックし、自動バックアップのスケジュール対象として使用する BizTalk サービス名をクリックして、**[構成]** タブをクリックします。
2. 自動バックアップのスケジュールを設定しない場合は、**[バックアップ ステータス]** として **[なし]** を選択します。自動バックアップをスケジュールするには、**[自動]** をクリックします。
3. **ストレージ アカウント**を対象にする場合は、バックアップの作成場所として使用する Azure ストレージ アカウントを選択します。
4. **[頻度]** で、最初のバックアップに対応する開始日と時刻、およびバックアップを実行する間隔 (日数単位) を指定します。
5. **[保持日数]** で、バックアップを保持する期間 (日数単位) を指定します。保持期間は、バックアップ頻度より大きくする必要があります。
6. **[常に 1 つ以上のバックアップを保存してください]** チェックボックスをオンにし、保持期間が過ぎた場合でも少なくとも 1 つのバックアップが利用できるようにします。
7. **[保存]** をクリックします。

スケジュールされたバックアップ ジョブを実行すると、指定したストレージ アカウント内に、(バックアップ データを格納するための) コンテナーが作成されます。コンテナーの名前は、*BizTalk サービス名-日付-時刻* という形式になります。

バックアップが失敗した場合は、BizTalk サービスのダッシュボード ページで、バックアップの状態として **[失敗]** が表示されます。

![最後のスケジュールされたバックアップの状態][BackupStatus]

リンクをクリックして、管理サービスの操作ログ ページに移動し、障害の詳細を確認することができます。BizTalk サービスの操作ログの詳細については、[BizTalk サービス: 操作ログを使用したトラブルシューティングに関するページ](http://go.microsoft.com/fwlink/?LinkId=391211)を参照してください。

##<a name="restore"></a>復元

Azure 管理ポータルから、または BizTalk サービスの REST API からバックアップを復元することができます。このセクションでは、管理ポータルを使用して復元を実行する方法について説明します。REST API を使用して復元を実行するには、[バックアップからの BizTalk サービスの復元に関するページ](http://go.microsoft.com/fwlink/p/?LinkID=325582)を参照してください。

###バックアップの復元前

バックアップを復元するときは、次の点を考慮してください。

- BizTalk サービスを復元するときに、新しいトラッキング、アーカイブ、および監視ストアを指定できます。

- 同じ名前を使用して BizTalk サービスを復元するには、復元を開始する前に、既存の BizTalk サービスを削除します。そうしないと、復元に失敗します。

- 同じ EDI ランタイム データが復元されます。EDI ランタイム バックアップでは、コントロール番号が保存されます。復元されるコントロール番号は、バックアップの時点から順番に付けられます。前回のバックアップ後にメッセージが処理された場合、このバックアップ コンテンツを復元するとコントロール番号が重複する可能性があります。

####バックアップを復元するには

1. Azure 管理ポータルで <b>[新規]</b> をクリックし、<b>[アプリケーション サービス]</b>、<b>[BizTalk サービス]</b> の順にポイントして、<b>[復元]</b>  をクリックします。

	![バックアップの復元][Restore]

2. 新しい BizTalk サービス - 復元 ウィザードの <b>[BizTalk サービスの復元]</b> ページで、<b>[バックアップ URL]</b> ボックスのフォルダー アイコンをクリックし、<b>[クラウド ストレージの参照]</b> ダイアログ ボックスを開きます。ダイアログ ボックスで、使用している Azure ストレージ アカウントが表示されます。

	バックアップを作成したとき、またはバックアップのスケジュールを設定したときに指定したストレージ アカウントを展開し、BizTalk サービス構成の復元元として使用する必要のあるコンテナー名を選択します。

	右側のウィンドウで、復元元として使用するバックアップに対応する .txt ファイルを選択し、<b>[開く]</b> をクリックします。

3. <b>[BizTalk サービスの復元]</b> ページで、次の情報を指定します。
- BizTalk サービス名を指定します。既定では、バックアップされた BizTalk サービスの名前が使用されます。
- 復元しようとする BizTalk サービスのドメイン URL、エディション、およびリージョンを確認します。
- トラッキング データベースに対応する新しい SQL データベース インスタンスを作成するように選択し、右矢印をクリックします。

4. 	<b>[データベース設定の指定]</b> ページで、SQL データベースの名前を確認し、SQL データベースの作成場所になる物理サーバーと、そのサーバーのユーザー名およびパスワードを指定します。

	SQL データベースの詳細設定を構成する場合は、<b>[データベースの詳細設定を構成します]</b> チェック ボックスをオンにし、右矢印をクリックします。

	データベースの詳細設定を構成しない場合は、右矢印をクリックし、ステップ 6 に進みます。
5. <b>[データベースの設定詳細]</b> ページで、使用するデータベースのエディション (<b>Web</b> または <b>Business</b>) を選択し、データベースの最大サイズと照合順序規則を指定します。右矢印をクリックします。

6. <b>[監視/アーカイブ設定の指定]</b> ページで、新しいストレージ アカウントを作成するか、BizTalk サービスの監視情報の格納に使用する既存のストレージ アカウントを指定します。

7. チェックマークをクリックして、復元処理を開始します。</b>

8. 復元が正常に完了すると、Azure 管理ポータルの [BizTalk サービス] ページで、新しい BizTalk サービスが中断という状態で表示されます。

###<a name="postrestore"></a>バックアップの復元後

BizTalk サービスは常に、**[中断]** 状態で復元されます。この結果、新しい環境を機能させる前に、開発したアプリケーション内や、必要に応じて BizTalk サービス アプリケーション内で、さまざまな構成を変更することができます。新しく復元した BizTalk サービスを起動する前に実行する必要がある、このような考慮事項のいくつかを次に示します。

- Azure BizTalk サービス SDK を使用して BizTalk サービス アプリケーションを作成した場合は、復元された環境で動作するように、アプリケーション内で ACS の資格情報を更新する必要が生じることがあります。

- 既に機能している BizTalk サービス環境を複製するために、BizTalk サービス機能を復元することも考えられます。このようなシナリオで、FTP 共有を使用する BizTalk サービス ポータルを複製元として使用し、そのポータル内で契約を構成した場合は、新しく復元した環境で契約を更新して、他の複製元または FTP 共有を使用する可能性もあります。このような作業に失敗した場合は、2 つの異なる契約が存在し、それらが同じメッセージを取得しようとする可能性があります。

- 復元操作を使用して複数の BizTalk サービス環境を用意した場合は、Visual Studio アプリケーション、PowerShell コマンドレット、REST API、または取引先管理オブジェクト モデル (TPM OM) API を使用して、適切な環境をターゲットにしたことを確認します。

- また、新しく復元した BizTalk サービス環境で自動バックアップを構成することをお勧めします。

これらの考慮事項や他の考慮事項を満たした後、Azure 管理ポータルの [BizTalk サービス] ページに移動し、中断された状態にある、新しく復元した BizTalk サービスを選択して、ページの下部にある **[再開]** をクリックしてそのサービスを開始します。

## 次のステップ

Azure BizTalk サービスを Azure 管理ポータルでプロビジョニングするには、[Azure 管理ポータルを使用した BizTalk サービスのプロビジョニング](http://go.microsoft.com/fwlink/p/?LinkID=302280)に関するページを参照してください。アプリケーションの作成を開始するには、[Azure BizTalk サービス](http://go.microsoft.com/fwlink/p/?LinkID=235197)に関するページを参照してください。

## 関連項目
- [BizTalk サービスのバックアップに関するページ](http://go.microsoft.com/fwlink/p/?LinkID=325584)
- [バックアップからの BizTalk サービスの復元に関するページ](http://go.microsoft.com/fwlink/p/?LinkID=325582)
- [BizTalk サービス: 開発者、基本、標準、およびプレミアム エディションのチャートに関するページ](http://go.microsoft.com/fwlink/p/?LinkID=302279)
- [BizTalk サービス: Azure 管理ポータルを使用した BizTalk サービスのプロビジョニングに関するページ](http://go.microsoft.com/fwlink/p/?LinkID=302280)
- [BizTalk サービス: プロビジョニングの状態のチャートに関するページ](http://go.microsoft.com/fwlink/p/?LinkID=329870)
- [BizTalk サービス: [ダッシュボード]、[監視]、および [スケール] タブに関するページ](http://go.microsoft.com/fwlink/p/?LinkID=302281)
- [BizTalk サービス: 調整に関するページ](http://go.microsoft.com/fwlink/p/?LinkID=302282)
- [BizTalk サービス: 発行者名および発行者キーに関するページ](http://go.microsoft.com/fwlink/p/?LinkID=303941)
- [Azure BizTalk サービス SDK の使用開始に関するページ](http://go.microsoft.com/fwlink/p/?LinkID=302335)

[BackupStatus]: ./media/biztalk-backup-restore/status-last-backup.png
[Restore]: ./media/biztalk-backup-restore/restore-ui.png
