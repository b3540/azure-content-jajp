<properties
   pageTitle="Traffic Manager エンドポイントの無効化または有効化"
   description="この記事では、Traffic Manager プロファイル エンドポイントの無効化または有効化について説明します。"
   services="traffic-manager"
   documentationCenter="na"
   authors="cherylmc"
   manager="adinah"
   editor="tysonn" />
<tags 
   ms.service="traffic-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/23/2015"
   ms.author="cherylmc" />

# Traffic Manager エンドポイントの無効化または有効化

Traffic Manager プロファイルを構成する個々のエンドポイントを無効にすることもできます。エンドポイントには、クラウド サービスと Web サイトの両方が含まれます。無効にしたエンドポイントはその後もプロファイルの一部として残りますが、プロファイルは、エンドポイントが含まれていないかのように機能します。この操作は、メンテナンス モードのエンドポイントや再デプロイされるエンドポイントを一時的に削除するときに役立ちます。エンドポイントが再度稼働状態になったら、有効にできます。

[AZURE.NOTE] **エンドポイントを無効にすることと Azure でのデプロイの状態とは無関係です。正常なエンドポイントは、Traffic Manager で無効になっていても、稼働状態のままでトラフィックを受信できます。また、1 つのプロファイル内のエンドポイントを無効にしても、別のプロファイル内の状態には影響しません。**

## エンドポイントを無効にするには

1. 管理ポータルの Traffic Manager ウィンドウで、変更対象のエンドポイント設定が保存されている Traffic Manager プロファイルを見つけて、プロファイルの右側にある矢印をクリックします。これにより、プロファイルの設定ページが開きます。
1. ページの上部にある、**[エンドポイント]** をクリックして、構成に含まれるエンドポイントを表示します。 
1. 無効にするエンドポイントをクリックしてから、ページの下部にある **[無効化]** をクリックします。
1. Traffic Manager ドメイン名用に構成された DNS Time To Live (TTL) に基づいて、トラフィックはエンドポイントに送信されなくなります。TTL は、Traffic Manager プロファイルの [構成] ページから変更できます。

## エンドポイントを有効にするには


1. 管理ポータルの Traffic Manager ウィンドウで、変更対象のエンドポイント設定が保存されている Traffic Manager プロファイルを見つけて、プロファイルの右側にある矢印をクリックします。これにより、プロファイルの設定ページが開きます。
1. ページの上部にある、**[エンドポイント]** をクリックして、構成に含まれるエンドポイントを表示します。
1. 有効にするエンドポイントをクリックしてから、ページの下部にある **[有効化]** をクリックします。
1. プロファイルに基づいて、サービスへのトラフィックの送信が再開されます。

## 関連項目

[Traffic Manager を構成する方法](https://msdn.microsoft.com/library/azure/hh744830.aspx)

[Traffic Manager の概要](../traffic-manager.md)

[クラウド サービス](http://go.microsoft.com/fwlink/?LinkId=314074)

[Web サイト](http://go.microsoft.com/fwlink/p/?LinkId=393327)


[Traffic Manager の操作 (REST API リファレンス)](http://go.microsoft.com/fwlink/?LinkId=313584)

<!--HONumber=49--> 