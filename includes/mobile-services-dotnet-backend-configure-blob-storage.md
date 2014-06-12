新規 Todo 項目を挿入すると **PostTodoItem** メソッドが SAS を生成するように、既存の **TodoItemController** を更新します。

0. まだストレージ アカウントを作成していない場合には、「[How To Create a Storage Account (ストレージ アカウントの作成方法)]」を参照してください。

1. 管理ポータルで、**[ストレージ]**、ストレージ アカウント、**[キーの管理]** の順にクリックします。

  	![](./media/mobile-services-configure-blob-storage/mobile-blob-storage-account.png)

2. **ストレージ アカウント名**と**アクセス キー**をメモします。

   	![](./media/mobile-services-configure-blob-storage/mobile-blob-storage-account-keys.png)

3. モバイル サービスで、**[構成]** タブをクリックし、**[アプリケーション設定]** まで下へスクロールします。次に、ストレージ アカウントから取得した次の項目の名前と値をそれぞれ **[名前]** と **[値]** に入力して、**[保存]** をクリックします。

	+ `STORAGE_ACCOUNT_NAME`
	+ `STORAGE_ACCOUNT_ACCESS_KEY`

	![](./media/mobile-services-configure-blob-storage/mobile-blob-storage-app-settings.png)

	ストレージ アカウントのアクセス キーは暗号化された状態でアプリケーション設定に保存されます。このキーには、実行時にどのサーバー スクリプトからでもアクセスできます。詳細については、「[アプリ設定]」を参照してください。

4. Visual Studio のソリューション エクスプローラーで、モバイル サービス プロジェクトに対応する Web.config ファイルを開き、次の新しいアプリケーション設定を追加します。プレースホルダーは、ストレージ アカウント名、およびポータルのアプリケーション設定として設定したアクセス キーと置き換えてください。

		<add key="STORAGE_ACCOUNT_NAME" value="**your_account_name**" />
		<add key="STORAGE_ACCOUNT_ACCESS_KEY" value="**your_access_token_secret**" />

	モバイル サービスをローカル コンピューター上で実行するときに、モバイル サービスはこれら保存されている設定を使用するため、コードを発行する前にテストすることができます。Azure 内で実行する場合、モバイル サービスは代わりにポータル内のアプリケーション設定値を使用し、これらのプロジェクト設定を無視します。

5. Controllers フォルダーで、TodoItemController.cs ファイルを開き、次の **using** ディレクティブを追加します。

		using System;
		using Microsoft.WindowsAzure.Storage.Auth;
		using Microsoft.WindowsAzure.Storage.Blob;
  
8. 既存の **PostTodoItem** メソッドを次のコードに置き換えます。

		var azure = require('azure');
		var qs = require('querystring');
		var appSettings = require('mobileservice-config').appSettings;
		
		function insert(item, user, request) {
		    // Get storage account settings from app settings. 
		    var accountName = appSettings.STORAGE_ACCOUNT_NAME;
		    var accountKey = appSettings.STORAGE_ACCOUNT_ACCESS_KEY;
		    var host = accountName + '.blob.core.windows.net';
		
		    if ((typeof item.containerName !== "undefined") && (
		    item.containerName !== null)) {
		        // Set the BLOB store container name on the item, which must be lowercase.
		        item.containerName = item.containerName.toLowerCase();
		
		        // If it does not already exist, create the container 
		        // with public read access for blobs.        
		        var blobService = azure.createBlobService(accountName, accountKey, host);
		        blobService.createContainerIfNotExists(item.containerName, {
		            publicAccessLevel: 'blob'
		        }, function(error) {
		            if (!error) {
		
		                // Provide write access to the container for the next 5 mins.        
		                var sharedAccessPolicy = {
		                    AccessPolicy: {
		                        Permissions: azure.Constants.BlobConstants.SharedAccessPermissions.WRITE,
		                        Expiry: new Date(new Date().getTime() + 5 * 60 * 1000)
		                    }
		                };
		
		                // Generate the upload URL with SAS for the new image.
		                var sasQueryUrl = 
		                blobService.generateSharedAccessSignature(item.containerName, 
		                item.resourceName, sharedAccessPolicy);
		
		                // Set the query string.
		                item.sasQueryString = qs.stringify(sasQueryUrl.queryString);
		
		                // Set the full path on the new new item, 
		                // which is used for data binding on the client. 
		                item.imageUri = sasQueryUrl.baseUrl + sasQueryUrl.path;
		
		            } else {
		                console.error(error);
		            }
		            request.execute();
		        });
		    } else {
		        request.execute();
		    }
		}

 	![][4]

   	この操作により、TodoItem テーブルで挿入が発生したときに呼び出される関数が、新しいスクリプトに置き換わります。この新しいスクリプトは、挿入のための新しい SAS を生成し (5 分間有効)、生成された SAS の値を返された項目の `sasQueryString` プロパティに割り当てます。`imageUri` プロパティに新しい BLOB のリソース パスが設定され、クライント UI でのバインド中にイメージを表示できるようになります。

	>[WACOM.NOTE]このコードでは、個々の BLOB に対して SAS が作成されます。同じ SAS を使用してコンテナーに複数の BLOB をアップロードする場合は、代わりに、空の BLOB リソース名を使って <a href="http://go.microsoft.com/fwlink/?LinkId=390455" target="_blank">generateSharedAccessSignature メソッド</a>を呼び出すことができます。次に例を示します。
	<pre><code>blobService.generateSharedAccessSignature(containerName, '', sharedAccessPolicy);</code></pre>

次に、挿入時に生成される SAS を使用してイメージ アップロード機能を追加する、クイック スタート アプリケーションを更新します。
 
<!-- Anchors. -->

<!-- Images. -->
[0]: ./media/mobile-services-configure-blob-storage/mobile-blob-storage-account.png
[1]: ./media/mobile-services-configure-blob-storage/mobile-blob-storage-account-keys.png

[3]: ./media/mobile-services-configure-blob-storage/mobile-portal-data-tables.png
[4]: ./media/mobile-services-configure-blob-storage/mobile-insert-script-blob.png





[10]: ./media/mobile-services-configure-blob-storage/mobile-blob-storage-app-settings.png

<!-- URLs. -->
[How To Create a Storage Account (ストレージ アカウントの作成方法)]: /ja-jp/manage/services/storage/how-to-create-a-storage-account
[アプリ設定]: http://msdn.microsoft.com/ja-jp/library/windowsazure/b6bb7d2d-35ae-47eb-a03f-6ee393e170f7
