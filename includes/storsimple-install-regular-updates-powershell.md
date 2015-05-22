<properties
   pageTitle="StorSimple 用 Windows PowerShell を使用した通常の更新プログラムのインストール"
   description="StorSimple 更新機能と StorSimple 用 Windows PowerShell を使用して定期的に更新プログラムをインストールする方法について説明します。"
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
   ms.date="04/27/2015"
   ms.author="v-sharos" />

### StorSimple 用 Windows PowerShell を使用して通常の更新プログラムをインストールするには

1. デバイスのシリアル コンソールを開き、オプション 1 の **\[フル アクセスによるログイン\]** を選択します。パスワードを入力します。既定のパスワードは *Password1* です。 

2. コマンド プロンプトに、次のコマンドを入力します。

    **Get-HcsUpdateAvailability**

    更新プログラムが利用可能かどうかと、更新プログラムが中断を伴うものであるかどうかが通知されます。

3. コマンド プロンプトに、次のコマンドを入力します。

    **Start-HcsUpdate**

    更新プロセスが開始します。

> [AZURE.IMPORTANT]
>
> - このコマンドは通常の更新プログラムのみに適用されます。このコマンドは 1 つのコントローラーでのみ実行しますが、両方のコントローラーが更新されます。 
> - 更新プロセス中にコントローラーのフェールオーバーが行われても、システムの可用性または処理には影響しません。

<!--HONumber=52-->