アプリケーション データを新しいモバイル サービスに格納できるようにするには、最初に新しいテーブルを関連付けられた SQL データベース インスタンスに作成する必要があります。

1. 管理ポータルで、**[モバイル サービス]** をクリックし、先ほど作成したモバイル サービスをクリックします。

2. **[データ]** タブをクリックし、**[作成]** をクリックします。
	
   	![mobile-data-tab-empty](./media/mobile-services-create-new-service-data-2/mobile-data-tab-empty.png)

   	**[新しいテーブルの作成]** ダイアログ ボックスが表示されます。

3. **[テーブル名]** に「TodoItem」と入力し、チェック ボタンをクリックします。

 	![mobile-create-todoitem-table](./media/mobile-services-create-new-service-data-2/mobile-create-todoitem-table.png)

  これにより、既定のアクセス許可が設定された新しいストレージ テーブル **TodoItem** が作成されます。これは、アプリケーションと共に配布されるアプリケーション キーを使用してだれもがテーブル内のデータにアクセスし、変更を加えることができることを表します。

> [WACOM.NOTE] 
> モバイル サービスのクイック スタートでも同じテーブル名が使用されています。ただし、それぞれのテーブルは、特定のモバイル サービスに固有のスキーマ内で作成されています。これには、複数のモバイル サービスで同じデータベースを使用した場合にデータの競合を回避する目的があります。

4. 新しい **TodoItem** テーブルをクリックし、データ行がないことを確認します。

5. **[列]** タブをクリックします。次の既定の列が自動的に作成されていることを確認します。
	
	<table border="1" cellpadding="10">
 	<tr>
 	<th>列名</th>
 	<th>種類</th>
 	<th>インデックス</th>
 	</tr>
 	<tr>
 	<td>id</td>
 	<td>string</td>
 	<td>インデックス設定済み</td>
 	</tr>
 	<tr>
 	<td>__createdAt</td>
 	<td>date</td>
 	<td>インデックス設定済み</td>
 	</tr>
 	<tr>
 	<td>__updatedAt</td>
 	<td>date</td>
 	<td><font color="transparent">-</font></td>
 	</tr>
 	<tr>
 	<td>__version</td>
 	<td>timestamp (MSSQL)</td>
 	<td><font color="transparent">-</font></td>
 	</tr> 	
 	</table> 	
		

  	これは、モバイル サービスのテーブルの最小要件です。

    <div class="dev-callout"><b>注</b>
	<p>モバイル サービスで動的スキーマを有効にすると、挿入操作または更新操作によって JSON オブジェクトがモバイル サービスに送信されるときに新しい列が自動的に作成されます。</p>
    </div>

これで、新しいモバイル サービスをアプリケーションのデータ ストレージとして使用する準備が整いました。

