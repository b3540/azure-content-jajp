<properties linkid="manage-services-storage-net-shared-access-signature-part-1" urlDisplayName="" pageTitle="共有アクセス署名: SAS モデルについて | Microsoft Azure" metaKeywords="Azure BLOB, Azure テーブル, Azure キュー, 共有アクセス署名" description="共有アクセス署名を使用して BLOB、キュー、およびテーブル リソースへのアクセスを委任する方法について説明します" metaCanonical="" services="storage" documentationCenter="" title="第 1 部: SAS モデルについて" authors="tamram" solutions="" manager="mbaldwin" editor="cgronlun" />



# 共有アクセス署名、第 1 部: SAS モデルについて

共有アクセス署名 (SAS) の使用は、アカウント キーを知らせずに、自分のストレージ アカウントの BLOB、テーブル、およびキューへの制限付きアクセスを他のクライアントに許可するための優れた方法です。共有アクセス署名についてのこのチュートリアルの第 1 部では、SAS モデルの概要と SAS のベスト プラクティスについて説明します。チュートリアルの[第 2 部](../storage-dotnet-shared-access-signature-part-2/)では、BLOB サービスで共有アクセス署名を作成するプロセスを詳しく説明します。

## 共有アクセス署名とは##

共有アクセス署名を使用すると、ストレージ アカウント内のリソースへの委任アクセスが可能になります。つまり、自分の BLOB、キュー、またはテーブルへの制限付きアクセス許可を、期間とアクセス許可セットを指定してクライアントに付与できます。また、アカウント アクセス キーを共有する必要はありません。SAS とは、ストレージ リソースへの認証アクセスに必要なすべての情報をクエリ パラメーター内に含む URI です。クライアントは、SAS 内で適切なコンストラクターまたはメソッドに渡すだけで、SAS でストレージ リソースにアクセスできます。

## 共有アクセス署名を使用するタイミング##

SAS は、自分のアカウント キーを知らせたくないクライアントに、自分のストレージ アカウント内のリソースへのアクセスを許可する場合に使用できます。ストレージ アカウント キーには、プライマリ キーとセカンダリ キーの両方が含まれており、これらによって、アカウントとそのすべてのリソースへの管理アクセスが付与されます。これらのアカウント キーのいずれかを知らせると、悪意で、または誤ってアカウントが使用される可能性が生じます。共有アクセス署名は、アカウント キーが不要で安全な代替方法です。この方法で、他のクライアントは、付与されたアクセス許可に従ってストレージ アカウント内のデータの読み取り、書き込み、削除を実行できます。

SAS が役立つ一般的なシナリオは、ストレージ アカウント内でユーザーが自分のデータの読み取りや書き込みを行うサービスです。ストレージ アカウントにユーザー データが格納されるシナリオには、2 種類の典型的な設計パターンがあります。


1\. 認証を実行するフロントエンド プロキシ サービス経由で、クライアントがデータのアップロードとダウンロードを行います。このフロントエンド プロキシ サービスには、ビジネス ルールの検証が可能であるという利点がありますが、データやトランザクションが大量である場合は、需要に応じて拡張可能なサービスの作成にコストがかかったり、困難が生じたりする可能性があります。

![sas-storage-fe-proxy-service][sas-storage-fe-proxy-service]

2\.	軽量サービスが、必要に応じてクライアントを認証してから、SAS を生成します。クライアントは、SAS を受信すると、SAS で定義されたアクセス許可と SAS で許可された期間で、ストレージ アカウントのリソースに直接アクセスできるようになります。SAS によって、すべてのデータをフロントエンド プロキシ サービス経由でルーティングする必要性が減少します。

![sas-storage-provider-service][sas-storage-provider-service]

実際のサービスの多くでは、関連するシナリオに応じて、この 2 種類のアプローチのハイブリッドを使用できます。つまり、一部のデータをフロントエンド プロキシで処理および検証し、それ以外のデータを SAS で直接保存したり、読み取ったりします。

## 共有アクセス署名の機能##

共有アクセス署名は、ストレージ リソースを指す URI であり、クライアントがリソースにアクセスする方法を示すクエリ パラメーターの特殊なセットを含んでいます。これらのパラメーターの 1 つである署名は、SAS パラメーターで作成されており、アカウント キーで署名されています。この署名を使用して、Azure のストレージが SAS を認証します。

共有アクセス署名は、その定義である、次の制約を含んでいます。各制約は、URI でパラメーターとして表示されます。

- **ストレージ リソース。**そのアクセスを委任できるストレージ リソースには、コンテナー、BLOB、キュー、テーブル、およびテーブル エンティティの範囲があります。
- **開始時刻。**この時刻に SAS が有効になります。共有アクセス署名の開始時刻は省略可能です。省略した場合は、SAS がすぐに有効になります。
- **有効期限。**この時刻の後、SAS が有効ではなくなります。ベスト プラクティスでは、SAS の有効期限を指定するか、保存されているアクセス ポリシーに SAS を関連付けることを推奨しています (以下を参照)。
- **アクセス許可。**SAS に指定されたアクセス許可は、クライアントが SAS を使用して、ストレージ リソースに対して実行できる操作を示します。

BLOB に読み書きアクセス許可を付与する SAS URI の例を、次に示します。表では、SAS での機能がわかりやすいように、URI の部分ごとに説明しています。

https://myaccount.blob.core.windows.net/sascontainer/sasblob.txt?sv=2012-02-12&st=2013-04-29T22%3A18%3A26Z&se=2013-04-30T02%3A23%3A26Z&sr=b&sp=rw&sig=Z%2FRHIX5Xcg0Mq2rqI3OlWTjEg2tYkboXr1P9ZUXDtkk%3D

<table border="1" cellpadding="0" cellspacing="0">
    <tbody>
        <tr>
            <td valign="top" width="213">
                <p>
                    Blob URI
                </p>
            </td>
            <td valign="top" width="213">
                <p>
                    https://myaccount.blob.core.windows.net/sascontainer/sasblob.txt
                </p>
            </td>
            <td valign="top" width="213">
                <p>
                    BLOB のアドレス。HTTPS の使用を強くお勧めします。
                </p>
            </td>
        </tr>
        <tr>
            <td valign="top" width="213">
                <p>
                    ストレージ サービスのバージョン
                </p>
            </td>
            <td valign="top" width="213">
                <p>
                    sv=2012-02-12
                </p>
            </td>
            <td valign="top" width="213">
                <p>
                    ストレージ サービス バージョン 2012-02-12 以降では、このパラメーターは、使用するバージョンを示します。
                </p>
            </td>
        </tr>
        <tr>
            <td valign="top" width="213">
                <p>
                    開始時刻
                </p>
            </td>
            <td valign="top" width="213">
                <p>
                    st=2013-04-29T22%3A18%3A26Z
                </p>
            </td>
            <td valign="top" width="213">
                <p>
                    ISO 8061 形式で指定されます。SAS をすぐに有効にする場合は、開始時刻を省略します。
                </p>
            </td>
        </tr>
        <tr>
            <td valign="top" width="213">
                <p>
                    有効期限
                </p>
            </td>
            <td valign="top" width="213">
                <p>
                    se=2013-04-30T02%3A23%3A26Z
                </p>
            </td>
            <td valign="top" width="213">
                <p>
                    ISO 8061 形式で指定されます。
                </p>
            </td>
        </tr>
        <tr>
            <td valign="top" width="213">
                <p>
                    リソース
                </p>
            </td>
            <td valign="top" width="213">
                <p>
                    sr=b
                </p>
            </td>
            <td valign="top" width="213">
                <p>
                    リソースは BLOB です。
                </p>
            </td>
        </tr>
        <tr>
            <td valign="top" width="213">
                <p>
                    アクセス許可
                </p>
            </td>
            <td valign="top" width="213">
                <p>
                    sp=rw
                </p>
            </td>
            <td valign="top" width="213">
                <p>
                    SAS で付与されるアクセス許可には、読み取り (r) および書き込み (w) が含まれます。
                </p>
            </td>
        </tr>
        <tr>
            <td valign="top" width="213">
                <p>
                    署名
                </p>
            </td>
            <td valign="top" width="213">
                <p>
                    sig=Z%2FRHIX5Xcg0Mq2rqI3OlWTjEg2tYkboXr1P9ZUXDtkk%3D
                </p>
            </td>
            <td valign="top" width="213">
                <p>
                    BLOB へのアクセスを認証するために使用します。署名は、SHA256 アルゴリズムを使用して署名対象文字列とキーを計算した後に、
                    Base 64 エンコードを使用してエンコードした HMAC 値です。
                </p>
            </td>
        </tr>
    </tbody>
</table>


## 保存されているアクセス ポリシーによる共有アクセス署名の制御##

共有アクセス署名の形式は、次の 2 つのいずれかです。

- **アドホック SAS:** アドホック SAS を作成すると、開始時刻、有効期限、および SAS へのアクセス許可がすべて、SAS URI で指定されます (または、開始時刻を省略した場合は、暗黙で指定されます)。この種類の SAS は、コンテナー、BLOB、テーブル、またはキューで作成できます。
- **保存されているアクセス ポリシーのある SAS:** 保存されているアクセス ポリシーは、リソース コンテナー (BLOB コンテナー、テーブル、またはキュー) で定義されており、これを使用して、1 つ以上の共有アクセス署名のコンテナーを管理できます。保存されているアクセス ポリシーに SAS を関連付けると、SAS は、保存されているアクセス ポリシーに定義されている制約 (開始時刻、有効期限、およびアクセス許可) を継承します。

1 つの重要なシナリオ、失効 では、この 2 つの形式の相違点が重要です。SAS は URL であるため、取得したユーザーはだれでも、どのユーザーが最初に要求したかに関係なく、SAS を使用できます。SAS が一般ユーザーに発行された場合は、世界中のだれでも使用できます。配布された SAS は、次の 4 つの状況のいずれかになるまで有効です。

1.	SAS に指定された有効期限に達した。
2.	保存されているアクセス ポリシーに指定された、SAS が参照する有効期限に達した (保存されているアクセス ポリシーが参照される場合、かつ有効期限が指定されている場合)。これは、期間が経過したため、または保存されているアクセス ポリシーの有効期限を過去の日時に変更したために発生します。このような有効期限の変更は、SAS を失効させる方法の 1 つです。
3.	SAS の参照先である保存されているアクセス ポリシーが削除されている。これは、SAS を失効させる、もう 1 つの方法です。保存されているアクセス ポリシーを、完全に同じ名前で再作成すると、そのアクセス ポリシーに関連付けられたアクセス許可に従って、すべての既存の SAS トークンが再び有効になります (SAS の有効期限が過ぎていないことが前提です)。SAS を失効させるつもりで、将来の時間を有効期限に指定してアクセス ポリシーを再作成する場合は必ず、異なる名前を使用してください。
4.	SAS の作成に使用したアカウント キーが再度生成された。これを行うと、そのアカウント キーを使用するすべてのアプリケーション コンポーネントが、別の有効なアカウント キー、または新しく再生成されたアカウント キーを使用するよう更新されるまで、認証に失敗します。
 
## 共有アクセス署名の使用のベスト プラクティス##

アプリケーションで共有アクセス署名を使用する場合は、次の 2 つの潜在的なリスクに注意する必要があります。

- SAS が漏えいすると、取得した人はだれでも使用できるため、ストレージ アカウントの安全性が損なわれるおそれがあります。
- クライアント アプリケーションに提供された SAS の期限切れになり、アプリケーションがサービスから新しい SAS を取得できない場合は、アプリケーションの機能が損なわれる可能性があります。

共有アクセス署名の使用に関する次の推奨事項に従うと、これらのリスクが軽減されます。

1. **常に HTTPS を使用**して SAS を作成または配布します。SAS が HTTP で渡され、傍受された場合、中間者攻撃を実行している攻撃者は、SAS を読み取って、宛先のユーザーと同様に使用することができます。このため、機密データの安全性が損なわれたり、悪意のあるユーザーによるデータ破損が発生したりする可能性があります。
2. **可能な場合は、保存されているアクセス ポリシーを参照します。**保存されているアクセス ポリシーを使用すると、ストレージ アカウント キーを再生成せずに、アクセス許可を失効させることが可能になります。これらの有効期限を非常に長い期間 (または無期限) に設定し、それがさらに将来へ移動するように、定期的に更新されるようにします。
3. **アドホック SAS には、短期間の有効期限を使用します。**こうすると、知らないうちに SAS の安全性が損なわれていても、有効であるのは短期間だけになります。この方法は、保存されているアクセス ポリシーを参照できない場合に特に重要です。また、この方法では、BLOB にアップロード可能な時間が制限されるので、BLOB に書き込むことのできるデータの量を制限するために役立ちます。
4. **必要に応じて、クライアントに SAS を自動更新させます。**クライアントは、SAS を提供するサービスが利用不可である場合に再試行する時間を考慮して、予期される有効期限までに余裕を持って SAS を更新する必要があります。使用する SAS が、すぐに実行する短期間の少数の操作用であり、操作が、指定された有効期限の前に完了する予定である場合は、SAS が更新されないため、この方法は必要ありません。ただし、クライアントが SAS 経由で日常的に要求を実行する場合は、有効期限に注意が必要になる可能性があります。重要な考慮事項は、SAS の有効期限を短くする必要性 (前述のように) と、更新の完了前に SAS の期限が切れることによる中断を避けるためにクライアントが早めに更新を要求する必要性とのバランスです。
5. **SAS の開始時刻に注意します。**SAS の開始時刻を **[現在]** に設定すると、クロック スキュー (さまざまなコンピューター間での現在時刻の差) により、最初の数分間にエラーが断続的に表示される場合があります。一般に、開始時刻を少なくとも 15 分前に設定するか、まったく設定しないようにします。設定しないと、すべての場合に、すぐに有効になります。同じことが、一般的には有効期限にも適用されます。どの要求でも、前後 15 分以内のクロック スキューが発生する可能性があることを憶えておいてください。2012-02-12 より前の REST バージョンを使用するクライアントの場合、保存されているアクセス ポリシーを参照しない SAS の最長期間は 1 時間であり、それより長い期間を指定するポリシーはすべて失敗します。
6.	**アクセス先のリソースを具体的に指定します。**一般的なセキュリティのベスト プラクティスは、必要最小限の権限をユーザーに付与することです。ユーザーに必要なのは、1 つのエンティティへの読み取りアクセスだけの場合は、すべてのエンティティへの読み取り/書き込み/削除アクセスではなく、その 1 つのエンティティへの読み取りアクセスだけをユーザーに許可します。こうすると、SAS の安全性に対する脅威の軽減にも役立ちます。攻撃者が入手した場合でも、SAS の効力が小さいためです。
7.	**アカウントは、SAS によるものも含め、すべての使用について課金されます。**BLOB への書き込みアクセスを許可した場合は、ユーザーが 200 GB の BLOB をアップロードする可能性があります。ユーザーに読み取りアクセスも許可すると、この BLOB を 10 回ダウンロードする可能性があります。この場合、2 TB の送信料金が発生します。したがって、悪意のあるユーザーによるリスクが軽減されるように、制限付きアクセス許可を付与してください。このような脅威が軽減されるように、短期間の SAS を使用してください (ただし、終了時刻のクロック スキューには注意してください)。
8.	**SAS を使用して書き込まれたデータを検証します。**クライアント アプリケーションがストレージ アカウントにデータを書き込む場合は、そのデータに問題がある可能性に注意してください。データが検証後または認証後に使用可能になることをアプリケーションが要求する場合は、書き込まれたデータをアプリケーションが使用する前に、この検証を実行する必要があります。これを実行すると、ユーザーが SAS を正当に入手している場合でも、漏えいした SAS を利用している場合でも、破損データまたは悪意によるデータの書き込みからアカウントが保護されます。
9. **場合によっては SAS を使用しないようにします。**ストレージ アカウントに対する特定の操作に関連するリスクが、SAS の利点より重大である場合もあります。このような操作については、ビジネス ルールの検証、認証、および監査を実行した後にストレージ アカウントに書き込む中間層サービスを作成します。また、別の方法でアクセスを管理した方が容易である場合もあります。たとえば、コンテナー内のすべての BLOB が一般ユーザーに読み取り可能である場合は、すべてのクライアントにアクセス用の SAS を提供するのではなく、コンテナーをパブリックにします。
10.	**Storage Analytics を使用してアプリケーションを管理します。**SAS プロバイダー サービスが中断したり、保存されているアクセス ポリシーを不注意で削除したりしたために発生する認証失敗の急増を、ログやメトリックを使用して監視できます。詳細については、[Microsoft Azure のストレージ チームのブログ](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/08/03/windows-azure-storage-logging-using-logs-to-track-storage-requests.aspx)を参照してください。

## まとめ##

共有アクセス署名は、アカウント キーを知らせずに、ストレージ アカウントへの制限付きアクセス許可をクライアントに付与する場合に便利です。したがって、Azure のストレージを使用するあらゆるアプリケーションのセキュリティ モデルの重要な部分となります。ここに示すベスト プラクティスに従うと、アプリケーションのセキュリティを損なうことなく、SAS を使用して、ストレージ アカウントのリソースへのアクセスの柔軟性を高めることができます。

## 次のステップ##

[共有アクセス署名、第 2 部 : BLOB サービスによる SAS の作成および使用](../storage-dotnet-shared-access-signature-part-2/)

[Microsoft Azure ストレージ リソースへのアクセスの管理](http://msdn.microsoft.com/ja-jp/library/windowsazure/ee393343.aspx)

[共有アクセス署名によるアクセスの委任 (REST API) に関するページ](http://msdn.microsoft.com/ja-jp/library/windowsazure/ee395415.aspx)

[テーブルおよびキュー SAS についての MSDN ブログ](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-table-sas-shared-access-signature-queue-sas-and-update-to-blob-sas.aspx)
[sas-storage-fe-proxy-service]: ./media/storage-dotnet-shared-access-signature-part-1/sas-storage-fe-proxy-service.png
[sas-storage-provider-service]: ./media/storage-dotnet-shared-access-signature-part-1/sas-storage-provider-service.png


