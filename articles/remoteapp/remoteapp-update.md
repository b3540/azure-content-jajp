<properties
   pageTitle="Azure RemoteApp コレクションの更新"
   description="Azure RemoteApp コレクションの更新方法を説明します。"
   services="remoteapp"
   documentationCenter=""
   authors="lizap"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="remoteapp"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="compute"
   ms.date="06/09/2015"
   ms.author="elizapo"/>

# Azure RemoteApp コレクションの更新

将来、必ず Azure RemoteApp コレクション内のアプリやイメージの更新が必要になる時期が来ます。クラウド コレクションまたはハイブリッド コレクションで、Azure RemoteApp サブスクリプションに含まれるイメージの 1 つを使用している場合は、すべての更新が Azure RemoteApp 自体によって処理されるので、安心できます。

ただし、カスタム イメージ (最初から構築したか、または提供されたイメージの 1 つを変更して作成したカスタム イメージ) を使用している場合は、イメージとアプリケーションの保守に責任を持つ必要があります。

では、どのようにして、コレクションを更新するのでしょうか。 その方法は、以下のようなわかりやすいものになっています。

1. コレクションで使用したイメージを更新します。必要となる任意のパッチまたは更新を適用し、新しい名前を付けて保存します。
2. RemoteApp にそのイメージを[アップロード](remoteapp-uploadimage.md)または[インポート](remoteapp-image-on-azurevm)します。
3. [コレクション] ページで **[更新]** をクリックします。
4. **[テンプレート イメージ]** リストから新しいイメージを選択します。
4. ここは、注意が必要な手順です。コレクション内のアプリを使用中のユーザーを処理する方法を決定する必要があります。選択肢には次の 2 つがあります。
	- **更新が完了してから 60 分後にユーザーをサインアウトさせる**。更新が終了すると、すぐに Azure RemoteApp はアクティブなユーザーに対してメッセージを表示します。そのメッセージにより、作業内容を保存してからログオフし、再度ログインするよう通知します。60 分後、ログオフしていないアクティブなユーザーは、自動的にログオフされます。ユーザーはすぐに再度ログオンできます。 
	- **すぐにユーザーをサインアウトさせる**。更新が終了すると、警告することなく、すぐにすべてのユーザーを自動的にログオフします。このオプションを選択すると、ユーザーはデータを失う可能性があります。ただし、ユーザーはすぐにアプリに再接続できます。

1. チェック マークをクリックして、更新を開始します。

 

<!---HONumber=July15_HO2-->