﻿データは、S&P 500 企業それぞれの記事に基づいて Wikipedia (<a href="http://www.wikipedia.org/">http://www.wikipedia.org/</a>) から取得され、XML 形式で格納されています。<p>Azure Machine Learning Studio へのアップロードの前に、データセットを次のように処理します。<ul><li>特定の企業のテキスト コンテンツを抽出します。</li><li>Wiki の書式設定を削除します。</li><li>英数字以外の文字を削除します。</li><li>すべてのテキストを小文字に変換します。</li><li>既知の会社のカテゴリを追加します。</li></ul><p>いくつかの企業の記事が見つからないため、レコード数は 500 未満であることに注意してください。

<!--HONumber=35_1-->