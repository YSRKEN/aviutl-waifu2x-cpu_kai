# aviutl-waifu2x-cpu_kai
waifu2xの処理を行うことが可能な、AviUtlのフィルタプラグインです。
RGB版なので、色ムラもきれいに取り去ることが可能です。

## インストール
 * aviutl-waifu2x-cpu_kai-***.aufとmodelsフォルダを、aviutl.exeと同じフォルダにあるpluginsフォルダにコピーしてください。
 * aufファイル、およびmodelsフォルダをaviutl.exeと同フォルダに置くと正常に動作しません。
 * SSE・AVX・FMA用にファイルが分かれていますので、自環境に適した奴を選択してください。
 * 動作には[Visual Studio 2013 の Visual C++ 再頒布可能パッケージ](https://www.microsoft.com/ja-jp/download/details.aspx?id=40784)が必要ですが、面倒な人はmsvcp120.dllとmsvcr120.dllとvcomp120.dllをaviutl.exeと同じフォルダにコピーしてください。

## 使用方法
設定画面は次の通り。デフォルトでは1・0・0・32・32・チェックなしにセットされています。
 * threadトラックバー……スレッド数
 * noiseトラックバー……デノイズレベル(0でデノイズしない)
 * scaleトラックバー……拡大するか否か(0だと拡大しない)
 * block_xトラックバー……分割するブロックの横サイズ。画像の横の公約数にしておけばOKかと。
 * block_yトラックバー……分割するブロックの横サイズ。画像の縦の公約数にしておけばOKかと。
 * use blockingチェックボックス……説明は不要でしょう。
なお、設定画面のタイトルバーに演算時間がミリ秒単位で表示されます。
noiseとscaleを0にすれば、フィルタ処理が無効化され最初の表示に戻ります。

## 注意事項
 * 処理開始時にメモリを確保しますが、コケた際にはダイアログで警告が表示されます(処理を行いません)。
 * メモリが足りない旨の警告が出た際は、use blockingにチェックを入れるか、ブロックサイズを小さくしてください。

## コンパイル
Microsoft Visual Studio 2013でコンパイルしました。
AviUtlなので32bitバイナリを作成してください。

## モデルデータについて
toolフォルダ内にjsoncnv.exeがありますが、これで元々のJSONデータを変換しています。
JSONデータの解釈には[picojson.h](https://github.com/kazuho/picojson)を使用しました。
そうして出来たモデルデータ(バイナリ)のフォーマットは次の通りです。(左のパターンがX個並ぶことを{X}と表す)
 * [出力ファイル] ::= [model]{S}
 * [model]    ::= [nInputPlane(I)] [nOutputPlane(O)] [weight] [bias]
 * [weight]   ::= [weight_1]{O}
 * [weight_1] ::= [weight_2]{I}
 * [weight_2] ::= [weight_3]{kH}
 * [weight_3] ::= [weight_4]{kW}
 * [weight_4] ::= [Real Number]
 * [bias] ::= [Real Number]{O}
 * ※変換過程で、ステップ数＝7、kW＝kH＝3だと仮定しています
変換する際は、コマンドライン引数を次のように設定します。
「jsoncnv {変換したいjsonファイル} {出力後のdatファイル}」
なお、jsoncnv.exeのソースコードはjsoncnv.cppです。

## その他
sampleフォルダにはサンプル画像を置いてあります。
……piaproがCC BY-NCで出してる奴だから問題ないよね？

## 更新履歴
Ver.1.0
初版。

## 参考にした資料
 * 「[SSEとAVXで高次元ベクトルの内積計算を高速化してみた](http://daily.belltail.jp/?p=1520)」
 * 「[組み込み関数（intrinsic）によるSIMD入門](http://www.slideshare.net/FukushimaNorishige/simd-10548373)」
 * 「[x86 SIMD Technique:: 水平加算での合計計算](http://www.kaede-software.com/2014/04/post_641.html)」
 * 「[インテル® C++ コンパイラー XE 13.1 ユーザー・リファレンス・ガイド](http://nf.nci.org.au/facilities/software/intel-ct/13.5.192/Documentation/ja_JP/compiler_c/main_cls/index.htm)」
 * 「[STLのコンテナで自作のallocatorを使う](http://d.hatena.ne.jp/sorayukinoyume/20121017/1350473588)」
