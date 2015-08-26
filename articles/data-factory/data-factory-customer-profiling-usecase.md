<properties 
	pageTitle="使用事例 - 顧客プロファイル" 
	description="Azure Data Factory を使用して、データ駆動型ワークフロー (パイプライン) を作成し、顧客のゲーム会社をプロファイリングする方法について説明します。" 
	services="data-factory" 
	documentationCenter="" 
	authors="spelluru" 
	manager="jhubbard" 
	editor="monicar"/>

<tags 
	ms.service="data-factory" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/26/2015" 
	ms.author="spelluru"/>

# 使用事例 - 顧客プロファイル

Azure Data Factory は、ソリューション アクセラレータの Cortana Analytics Suite の実装に使用されている数多くあるサービスの 1 つです。Cortana Analytics の詳細については、[Cortana Analytics Suite](http://www.microsoft.com/cortanaanalytics) を参照してください。このドキュメントでは、Azure Data Factory が一般的な分析の問題を解決する方法を理解するのに役立つ簡単な使用事例を説明します。

この簡単な使用事例にアクセスして試すのに必要なものは、[Azure サブスクリプション](https://azure.microsoft.com/pricing/free-trial/)だけです。「[サンプル](data-factory-samples.md)」記事で説明されている手順に従って、この使用事例を実装しているサンプルをデプロイできます。

## シナリオ

Contoso は、ゲーム機、携帯機器、パーソナル コンピューター (PC) など、複数のプラットフォーム向けにゲームを製作するゲーム会社です。ユーザーがこれらのゲームをプレイすると、使用パターン、ゲームのスタイル、およびユーザーの基本設定を追跡する大量のログ データが生成されます。人口統計、地域、製品データと組み合わせて、Contoso は分析を実行し、各ユーザーのエクスペリエンスを拡張する方法をガイドし、ユーザーをアップグレードおよびゲーム内購入のターゲットにできます。

Contoso の目標は、ユーザーのゲーム履歴プロファイルに基づいてアップセルおよびクロスセルの機会を識別し、ビジネスの成長を促進して、より優れたエクスペリエンスを顧客に提供する新しい魅力的な機能を開発することです。この使用事例では、ビジネスの一例として、ユーザーの行動に合わせて最適化したいゲーム会社を取り上げていますが、ここで説明する原則は、商品やサービスに対する顧客の関心を引き、顧客のエクスペリエンスを強化したいと考えているすべての企業に適用できます。

## 課題

ゲーム会社がこのような使用事例を実装するときに直面する課題は数多くあります。第 1 に、オンプレミスとクラウドに保存されている複数のデータ ソースから、さまざまな規模と形のデータを取り込み、製品データ、ユーザーの行動履歴データ、ユーザーが複数のプラットフォームでゲームをプレイしたときのユーザー データをキャプチャする必要があります。第 2 に、ゲームの使用パターンを適切かつ正確に計算する必要があります。第 3 に、ゲーム会社は、全体的なアップセルとクロスセルについてプロファイルからゲーム内購入の成功を追跡して、アプローチの効果を測定し、調整して今後のマーケティング キャンペーンに生かす必要があります。

## ソリューションの概要

この単純な使用事例は、Azure Data Factory を使用してデータを取得、準備、変換、分析、および発行する方法の例として使用できます。

![エンド ツー エンド ワークフロー](./media/data-factory-customer-profiling-usecase/EndToEndWorkflow.png)上の図は、デプロイ後にデータ パイプラインが Azure ポータル UI にどのように表示されるかを示しています。

1.	**PartitionGameLogsPipeline** は、未処理のゲーム イベントを BLOB ストレージから読み取り、年、月、日に基づくパーティションを作成します。
2.	**EnrichGameLogsPipeline** は、パーティション分割されたゲーム イベントを geo コードの参照データと結合し、IP アドレスを対応する地理的場所にマップすることによってデータを強化します。
3.	**AnalyzeMarketingCampaignPipeline** パイプラインは、強化されたデータを利用し、そのデータを広告データと共に処理して、マーケティング キャンペーンの有効性を含む最終的な出力を作成します。

この使用事例では、Azure Data Factory を使用して、入力データをコピーし、HDInsight のアクティビティ (Hive および Pig 変換) でデータを変換および処理し、Azure SQL Database に最終データを出力する、アクティビティを調整しています。また、UI を使用して、データ パイプラインのネットワークを視覚化し、管理し、ステータスを監視できます。

## メリット

ゲーム会社は、ユーザー プロファイルの分析を最適化してビジネス目標と一致させることで、迅速に使用状況パターンを収集し、さまざまなゲーム製品のマーケティング キャンペーンの効果を分析できます。

<!---HONumber=August15_HO6-->