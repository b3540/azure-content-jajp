<properties linkid="develop-mobile-tutorials-twilio-for-voice-and-sms" pageTitle="音声および SMS 機能での Twilio の使用 | モバイル デベロッパー センター" metaKeywords="" description="Azure モバイル サービスで Twilio API を使用して一般的なタスクを実行する方法について説明します。" metaCanonical="" services="" documentationCenter="Mobile" title="モバイル サービスから音声および SMS 機能に Twilio を使用する方法" authors="twilio" solutions="" manager="" editor="" />


<h1>モバイル サービスから音声および SMS 機能に Twilio を使用する方法</h1>

このトピックでは、Azure モバイル サービスで Twilio API を使用して一般的なタスクを実行する方法について説明します。このチュートリアルでは、Twilio API を使用して通話を開始したりショート メッセージ サービス (SMS) メッセージを送信したりするカスタム API スクリプトの作成方法を学習します。

<h2><a id="WhatIs"></a>Twilio とは</h2>
Twilio は、開発者がアプリケーションに音声、VoIP、およびメッセージングを埋め込むことを可能にし、ビジネス コミュニケーションを強化していきます。必要なすべてのインフラストラクチャをクラウド ベースのグローバル環境で仮想化し、Twilio 通信 API プラットフォームを通じてそれを公開します。アプリケーションは構築しやすく、スケーラビリティも優れています。従量課金制の柔軟性と、クラウドの信頼性の利点を活用できます。

**Twilio Voice** を使用すると、アプリケーションで音声通話の発着信処理を行うことができます。**Twilio SMS** を使用すると、アプリケーションで SMS メッセージの送受信を行うことができます。**Twilio Client** では、任意の電話、タブレット、またはブラウザーから VoIP 通話を行うことができ、WebRTC がサポートされています。

<h2><a id="Pricing"></a>Twilio の料金および特別プラン</h2>
Azure ユーザーには、[特別プラン][special_offer]として、Twilio アカウントをアップグレードする際に、$10 の Twilio クレジットが提供されます。この Twilio クレジットは、任意の Twilio 使用に対して利用できます。$10 のクレジットは、約 1,000 件の SMS メッセージの送信、または最大で 1,000 分の受信音声に相当します (ご利用の電話番号の場所と、メッセージまたは通話の相手の場所に応じて異なります)。この Twilio クレジットを利用するには、[ahoy.twilio.com/azure][special_offer] にアクセスします。

Twilio は、従量課金制サービスです。セットアップ料金は不要で、いつでもアカウントを閉じることができます。詳細については、[Twilio の料金のページ][twilio_pricing]をご覧ください。

<h2><a id="Concepts"></a>概念</h2>
Twilio API は、アプリケーションに音声および SMS 機能を提供する REST ベースの API です。クライアント ライブラリはさまざまな言語で用意されています。言語の一覧については、「[Twilio API Libraries (Twilio API ライブラリ)] [twilio_libraries]」を参照してください。各種の言語で記述された Azure アプリケーションで Twilio を使用するための追加のチュートリアルも用意されています。記述言語は、[.NET][azure_twilio_howto_dotnet]、[node.js][azure_twilio_howto_node]、[Java][azure_twilio_howto_java]、[PHP][azure_twilio_howto_php]、[Python][azure_twilio_howto_python]、[Ruby][azure_twilio_howto_ruby] です。

Twilio API の主要な側面として、Twilio 動詞と Twilio Markup Language (TwiML) が挙げられます。

<h3><a id="Verbs"></a>Twilio 動詞</h3>
API では、Twilio 動詞を使用します。たとえば、**&lt;Say&gt;** 動詞は、メッセージを音声で返すことを Twilio に指示します。

Twilio 動詞の一覧を次に示します。他の動詞と機能については、[Twilio Markup Language のドキュメント](http://www.twilio.com/docs/api/twiml)を参照してください。

* **&lt;Dial&gt;**: 呼び出し元を別の電話に接続します。
* **&lt;Gather&gt;**: 電話キーパッドに入力された数字を収集します。
* **&lt;Hangup&gt;**: 通話を終了します。
* **&lt;Play&gt;**: 音声ファイルを再生します。
* **&lt;Pause&gt;**: 何も行わずに指定された秒数待機します。
* **&lt;Record&gt;**: 呼び出し元の声を録音し、声が録音されたファイルの URL を返します。
* **&lt;Redirect&gt;**: 通話または SMS の制御を別の URL の TwiML に転送します。
* **&lt;Reject&gt;**: Twilio 番号への着信通話を拒否します。課金はされません。
* **&lt;Say&gt;**: テキストを音声に変換して返します。
* **&lt;Sms&gt;**: SMS メッセージを送信します。

<h3> <a id="TwiML"></a>TwiML</h3>
TwiML は、Twilio 動詞に基づいた XML ベースの命令のセットで、通話または SMS をどのように処理するかを Twilio に通知します。

たとえば、次の TwiML は、テキスト **Hello World** を音声に変換します。

    <?xml version="1.0" encoding="UTF-8" ?>
    <Response>
       <Say>Hello World</Say>
    </Response>

アプリケーションで Twilio API を呼び出す場合は、API パラメーターの 1 つで TwiML 応答を返す URL を指定します。開発用には、Twilio から提供される URL を使用して、アプリケーションで使用する TwiML 応答を提供することができます。また、独自に URL をホストして、TwiML 応答を生成することもできます。別のオプションとして、**TwiMLResponse** オブジェクトを使用することもできます。

Twilio の動詞と属性、および TwiML の詳細については、[TwiML に関するページ][twiml]を参照してください。Twilio API の詳細については、[Twilio API に関するページ][twilio_api]を参照してください。

<h2><a id="CreateAccount"></a>Twilio アカウントを作成する</h2>
Twilio アカウントを取得する準備ができたら、[Twilio のサインアップ ページ][try_twilio]でサインアップします。無料アカウントで始め、後でアカウントをアップグレードすることができます。

Twilio アカウントにサインアップすると、アカウント ID と認証トークンが発行されます。Twilio API を呼び出すには、この両方が必要になります。自分のアカウントが不正にアクセスされないように、認証トークンを安全に保管してください。アカウント ID と認証トークンは、[Twilio アカウント ページ][twilio_account]の **[ACCOUNT SID]** フィールドと **[AUTH TOKEN]** フィールドでそれぞれ確認できます。

<h2><a id="VerifyPhoneNumbers"></a>電話番号を確認する</h2>
アカウントに対して、さまざまな電話番号を Twilio で確認する必要があります。たとえば、電話を発信する場合は、電話番号を発信元 ID として Twilio で確認する必要があります。同様に、電話番号を使用して SMS メッセージを受信する場合は、受信に使用する電話番号を Twilio で確認する必要があります。電話番号を確認する方法の詳細については、「[Manage numbers (番号の管理)] [verify_phone]」を参照してください。次に示すコードの一部は、Twilio で確認する必要がある電話番号に依存しています。

アプリケーションで既存の番号を使用する代わりに、Twilio 電話番号を購入することができます。Twilio 電話番号の購入については、[Twilio 電話番号のヘルプに関するページ](https://www.twilio.com/help/faq/phone-numbers)を参照してください。

<h2><a id="create_app"></a>モバイル サービスの作成</h2>
Twilio 対応のアプリケーションをホストするモバイル サービスには、他のモバイル サービスとの違いはありません。モバイル サービス カスタム API スクリプトから参照するために、Twilio node.js ライブラリを単に追加します。モバイル サービスを初めて作成する場合の詳細については、「[モバイル サービスの使用](http://www.windowsazure.com/ja-jp/develop/mobile/tutorials/get-started/)」を参照してください。

<h2><a id="VerifyPhoneNumbers"></a>Twilio Node.js ライブラリを使用するためのモバイル サービスの構成</h2>
Twilio は、Node.js ライブラリを提供します。このライブラリは、Twilio のさまざまな側面をラップし、Twilio REST API および Twilio Client と対話して TwiML 応答を生成するためのシンプルで簡単な方法を提供します。

モバイル サービスで Twilio node.js ライブラリを使用するには、モバイル サービス npm モジュールのサポートを利用する必要があります。そのためには、スクリプトをソース管理に格納します。チュートリアル「[Store Scripts in Source Control (ソース管理へのスクリプトの保存)](http://www.windowsazure.com/ja-jp/develop/mobile/tutorials/store-scripts-in-source-control/)」では、モバイル サービスでの初めてのソース管理のセットアップと、Git リポジトリへのサーバー スクリプトの保存について具体的に説明しています。

モバイル サービスのソース管理のセットアップが済んだら、モバイル サービス ダッシュボードの [構成] タブを開き、Git URL を探してコピーします。

この URL をブラウザーに貼り付け、リポジトリ名を */DebugConsole/index.html* に置き換えます。

たとえば、

    https://twilioSample.scm.azure-mobile.net/twilioSample.git

を次のように変更します。

    https://twilioSample.scm.azure-mobile.net/DebugConsole/index.html

入力を求めるメッセージが表示されたら、サービスのソース管理のセットアップに使用した資格情報を入力します。ログインすると、Azure モバイル サービス コンソールが表示されます。

![モバイル サービス コンソール](./media/partner-twilio-mobile-services-how-to-use-voice-sms/twilio-kuduconsole.png)

コンソールで、次のようにして、scripts フォルダーに移動します。

    cd site\wwwroot\App_Data\config\scripts

api フォルダーで次のコマンドを実行して、Twilio ノード モジュールをインストールできます。

    npm install twilio

これで、カスタム API とテーブル スクリプトで Twilio ライブラリを参照し、使用できるようになりました。

<h2><a id="howto_make_call"></a>方法: 発信通話する</h2>
次のスクリプトは、**makeCall** 関数を使用してモバイル サービスから発信通話を開始する方法を示しています。このコードは、Twilio から提供されるサイトも使用して、Twilio Markup Language (TwiML) 応答を返します。コードを実行する前に、**From** および **To** の電話番号の値を置き換えて、Twilio アカウントの **From** の電話番号を確認します。

    var twilio = require('twilio');

    exports.post = function(request, response) {

        var client = new twilio.RestClient('[ACCOUNT_SID]', 'AUTH_TOKEN');

        client.makeCall({
            to:'+16515556677', 
            from: '+14506667788',
            url: 'http://www.example.com/twiml.php' 

        }, function(err, responseData) {
            console.log(responseData.from); 
            response.send(200, '');
        });
    };

**client.makeCall** 関数に渡されるパラメーターの詳細については、[http://www.twilio.com/docs/api/rest/making-calls][twilio_rest_making_calls] を参照してください。

既に説明したように、このコードは Twilio から提供されるサイトを使用して、TwiML 応答を返します。代わりに独自のサイトを使用して TwiML 応答を返すこともできます。詳細については、「[方法: 独自の Web サイトから TwiML 応答を返す](#howto_provide_twiml_responses)」を参照してください

<h2><a id="howto_send_sms"></a>方法: SMS メッセージを送信する</h2>
次のコードでは、**sendSms** 関数を使用して SMS メッセージを送信する方法を示しています。試用アカウントで SMS メッセージを送信できるように、**From** の番号が Twilio から提供されます。コードを実行する前に、Twilio アカウントの **To** の番号を確認する必要があります。

    var twilio = require('twilio');

    exports.post = function(request, response) {

        var client = new twilio.RestClient('[ACCOUNT_SID]', 'AUTH_TOKEN');
 
        client.sendSms({
            to:'[]',
            from:'[]',
            body:'ahoy hoy! Testing Twilio and node.js'
        }, function(error, message) {
    
            // The "error" variable will contain error information, if any.
            // If the request was successful, this value will be "false"
            if (!error) {
                console.log('Success! The SID for this SMS message is: ' + message.sid);
                console.log('Message sent on: ' + message.dateCreated);
            }
            else {
                console.log('Oops! There was an error.');
            }
            response.send(200, { error : error } );
        });
    };


<h2><a id="howto_provide_twiml_responses"></a>方法: 独自の Web サイトから TwiML 応答を返す</h2>

アプリケーションで Twilio API の呼び出しを開始する場合 (たとえば、client.InitiateOutboundCall メソッドを使用した場合)、Twilio は TwiML 応答を返すことが想定されている URL にユーザーの要求を送信します。「方法: 発信通話する」の例では、Twilio から提供される URL http://twimlets.com/message を使用して応答を返します。

<div class="dev-callout">
<b>注</b>
<p>TwiML は Web サービスで使用するように設計されており、ブラウザーで表示できます。たとえば、<a href="http://twimlets.com/message">twimlet_message_url</a> をクリックすると、空の &lt;Response&gt; 要素が表示されます。もう 1 つの例として、<a href="http://twimlets.com/message?Message%5B0%5D=Hello%20World">twimlet_message_url_hello_world</a> をクリックすると、&lt;Say&gt; 要素を格納している &lt;Response&gt; 要素が表示されます。</p>
</div>

Twilio から提供される URL を使用する代わりに、HTTP 応答を返す独自の URL サイトを作成できます。HTTP 応答を返すサイトは、任意の言語で作成できます。このトピックでは、ASP.NET 汎用ハンドラーから URL をホストすることを想定しています。

次のスクリプトでは、通話時の TwiML 応答で "Hello World" というテキストが読み上げられます。

    var twilio = require('twilio');

    exports.post = function(request, response) {
        var resp = new twilio.TwimlResponse();
        resp.say({voice:'woman'}, 'ahoy hoy! Testing Twilio and node.js');
        response.set('Content-Type', 'text/xml');
        response.send(200, resp.toString());
    };

TwiML の詳細については、[https://www.twilio.com/docs/api/twiml](https://www.twilio.com/docs/api/twiml) を参照してください。

TwiML 応答を提供する方法をセットアップしたら、次のコード サンプルで示すように、その URL を **client.makeCall** メソッドに渡すことができます。
    
    var twilio = require('twilio');

    exports.post = function(request, response) {

        var client = new twilio.RestClient('[ACCOUNT_SID]', 'AUTH_TOKEN');

        client.makeCall({
            to:'+16515556677', 
            from: '+14506667788',
            url: 'http://<your_mobile_service>.azure-mobile.net/api/makeCall' 

        }, function(err, responseData) {

            console.log(responseData.from);
            response.send(200, '');
        });
    };

[WACOM.INCLUDE [twilio_additional_services_and_next_steps](../includes/twilio_additional_services_and_next_steps.md)]


[twilio_rest_making_calls]: http://www.twilio.com/docs/api/rest/making-calls

[twilio_pricing]: http://www.twilio.com/pricing
[special_offer]: http://ahoy.twilio.com/azure
[twilio_libraries]: https://www.twilio.com/docs/libraries
[twiml]: http://www.twilio.com/docs/api/twiml
[twilio_api]: http://www.twilio.com/api
[try_twilio]: https://www.twilio.com/try-twilio
[twilio_account]:  https://www.twilio.com/user/account
[verify_phone]: https://www.twilio.com/user/account/phone-numbers/verified#


[azure_twilio_howto_dotnet]: /ja-jp/develop/net/how-to-guides/twilio-voice-and-sms-service/
[azure_twilio_howto_java]: /ja-jp/develop/java/how-to-guides/twilio-voice-and-sms-service/
[azure_twilio_howto_node]: /ja-jp/develop/nodejs/how-to-guides/twilio-voice-and-sms-service/
[azure_twilio_howto_ruby]: /ja-jp/develop/ruby/how-to-guides/twilio-voice-and-sms-service/
[azure_twilio_howto_python]: /ja-jp/develop/python/how-to-guides/twilio-voice-and-sms-service/
[azure_twilio_howto_php]: /ja-jp/develop/php/how-to-guides/twilio-voice-and-sms-service/
