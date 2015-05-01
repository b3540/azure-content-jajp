﻿<properties title="Configure Active Directory for Azure RemoteApp" pageTitle="Azure RemoteApp の Active Directory を構成する" description="Azure の RemoteApp を使用する Active Directory をセットアップする方法を学習します。" metaKeywords="" services="" solutions="" documentationCenter="" authors="elizapo"  />

<tags ms.service="remoteapp" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="12/08/2014" ms.author="elizapo" />

#Azure RemoteApp の Active Directory の構成


RemoteApp のハイブリッド コレクションでは、内部設置型 Active Directory ドメイン インフラストラクチャと Azure Active Directory テナントをディレクトリ統合で設定する必要があります (および必要に応じてシングル サインオン)。さらに、内部設置型ディレクトリに Active Directory オブジェクトを作成する必要があります。次の情報を使用して、内部設置型 Active Directory と Azure AD を構成し、その 2 つを統合します。

##内部設置型 Active Directory を構成する
内部設置型 Active Directory を構成して開始します。使用する UPN ドメイン サフィックスを特定し、RemoteApp に Active Directory オブジェクトを作成します。 

Active Directory ドメイン サービスは、Windows Server 2012 R2 環境にまだ追加されていませんか。追加方法の詳細については、[次の手順](http://technet.microsoft.com/ja-jp/library/cc731053(v=WS.10)を参照してください。
###UPN ドメイン サフィックスを検証し、構成します。
フォレストが RemoteApp で使用する Azure AD ドメインに一致する UPN のサフィックスに構成されていない場合は、追加する必要があります。必要なサフィックスが構成されているかどうかを確認します。


1. Active Directory ユーザーとコンピューターを起動します。
2.	ドメイン名を展開して、**[ユーザー]** をクリックしてします。
3.	**[管理者]** を右クリックし、**[プロパティ]** をクリックします。
4.	**[アカウント]** タブで、**[ユーザー ログオン名]**フィールドでこのドメイン用に構成された UPN 名を確認します。
5.	RemoteApp コレクションに使用するドメイン名に一致するサフィックスが表示されない場合は、次の手順を行います。
	1.	Active Directory ドメインと信頼関係を起動します。
	2.	**[Active Directory ドメインと信頼関係]** を右クリックし、**[プロパティ]** をクリックします。
	3.	サフィックスの一覧を確認します。
	4.	ボックスにドメイン名の FQDN を入力し、**[追加]**をクリックし、**[OK]** をクリックします。例: contoso.com. 

		サフィックスには、"@" を含めないでください。

今後は新しいユーザーを作成する場合は、ユーザーのログオン名から新しいサフィックスを選択できます。また、ユーザーのプロパティの [アカウント] タブで既存のユーザーのサフィックスを変更できます。

詳細については、[Add User Principal Name Suffixes (ユーザー プリンシパル名サフィックスを追加する)](http://technet.microsoft.com/library/cc772007.aspx) を参照してください。

###Active Directory に RemoteApp のオブジェクトを作成します。
RemoteApp は、内部設置型 Active Directory に 2 つのオブジェクトを必要とします。


- 内部設置型ドメインに RDSH エンド ポイントを結合して、RemoteApp プログラムのドメイン リソースにアクセスするサービス アカウント。
- RemoteApp マシン オブジェクトを含む組織単位 (OU)。RemoteApp で使用するアカウントとポリシーを特定するために OU の使用を推奨します (必須ではありません)。

次の情報を使用してこれらの各オブジェクトを作成します。

####サービス アカウントを作成します:


1. Active Directory ユーザーとコンピューターを起動します。
2.	**[ユーザー]** を右クリックして、**[新規] > [ユーザー]** をクリックします。
3.	RemoteApp サービス アカウントのユーザー名とパスワードを入力します。

	**注:** RemoteApp コレクションを作成する場合は、このアカウント情報が必要です。

####新しい組織単位 (OU) を作成します。


1. [Active Directory ユーザーとコンピューター] で、ドメインを右クリックします。**[新規] > [組織単位]** をクリックします。
2. RemoteApp 用に作成したサービス アカウントをこの新しい OU に追加します。
3.

	そのためには、作成したサービス アカウントを検索します。右クリックして、**[移動]** をクリックします。新しい OU を選択して**[OK]** をクリックします。


1. RemoteApp サービス アカウントに、この OU にコンピューターを追加または削除する権限を付与します 
:
	1. [Active Directory ユーザーとコンピューターのスナップイン] で、**[ビュー] > [高度な機能]** をクリックします。これで、**[セキュリティ] **タブがプロパティ情報に追加されます。
	2. RemoteApp サービス OU を右クリックし、**[プロパティ]** をクリックします。
	3. **[セキュリティ]** タブで、**[追加]** をクリックします。RemoteApp サービス アカウントのユーザー オブジェクトを選択して、**[OK]** をクリックします。
	4. **[詳細設定]** をクリックします。
	5. **のアクセス許可**] タブで、RemoteApp サービスのアカウントを選択して、**[編集]** をクリックします。
	6. **[次に適用]** フィールドで **[このオブジェクトとすべての子オブジェクト]** を選択します。
	7. **[アクセス許可]** フィールドで、[コンピューター オブジェクトの作成] と [コンピューター オブジェクトの削除] の横にある **[許可]** を選択し、**[OK]** をクリックします。 
	8. 残りの 2 つのウィンドウで **[OK]** をクリックします。


##Azure Active Directory を構成します
これで内部設置型 Active Directory が設定されました。Azure AD に移動します。内部設置型ドメインの UPN ドメイン サフィックスと一致するカスタム ドメインを作成し、ディレクトリ統合を設定する必要があります。ハイブリッド コレクションは、Windows Server Active Directory の展開から同期されている (DirSync のようなツールを使用して) Azure Active Directory アカウントのみをサポートします。具体的には、パスワード同期オプションで同期されているか、または Active Directory フェデレーション サービス (AD FS) の構成されたフェデレーションのいずれかで同期されます。 

次の情報を使用して、Azure Active Directory を構成します。


- [Add your domain (ドメインを追加する)](http://technet.microsoft.com/ja-jp/library/hh969247.aspx) - この情報を使用して、内部設置型 Active Directory ドメインの UPN ドメイン サフィックスと一致するドメインを追加します。
- [Directory integration (ディレクトリ統合)](http://technet.microsoft.com/ja-jp/library/jj573653.aspx) - この情報を使用して、次のいずれかのディレクトリ統合オプションを選択します。 - [パスワード同期による DirSync](http://technet.microsoft.com/ja-jp/library/dn441214.aspx) または [フェデレーションによる DirSync](http://technet.microsoft.com/ja-jp/library/dn441213.aspx)

[Multi-Factor Authentication (MFA) (多要素認証 (MFA))](http://technet.microsoft.com/ja-jp/library/dn249466.aspx) を構成することもできます。

<!--HONumber=35.2-->