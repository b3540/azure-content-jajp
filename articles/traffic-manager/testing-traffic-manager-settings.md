<properties
   pageTitle="Traffic Manager 設定のテスト"
   description="この記事では、Traffic Manager 設定をテストする方法について説明します。"
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

# Traffic Manager の設定のテスト

Traffic Manager の設定をテストするには、複数のクライアントを設定してから、プロファイル内の、クラウド サービスと Web サイトで構成されるエンドポイントを一度に 1 つずつ停止するのが最善の方法です。以下の各ヒントでは、Traffic Manager プロファイルをテストする方法について説明しています。

## 基本的なテスト手順

- **DNS TTL を非常に短い時間に設定**して、変更がすぐに反映されるようにします。たとえば 30 秒に設定します。

- テストするプロファイル内の **Azure Cloud Services や Azure Websites の IP アドレスを確認**します。

- **DNS 名を IP アドレスに解決**してそのアドレスを表示できるツールを使用します。会社のドメイン名がプロファイル内のエンドポイントの IP アドレスに解決されることを確認します。その名前解決は Traffic Manager プロファイルと同じ負荷分散方法で行われる必要があります。Windows を実行しているコンピューターでは、コマンド プロンプトまたは Windows PowerShell プロンプトから Nslookup.exe ツールを使用できます。他にも、インターネット上には IP アドレスを調べることができるツールがあり、すぐに使用できる状態で公開されています。

### nslookup を使用して Traffic Manager プロファイルを確認するには

1 - 管理者としてコマンド プロンプトまたは Windows PowerShell プロンプトを開きます。

2 - 「 `ipconfig /flushdns`」と入力して DNS リゾルバー キャッシュをフラッシュします。

3 - 「 `nslookup <your Traffic Manager domain name>`」と入力します。たとえば、次のコマンドはプレフィックス  *myapp.contoso* を持つドメイン名を確認します。
    nslookup myapp.contoso.trafficmanager.net
通常、結果として次の情報が表示されます。
- この Traffic Manager ドメイン名を解決するためにアクセスされる DNS サーバーの DNS 名と IP アドレス。
- コマンド ラインで "nslookup" の後に入力した Traffic Manager ドメイン名と、その Traffic Manager ドメインから解決された IP アドレス。この 2 番目の IP アドレスを確認することが重要です。この IP アドレスは、テスト対象の Traffic Manager プロファイルに含まれるいずれかのクラウド サービスまたは Web サイトのパブリック仮想 IP (VIP) アドレスと一致している必要があります。

## 負荷分散方法のテスト


### フェールオーバー負荷分散方法をテストするには

1 - すべてのエンドポイントを稼働したままにしておきます。
2 - 1 つのクライアントを使用します。
3 - Nslookup.exe ツールまたは同様のユーティリティを使用して、会社のドメイン名の DNS 解決を要求します。
4 - 取得した解決済み IP アドレスが、プライマリ エンドポイントに対応していることを確認します。
5 - プライマリ エンドポイントを停止するか、監視ファイルを削除して、Traffic Manager にエンドポイントの停止を認識させます。
6 - Traffic Manager の DNS Time-to-Live (TTL) に 2 分を追加した時間が経過するまで待ちます。たとえば、DNS TTL が 300 秒 (5 分) の場合は、7 分待つ必要があります。
7 - DNS クライアント キャッシュをフラッシュし、DNS 解決を要求します。Windows では、コマンド プロンプトまたは Windows PowerShell プロンプトで発行した ipconfig /flushdns コマンドで、DNS キャッシュをフラッシュできます。
8 - 取得した IP アドレスが、セカンダリ エンドポイントに対応していることを確認します。
9 - セカンダリ エンドポイントを停止し、さらに 3 番目のエンドポイントを停止して、という具合に、ここまでの手順を繰り返します。手順を実行するたびに、DNS 解決によって、一覧内の次のエンドポイントの IP アドレスが返されることを確認します。すべてのエンドポイントを停止したら、プライマリ エンドポイントの IP アドレスが再度返されます。

### ラウンド ロビン負荷分散方法をテストするには

1 - すべてのエンドポイントを稼働したままにしておきます。
2 - 1 つのクライアントを使用します。
3 - Nslookup.exe ツールまたは同様のユーティリティを使用して、会社のドメインの DNS 解決を要求します。
4 - 取得した IP アドレスが一覧内の IP アドレスのいずれかに一致することを確認します。
5 - DNS クライアントをフラッシュし、手順 3 と 4 を繰り返します。返された IP アドレスが、各エンドポイントに対応していることを確認します。このような処理を繰り返します。

### パフォーマンス負荷分散方法をテストするには

パフォーマンス負荷分散方法を効果的にテストするには、世界各地のクライアントが必要になります。1 つの方法として、Azure 上にクライアントを作成し、それらのクライアントから会社のドメイン名を使用してサービスの呼び出しをテストできます。または、世界各国に事業所がある会社の場合は、他の地域にあるクライアントにリモート ログインし、そらのクライアントからテストを行うことができます。

無料の Web ベースの DNS 検索と検出サービスを利用できます。これらのサービスの中には、さまざまな場所から DNS 名前解決を確認できるものもあります。たとえば、"DNS 検索" の検索などを実行できます。さらに、Gomez や Keynote のようなサード パーティ製ソリューションもあります。このようなソリューションを使用して、トラフィックがプロファイルに基づいて予期したとおりに振り分けられることを確認できます。

## 関連項目

[Traffic Manager での負荷分散方法について](../about-traffic-manager-balancing-methods.md)
[Traffic Manager を構成する方法](https://msdn.microsoft.com/library/azure/hh744830.aspx)
[Traffic Manager](../traffic-manager.md)

<!--HONumber=49--> 