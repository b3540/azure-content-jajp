<properties 
   pageTitle="Virtual Network 構成ファイルでの DNS 設定の指定"
   description="説明"
   services="virtual-network"
   documentationCenter="na"
   authors="joaoma"
   manager="jdial"
   editor="tysonn" />
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="05/28/2015"
   ms.author="joaoma" />

# Virtual Network 構成ファイルでの DNS 設定の指定

ネットワーク構成ファイルの 2 つの要素を使用して、ドメイン ネーム システム (DNS) の設定 **DnsServers** と **DnsServerRef** を指定することができます。**DnsServers** 要素に IP アドレスと参照名を指定することにより、DNS サーバーのリストを追加できます。その後、**DnsServerRef** 要素を使用して、DnsServers 要素から、仮想ネットワーク内のネットワーク サイトに使用する DNS サーバー エントリを指定できます。

>[AZURE.IMPORTANT]ネットワーク構成ファイルの構成方法について詳しくは、「[ネットワーク構成ファイルを使用した仮想ネットワークの構成](https://msdn.microsoft.com/library/azure/jj156097.aspx)」をご覧ください。ネットワーク構成ファイルに含まれる各要素については、「[Azure 仮想ネットワークの構成スキーマ](https://msdn.microsoft.com/library/azure/jj157100.aspx)」をご覧ください。

ネットワーク構成ファイルは、次の要素を含むことができます。各要素のタイトルは、要素値の設定に関する追加情報を提供するページにリンクされています。

[Dns 要素](http://go.microsoft.com/fwlink/?LinkId=248093)

    <Dns>
      <DnsServers>
        <DnsServer name="ID1" IPAddress="IPAddress1" />
        <DnsServer name="ID2" IPAddress="IPAddress2" />
        <DnsServer name="ID3" IPAddress="IPAddress3" />
      </DnsServers>
    </Dns>

>[AZURE.WARNING]**DnsServer** 要素の **name** 属性は、**DnsServerRef** 要素の参照としてのみ使用されます。DNS サーバーのホスト名を表してはいません。各 **DnsServer** 属性の値は、Microsoft Azure サブスクリプション全体で一意である必要があります

[VirtualNetworkSites 要素](http://go.microsoft.com/fwlink/?LinkId=248093)

	<DnsServersRef>
	  <DnsServerRef name="ID1" />
	  <DnsServerRef name="ID2" />
	  <DnsServerRef name="ID3" />
	</DnsServersRef>

>[AZURE.NOTE]VirtualNetworkSites 要素に対してこの設定を指定するには、その前に DNS 要素で定義されている必要があります。VirtualNetworkSites 要素の DnsServerRef *name* は、DNS 要素で DnsServer *name* に対して指定されている名前の値を参照している必要があります。

## 関連項目

[ネットワーク構成ファイルを使用した仮想ネットワークの構成](http://go.microsoft.com/fwlink/?LinkId=248094)

[Azure 仮想ネットワークの構成スキーマ](http://go.microsoft.com/fwlink/?LinkId=248093)

[Azure サービス構成スキーマ](https://msdn.microsoft.com/library/windowsazure/ee758710)

<!---HONumber=July15_HO2-->