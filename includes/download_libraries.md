#Windows Azure SDK for Java のダウンロード

##Windows Azure Client Libraries for Java - 手動ダウンロード

Windows Azure Libraries for Java は、[Apache License Version 2.0][license] に準拠して配布されます。ライブラリの ZIP ファイルおよびすべての依存関係については、[ここ][zip-download]をクリックしてください。これは Microsoft Open Technologies, Inc. によって公開されています。ライセンスおよびその他の情報については、ZIP 内の license.txt と ThirdPartyNotices.txt ファイルを参照してください。

##Windows Azure Libraries for Java - Maven

プロジェクトが既に Maven を使用してビルドする設定になっている場合には、pom.xml ファイルに次の依存関係を追加します。

	<dependency>
    	<groupId>com.microsoft.windowsazure</groupId>
    	<artifactId>microsoft-windowsazure-api</artifactId>
    	<version>n.n.n</version>
	</dependency>

<version> 要素内の *n.n.n* を有効なバージョン番号に置き換えてください。このバージョン番号は、[Maven の Windows Azure ライブラリ リポジトリ](http://go.microsoft.com/fwlink/?LinkID=286274)から入手できます。

[license]: http://www.apache.org/licenses/LICENSE-2.0.html
[zip-download]:  http://go.microsoft.com/fwlink/?LinkId=253887
