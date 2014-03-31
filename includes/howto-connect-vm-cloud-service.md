<properties writer="kathydav" editor="tysonn" manager="jeffreyg" /> 


#クラウド サービス内の仮想マシンを相互に接続する方法



仮想マシンを作成すると、そのマシンを含めるクラウド サービスが自動的に作成されます。同じクラウド サービス内に複数の仮想マシンを作成すると、仮想マシン間の相互通信、仮想マシン間での負荷分散、仮想マシンの高可用性を有効にできます。

仮想マシンの負荷分散の詳細については、「[仮想マシンの負荷分散][Load balancing virtual machines]」を参照してください。アプリケーションの可用性管理の詳細については、「[仮想マシンの可用性管理][Manage the availability of virtual machines]」を参照してください。


まず、新しいクラウド サービスで仮想マシンを作成する必要があります。その後、そのクラウド サービスの最初の仮想マシンに追加の仮想マシンを接続できます。



1. 「Create a virtual machine using the steps in [カスタム仮想マシンを作成する方法][How to create a custom virtual machine]」に示された手順に従って仮想マシンを作成します。


2. 最初のカスタム仮想マシンを作成したら、[管理ポータル](http://manage.windowsazure.com) コマンド バーで、**[新規]** をクリックします。


	![新しい仮想マシンの作成](./media/howto-connect-vm-cloud-service/Create.png)

3. **[仮想マシン]**、**[ギャラリーから]** の順にクリックします。

	
	![カスタム仮想マシンの作成](./media/howto-connect-vm-cloud-service/CreateNew.png)

	**[仮想マシンのオペレーティング システムの選択]** ダイアログ ボックスが表示されます。


4. **[イメージの選択]** ページからイメージを選択し、矢印をクリックして続行します。


	最初に **[仮想マシンの構成]** ページが表示されます。


5. **[仮想マシン名]** ボックスに、仮想マシンに使用する名前を入力します。

6. **[サイズ]** で、仮想マシンに使用するサイズを選択します。選択するサイズは、アプリケーションが必要とするコア数に応じて変わります。

7. **[新しいユーザー名]** に、サーバーの管理に使用する管理アカウントの名前を入力します。


8. **[新しいパスワード]** に、管理アカウントの強力なパスワードを入力します。**[パスワードの確認]** に、パスワードを再度入力します。


9. Linux オペレーティング システムが実行されている仮想マシンについては、SSH キーでマシンを保護するように選択できます。


10. **[クラウド サービス]** で、新しい仮想マシンを含めるクラウド サービスを選択します。

11. **[リージョン/アフィニティ グループ/仮想ネットワーク]** で、仮想マシンを含めるリージョンを選択します。

12. **[ストレージ アカウント]** で、.vhd ファイルを保存するストレージ アカウントを選択します。または、フィールドの値を既定のままにして、ストレージ アカウントを自動的に作成することもできます。自動的に作成されるストレージ アカウントは 1 つだけです。この設定で作成する他のすべての仮想マシンがこのストレージ アカウントに配置されます。ストレージ アカウントは 20 個に制限されています。


13. 可用性セットを使用するには、最初の仮想マシンを作成したときに作成された可用性セットを選択します。

14. 既定のエンドポイントの構成を確認し、必要に応じて変更します。

15. チェック マークをクリックして、接続された仮想マシンを作成します。


[How to create a custom virtual machine]: http://windowsazure.com/ja-jp/documentation/articles/virtual-machines-create-custom/
[Load balancing virtual machines]: http://windowsazure.com/ja-jp/documentation/articles/load-balance-virtual-machines/
[Manage the availability of virtual machines]: http://windowsazure.com/ja-jp/documentation/articles/virtual-machines-manage-availability/
