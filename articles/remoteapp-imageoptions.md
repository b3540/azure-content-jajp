<properties 
    pageTitle="RemoteApp イメージの作成"
    description="RemoteApp のイメージ作成に使用できるオプションの詳細" 
    services="remoteapp" 
    solutions="" 
	documentationCenter="" 
    authors="lizap" 
    manager="mbaldwin" />

<tags 
    ms.service="remoteapp" 
    ms.workload="compute" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="04/08/2015" 
    ms.author="elizapo" />



# RemoteApp イメージの作成

Azure RemoteApp は、イメージを使用してユーザーと共有しているアプリを保持します。クラウド型であれハイブリッド型であれ、選択したアプリケーションの RemoteApp コレクションを作成するには、インストールされているアプリケーションのイメージを最初に作成します。次に、そのイメージを使用するコレクションを作成し、コレクションにユーザーを割り当てて、それらのユーザーにアプリを公開します。

イメージの作成または使用には、いくつかのオプションあります。イメージの基本的な[要件](remoteapp-imagereqs.md)として、 イメージが Windows Server 2012 R2 を実行すること、およびリモート デスクトップ セッション ホスト \(RDSH\) の役割がインストールされていることが必要です。興味深いのは、その取得方法です。

イメージの場合、次のオプションがあります。

- [Azure 仮想マシンに基づくイメージ](remoteapp-image-on-azurevm.md)をインポートして使用できます。これは、カスタム設定を必要とする基幹業務アプリに最適です。イメージをカスタマイズして、アプリ用に動作させることができます。
- [カスタム イメージを作成してアップロード](remoteapp-create-custom-image.md)できます。オンプレミスのリモート デスクトップ サービス デプロイメントに使用するイメージが既にある場合は、これが最適です。
- RemoteApp サブスクリプションに含まれる[テンプレート イメージ](remoteapp-images.md)のいずれか 1 つを使用できます。これらのイメージは、RemoteApp チームによって作成および管理されているもので、イメージに含まれる標準的なアプリケーション \(Office スイートなど\) をユーザーが使用できます。なお、運用設定では Office 365 Pro Plus イメージのみ使用可能です。

イメージの取得および作成方法に関わりなく、[アプリ要件](remoteapp-appreqs.md)を正しく理解し、アプリが RemoteApp で正常に動作することを確認してください。次の手順は、[クラウド](remoteapp-create-cloud-deployment.md) コレクションまたは[ハイブリッド](remoteapp-create-hybrid-deployment.md) コレクションの作成です。

<!--HONumber=52-->