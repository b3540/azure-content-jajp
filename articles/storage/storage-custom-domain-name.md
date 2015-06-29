<properties 
	pageTitle="ストレージ アカウントの BLOB データのカスタム ドメイン名の構成 | Microsoft Azure" 
	description="Azure ストレージ アカウントの BLOB データにアクセスするためにカスタム ドメインを構成する方法について学習します。" 
	services="storage" 
	documentationCenter="" 
	authors="tamram" 
	manager="adinah" 
	editor=""/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="12/11/2014" 
	ms.author="tamram"/>


# Azure ストレージ アカウントの BLOB データのカスタム ドメイン名の構成
Azure ストレージ アカウントの BLOB データにアクセスするためのカスタム ドメインを構成できます。BLOB サービスの既定のエンドポイントは https://<*mystorageaccount*>.blob.core.windows.net です。**www.contoso.com** などのカスタム ドメインおよびサブドメインをストレージ アカウントの BLOB エンドポイントにマッピングしている場合、ユーザーはそのドメインを使って、ストレージ アカウントの BLOB データにもアクセスできます。 


> [AZURE.NOTE]	このタスクの手順は、Azure ストレージ アカウントに適用されます。クラウド サービスの場合は「<a href = "/ja-jp/develop/net/common-tasks/custom-dns/">Azure クラウド サービスのカスタム ドメイン名の構成</a>」を、Web サイトの場合は「<a href="/ja-jp/develop/net/common-tasks/custom-dns-web-site/">Azure の Web サイトのカスタム ドメイン名の構成</a>」を参照してください。 

> [AZURE.NOTE]	Premium Storage アカウントをカスタムのドメイン名にマッピングすることはできません。Premium Storage アカウントの詳細については、[Premium Storage:High-Performance Storage for Azure Virtual Machine Workloads (Azure 仮想マシンのワークロード向けの高パフォーマンス ストレージ)](http://go.microsoft.com/fwlink/?LinkId=521898) を参照してください。

ストレージ アカウントの BLOB エンドポイントをカスタム ドメインで参照するには、2 つの方法があります。最も簡単な方法は、CNAME レコードを作成して、カスタム ドメインおよびサブドメインを BLOB エンドポイントにマッピングすることです。CNAME レコードは、ソース ドメインを目的のドメインにマッピングする DNS 機能です。この場合、ソース ドメインは、カスタム ドメインおよびサブドメインです。サブドメインは常に必要であることに注意してください。目的のドメインは、BLOB サービス エンドポイントです。

ただし、カスタム ドメインを BLOB エンドポイントにマッピングする処理によって、Azure 管理ポータルでのドメインの登録中にドメインが短時間ダウンする場合があります。カスタム ドメインが現在、ダウンタイムが発生しないことを要求するサービス レベル アグリーメント (SLA) のアプリケーションをサポートしている場合は、DNS マッピングの実行中にユーザーがドメインにアクセスできるように、Azure **asverify** サブドメインを使用して中間登録ステップを提供できます。

次の表は、**mystorageaccount** というストレージ アカウントの BLOB データにアクセスするためのサンプル URL を示します。ストレージ アカウントに登録されているカスタム ドメインは **www.contoso.com** です。

<table border="1" cellspacing="0" cellpadding="5" style="border: 1px solid #000000;">
	<tbody>
		<tr>
			<td style="width: 100px;"><strong>リソースの種類</strong></td>
			<td><strong>URL の形式</strong></td>
		</tr>
		<tr>
			<td>ストレージ アカウント</td>
			<td><strong>既定の URL:</strong> http://mystorageaccount.blob.core.windows.net<br />
			<strong>カスタム ドメインの URL:</strong> http://www.contoso.com</td>
		</tr>
		<tr>
			<td>BLOB</td>
			<td><strong>既定の URL:</strong> http://mystorageaccount.blob.core.windows.net/mycontainer/myblob<br /><strong>カスタム ドメインの URL:</strong>
			http://www.contoso.com/mycontainer/myblob</td>
		</tr>
		<tr>
			<td>ルート コンテナー</td>
			<td><strong>既定の URL:</strong> http://mystorageaccount.blob.core.windows.net/myblob 
			<br/>または<br />
			http://mystorageaccount.blob.core.windows.net/$root/myblob<br />
			<strong>カスタム ドメインの URL:</strong> http://www.contoso.com/myblob
			<br/>または<br />
			http://www.contoso.com/$root/myblob</td>
		</tr>
	</tbody>
</table>

ここでは、次の操作方法について説明します。



- <a href="#register-domain">ストレージ アカウントのカスタム ドメインの登録</a>
- <a href="#register-asverify">中間 asverify サブドメインを使用したストレージ アカウントのカスタム ドメインの登録</a>
- <a name="#verify-subdomain">カスタム ドメインが BLOB サービス エンドポイントを参照することの確認</a>

<h2><a name="register-domain"></a>ストレージ アカウントのカスタム ドメインの登録</h2>

ユーザーが短時間、ドメインを使用できなくても問題ではない場合、またはカスタム ドメインが現在、アプリケーションのホストでない場合は、この手順でカスタム ドメインを登録します。 

カスタム ドメインが現在、ダウンタイムの発生が許容されないアプリケーションをサポートしている場合は、「<a href="#register-asverify">中間 asverify サブドメインを使用したストレージ アカウントのカスタム ドメインの登録</a>」で説明している手順に従ってください。

カスタム ドメイン名を構成するには、ドメイン レジストラーで新しい CNAME レコードを作成する必要があります。CNAME レコードによって、ドメイン名のエイリアスが指定されます。この場合、カスタム ドメインのアドレスが、ストレージ アカウントの BLOB サービス エンドポイントにマッピングされます。

それぞれのレジストラーでは CNAME レコードを指定するための同様の (多少異なる) 手段が用意されていますが、その概念は
同じです。多くの基本ドメイン登録パッケージには DNS 構成が用意されていないため、CNAME レコードを作成するには、事前にドメイン登録パッケージのアップグレードが必要である可能性があります。 

1.  Azure 管理ポータルで **[ストレージ]** タブに移動します。

2.  **[ストレージ]** タブで、カスタム ドメインをマッピングするストレージ アカウントの名前をクリックします。

3.  **[構成]** タブをクリックします。

4.  画面の下部で、**[ドメインの管理]** をクリックして **[カスタム ドメインの管理]** ダイアログを表示します。ダイアログ上部のテキストには、CNAME レコードの作成方法に関する情報が含まれています。この手順では、**asverify** サブドメインに関するテキストは無視してください。

5.  DNS レジストラーの Web サイトにログオンし、
    DNS の管理ページに移動します。これは、"**ドメイン
    名**"、"**DNS**"、"**ネーム サーバー管理**" などのセクションにあります。

6.  CNAME 管理セクションを見つけます。詳細設定ページに進み、
    "**CNAME**"、"**エイリアス**"、
    "**サブドメイン**" などの語句を探します。

7.  新しい CNAME レコードを作成し、"**www**" や "**photos**" などのサブドメイン エイリアスを指定します。次に、
    **mystorageaccount.blob.core.windows.net** (ここで、**mystorageaccount** はストレージ アカウントの名前です) という形式でホスト名を指定します。これは、BLOB サービス エンドポイントです。使用するホスト名は、**[カスタム ドメインの管理]** ダイアログ内のテキストに含まれています。

8.  新しい CNAME レコードを作成したら、**[カスタム ドメインの管理]** ダイアログに戻り、サブドメインを含むカスタム ドメイン名を **[カスタム ドメイン名]** フィールドに入力します。たとえば、ドメインが "**contoso.com**" で、サブドメインが "**www**" の場合は、"**www.contoso.com**" と入力します。サブドメインが "**photos**" の場合は、"**photos.contoso.com**" と入力します。サブドメインは必須であることに注意してください。

9. **[登録]** ボタンをクリックしてカスタム ドメインを登録します。 

	登録が成功すると、**"カスタム ドメインはアクティブです"** というメッセージが表示されます。これで、ユーザーは、適切なアクセス許可を持っている限り、カスタム ドメインの BLOB データを表示できます。 

<h2><a name="register-asverify"></a>中間 asverify サブドメインを使用したストレージ アカウントのカスタム ドメインの登録</h2>

カスタム ドメインが現在、ダウンタイムが発生しないことが SLA で要求されているアプリケーションをサポートしている場合は、この手順でカスタム ドメインを登録します。asverify.&lt;subdomain&gt;.&lt;customdomain&gt; から asverify.&lt;storageaccount&gt;.blob.core.windows.net を指す CNAME を作成すると、ドメインを Azure に事前登録できます。そして、&lt;subdomain&gt;.&lt;customdomain&gt; から &lt;storageaccount&gt;.blob.core.windows.net を指す、もう 1 つの CNAME を作成します。このポイントから、カスタム ドメインへのトラフィックが BLOB エンドポイントにダイレクトされます。

asverify サブドメインは、Azure で認識される特殊なサブドメインです。自分のサブドメインの前に **asverify** を付けると、ドメインの DNS レコードを変更しなくても、カスタム ドメインが Azure に認識されます。ドメインの DNS レコードを変更すると、ダウンタイムが発生することなく、ドメインが BLOB エンドポイントにマッピングされます。

1.  Azure 管理ポータルで **[ストレージ]** タブに移動します。

2.  **[ストレージ]** タブで、カスタム ドメインをマッピングするストレージ アカウントの名前をクリックします。

3.  **[構成]** タブをクリックします。

4.  画面の下部で、**[ドメインの管理]** をクリックして **[カスタム ドメインの管理]** ダイアログを表示します。ダイアログ上部のテキストには、**asverify** サブドメインを使用して CNAME レコードを作成する方法に関する情報が含まれます。

5.  DNS レジストラーの Web サイトにログオンし、
    DNS の管理ページに移動します。これは、"**ドメイン
    名**"、"**DNS**"、"**ネーム サーバー管理**" などのセクションにあります。

6.  CNAME 管理セクションを見つけます。詳細設定ページに進み、
    "**CNAME**"、"**エイリアス**"、
    "**サブドメイン**" などの語句を探します。

7.  新しい CNAME レコードを作成し、asverify サブドメインを含むサブドメイン エイリアスを指定します。たとえば、指定するサブドメインは、"**asverify.www**" または "**asverify.photos**" という形式になります。次に、
    **asverify.mystorageaccount.blob.core.windows.net** (ここで、**mystorageaccount** はストレージ アカウントの名前です) という形式でホスト名を指定します。これは、BLOB サービス エンドポイントです。使用するホスト名は、**[カスタム ドメインの管理]** ダイアログ内のテキストに含まれています。

8.  新しい CNAME レコードを作成したら、**[カスタム ドメインの管理]** ダイアログに戻り、カスタム ドメイン名を **[カスタム ドメイン名]** フィールドに入力します。たとえば、ドメインが "**contoso.com**" で、サブドメインが "**www**" の場合は、"**www.contoso.com**" と入力します。サブドメインが "**photos**" の場合は、"**photos.contoso.com**" と入力します。サブドメインは必須であることに注意してください。

9.	**[高度:'asverify'' サブドメインを使用してカスタム ドメインを事前登録します] チェック ボックスをオンにします**。 

10. **[登録]** ボタンをクリックしてカスタム ドメインを事前登録します。 

	事前登録が成功すると、**"カスタム ドメインはアクティブです"** というメッセージが表示されます。 

11. この時点で、カスタム ドメインは Azure によって確認されていますが、ドメインへのトラフィックは、まだストレージ アカウントへはルーティングされません。処理を完了するには、DNS レジストラーの Web サイトに戻り、サブドメインを BLOB サービス エンドポイントにマッピングする別の CNAME レコードを作成します。たとえば、"**www**" または "**photos**" というサブドメインと、"**mystorageaccount.blob.core.windows.net**" (ここで、"**mystorageaccount**" はストレージ アカウントの名前です) というホスト名を指定します。この手順で、カスタム ドメインの登録が完了します。

12. 最後に、**asverify** を使って作成した CNAME レコードを削除します。このレコードは、中間の手順としてのみ必要であったためです。

これで、ユーザーは、適切なアクセス許可を持っている限り、カスタム ドメインの BLOB データを表示できます。

<a name="verify-subdomain"> </a>

<h2>カスタム ドメインが BLOB サービス エンドポイントを参照することの確認</h2>

カスタム ドメインが実際に BLOB サービス エンドポイントにマッピングされていることを確認するために、ストレージ アカウントのパブリック コンテナーで BLOB を作成します。そして、Web ブラウザーで、次の形式の URI を使って BLOB にアクセスします。

-   http://<*subdomain.customdomain*>/<*mycontainer*>/<*myblob*>

たとえば、myforms コンテナー内の BLOB にマップされている
**photos.contoso.com** カスタム サブドメイン経由で Web フォームにアクセスするには、
次の URI を入力します。****

-   http://photos.contoso.com/myforms/applicationform.htm

## その他のリソース

-   <a href="http://msdn.microsoft.com/library/windowsazure/gg680307.aspx">CDN コンテンツをカスタム ドメインにマッピングする方法</a>

<!--HONumber=42-->
 