・もげRPGさーばーGUIとは

http://mogerpg.pcgw.pgw.jp/
でプレイできるゲームを、標準入出力のやり取りだけで
攻略するAIを作るゲームです。

起動にはtcl,tkが必要です。
ubuntuなら sudo apt install tcl tk でインストールできます。
動作確認済み　ubuntuのみ

・ダウンロード
https://github.com/fusuya/mogeRPGserverGUI/releases

・GUI操作説明
AI起動コマンド入力フォームにAI起動コマンドを入力します。
(ai.txtに予め起動コマンドを書いているとデフォルトで設定されます)
起動コマンドを入力したあとにAI読み込みボタンを押します。
AIが正常に読み込まれるとAIの名前が表示されます。

スタートボタン　　：読み込んだAIでゲームを開始します。
ストップボタン　　：マップ移動中のみ一時停止します。再開はスタートボタン
再挑戦ボタン　　　：現在のAIで最初からゲームを始めます。
初期化ボタン　　　：ゲームデータとAIをリセットします。
各種ディレイボタン：表示ディレイの変更、ゲーム中でも変更できます。

・終了の仕方
☒ボタンを押すか、Alt+qで終わります。

・勝敗の決定
100階のボスを倒したら勝利です。

・AIの作り方

AIプログラムとサーバは標準入出力でやりとりをします。
AIプログラムは最初に名前を出力します。
以降はJSONデータを受け取り、アクションの出力を繰り返します。


・AIに送られてくるJSONデータ

・マップ移動モード

地下1階  Lv 1, NIL : HP 30/30 力 30/30 素早さ 30/30 

ロロロロロロロロロロロ 武器[i]   なし
ロ　　　　　　　　宝ロ 回復薬    2個
ロ　ロロロロロ　ロロロ ハンマー  5個
ロ　　　　宝ロ　　　ロ Exp       0/100
ロ　ロロロロロ　ロ　ロ
ロ宝ロ主　　ロ宝ロ　ロ 薬を使う[q]
ロロロロロ　ロロロ　ロ 終わる[r]
ロ　　　　　ロ　　　ロ
ロ　ロロロロロ　ロ　ロ
ロ　　　　　　　ロ下ロ
ロロロロロロロロロロロ

上記のデータをJSON化すると以下になります。
{"map":1,"player":{"hp":30,"maxhp":30,"str":30,"maxstr":30,"agi":30,"maxagi":30,"level":1,"exp":0,"heal":2,"hammer":5,"map-level":1,"buki":["なし",0,0,0],"pos":{"x":3,"y":5}},"blocks":[[8,9],[8,8],[6,8],[5,8],[4,8],[3,8],[2,8],[6,7],[8,6],[7,6],[6,6],[4,6],[3,6],[2,6],[1,6],[8,5],[6,5],[2,5],[8,4],[6,4],[5,4],[4,4],[3,4],[2,4],[6,3],[9,2],[8,2],[6,2],[5,2],[4,2],[3,2],[2,2]],"walls":[[10,10],[9,10],[8,10],[7,10],[6,10],[5,10],[4,10],[3,10],[2,10],[1,10],[0,10],[10,9],[0,9],[10,8],[0,8],[10,7],[0,7],[10,6],[0,6],[10,5],[0,5],[10,4],[0,4],[10,3],[0,3],[10,2],[0,2],[10,1],[0,1],[10,0],[9,0],[8,0],[7,0],[6,0],[5,0],[4,0],[3,0],[2,0],[1,0],[0,0]],"items":[[7,5],[1,5],[5,3],[9,1]],"boss":[],"kaidan":[[9,9]],"events":[],"ha2":[]}

walls  壊せない壁
blocks ハンマーを持ってると壊せる壁
items  宝箱の座標
boss   ボスの座標
ha2    中ボスの座標
kaidan 階段の座標
events イベントの座標
要素はそれぞれの位置の(x座標 y座標)です。

playerについて
{"hp":30,"maxhp":30,"str":30,"maxstr":30,"agi":30,"maxagi":30,"level":1,"exp":0,"heal":2,"hammer":5,"map-level":1,"buki":["なし",0,0,0],"pos":{"x":1,"y":1}}

hp        現在のHP
maxhp     最大HP
str       現在のstr(攻撃力)
maxstr    最大str
agi       現在のagi(素早さ)
maxagi    最大agi
level     レヴェル
exp       取得経験値
pos       プレイヤー位置
heal      回復薬の数
hammer    ハンマーの数
map-level 現在いる階
buki      装備してる武器("名前" 攻撃力　HP 素早さ)

・バトルモード時に送られるJSONデータ
{"battle":1,"monsters":[{"name":"スライム","number":4,"level":1,"hp":8},{"name":"スライム","number":3,"level":6,"hp":7},{"name":"オーク","number":2,"level":5,"hp":10},{"name":"ヒドラ","number":1,"level":6,"hp":6},{"name":"ヒドラ","number":0,"level":2,"hp":2}],"player":{"hp":30,"maxhp":30,"str":30,"maxstr":30,"agi":30,"maxagi":30,"level":1,"exp":0,"heal":2,"hammer":5,"map-level":1,"buki":["なし",0,0,0],"pos":{"x":0,"y":0}}}

monstersについて
:[{"name":"スライム","number":4,"level":1,"hp":8},{"name":"スライム","number":3,"level":6,"hp":7},{"name":"オーク","number":2,"level":5,"hp":10},{"name":"ヒドラ","number":1,"level":6,"hp":6},{"name":"ヒドラ","number":0,"level":2,"hp":2}]

name   名前
number モンスター番号 (敵を指定するときに使う)
level  モンスターのレベル
hp     モンスターのHP


・バトル時の敵の行動ターン時
{"damage-info":1,"monsters":[{"name":"ヒドラ","number":5,"level":2,"hp":2,"damage":["hp",1]},{"name":"メタルヨテイチ","number":4,"level":10,"hp":3,"damage":["no"]},{"name":"ヒドラ","number":3,"level":1,"hp":1,"damage":["hp",1]},{"name":"ヒドラ","number":2,"level":5,"hp":5,"damage":["hp",2]},{"name":"ヒドラ","number":1,"level":4,"hp":4,"damage":["hp",2]},{"name":"ブリガンド","number":0,"level":2,"hp":11,"damage":["hp",2]}],"player":{"hp":22,"maxhp":30,"str":30,"maxstr":30,"agi":30,"maxagi":30,"level":1,"exp":0,"heal":2,"hammer":5,"map-level":1,"buki":["なし",0,0,0],"pos":{"x":0,"y":0}}}

上のバトル時のデータをほぼ同じですがmonstersの中にdamageという項目が増えてます。
"damage":["hp",1]  プレイヤーのHPに1ダメージ与えた
"damage":["agi",1] プレイヤーのAGIに1ダメージ与えた
"damage":["str",1] プレイヤーのSTRに1ダメージ与えた
"damage":["no"]    なにもしなかった
"damage":["heal",5]  敵がHP5回復した。
"damage":["all",1]   プレイヤーのすべてのステータスに1ダメージ与えた

※このデータが送られてきた場合は、AIはなにも出力しなくていいです※


・装備モード時に送られるJSONデータ
{"equip":1,"now":{"name":"なし","str":0,"hp":0,"agi":0},"discover":{"name":"もげぞーの剣","str":1,"hp":0,"agi":0}}

now       現在装備してる武器
desicover 発見した武器
str       攻撃力
hp        HP
agi       agi


・レベルアップモード時に送られるJSONデータ
{"levelup":1,"player":{"hp":30,"maxhp":30,"str":30,"maxstr":30,"agi":30,"maxagi":30,"level":1,"exp":0,"heal":2,"hammer":5,"map-level":1,"buki":["なし",0,0,0],"pos":{"x":0,"y":0}}}



・入出力データの詳細

4つのモードがありそれぞれAIが主力するアクションは異なります。

1,
マップ移動モードのときのAIが出力するアクションは "移動方向" or "HEAL" という文字列です。
移動方向は "UP" "DOWN" "RIGHT" "LEFT"のいずれかです。
サンプル
println "UP"; //上に移動
println "HEAL"; // 回復薬を使う

2,
バトルモードのときのAIが出力するアクションは
"STAB n"     敵を一体指定して強めの攻撃(ランダム)。
"DOUBLE n n" 敵を２体指定してしょぼめの攻撃(ランダム)
"SWING"      敵指定なし、ランダム回数、ランダムな敵に１ダメージ与える。
"HEAL"       回復
のいずれかです
(nはモンスター番号)
(STAB,DOUBLEには半角スペース大事)

2.5,
敵の攻撃時にjsonデータ(damage-info)が送られてきた場合は
AIはなにも出力しなくていいです。

3,
装備モードのときのAIが出力するアクションは
"YES" 装備する
"NO"  装備しない(捨てる)
のいずれかです

4,
レベルアップもーどのときにAIが出力するアクションは
"HP"   体力、0になったらゲームオーバー
"STR"  攻撃力、敵に与えるダメージに影響
"AGI"  素早さ、1ターンに攻撃できる回数に影響(60~74で5回行動、45~59で4回行動、30~44で3回行動、15~29で2回行動、14以下で1回行動)
のいずれかです。
増やしたいステータスを出力してください


・エラーメッセージ

1 AIプログラムを起動できない場合
2 AIが名前として空行を送って来た場合
3 AIの予期せぬ終了などでパイプへの書き込み、読み込みができない場合

1,
Couldn't execute "hoge": そのようなファイルやディレクトリはありません

2,
AIの名前が空です。
AIから名前を受け取ることができませんでした。

2,
end of file on #<TWO-WAY-STREAM
                 :INPUT-STREAM #<SB-SYS:FD-STREAM for "descriptor 6" {1002DFAB93}>
                 :OUTPUT-STREAM #<SB-SYS:FD-STREAM for "descriptor 5" {1002DFA763}>>
AIから名前を受け取ることができませんでした。

3,
ストリームエラーが発生しました。
Couldn't write to #<SB-SYS:FD-STREAM for "descriptor 5" {1002DFAAA3}>:
  Broken pipe

