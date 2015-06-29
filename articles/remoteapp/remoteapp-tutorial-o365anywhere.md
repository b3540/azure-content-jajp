<properties
   pageTitle="どのデバイスでも同じ Office 365 のエクスペリエンスを得るには"
   description="RemoteApp を使用して、ユーザーと任意の Office 365 アプリを共有する方法について説明します。"
   services="remoteapp"
   documentationCenter=""
   authors="guscatalano"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="remoteapp"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="compute"
   ms.date="04/14/2015"
   ms.author="guscatal"/>


どのデバイスでも同じ Office 365 のエクスペリエンスを得るには
===================


この記事では、会社内の任意のデバイスで Office 365 をデプロイする方法を説明します。ユーザーには、Android、Apple、Windows のいずれからでも、同じ機能と UI 操作が提供されます。

これは、Azure RemoteApp を使用し、ユーザーが接続できる Azure のスケーラブル Virtual Machines で Office 365 をホストすることにより実現されます。この仮想マシンのセットを「クラウド コレクション」と呼びます。

----------


クラウド コレクションの作成
-------------
まず、Azure アカウントを作成してから、左側にあるリンクをクリックして「RemoteApp」 に移動します。 ![Azure ポータルでの Azure RemoteApp の表示](https://raw.githubusercontent.com/guscatalano/azure-content/master/articles/media/remoteapp-tutorial-o365anywhere/1-menu.png)

次に、下部にある [新規] をクリックし、コレクションを「簡易作成」します。名前、地域、サブスクリプション、プラン、および提供されている「Office Proffesional 2013」イメージを指定します。 ![[作成] ダイアログ](https://raw.githubusercontent.com/guscatalano/azure-content/master/articles/media/remoteapp-tutorial-o365anywhere/2-quickcreate.PNG)

フォームを完了すると、コレクションの作成プロセスが開始されます。これには 1 時間ほどかかる場合があります。

![待機中](https://raw.githubusercontent.com/guscatalano/azure-content/master/articles/media/remoteapp-tutorial-o365anywhere/3-waiting.PNG)

プロセスが完了すると、以下のようになります。[発行] をクリックすると、ほとんどの Office アプリケーションが既に発行されていることを確認できます。![作成されたコレクション](https://raw.githubusercontent.com/guscatalano/azure-content/master/articles/media/remoteapp-tutorial-o365anywhere/4-done.PNG)

![発行されたアプリ](https://raw.githubusercontent.com/guscatalano/azure-content/master/articles/media/remoteapp-tutorial-o365anywhere/5-publish.PNG)

この時点で、[ユーザー アクセス] をクリックし、このコレクションへのアクセスが可能なユーザーを追加できます。![ユーザー アクセスの構成](https://raw.githubusercontent.com/guscatalano/azure-content/master/articles/media/remoteapp-tutorial-o365anywhere/6-user.PNG)

それでは、Office 365 への接続を試してみましょう。

Office 365 への接続
-------------
https://www.remoteapp.windowsazure.com/ に移動し、[クライアントのインストール] をクリックして、使用しているデバイスに Azure RemoteApp クライアントをインストールします。次のスクリーンショットは、Windows  の場合です。

アプリケーションが開始されると、live ID でサインインするように求められます。ここでは、Azure アカウントと同じものを使用してください。サインインすると、新しい招待に関する通知が表示されます。これをクリックすると、次のようなリストが表示されますので、Azure アカウント所有者の電子メールと一致する招待を承諾してください。

![新しい招待](https://raw.githubusercontent.com/guscatalano/azure-content/master/articles/media/remoteapp-tutorial-o365anywhere/7-araclient.PNG)

新しい招待がある場合、このように表示されます。

![アプリケーションの承諾](https://raw.githubusercontent.com/guscatalano/azure-content/master/articles/media/remoteapp-tutorial-o365anywhere/8-invitation.PNG)

招待を承諾すると、Azure RemoteApp クライアントにあるすべての Office アプリが表示されます。

![アプリのリスト](https://raw.githubusercontent.com/guscatalano/azure-content/master/articles/media/remoteapp-tutorial-o365anywhere/9-work.PNG)

そのいずれかをクリックすると、アプリケーションが Azure Virtual Machine 上で起動します。これですべて完了です。 機能を有効にご活用ください。

![開始中](https://raw.githubusercontent.com/guscatalano/azure-content/master/articles/media/remoteapp-tutorial-o365anywhere/10-arastart.PNG)

![PowerPoint](https://raw.githubusercontent.com/guscatalano/azure-content/master/articles/media/remoteapp-tutorial-o365anywhere/11-pp.PNG)

<!--HONumber=52-->
 