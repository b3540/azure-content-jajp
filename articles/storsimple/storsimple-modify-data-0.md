<properties 
   pageTitle="StorSimple デバイスの DATA 0 ネットワーク インターフェイスの設定の変更"
   description="StorSimple デバイスの DATA 0 のネットワーク インターフェイスを再構成する方法について説明します。"
   services="storsimple"
   documentationCenter=""
   authors="alkohli"
   manager="carolz"
   editor="tysonn" />
<tags 
   ms.service="storsimple"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/30/2015"
   ms.author="alkohli" />

# StorSimple デバイスの DATA 0 ネットワーク インターフェイスの設定の変更

## 概要
Microsoft Azure StorSimple デバイスには、DATA 0 ～ DATA 5 の 6 つのネットワーク インターフェイスがあります。DATA 0 インターフェイスは、必ず Windows PowerShell インターフェイスまたはシリアル コンソールを介して構成され、自動的にクラウドに対応します。DATA 0 インターフェイスは、まず、StorSimple デバイスを初めてデプロイするときにセットアップ ウィザードで構成されます。デバイスが操作モードのときは、DATA 0 の設定を再構成することが必要になる場合があります。このチュートリアルでは、StorSimple 用 Windows PowerShell を使って DATA 0 ネットワーク設定を変更する 2 つの方法について説明します。

このチュートリアルを読むと、次のことができるようになります。

- セットアップ ウィザードを使用して DATA 0 ネットワーク設定を変更する
- `Set-HcsNetInterface` コマンドレットを使用して DATA 0 ネットワーク設定を変更する


## セットアップ ウィザードを使用して DATA 0 ネットワーク設定を変更する
DATA 0 ネットワーク設定を再構成するには、StorSimple デバイスの Windows PowerShell インターフェイスに接続し、セットアップ ウィザードのセッションを起動します。DATA 0 の設定を変更するには、次の手順を実行します。

#### セットアップ ウィザードを使用して DATA 0 ネットワーク設定を変更するには

1. シリアル コンソール メニューで、オプション 1 を選択し、**フル アクセスでログイン**します。画面の指示に従って、**デバイス管理者のパスワード**を入力します。既定のパスワードは `Password1` です。

1. コマンド プロンプトに、次のコマンドを入力します。


	`Invoke-HcsSetupWizard`

1. デバイスの DATA 0 インターフェイスの構成に役立つセットアップ ウィザードが表示されます。IP アドレス、ゲートウェイ、およびネットマスクの新しい値を指定します。

> [AZURE.NOTE]固定コントローラーの IP は、Azure 管理ポータルの StorSimple デバイスの [構成] ページで再構成する必要があります。詳細については、[[構成 (デバイス)] ページでネットワーク インターフェイスを変更する](storsimple-modify-device-config.md#modify-network-interfaces)方法に関するセクションを参照してください。


## Set-HcsNetInterface コマンドレットを使用して DATA 0 ネットワーク設定を変更する
DATA 0 ネットワーク インターフェイスを再構成するためのもう 1 つの方法は、`Set-HcsNetInterface` コマンドレットを使用することです。このコマンドレットは、StorSimple デバイスの Windows PowerShell インターフェイスから実行します。DATA 0 の設定を変更するには、次の手順を実行します。

#### Set-HcsNetInterface コマンドレットを使用して DATA 0 ネットワーク設定を変更するには

1. シリアル コンソール メニューで、オプション 1 を選択し、**フル アクセスでログイン**します。画面の指示に従って、**デバイス管理者のパスワード**を入力します。既定のパスワードは `Password1` です。

1. コマンド プロンプトに、次のコマンドを入力します。

	`Set-HCSNetInterface -InterfaceAlias Data0 -IPv4Address <> -IPv4Netmask <> -IPv4Gateway <> -Controller0IPv4Address <> -Controller1IPv4Address <> -IsiScsiEnabled 1 -IsCloudEnabled 1`

	IPv6 アドレスを使用している場合は、次のコマンドを使用します。

	`Set-HCSNetInterface -InterfaceAlias Data0 -IPv6Address <> -IPv6Netmask <> -IPv6Gateway <> -Controller0IPv6Address <> -Controller1IPv6Address <> -IsiScsiEnabled 1 -IsCloudEnabled 1`

1. 次の項目について、DATA 0 の値を山かっこ (< >) で囲んで入力します。
											
	- IPv4/IPv6 アドレス
	
	- IPv4/IPv6 ゲートウェイ
	
	- IPv4/IPv6 サブネット マスク
	
	- コント ローラー 0 の固定 IPv4/IPv6 アドレス

	- コント ローラー 1 の固定 IPv4/IPv6 アドレス

## 次のステップ
ネットワーク インターフェイスを構成するときに問題が発生した場合は、[デプロイに関する問題のトラブルシューティング](storsimple-troubleshoot-deployment.md)のページを参照してください。

<!---HONumber=July15_HO5-->