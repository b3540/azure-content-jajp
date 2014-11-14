<properties urlDisplayName="Get Started with Mobile Analytics" pageTitle="モバイル分析の使用 | モバイル デベロッパー センター" metaKeywords="" description="モバイル分析の使用" metaCanonical="" disqusComments="1" umbracoNaviHide="1" documentationCenter="Mobile" title="モバイル分析の使用" authors="mahender" manager="kirillg"/>

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="10/10/2014" ms.author="mahender" />

# モバイル分析の使用 (Capptain)

<div class="dev-center-tutorial-selector sublanding">
<a href="/ja-jp/documentation/articles/mobile-services-ios-get-started-mobile-analytics" title="iOS" class="current">iOS</a>
</div>

このチュートリアルでは、[Capptain][Capptain] を使用して、モバイル分析機能をアプリに追加します。モバイル分析によって、ユーザーがアプリとやり取りしている方法と、アクティビティが最大のセクションを判別できます。これらのデータの取得を開始するには、Capptain SDK を使用してアプリをインストルメントします。

> [AZURE.NOTE] マイクロソフトが所有する Capptain.com は、Azure Mobile Services Standard レベルのお客様を対象として、月間最大 100,000 アクティブ ユーザーにモバイル アプリの分析を無償で提供しています。このサービスを利用するための詳細な手順については、<mobileservices@microsoft.com> にお問い合わせください。以下のチュートリアルでは、Capptain.com 機能の概要と、それらの使用方法に関する手順を説明します。

このチュートリアルでは、次の基本的な手順について説明します。

1.  [Capptain SDK の開始][Capptain SDK の開始]
2.  [UIViewController のオーバーロード][UIViewController のオーバーロード]

このチュートリアルには、次のものが必要です。

-   [Capptain][Capptain] アカウント
-   [Mobile Services Standard レベル][Mobile Services Standard レベル]のアプリ

## <a name="initialize"></a>Capptain SDK の開始

1.  Capptain に登録済みのアプリの **[アプリケーションの詳細]** ページに移動します。[SDK] タブをクリックして、パッケージをダウンロードします。

2.  [XCode] で、プロジェクトを右クリックし、[ファイルの追加] を選択して、Capptain SDK をプロジェクトに追加します。CapptainSDK フォルダーを選択します。

3.  プロジェクトを選択します。**[フェーズの作成]** タブの下で、**[バイナリとライブラリをリンク]** を選択して、次のフレームワークを追加します。

    -   AdSupport.framework - リンクをオプションとして設定します
    -   SystemConfiguration.framework
    -   CoreTelephony.framework
    -   CFNetwork.framework
    -   CoreLocation.framework
    -   libxml2.dylib

    > [AZURE.NOTE] AdSupport フレームワークは削除してもかまいません。Capptain では、IDFA を収集するために、このフレームワークが必要となります。ただし、IDFA コレクションは、この ID に関する Apple ポリシーに準じて無効にできます。

4.  アプリケーション デリゲート実装ファイルで、Capptain エージェントをインポートします。

        #import "CapptainAgent.h"

5.  `applicationDidFinishLaunching:` または `application:didFinishLaunchingWithOptions:` 内で`registerapp:identifiedby:` にアプリ ID と SDK キーを渡して、Capptain エージェントを開始します。

         - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
        {
          [...]
          [CapptainAgent registerApp:@"YOUR_APPID" identifiedBy:@"YOUR_SDK_KEY"];
          [...]
        }

## <a name="instrument"></a>UIViewController のオーバーロード

1.  プロジェクト内で `UIViewController` の子を見つけ、それぞれ代わりに `CapptainViewController` から継承していることを確認します。

        #import <UIKit/UIKit.h>
        #import "CapptainViewController.h"

        // formerly @interface Tab1ViewController : UIViewController<UITextFieldDelegate>
        @interface Tab1ViewController : CapptainViewController<UITextFieldDelegate> {
          UITextField* myTextField1;
          UITextField* myTextField2;
        }

        @property (nonatomic, retain) IBOutlet UITextField* myTextField1;
        @property (nonatomic, retain) IBOutlet UITextField* myTextField2;

2.  プロジェクト内で `UITableViewController` の子を見つけ、それぞれかわりに `CapptainTableViewController` から継承していることを確認します。

    これで、アプリは分析データを Capptain に送信するように構成されました。

## 次のステップ

Capptain がアプリに対して実行できる機能の詳細については、[][Capptain]<http://www.capptain.com></a> を参照してください。

<!-- Anchors. --> <!-- URLs. -->

  [Capptain]: http://www.capptain.com
  [Capptain SDK の開始]: #initialize
  [UIViewController のオーバーロード]: #instrument
  [Mobile Services Standard レベル]: /ja-jp/pricing/details/mobile-services/