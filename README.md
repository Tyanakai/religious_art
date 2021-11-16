# ProbSpace　<br>宗教画テーマの分類
このレポジトリは、ProbSpace上で開催された[宗教画テーマの分類](https://comp.probspace.com/competitions/religious_art)で使用したipynbファイルを保管するものです。<br>

## コンペティションの概要
上述のコンペティションのタスクは、画像を入力として、その画像が所属するクラス(13種類)を予測する多クラス単一ラベル問題です。
評価はaccuracy(正解数/全サンプル数)によって行われます。

<p>データ数は、訓練データが654枚、テストデータが497枚です。<br>各画像のサイズは224×224で、すべてRGBの画像データです。</p>
<h3 id="-">データラベル</h3>
<p>「訓練データラベル」のラベル毎のテーマは下記の通りです。</p>
<table class="table table-hover"><thead><tr>
<th>label</th>
<th>テーマ</th>
<th>絵画の詳細(一例)</th>
</tr>
</thead><tbody><tr>
<td>0</td>
<td>受胎告知</td>
<td>処女マリアがキリストを妊娠したことを告げられる</td>
</tr>
<tr>
<td>1</td>
<td>キリストの降誕</td>
<td>イエス・キリストが誕生する</td>
</tr>
<tr>
<td>2</td>
<td>聖家族</td>
<td>幼少年時代のイエス・キリスト、養父ヨセフ、聖母マリア</td>
</tr>
<tr>
<td>3</td>
<td>羊飼いの礼拝</td>
<td>イエス・キリストを目にするために羊飼いが訪れる</td>
</tr>
<tr>
<td>4</td>
<td>東方三博士の礼拝</td>
<td>マリアとイエス・キリストを拝み、乳香、没役、黄金を捧げる</td>
</tr>
<tr>
<td>5</td>
<td>エジプトへの逃避</td>
<td>養父ヨセフは、ヘロデ王のたくらみを避けて、マリアとともにエジプトへと逃れる</td>
</tr>
<tr>
<td>6</td>
<td>イエスの洗礼</td>
<td>ヨルダン川で洗礼者ヨハネからイエスは洗礼を受ける</td>
</tr>
<tr>
<td>7</td>
<td>最後の晩餐</td>
<td>イエスが処刑される前夜、十二使徒と共に食事をしている。使徒の一人が裏切者であることが告げられる</td>
</tr>
<tr>
<td>8</td>
<td>茨の冠</td>
<td>笞打ちの刑となったイエスが、兵士から茨でできた冠をかぶせられあざ笑われる</td>
</tr>
<tr>
<td>9</td>
<td>キリストの磔刑</td>
<td>イエスは十字架に磔となって処刑される</td>
</tr>
<tr>
<td>10</td>
<td>キリストの嘆き</td>
<td>キリストの体が十字架から剥がされ、その体をイエスの友人たちが悼む</td>
</tr>
<tr>
<td>11</td>
<td>ラザロの復活</td>
<td>ラザロの墓の前で、イエスが祈った直後、イエスの友人であるラザロが蘇生する</td>
</tr>
<tr>
<td>12</td>
<td>最後の審判</td>
<td>再臨したイエスが死者に裁きを下し、天国と地獄へと振り分けられる</td>
</tr></tbody></table>
<br>

## ipynbの概要
1. [religious_art_eda.ipynb](religious_art_eda.ipynb) : データを分析し、重複がないか調べるためクラスタ分析した画像を保存します。
2. [religious_art_train.ipynb](religious_art_train.ipynb) : モデルを訓練し、保存します。3.が完了している場合、擬似ラベルを使った訓練も選択できます。
3. [religious_art_infere.ipynb](religious_art_infere.ipynb) : 2.で保存したモデルを用いて、out of fold 訓練データとテストデータに対して予測を行います。<br>
また、アンサンブル手法、擬似ラベル作成も行います。

## 実行環境
Google colabratoryでの実行を想定しています。

## 試行錯誤
### RandAugment
データ拡張法として、[RandAugment](https://arxiv.org/abs/1909.13719)を実装、試行しました。<br>
実装に当っては、[こちらの記事](https://qiita.com/kitfactory/items/d89457eeab5c185880be)を一部参考にさせて頂きました。

### image clustering
画像を概観したところ重複画像が存在したので、下記の方法を用いて、重複画像のあぶり出しを試みました。<br>
1. 全画像をEfficientNetB0に入力
2. 出力された特徴ベクトルをk-means法でクラスタリング
3. クラスタごとにフォルダを分けて画像を保存後、フォルダ毎に目視で調査

結果18組の重複画像を発見しました。詳細は religious_art_eda.ipynbをご参照ください。

### model
使用するモデルとして、EfficientNetB0-B7, EfficientNetV2-S M, seresnext101, nasnetlarge, vitを試しました。<br>
最終的に評価指標の良かった、EfficientNetB4 ,EfficientNetB7, EfficientNetV2-Sを提出ファイル作成に使用しました。

## スコア
|項目|説明|
|----|----|
|model|使用した事前学習モデル|
|public score|リーダーボードpublic scoreのaccuracy|
|private score|リーダーボードprivate scoreのaccuracy|
|cv val loss|交差検証でのlossの平均値|
|cv val acc|交差検証でのaccuracyの平均値|
|n ops|RandAugmentにおいて一度の拡張で適用する手法の数|
|magnitude|RandAugmentにおいて一度の拡張で適用する手法の度合|
|pseudo|擬似ラベルデータを使用して訓練したか否か|

|model|public score|private score|cv val loss|cv val acc|n ops|magnitude|pseudo|
|----|----|----|----|----|----|----|----|
|b4|0.719|0.658|1.1238|0.6453|10|5||
|b4|0.719|0.647|1.1407|0.6343|10|3||
|b4|0.703|0.640|1.146|0.6437|5|5||
|b4|0.688|0.684|1.016|0.701|10|5|O|
|b7|0.669|0.674|1.1317|0.6484|10|7||
|b7|0.578|0.665|1.070|0.7203|10|5|O|
|b7|0.672|0.665|1.005|0.703|10|5|O|
|v2-S|0.656|0.654|1.007|0.6953|10|5|O|
|v2-M|0.609|0.644|1.155|0.6453|10|5||
|vit-b16|||1.761|0.5781|10|5||
|seresnext101|||1.3993|0.5468|10|5||
|ensemble b4 b7 v2-s|0.703|0.684|1.08|0.6667||||
|ensemble b4 b7 v2-s|0.672|0.679|1.023|0.6965|||O|

## 最終リーダーボード順位
Private LB 7位/116チーム投稿(上位6%)で金トロフィーを獲得しました。

![top](https://user-images.githubusercontent.com/81244428/141953682-51572b24-59ed-489e-8332-447253c1a46f.PNG)

![rank](https://user-images.githubusercontent.com/81244428/141954132-225aa0b1-3877-4914-96ee-c75f268a9051.PNG)









