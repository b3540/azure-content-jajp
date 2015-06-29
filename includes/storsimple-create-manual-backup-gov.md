<properties pageTitle="手動でバックアップを作成する" description="オンデマンドのバックアップ ジョブを手動で開始する方法について説明します。" services="storsimple" documentationCenter="NA" authors="SharS" manager="adinah" edito**r="tysonn" /> <tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="04/01/2015"
   ms.author="v-sharos" />

#### 手動でバックアップを作成するには

1. **[デバイス]** ページで、**[バックアップ ポリシー]** タブに移動します。このタブには、バックアップするボリュームのポリシーを含むすべてのバックアップ ポリシーが表形式で表示されています。

2. 対応する行の任意の場所 (最初の列を除く) をクリックして、ポリシーを選択します。ページの下部にある **[バックアップの取得]** をクリックします。ボタンが展開され、バックアップ オプションの [ローカル スナップショット] と [クラウド スナップショット] が表示されます。

3. これらのオプションのいずれかを選択すると、確認を求めるメッセージが表示されます。**[はい]** をクリックします。

    ![手動バックアップの作成 1](./media/storsimple-create-manual-backup-gov/HCS_CreateManualBackup1-gov-include.png)
 
    これでスナップショットを作成するジョブが開始されます。ジョブが正常に作成されると、ページの下部に通知が表示されます。

4. ジョブを監視するには、(ページの下部にある) 通知領域の **[ジョブの表示]** をクリックします。

    ![手動バックアップの作成 2](./media/storsimple-create-manual-backup-gov/HCS_CreateManualBackup2-gov-include.png)

5. バックアップ ジョブが完了したら、**[バックアップ カタログ]** タブに移動します。

6. フィルター選択項目を適切なデバイス、バックアップ ポリシー、および時間範囲に設定します。フィルターを設定したら、チェック マーク アイコン ![チェック マーク アイコン](./media/storsimple-create-manual-backup/HCS_CheckIcon-include.png) をクリックします。

  カタログに表示されているバックアップ セットの一覧に、そのバックアップが表示されます。

<!---HONumber=58_postMigration-->