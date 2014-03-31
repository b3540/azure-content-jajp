

1. 管理ポータルで、**[データ]** タブをクリックし、**Registrations** テーブルをクリックします。

	![][3]

2. **[Registrations]** で、**[スクリプト]** タブをクリックし、**[挿入]** をクリックします。
   
	![][4]

	**Registrations** テーブルで挿入が発生したときに呼び出される関数が表示されます。

3. insert 関数を次のコードに置き換え、**[保存]** をクリックします。

		function insert(item, user, request) {
			var registrationTable = tables.getTable('Registrations');
			registrationTable
				.where({ handle: item.handle })
				.read({ success: insertChannelIfNotFound });
	        function insertChannelIfNotFound(existingRegistrations) {
        	    if (existingRegistrations.length > 0) {
            	    request.respond(200, existingRegistrations[0]);
        	    } else {
            	    request.execute();
        	    }
    	    }
	    }

   これにより、新しいテーブルに登録情報を格納する新しい挿入スクリプトが登録されます。

<!-- images-->
[3]: ./media/mobile-services-update-registrations-script/mobile-portal-data-tables-registrations.png
[4]: ./media/mobile-services-update-registrations-script/mobile-insert-script-registrations.png
