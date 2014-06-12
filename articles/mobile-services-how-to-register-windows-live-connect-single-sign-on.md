<properties linkid="develop-mobile-how-to-guides-register-for-single-sign-on" urlDisplayName="シングル サインオン用の登録" pageTitle="シングル サインオン用の登録 - Azure のモバイル サービス" metaKeywords="" description="Azure のモバイル サービス アプリケーションでシングル サインオン認証用に登録する方法について説明します。" metaCanonical="" services="" documentationCenter="" title="Windows Live Connect シングル サインオンを使用するための Windows ストア アプリケーションの登録" authors="" solutions="" manager="" editor="" />

# Windows Live Connect シングル サインオンを使用するための Windows ストア アプリケーションの登録

このトピックでは、Azure のモバイル サービスの ID プロバイダーとして Live Connect をシングル サインオンに使用できるように、アプリケーションを Windows ストアに登録する方法について説明します。この手順は、プッシュ通知を使用する場合にも必要です。

<div class="dev-callout"><b>注</b>
<p>アプリケーションを発行する前に、認証用に Microsoft アカウントを使用できるようにアプリケーションを Windows ストアに登録する必要はありません。Windows ストア アプリにシングル サインオンまたはプッシュ通知が必要でない場合は、Microsoft アカウント ログインを使用できるようにアプリケーションを Live Connect に登録するだけでかまいません。詳細については、「<a href="/ja-jp/develop/mobile/how-to-guides/register-for-microsoft-authentication">Microsoft アカウント ログインを使用するためのアプリケーションの登録</a>」を参照してください。</p>
</div>

1. アプリケーションをまだ登録していない場合は、Windows ストア アプリケーションのデベロッパー センターで[アプリケーション送信のページ]に移動し、Microsoft アカウントでログインして、**[アプリケーション名]** をクリックします。

   	![][0]

2. **[アプリケーション名]** にアプリケーションの名前を入力し、**[アプリケーション名の予約]** をクリックして、**[保存]** をクリックします。

   	![][1]

   	これでアプリケーションの新しい Windows ストア登録が作成されます。

3. Visual Studio 2012 Express for Windows 8 で、チュートリアル「[モバイル サービスの使用]」を実行したときに作成したプロジェクトを開きます。

4. ソリューション エクスプローラーでプロジェクトを右クリックし、**[ストア]**、**[アプリケーションをストアと関連付ける]** の順にクリックします。

  	![][2]

   	**アプリケーションを Windows ストアと関連付ける**ウィザードが表示されます。

5. ウィザードで **[サインイン]** をクリックし、Microsoft アカウントでログインします。

6. 手順 2. で登録したアプリケーションを選択し、**[次へ]**、**[関連付け]** の順にクリックします。

   	![][3]

   	この操作により、必要な Windows ストア登録情報がアプリケーション マニフェストに追加されます。

9. Live Connect デベロッパー センターの [[マイ アプリケーション]] ページに移動し、**[マイ アプリケーション]** の一覧でアプリケーションをクリックします。

   	![][6] 

10. **[設定の編集]**、**[API 設定]** の順にクリックし、**[クライアント シークレット]** の値を書き留めます。

   	![][7]

    <div class="dev-callout"><b>セキュリティに関する注意</b>
	<p>クライアント シークレットは、重要なセキュリティ資格情報です。クライアント シークレットは、他のユーザーと共有したり、アプリケーションで配信したりしないでください。</p>
    </div>

11. **[リダイレクト ドメイン]** で手順 8. のモバイル サービスの URL を入力し、**[保存]** をクリックします。

Live Connect を使用して認証をアプリケーションに統合する準備ができました。モバイル サービスで Live Connect を使用してユーザーを認証する方法には、次の 2 種類があります。

   - Windows ストア アプリケーションへのシングル サインオン。この方法では、ユーザーがアプリケーションで Live Connect を使用して認証の承認を 1 回行うだけで、ユーザーの初期設定に基づいて Windows で資格情報が管理されます。詳細については、「[Live Connect を使用した Windows ストア アプリへのシングル サインオン]」を参照してください。

   - 基本認証。この方法では、さまざまな認証プロバイダーがサポートされますが、ユーザーはアプリケーションの開始ごとにログインする必要があります。詳細については、「[認証の使用]」を参照してください。

<!-- Anchors. -->

<!-- Images. -->
[0]: ./media/mobile-services-how-to-register-windows-live-connect-single-sign-on/mobile-services-submit-win8-app.png
[1]: ./media/mobile-services-how-to-register-windows-live-connect-single-sign-on/mobile-services-win8-app-name.png
[2]: ./media/mobile-services-how-to-register-windows-live-connect-single-sign-on/mobile-services-store-association.png
[3]: ./media/mobile-services-how-to-register-windows-live-connect-single-sign-on/mobile-services-select-app-name.png


[6]: ./media/mobile-services-how-to-register-windows-live-connect-single-sign-on/mobile-live-connect-apps-list.png
[7]: ./media/mobile-services-how-to-register-windows-live-connect-single-sign-on/mobile-live-connect-app-api-settings.png



<!-- URLs. -->
[Live Connect を使用した Windows ストア アプリへのシングル サインオン]: /ja-jp/develop/mobile/tutorials/single-sign-on-windows-8-dotnet
[アプリケーション送信のページ]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[モバイル サービスの使用]: /ja-jp/develop/mobile/tutorials/get-started
[認証の使用]: /ja-jp/develop/mobile/tutorials/get-started-with-users-dotnet
[プッシュ通知の使用]: /ja-jp/develop/mobile/tutorials/get-started-with-push-dotnet/
[スクリプトを使用したユーザーの認証]: /ja-jp/develop/mobile/tutorials/authorize-users-in-scripts-dotnet/
[JavaScript と HTML]: /ja-jp/develop/mobile/tutorials/get-started-with-users-js/
[Azure の管理ポータル]: https://manage.windowsazure.com/
