<properties
   pageTitle="メンテナンス モードを開始する"
   description="StorSimple デバイスをメンテナンス モードにする方法について説明します。"
   services="storsimple"
   documentationCenter="NA"
   authors="SharS"
   manager="adinah"
   editor="tysonn" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="04/21/2015"
   ms.author="v-sharos" />

### メンテナンス モードを開始するには

1. シリアル コンソール メニューで、オプション 1 を選択し、**フル アクセスでログイン**します。

2. パスワードを入力します。既定のパスワードは **Password1** です。

3. コマンド プロンプトに、次のコマンドを入力します。

    **Enter-HcsMaintenanceMode**

4. メンテナンス モードにすると、すべての I/O 要求が中断され管理ポータルへの接続が切断されるという警告メッセージが表示され、確認が求められます。メンテナンス モードを開始するには、「**Y**」と入力します。

    両方のコントローラーが再起動します。再起動が完了すると、デバイスがメンテナンス モードであることを示すメッセージが表示されます。

<!--HONumber=52-->