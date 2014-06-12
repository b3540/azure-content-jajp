<properties title="SendGrid 電子メール サービスの使用方法 (PHP) - Azure" pageTitle="SendGrid 電子メール サービスの使用方法 (PHP) - Azure" metaKeywords="Azure SendGrid, Azure 電子メール サービス, Azure SendGrid PHP, Azure 電子メール PHP" description="Azure の SendGrid 電子メール サービスを使用して電子メールを送信する方法について説明します。コード サンプルは PHP で記述されています。" documentationCenter="PHP" services="" />

# PHP から SendGrid 電子メール サービスを使用する方法

このガイドでは、Azure の SendGrid 電子メール サービスを使用して一般的なプログラム タスクを実行する方法を紹介します。サンプルは PHP で記述されています。
紹介するシナリオは、**電子メールの作成**、**電子メールの送信**、および**添付ファイルの追加**です。SendGrid と電子メールの送信の詳細については、「[次のステップ][]」を参照してください。

## 目次

-   [SendGrid 電子メール サービスとは][]
-   [SendGrid アカウントの作成][]
-   [PHP アプリケーションからの SendGrid の使用][]
-   [方法: 電子メールを送信する][]
-   [方法: 添付ファイルを追加する][]
-   [方法: フィルターを使用してフッター、追跡、および分析を有効にする][]
-   [方法: その他の SendGrid サービスを使用する][]
-   [次のステップ][]

## <a name="bkmk_WhatIsSendGrid"> </a>SendGrid 電子メール サービスとは

SendGrid は、信頼性の高い[トランザクション電子メール配信]、
拡張性、およびリアルタイム分析の機能を備えた[クラウドベース電子メール サービス]であり、
柔軟な API を備えているためカスタム統合も容易です。SendGrid の一般的な使用シナリオを
次に示します。

-   顧客に受信通知を自動送信する
-   顧客に広告メールを月 1 回送信するための配布リストを
    管理する
-   ブロックされた電子メールや顧客の応答性などを表す測定値を
    リアルタイムで収集する
-   傾向を認識するために役立つレポートを生成する
-   顧客の問い合わせを転送する
- アプリケーションからの電子メール通知

詳細については、[http://sendgrid.com][] を参照してください。

## <a name="bkmk_CreateSendGrid"> </a>SendGrid アカウントの作成

[WACOM.INCLUDE [sendgrid-sign-up](../includes/sendgrid-sign-up.md)]

## <a name="bkmk_UsingSendGridfromPHP"> </a>PHP アプリケーションからの SendGrid の使用

Azure PHP アプリケーションで SendGrid を使用する際に、
特別な構成やコーディングは不要です。SendGrid はサービスであるため、
内部設置型アプリケーションからとまったく同じ方法で、クラウド アプリケーション
からアクセスできます。

電子メール サポートをアプリケーションに追加した後、アプリケーションを
パッケージ化して展開できます (その方法の概要については
「[Azure に対する PHP アプリケーションのパッケージ化と配置][]」を参照してください。

## <a name="bkmk_HowToSendEmail"> </a>方法: 電子メールを送信する

SMTP、または SendGrid の Web API を使用して電子メールを
送信できます。各 API の長所と API 間の違いの詳細については、[SMTP と Web API を
比較した SendGrid ドキュメント][]を参照してください。

### SMTP API

SendGrid SMTP API を使用して電子メールを送信するには、*Swift Mailer* を使用します。
Swift Mailer は、PHP アプリケーションから電子メールを送信するためのコンポーネントベースのライブラリです。*Swift Mailer* 
ライブラリは [http://swiftmailer.org/download][] から
ダウンロードできます。このライブラリを使用して
電子メールを送信するには、
<span class="auto-style2">Swift\_SmtpTransport</span>、
<span class="auto-style2">Swift\_Mailer</span>、および
<span class="auto-style2">Swift\_Message</span> クラスの
インスタンスを作成し、適切なプロパティを設定して、
<span class="auto-style2">Swift\_Mailer::send</span> メソッドを呼び出します。

    <?php
     include_once "lib/swift_required.php";
     /*
      * Create the body of the message (a plain-text and an HTML version).
      * $text is your plain-text email
      * $html is your html version of the email
      * If the reciever is able to view html emails then only the html
      * email will be displayed
      */ 
     $text = "Hi!\nHow are you?\n";
     $html = <<<EOM
           <html>
           <head></head>
           <body>
               <p>Hi!<br>
                   How are you?<br>
               </p>
           </body>
           </html>
           EOM;
     // This is your From email address
     $from = array('someone@example.com' => 'Name To Appear');
     // Email recipients
     $to = array(
           'john@contoso.com'=>'Destination 1 Name',
           'anna@contoso.com'=>'Destination 2 Name'
     );
     // Email subject
     $subject = 'Example PHP Email';

     // Login credentials
     $username = 'yoursendgridusername';
     $password = 'yourpassword';
     
     // Setup Swift mailer parameters
     $transport = Swift_SmtpTransport::newInstance('smtp.sendgrid.net', 587);
     $transport->setUsername($username);
     $transport->setPassword($password);
     $swift = Swift_Mailer::newInstance($transport);

     // Create a message (subject)
     $message = new Swift_Message($subject);

     // attach the body of the email
     $message->setFrom($from);
     $message->setBody($html, 'text/html');
     $message->setTo($to);
     $message->addPart($text, 'text/plain');
     
     // send message 
     if ($recipients = $swift->send($message, $failures))
     {
         // This will let us know how many users received this message
         echo 'Message sent out to '.$recipients.' users';
     }
     // something went wrong =(
     else
     {
         echo "Something went wrong - ";
         print_r($failures);
     }

**メモ**: この例のスクリプトは SendGrid ドキュメント (
[http://docs.sendgrid.com/documentation/get-started/integrate/examples/php-email-example-using-smtp/][]
) からの抜粋です。

SMTP API および X-SMTPAPI ヘッダーの詳細については、
SendGrid ドキュメントの SMTP API 開発者ガイド 
[http://docs.sendgrid.com/documentation/api/smtp-api/developers-guide/][].
PHP で SMTP API を使用するその他の例については、SendGrid ドキュメント 
[http://docs.sendgrid.com/documentation/api/smtp-api/php-example/][].

### Web API

PHP の [curl 関数][]で SendGrid Web API を使用して電子メールを送信します。

    <?php

     $url = 'http://sendgrid.com/';
     $user = 'USERNAME';
     $pass = 'PASSWORD'; 

     $params = array(
          'api_user' => $user,
          'api_key' => $pass,
          'to' => 'john@contoso.com',
          'subject' => 'testing from curl',
          'html' => 'testing body',
          'text' => 'testing body',
          'from' => 'anna@sendgrid.com',
       );
       
     $request = $url.'api/mail.send.json';
     
     // Generate curl request
     $session = curl_init($request);
     
     // Tell curl to use HTTP POST
     curl_setopt ($session, CURLOPT_POST, true);
     
     // Tell curl that this is the body of the POST
     curl_setopt ($session, CURLOPT_POSTFIELDS, $params);
     
     // Tell curl not to return headers, but do return the response
     curl_setopt($session, CURLOPT_HEADER, false);
     curl_setopt($session, CURLOPT_RETURNTRANSFER, true);
     
     // obtain response
     $response = curl_exec($session);
     curl_close($session);
     
     // print everything out
     print_r($response);

**メモ: **この例のスクリプトは SendGrid 
ドキュメント 
[http://docs.sendgrid.com/documentation/get-started/integrate/examples/php-example-using-the-web-api/][].

**メモ**: SendGrid の Web API は REST API と非常に似ていますが、
実際は REST ベースの API ではありません。ほとんどの呼び出しで GET と POST の
両方の動詞を区別しないで使用できるためです。

Web API の概要については、SendGrid ドキュメント 
[http://docs.sendgrid.com/documentation/api/web-api/][].

送信メール API のすべてのパラメーターと一般的な例の一覧については、
[http://docs.sendgrid.com/documentation/api/web-api/mail/][] を参照してください。

## <a name="bkmk_HowToAddAttachment"> </a>方法: 添付ファイルを追加する

### SMTP API

SMTP API を使用して添付ファイルを送信するには、この例のスクリプトに、Swift Mailer を使用して電子メールを送信するコード行を追加する必要があります。

    <?php
     include_once "lib/swift_required.php";
     /*
      * Create the body of the message (a plain-text and an HTML version).
      * $text is your plain-text email
      * $html is your html version of the email
      * If the reciever is able to view html emails then only the html
      * email will be displayed
      */
     $text = "Hi!\nHow are you?\n";
      $html = <<<EOM
          <html>
          <head></head>
          <body>
             <p>Hi!<br>
                How are you?<br>
             </p>
          </body>
          </html>
     EOM;

     // This is your From email address
     $from = array('someone@example.com' => 'Name To Appear');
     
     // Email recipients
     $to = array(
          'john@contoso.com'=>'Destination 1 Name',
          'anna@contoso.com'=>'Destination 2 Name'
     );
     // Email subject
     $subject = 'Example PHP Email';
     
     // Login credentials
     $username = 'yoursendgridusername';
     $password = 'yourpassword';
     
     // Setup Swift mailer parameters
     $transport = Swift_SmtpTransport::newInstance('smtp.sendgrid.net', 587);
     $transport->setUsername($username);
     $transport->setPassword($password);
     $swift = Swift_Mailer::newInstance($transport);
     
     // Create a message (subject)
     $message = new Swift_Message($subject);
     
     // attach the body of the email
     $message->setFrom($from);
     $message->setBody($html, 'text/html');
     $message->setTo($to);
     $message->addPart($text, 'text/plain');
     $message->attach(Swift_Attachment::fromPath("path\to\file")->setFileName("file_name"));
     
     // send message 
     if ($recipients = $swift->send($message, $failures))
     {
          // This will let us know how many users received this message
          echo 'Message sent out to '.$recipients.' users';
     }
     // something went wrong =(
     else
     {
          echo "Something went wrong - "
          print_r($failures);
     }

**メモ: **この例のスクリプトは SendGrid 
ドキュメント 
[http://docs.sendgrid.com/documentation/get-started/integrate/examples/php-email-example-using-smtp/][].

追加するコード行は次のとおりです。

     $message->attach(Swift_Attachment::fromPath("path\to\file")->setFileName(ï¿½file_nameï¿½));

このコード行では、
<span class="auto-style2">Swift\_Message</span> オブジェクトの attach メソッドを呼び出し、
<span class="auto-style2">Swift\_Attachment</span> クラスの
静的メソッド <span class="auto-style2">fromPath</span> を使用して
ファイルを取得してメッセージに添付しています。

### Web API

Web API を使用した添付ファイルの送信は、Web API を使用した電子メールの
送信と非常によく似ています。ただし、次の例では、パラメーターの配列に次の要素を
格納する必要があることに注意してください。

     'files['.$fileName.']' => '@'.$filePath.'/'.$fileName

    <?php

     $url = 'http://sendgrid.com/';
     $user = 'USERNAME';
     $pass = 'PASSWORD';
     
     $fileName = 'myfile';
     $filePath = dirname(__FILE__);

     $params = array(
         'api_user' => $user,
         'api_key' => $pass,
         'to' =>'john@contoso.com',
         'subject' => 'test of file sends',
         'html' => '<p> the HTML </p>',
         'text' => 'the plain text',
         'from' => 'anna@sendgrid.com',
         'files['.$fileName.']' => '@'.$filePath.'/'.$fileName
     );
     
     print_r($params);
     
     $request = $url.'api/mail.send.json';
     
     // Generate curl request
     $session = curl_init($request);
     
     // Tell curl to use HTTP POST
     curl_setopt ($session, CURLOPT_POST, true);
     
     // Tell curl that this is the body of the POST
     curl_setopt ($session, CURLOPT_POSTFIELDS, $params);
     
     // Tell curl not to return headers, but do return the response
     curl_setopt($session, CURLOPT_HEADER, false);
     curl_setopt($session, CURLOPT_RETURNTRANSFER, true);
     
     // obtain response
     $response = curl_exec($session);
     curl_close($session);
     
     // print everything out
     print_r($response);

**メモ: **この例のスクリプトは SendGrid 
ドキュメント 
[http://docs.sendgrid.com/documentation/get-started/integrate/examples/php-example-using-the-web-api/][].

## <a name="bkmk_HowToUseFilters"> </a>方法: フィルターを使用してフッター、追跡、および分析を有効にする

SendGrid では、"フィルター" を使用することでその他の電子メール機能も
利用することができます。その設定を電子メール メッセージに追加することで、
クリック追跡、Google 分析、サブスクリプション追跡などの
独自の機能を有効にすることができます。すべてのフィルターの一覧については、
[フィルター設定に関するページ][]を参照してください。

フィルターは、フィルターのプロパティを使用してメッセージに適用できます。各フィルターは、
フィルター固有の設定を格納したハッシュで指定します。次の例では、
フッター フィルターを有効にし、電子メール メッセージの下部に追加される
テキスト メッセージを指定しています。

    <?php
     /*
      * This example is used for Swift Mailer V4
      */
     include "./lib/swift_required.php";
     include 'SmtpApiHeader.php';
     
     $hdr = new SmtpApiHeader();
     // The list of addresses this message will be sent to
     // [This list is used for sending multiple emails using just ONE request to 
     SendGrid]
     $toList = array('john@contoso.com', 'anna@contoso.com');
     
     // Specify the names of the recipients
     $nameList = array('Name 1', 'Name 2');
     
     // Used as an example of variable substitution
     $timeList = array('4 PM', '5 PM');
     
     // Set all of the above variables
     $hdr->addTo($toList);
     $hdr->addSubVal('-name-', $nameList);
     $hdr->addSubVal('-time-', $timeList);
     
     // Specify that this is an initial contact message
     $hdr->setCategory("initial");
     
     // You can optionally setup individual filters here, in this example, we have 
     enabled the footer filter
     $hdr->addFilterSetting('footer', 'enable', 1);
     $hdr->addFilterSetting('footer', "text/plain", "Thank you for your business");
     
     // The subject of your email
     $subject = 'Example SendGrid Email';
     
     // Where is this message coming from.For example, this message can be from support@yourcompany.com, info@yourcompany.com
     $from = array('someone@example.com' =&gt; 'Name Of Your Company');
     
     // If you do not specify a sender list above, you can specifiy the user here.If 
     // a sender list IS specified above, this email address becomes irrelevant.
     $to = array('john@contoso.com'=&gt;'Personal Name Of Recipient');
     
     # Create the body of the message (a plain-text and an HTML version).
     # text is your plain-text email
     # html is your html version of the email
     # if the receiver is able to view html emails then only the html
     # email will be displayed
     
     /*
     * Note the variable substitution here =)
     */
     $text = <<<EOM 
     Hello -name-,
     Thank you for your interest in our products.We have set up an appointment to call you at -time- EST to discuss your needs in more detail.
     Regards,
     Fred
     EOM;
     
     $html = <<<EOM
     < html> 
     <head></head>
     <body>
     <p>Hello -name-,<br>
     Thank you for your interest in our products.We have set up an appointment
     to call you at -time- EST to discuss your needs in more detail.
     
     Regards,
     
     Fred, How are you?<br>
     </p>
     </body>
     < /html>
     EOM;
     
     // Your SendGrid account credentials
     $username = 'sendgridusername@yourdomain.com';
     $password = 'example';
     
     // Create new swift connection and authenticate
     $transport = Swift_SmtpTransport::newInstance('smtp.sendgrid.net', 25);
     $transport ->setUsername($username);
     $transport ->setPassword($password);
     $swift = Swift_Mailer::newInstance($transport);
     
     // Create a message (subject)
     $message = new Swift_Message($subject);
     
     // add SMTPAPI header to the message
     // *****IMPORTANT NOTE*****
     // SendGrid's asJSON function escapes characters.If you are using Swift 
     Mailer's
     // PHP Mailer functions, the getTextHeader function will also escape characters.
     // This can cause the filter to be dropped.
     $headers = $message->getHeaders();
     $headers->addTextHeader('X-SMTPAPI', $hdr->asJSON());
     
     // attach the body of the email
     $message->setFrom($from);
     $message->setBody($html, 'text/html');
     $message->setTo($to);
     $message->addPart($text, 'text/plain');
     
     // send message
     if ($recipients = $swift->send($message, $failures))
     {
     // This will let us know how many users received this message
     // If we specify the names in the X-SMTPAPI header, then this will always be 1.
     echo 'Message sent out to '.$recipients.' users';
     }

     // something went wrong=(
     else
     {
     echo "Something went wrong -";
     print_r($failures);
     }

**メモ: **この例のスクリプトは SendGrid 
ドキュメント 
[http://docs.sendgrid.com/documentation/api/smtp-api/php-example/][].

## <a name="bkmk_HowToUseAdditionalSvcs"> </a>方法: その他の SendGrid サービスを使用する

SendGrid の Web ベース API を使用して、Azure アプリケーション
からその他の SendGrid 機能を利用することができます。詳細については、
[SendGrid API に関するドキュメント][]を参照してください。

## <a name="bkmk_NextSteps"> </a>次のステップ

これで、SendGrid 電子メール サービスの基本を学習できました。
さらに詳細な情報が必要な場合は、次のリンク先を参照してください。

-   SendGrid API に関するドキュメント: <http://docs.sendgrid.com/documentation/api/>
-   Azure ユーザー向けの SendGrid 特別プラン: 
    [http://sendgrid.com/azure.html][]

  [次のステップ]: #bkmk_NextSteps
  [SendGrid 電子メール サービスとは]: #bkmk_WhatIsSendGrid
  [SendGrid アカウントの作成]: #bkmk_CreateSendGrid
  [PHP アプリケーションからの SendGrid の使用]: #bkmk_UsingSendGridfromPHP
  [方法: 電子メールを送信する]: #bkmk_HowToSendEmail
  [方法: 添付ファイルを追加する]: #bkmk_HowToAddAttachment
  [方法: フィルターを使用してフッター、追跡、および分析を有効にする]: #bkmk_HowToUseFilters
  [方法: その他の SendGrid サービスを使用する]: #bkmk_HowToUseAdditionalSvcs

  [http://sendgrid.com]: http://sendgrid.com
  [http://sendgrid.com/pricing.html]: http://sendgrid.com/pricing.html
  [特別プラン]: http://www.sendgrid.com/azure.html
  [http://docs.sendgrid.com/documentation/get-started/]: http://docs.sendgrid.com/documentation/get-started/
  [http://sendgrid.com/features]: http://sendgrid.com/features
  [Azure に対する PHP アプリケーションのパッケージ化と配置]: http://msdn.microsoft.com/ja-jp/library/windowsazure/hh674499(v=VS.103).aspx
  [SMTP と Web API を 比較した SendGrid ドキュメント]: http://docs.sendgrid.com/documentation/get-started/integrate/examples/smtp-vs-rest/
  [http://swiftmailer.org/download]: http://swiftmailer.org/download
  [http://docs.sendgrid.com/documentation/get-started/integrate/examples/php-email-example-using-smtp/]: http://docs.sendgrid.com/documentation/get-started/integrate/examples/php-email-example-using-smtp/
  [http://docs.sendgrid.com/documentation/api/smtp-api/developers-guide/]: http://docs.sendgrid.com/documentation/api/smtp-api/developers-guide/
  [http://docs.sendgrid.com/documentation/api/smtp-api/php-example/]: http://docs.sendgrid.com/documentation/api/smtp-api/php-example/
  [curl 関数]: http://php.net/curl
  [http://docs.sendgrid.com/documentation/get-started/integrate/examples/php-example-using-the-web-api/]: http://docs.sendgrid.com/documentation/get-started/integrate/examples/php-example-using-the-web-api/
  [http://docs.sendgrid.com/documentation/api/web-api/]: http://docs.sendgrid.com/documentation/api/web-api/
  [http://docs.sendgrid.com/documentation/api/web-api/mail/]: http://docs.sendgrid.com/documentation/api/web-api/mail/
  [フィルター設定に関するページ]: http://docs.sendgrid.com/documentation/api/smtp-api/filter-settings/
  [SendGrid API に関するドキュメント]: http://docs.sendgrid.com/documentation/api/
  [http://sendgrid.com/azure.html]: http://sendgrid.com/azure.html
  [クラウドベース電子メール サービス]: http://sendgrid.com/solutions
  [トランザクション電子メール配信]: http://sendgrid.com/transactional-email
