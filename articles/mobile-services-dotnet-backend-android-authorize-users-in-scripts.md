<properties pageTitle="サービス側の承認 (Android) | モバイル デベロッパー センター" metaKeywords="" description="Azure Mobile Services の .NET バックエンドでユーザーを承認する方法について説明します。" metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="モバイル サービス ユーザーのサービス側の承認" authors="glenga" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-android" ms.devlang="java" ms.topic="article" ms.date="10/20/2014" ms.author="glenga" />

# モバイル サービス ユーザーのサービス側の承認

[WACOM.INCLUDE [mobile-services-selector-service-auth-users](../includes/mobile-services-selector-service-auth-users.md)]

このトピックでは、認証済みのユーザーを承認し、Android アプリから Azure Mobile Services のデータにアクセス可能にする方法について説明します。このチュートリアルでは、コントローラー内にあるデータ アクセス メソッドに、認証済みのユーザーの ID に基づいてクエリにフィルター処理を実施するコードを追加します。これによって、それぞれのユーザーが自分のデータのみを閲覧できる状態を実現できます。

このチュートリアルは、モバイル サービスのクイック スタートと、1 つ前の[認証の使用][認証の使用]に関するチュートリアルの内容を前提としています。このため、このチュートリアルの前に、[認証の使用][認証の使用]に関するチュートリアルを完了している必要があります。

## <a name="register-scripts"></a>データ アクセス メソッドの変更

[WACOM.INCLUDE [mobile-services-filter-user-results-dotnet-backend](../includes/mobile-services-filter-user-results-dotnet-backend.md)]

## アプリケーションをテストする

1.  Eclipse で、チュートリアル「[認証の使用][認証の使用]」を実行したときに変更したプロジェクトを開きます。

2.  **[Run]** メニューの **[Run]** をクリックして、Android エミュレーター内でプロジェクトを開始します。

    このとき、前のチュートリアルで TodoItem テーブルに項目を挿入していても、項目が返されることはない点に注意してください。このようなことが起こるのは、その項目がユーザー ID 列のない状態で挿入されており、ユーザー ID の値が null になっているためです。

3.  そのアプリで、**[Add a ToDo item]** にテキストを入力し、**[Save]** をクリックします。

    ![][0]

    この操作によって、モバイル サービスの TodoItem テーブルにテキストおよびユーザー ID が挿入されます。新しい項目に正しいユーザー ID が設定されたため、モバイル サービスでその項目が返され、2 番目の列に表示されるようになります。

4.  [管理ポータル] [Azure の管理ポータル] の **todoitem** テーブルに戻り、**[参照]** をクリックして、新しく追加された項目に対してユーザー ID の値が設定されているかどうかを確認します。

5.  (省略可能) ログイン アカウントが他にある場合には、ユーザーがそれぞれ自分のデータのみを閲覧できる状態になっていることを確認できます。これにはまず、アプリケーションを終了して再度実行します。ログイン資格情報の入力を求めるダイアログが表示されたら別のログインを入力し、前のアカウントで入力した項目が表示されないことを確認してください。

## 次のステップ

これで、認証の基本について説明するチュートリアルは終了です。次のモバイル サービスのトピックの詳細を確認することをお勧めします。

-   [データの使用][データの使用]
    Mobile Services を使用してデータの格納およびクエリを実行する方法について説明します。

-   [プッシュ通知の使用][プッシュ通知の使用]
    アプリケーションにごく基本的なプッシュ通知を送信する方法について説明します。

-   [Mobile Services Android の使用方法の概念リファレンス][Mobile Services Android の使用方法の概念リファレンス]
    Android で Mobile Services を使用する方法について説明します。

<!-- Anchors. --> <!-- Images. --> <!-- URLs. -->

  [認証の使用]: /ja-jp/documentation/articles/mobile-services-dotnet-backend-android-get-started-users
  [0]: ./media/mobile-services-dotnet-backend-android-authorize-users-in-scripts/mobile-quickstart-startup-android.png
  [データの使用]: /ja-jp/documentation/articles/mobile-services-dotnet-backend-android-get-started-data
  [プッシュ通知の使用]: /ja-jp/documentation/articles/mobile-services-dotnet-backend-android-get-started-push
  [Mobile Services Android の使用方法の概念リファレンス]: /ja-jp/documentation/articles/mobile-services-android-how-to-use-client-library/