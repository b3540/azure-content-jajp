<properties linkid="manage-linux-howto-logon-linux-vm" urlDisplayName="VM へのログオン" pageTitle="Azure 上で Linux を実行する仮想マシンへのログオン" metaKeywords="Azure Linux vm, Linux SSH" description="Linux を実行する Azure の仮想マシンに Secure Shell (SSH) クライアントを使用してログオンする方法について説明します。" metaCanonical="" services="virtual-machines" documentationCenter="" title="Linux を実行する仮想マシンにログオンする方法" authors=""  solutions="" writer="" manager="" editor=""  />




#Linux を実行する仮想マシンにログオンする方法#

Linux オペレーティング システムを実行する仮想マシンへのログオンには、Secure Shell (SSH) クライアントを使用します。

仮想マシンにログオンするときに使用するコンピューターには、SSH クライアントをインストールする必要があります。SSH クライアント プログラムの選択肢は多数あります。たとえば、次のプログラムを選択できます。

- Windows オペレーティング システムが実行されているコンピューターを使用している場合は、PuTTY などの SSH クライアントを使用できます。詳細については、[PuTTY Download Page (PuTTY のダウンロード ページ)](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html) を参照してください。
- Linux オペレーティング システムが実行されているコンピューターを使用している場合は、OpenSSH などの SSH クライアントを使用できます。詳細については、「[OpenSSH (英語)](http://www.openssh.org/)」を参照してください。

ここでは、PuTTY プログラムを使用して仮想マシンにアクセスする手順を示します。

1. [管理ポータル](http://manage.windowsazure.com)で**ホスト名**と**ポート情報**を検索します。仮想マシンのダッシュボードから必要な情報を見つけることができます。仮想マシン名をクリックし、ダッシュボードの **[概要]** セクションで **[SSH の詳細]** を探します。

	![SSH の詳細の取得](./media/virtual-machines-linux-how-to-log-on/sshdetails.png)

2. PuTTY プログラムを開きます。

3. ダッシュボードから収集したホスト名とポート情報を入力し、**[開く]** をクリックします。

	![PuTTY を開く](./media/virtual-machines-linux-how-to-log-on/putty.png)

4. マシンの作成時に指定したアカウントを使用して仮想マシンにログオンします。

	![仮想マシンへのログオン](./media/virtual-machines-linux-how-to-log-on/sshlogin.png)

	これで、仮想マシンを他のサーバーとまったく同様に扱うことができます。
