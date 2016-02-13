---
layout: post
title: "Tokyo SuperCollider #2 pre tutorial"
description: ""
category: 
tags: []
---
{% include JB/setup %}

今週末、9/11（土）いよいよTokyo SuperCollider #2開催です。

事前資料として、SuperColliderのグラフィック描画、アニメーションプログラムのチュートリアルをアップします。当日は、このSCの描画機能と音響合成を組み合わせて、SCのみでオーディオビジュアルプログラミングを行う方法について解説します。少し長いですが、以下のチュートリアルを読んで興味を持った方は是非、土曜日Bullet'sへお越しください。

*イベント詳細はこちらの投稿をご覧ください。
http://tokyosupercollider.blogspot.com/2010/08/tokyo-supercollider-2.html


以下のリンクどちらからでもテキストファイルのダウンロード可能です。
ぜひSCでいろいろと実行してみて下さい。


http://supercollider.jp/modules/bwiki/index.php?TKSC02

https://docs.google.com/leaf?id=0B9hVaOZN5-J4MTUyOGZjODctNDA2NS00MzA5LThmNDUtOTA0OGJiZGQ2OGQz&hl





//Tokyo SuperCollider #2
//pre-tutorial 100907


//--0. SCのグラフィック機能について
SuperColliderでは"Pen"クラスを使って、単純な2Dグラフィックを描画することができます。Penには"SCPen"と"JPen"の二種類があります。SCPenはMacOSのCocoaフレームワークを用いるクラス、JPenは"SwingOSC" というjava/OSCベースのクラスライブラリを用いるクラスです。Penに限らず、多くのGUIクラスはCocoa/Swingの両方で実装されています。[参照：Help=>Overviews=>GUI-Classes]

ただ、実際にコーディングする時には単にPen、またはGUI.penと書いておけばクロスプラットフォームで動作するようにできています。[参照：Help=>Extending SC=>Crossplatform、GUI implementationの項]

つまり、Macの場合ははじめからCocoaが利用できるので、特に準備は必要ないのですが、Linux、WindowsユーザーはSwingOSCをインストールする必要があります。

参照：SwingOSC for SuperCollider3 on Linux
http://d.hatena.ne.jp/sc3onlinux/20080123/1201094063

＊Windows+SwingOSCについての情報募集中です。


//________________________________________________
//--1. Window
"Window"クラスはPenを使って何かを描画するためのウィンドウを提供してくれます。一般的にはSliderやButtonなどを配置してオリジナルのGUIを作る際によく利用されます。Windowもまた"SCWindow"と"JSCWindow"の二種類ありますが、Penと同様の理由で単にWindowと記述するのが一番良いです。

//Windowを生成する（音響合成は行わないのでserverを起動する必要はありません）
w= Window.new; //Windowの新規インスタンスを生成する
w.front; //実際にWindowを描画し、デスクトップ画面前面に表示する

//ひと続きにも書ける
Window.new.front


.newメソッドの引数は、*new(name, bounds, resizable, border, server, scroll)です。
boundsはWindowの位置とウィンドウのサイズで、"Rect"クラスを使って指定します。
serverはSwingOSCとの互換性のために便宜上設定されているダミー引数で特に何の効果も役割もありません。


//Windowの名前とサイズ、描画位置を指定して生成
Window.new("window's name", Rect(200, 100, 640, 480)).front

""の中がウィンドウの名前、日本語は不可。書くとSCがクラッシュします。
Rect(デスクトップ画面左下を基点としたx軸の位置、y軸の位置、ウィンドウの幅、高さ)
デフォルト値はRect(128, 64, 400, 400)
Window生成時はx/y軸の位置は左下を基点として指定しますが、通常Window内に描画を行う際は左上が基点となります。ここは少し混乱しやすい点です。

//リサイズ不可のWindow
w= Window.new(resizable: false).front;

//ボーダー無しのWindow
w= Window.new(border: false).front; //手動では閉じられない
w.close; //コードで閉じる


//スクロール付きWindow
(
w= Window.new(scroll: true).front;
a= Slider(w, Rect(180, 40, 40, 600)); //Window左上からx軸180px、y軸40pxの位置に、幅40px、高さ600pxのスライダーを生成する
)

//*new(name, bounds, resizable, border, server, scroll)全てを設定する
(
w= Window.new("w", Rect(200,200,400,400), false, true, 0, true).front; //引数serverには何も指定する必要がないので「0」と書いておく
a= Slider(w, Rect(180, 40, 40, 600));
)

//半透明Window
(
w= Window.new.front;
w.alpha= 0.5;
)

//背景色の変更
(
w= Window.new.front;
w.view.background= Color.white; //背景白
)

(
w= Window.new.front;
w.view.background= Color.black; //背景黒
)

(
w= Window.new.front;
w.view.background= Color.new(1.0.rand, 1.0.rand, 1.0.rand, 1.0.rand); //ランダム。*new (red, green, blue, alpha) 
)

[参照：Color]

//________________________________________________
//--2. Pen
いよいよPenを使ってWindowにグラフィックを描いていきます。

//直線を描く
(
var width= 400, height= 300; //Windowのサイズを変数として宣言
w= Window.new("w", Rect(100, 200, width, height), false).front;
w.view.background= Color.white;
w.drawHook= { //このdrawHook関数内にPenのコードを書いていく
 Pen.moveTo(width/2@0); //Penの始点を指定。座標（Point）は"x@y"と書く
 Pen.lineTo(width/2@height); //Penが線を描くパスを指定
 Pen.stroke; //実際に線を描画
};
)

[参照：Point]

ここでは線の太さや色は特に指定していないので、デフォルトの太さ1px、黒色の線が描かれます。
が、よく見ると2pxのグレーの線に見えるはずです。これは、Penにデフォルトでアンチエイリアスがかかっているからです。正確に1pxの黒い線を描くにはアンチエイリアスをオフにします。

//1pxの直線
(
var width= 400, height= 300;
w= Window.new("w", Rect(100, 200, width, height), false).front;
w.view.background= Color.white;
w.drawHook= {
 Pen.smoothing_(false); //アンチエイリアスをオフにする
 Pen.moveTo(width/2@0); 
 Pen.lineTo(width/2@height);
 Pen.stroke;
};
)

//10pxの白い直線を描く
(
var width= 400, height= 300;
w= Window.new("w", Rect(100, 200, width, height), false).front;
w.view.background= Color.black;
w.drawHook= {
 Pen.color= Color.white; //Penの色を白に
 Pen.width= 10; //Penの太さを10pxに
 Pen.line(width/2@0, width/2@height); //線の始点と終点を一度に指定
 Pen.stroke; //線を描画
};
)


//100本の直線
(
var width= 400, height= 300; 
w= Window.new("w", Rect(100, 200, width, height), false).front;
w.view.background= Color.black;
w.drawHook= { 
 Pen.color= Color.white;
 Pen.width= 2;
 100.do{
  Pen.line(width.rand@0, width.rand@height); //width.randは、width=400なので、0~399から数値を均等にランダム選択
 };
 Pen.stroke;
};
)

[参照：Randomness]


//アンチエイリアスあり、なしの比較
(
var width= 300, height= 600; 
w= Window.new("w", Rect(100, 200, width, height), false).front;
w.view.background= Color.black;
w.drawHook= { 
 var hh= height/2;
 Pen.color= Color.white;
 
 //Penにはデフォルトでアンチエイリアスがかかっている
 100.do{
  Pen.line(width.rand@0, width.rand@hh); //Window上半分
 };
 Pen.stroke;
 
 //アンチエイリアスをオフにする
 Pen.smoothing_(false); 
 100.do{
  Pen.line(width.rand@hh, width.rand@height); //Window下半分
 };
 Pen.stroke;
 
};
)


//四角-線
(
var width= 400, height=300;
w= Window.new("w", Rect(100, 200, width, height), false).front;
w.view.background= Color.black;
w.drawHook= {
 var size= 100;
 Pen.color= Color.red;
 Pen.addRect(Rect(150, 100, size, size)); //四角の位置とサイズをRectで指定
 Pen.stroke; //実際に四角の線を描画
};
)


//四角-線・塗り
(
var width= 400, height=300;
w= Window.new("w", Rect(100, 200, width, height), false).front;
w.view.background= Color.black;
w.drawHook= {
 var size= 100;
 Pen.color= Color.red;
 Pen.strokeRect(Rect(width/4, height/6, size, size)); //四角-線
 Pen.fillRect(Rect(width/4+size, height/6+size, size, size)); //四角-塗り
};
)

//四角-線と塗りで色を分ける
(
var width= 400, height=300;
w= Window.new("w", Rect(100, 200, width, height), false).front;
w.view.background= Color.black;
w.drawHook= {
 var size= 100;
 Pen.strokeColor= Color.red; //線-赤
 Pen.fillColor= Color.green; //塗り-緑
 Pen.strokeRect(Rect(width/4, height/6, size, size)); //四角-線
 Pen.fillRect(Rect(width/4+size, height/6+size, size, size)); //四角-塗り
};
)

//50個の四角-ランダム（色、位置、大きさ）
(
var width= 400, height=300;
w= Window.new("w", Rect(100, 200, width, height), false).front;
w.view.background= Color.black;
w.drawHook= {
 50.do{
  Pen.color= Color.blue(rrand(0.0, 1), rrand(0.2, 0.8)); //*green(val, alpha) 、色の明暗=0.0~1からランダム、色の透明度= 0.2~0.8からランダム
  Pen.fillRect(Rect(width.rand, height.rand, 100.rand, 100.rand));
 }
};
)

//100個の四角-ランダム（色、位置、大きさ、線か塗りか）
(
var width= 400, height=300;
w= Window.new("w", Rect(100, 200, width, height), false).front;
w.view.background= Color.black;
w.drawHook= {
 100.do{
  Pen.color= Color.grey(rrand(0.1, 1), rrand(0.1, 1));
  Pen.addRect(Rect(width.rand, height.rand, 100.rand, 100.rand));
  Pen.perform([\stroke, \fill].choose); //毎回、strokeかfillか選ぶ
 }
};
)


//円
(
var width= 400, height=300;
w= Window.new("w", Rect(100, 200, width, height), false).front;
w.view.background= Color.black;
w.drawHook= {
 var size= 100;
 Pen.color= Color.red;
 Pen.width= 5;
 Pen.addOval(Rect(150, 100, size, size)); //円の位置とサイズをRectで指定
 Pen.stroke; //実際に円の線を描画
 
 Pen.color= Color.green;
 Pen.width= 15;
 Pen.strokeOval(Rect(20, 100, size, size)); //円-線
 
 Pen.color= Color.blue;
 Pen.fillOval(Rect(270, 100, size, size)); //円-塗り
};
)


//格子状に並んだ円を描画
//配列-Arrayを使う
//Array.series(size, start, step) 
Array.series(20, 0, 20).postln //0から20ずつ増える20個の数の集合
Array.series(15, 0, 20).postln //0から20ずつ増える15個の数の集合

(
var width= 400, height=300;
w= Window.new("w", Rect(100, 200, width, height), false).front;
w.view.background= Color.white;
w.drawHook= {
 Array.series(20, 0, 20).do{|x|
  Array.series(15, 0, 20).do{|y|
   Pen.strokeOval(Rect(x, y, 20, 20));
  };
 };
};
)

//forByを使う
//forBy ( startValue, endValue, stepValue, function );
forBy(0, 400, 20, {|i| i.postln}); //0から400まで20ずつ増える
forBy(0, 300, 20, {|i| i.postln}); //0から300まで20ずつ増える

(
var width= 400, height=300;
w= Window.new("w", Rect(100, 200, width, height), false).front;
w.view.background= Color.white;
w.drawHook= {
 forBy(0, width, 20, {|x|
  forBy(0, height, 20, {|y|
   Pen.strokeOval(Rect(x, y, 20, 20));
  });
 });
};
)


//ランダムカラー同心円
(
var width= 400, height=300;
w= Window.new("w", Rect(100, 200, width, height), false).front;
w.view.background= Color.white;
w.drawHook= {
 forBy(0, height, 3, {|i|
  Pen.width= 3;
  Pen.color= Color.new(1.0.rand, 1.0.rand, 1.0.rand, 1.0.rand); //*new(R,G,B,alpha)
  Pen.strokeOval(Rect(width-i/2, height-i/2, i, i));
  Pen.stroke;
 });
};
)


//三角関数を利用して放射状の円集合を描く
(
var width= 400, height=400;
w= Window.new("w", Rect(100, 200, width, height), false).front;
w.view.background= Color.black;
w.drawHook= {
 var d= 12; //diameter 円の直径
 var cx= width-d/2, cy= height-d/2; //x,yの中心
 var x, y, radian; //x,yの位置、ラジアン
 Pen.translate(cx, cy); //描画開始位置をx,yの中心に設定
 Pen.strokeColor= Color.white; 
 forBy(1, 360, 6, {|i| //1, 7, 13...355 =>角度 
  radian= pi/180*i; //各角度をラジアンに変換
  forBy(0, height, 12, {|l| //0, 12, 24...396 =>円軌道の半径、ラジウス radius
   x= cos(radian)*l; //cosine
   y= sin(radian)*l; //sine
   Pen.strokeOval(Rect(x, y, d, d));
  });
 });
};
)

//________________________________________________
//3. アニメーション

SCでアニメーションを行う際にはこれまでみてきたWindow+drawHook関数の代わりに、UserView+drawFunc関数の使用が推奨されます。大きな違いとして、drawHook関数は画面更新時、常に前の画面を全て消してしまいますが、drawFunc関数では描画したグラフィックがいつどのように消えるかを調節することができます。また、SC3.4からUserViewに".animate"メソッドが加わり、簡単にアニメーションを行えるようになりました。

[参照：UserView]


//平行移動 - 1本の直線
(
var width= 400, height= 300;

//drawFunc用の変数もここであらかじめ宣言しておく。
var cnt= 0; //counter: 線を動かすための変数
var x; //線のx軸位置

w= Window.new("w", Rect(100, 200, width, height), false).front;
u= UserView(w, w.view.bounds); //UserView(parent, bounds) ここではWindowを親として、Windowと同じ位置、サイズを指定している。Windowの上に同じ大きさのUserViewが乗っかっているイメージ
u.background= Color.black; //UserViewの背景色を黒に
u.animate= true; //アニメーションON => drawFuncが繰り返し実行される

~spd= 1; //線が動く速さ、グローバル変数なのでプログラム実行後リアルタイムに変更可能

u.drawFunc= {
 /*
 ここで変数を宣言してしまうとdrawFuncが実行されるごとに変数が毎回リセットされてしまい、アニメーションが更新されない
 var cnt= 0;
 var x=0;
 */
 cnt= cnt + ~spd; //更新ごとに数値が増える
 x= cnt; //xにcntを代入し、線をx座標+方向へ進める
 if(x > width, {cnt= 0}); //線が画面端を超えたら0にリセットする
 Pen.width= 2;
 Pen.color= Color.white;
 Pen.line(x@0, x@height);
 Pen.stroke; 
 Pen.color= Color.red;
 Pen.stringAtPoint(u.frameRate.asString, Point(10, 10)); //fpsを表示
};
)

//速さを変えてみる
~spd= 10;
~spd= 80;
~spd= 1;

//fpsを変えてみる。（デフォルトのfpsは60）
thisProcess.setDeferredTaskInterval(1/120);
thisProcess.setDeferredTaskInterval(1/30);
thisProcess.setDeferredTaskInterval(1/60);


//SC3.3.1でも以下のコードでほぼ同様にアニメーション可能
(
var width= 400, height= 300;
var cnt= 0;
var x= 0;
w= Window.new("w", Rect(100, 200, width, height), false).front;
u= UserView(w, w.view.bounds);
u.background= Color.black;

~fps= 60; //後で変更できるようにfpsをグローバル変数で宣言しておく
~spd= 3;

u.drawFunc= {
 cnt= cnt + ~spd;
 x= cnt;
 if(x > width, {cnt= 0});
 Pen.width= 2;
 Pen.color= Color.white;
 Pen.line(x@0, x@height);
 Pen.stroke;
};
{while{w.isClosed.not} {u.refresh; (1/~fps).wait}}.fork(AppClock); //Windowが開いている間、1/60秒間隔でUserViewを更新する。"{}.fork"は{}=関数をRoutineとして実行するメソッド。
)

//fpsを変えてみる（SC3.3.1では3.4の様にはfpsを表示できない）
~fps= 30;
~fps= 90;


//＊以下、3.4の書式で書いていきます

//特定のタイミングでグラフィックを消去する
(
var width= 400, height= 300;
var x, cnt= 0;
var tmg= 0; //グラフィック消去のタイミング設定に使用する
 
w= Window.new("w", Rect(100, 200, width, height), false).front;
u= UserView(w, w.view.bounds);
u.background= Color.black;
u.animate= true;
u.clearOnRefresh= false; //UserView更新ごとの消去をオフにする

~spd= 1;
~clr= 60; //グラフィック消去のレート

u.drawFunc= {
 cnt= cnt + ~spd;
 x= cnt;
 if(x > width, {cnt=0});
 tmg= tmg + 1 % 60; //tmgの更新。60フレーム＝1秒毎にリセット
 Pen.width= 2;
 Pen.strokeColor= Color.white; 
 Pen.line(x@0, x@height);
 Pen.stroke;
 Pen.color= Color.red;
 Pen.stringAtPoint(u.frameRate.asString, Point(10, 10));
 if(tmg%~clr== 0, {u.clearDrawing;}) //tmg%~clr==0になるタイミングでUserView上のグラフィックを消去する
};
)

//グラフィック消去レートの変更
~clr= 30;
~clr= 15;


//フェード効果
(
var width= 400, height= 300;
var cnt= 0;
var x;

w= Window.new("w", Rect(100, 200, width, height), false).front;
u= UserView(w, w.view.bounds);
u.background= Color.black;
u.animate= true;
u.clearOnRefresh= false;

~spd= 0.3;
~fad= 0.5; //フェードのレート

u.drawFunc= {
 cnt= cnt + ~spd;
 x= cnt;
 if(x > width, {cnt= 0});
 Pen.width= 2;
 Pen.color= Color.white;
 Pen.line(x@0, x@height);
 Pen.stroke;
 Pen.color= Color.red;
 Pen.stringAtPoint(u.frameRate.asString, Point(10, 10));
 //UserViewと同じサイズ背景色（ここでは黒）の四角を上から塗ることでフェード効果を出す
 Pen.color= Color.gray(0, ~fad); //alpha値（透明度）=~fad
 Pen.fillRect(Rect(0, 0, width, height)); 
};
)

//フェードレートの変更
~fad= 0.1;
~fad= 0.01;
~fad= 0.005;
~fad= 1; //alpha値=1(透明度ゼロ）だと全て消えてしまう


//平行移動 - 20本の直線
(
var width= 400, height= 300;
var cnt= 0;
var x=0; 
w= Window.new("w", Rect(100, 200, width, height), false).front;
u= UserView(w, w.view.bounds);
u.background= Color.black; 
u.animate= true;

~spd= 1;

u.drawFunc= {
 cnt= cnt + ~spd; 
 Array.series(20, 0, 20).do{|i| //配列を使って20本の線を20px間隔で描画
  x= i+cnt; //20本それぞれの線を動かす
  Pen.width= 10;
  Pen.color= Color.white;
  Pen.line(x@0, x@height);
  Pen.stroke;
 };
 if(x > (width), {cnt= 0}); //リセット
 Pen.color= Color.red;
 Pen.stringAtPoint(u.frameRate.asString, Point(10, 10)); 
};
)

//リセットを滑らかに
(
var width= 400, height= 300;
var cnt= 0;
var x=0; 
w= Window.new("w", Rect(100, 200, width, height), false).front;
u= UserView(w, w.view.bounds);
u.background= Color.black; 
u.animate= true;

~spd= 1;

u.drawFunc= {
  cnt= cnt + ~spd; 
  Array.series(21, -20, 20).do{|i| //Windowの外(x座標-20)の位置に一本余分に線を用意する
   x= i+cnt;
   Pen.width= 10;
   Pen.color= Color.white;
   Pen.line(x@0, x@height);
   Pen.stroke;
  };
  if(x > (width), {cnt= 0});
  
 Pen.color= Color.red;
 Pen.stringAtPoint(u.frameRate.asString, Point(10, 10));
};
)


//".fold"、".wrap"メソッドを使う
110.fold(0, 100)  //100を超えた数は折り返す=>90がpostされる
110.wrap(0, 100) //100を超えた数は0からリセット=>9がpostされる

//fold 画面端に当たったら跳ね返る
(
var width= 400, height= 300;
var cnt= 0;
var x=0; 
w= Window.new("w", Rect(100, 200, width, height), false).front;
u= UserView(w, w.view.bounds);
u.background= Color.black; 
u.animate= true;

~spd= 1;

u.drawFunc= {
  cnt= cnt + ~spd; 
  Array.series(21, -20, 20).do{|i|
   x= (i+cnt).fold(0, width); //***
   Pen.width= 10;
   Pen.color= Color.gray(1, 0.95);
   Pen.line(x@0, x@height);
   Pen.stroke;
  };
  
 Pen.color= Color.red;
 Pen.stringAtPoint(u.frameRate.asString, Point(10, 10));
};
)

//wrap if文よりも短いコードでリセットができる
(
var width= 400, height= 300;
var cnt= 0;
var x=0; 
w= Window.new("w", Rect(100, 200, width, height), false).front;
u= UserView(w, w.view.bounds);
u.background= Color.black; 
u.animate= true;

~spd= 1;

u.drawFunc= {
  cnt= cnt + ~spd; 
  Array.series(21, -20, 20).do{|i|
   x= (i+cnt).wrap(0, width); //***
   Pen.width= 10;
   Pen.color= Color.gray(1, 1);
   Pen.line(x@0, x@height);
   Pen.stroke;
  };
  
 Pen.color= Color.red;
 Pen.stringAtPoint(u.frameRate.asString, Point(10, 10));
};
)


//格子状に並んだ円の平行移動アニメーション
(
var width= 400, height= 300;
var cnt= 0;
var x= 0; 
w= Window.new("w", Rect(100, 200, width, height), false).front;
u= UserView(w, w.view.bounds);
u.background= Color.black; 
u.animate= true;

~spd= 1;

u.drawFunc= {
  cnt= cnt + ~spd;
  Pen.strokeColor= Color.white;
  Array.series(20, 0, 20).do{|x|
   x= (x+cnt).fold(0, width); //***
   Array.series(15, 0, 20).do{|y|
    y= (y+cnt).fold(0, height); //***
    Pen.strokeOval(Rect(x, y, 20, 20));
   };
  };
 Pen.color= Color.red;
 Pen.stringAtPoint(u.frameRate.asString, Point(10, 10));
};
)


//三角関数を使った円軌道アニメーション
(
var width= 400, height=400;
var d= 40; //diameter 円の直径
var cx= width-d/2, cy= height-d/2;
var x, y, radian, cnt= 0;
 
w= Window.new("w", Rect(100, 200, width, height), false).front;
u= UserView(w, w.view.bounds);
u.background= Color.black;
u.animate= true;

~spd= 2;
~rds= 75; //radius 円軌道の半径 

u.drawFunc= {
 Pen.translate(cx, cy);
 Pen.color= Color.white;
 Pen.width= 3;
 cnt= cnt + ~spd % 360; //%360=>360度で一周したら0にリセット
 radian= pi/180*cnt; //ラジアンを更新
 x= cos(radian)*~rds;
 y= sin(radian)*~rds;
 Pen.fillOval(Rect(x, y, d, d));
 
 //fps表示用にPenの描画開始位置を元に戻す
 Pen.translate(cx.neg, cy.neg); //".neg"はnegative、符号をマイナスにする
 Pen.color= Color.red;
 Pen.stringAtPoint(u.frameRate.asString, Point(10, 10));
};
)

~rds= 150; //円軌道を大きくしてみる


//10個の直列に並んだ円に円軌道を描かせる
(
var width= 400, height=400;
var d= 20;
var cx= width-d/2, cy= height-d/2;
var x, y, radian, cnt= 0;
 
w= Window.new("w", Rect(100, 200, width, height), false).front;
u= UserView(w, w.view.bounds);
u.background= Color.black;
u.animate= true;

~spd= 1.5;

u.drawFunc= {
 Pen.translate(cx, cy);
 Pen.strokeColor= Color.white;
 Pen.width= 3;
 cnt= cnt + ~spd % 360;
 radian= pi/180*cnt;
 Array.series(10, 0, d).do {|rds| //radiusは0, 20, 40, ..., 200。stepをdにしているのは10個の各円が重ならないようにするため
  x= cos(radian)*rds;
  y= sin(radian)*rds;
  Pen.strokeOval(Rect(x, y, d, d));
 };
 Pen.translate(cx.neg, cy.neg);
 Pen.color= Color.red;
 Pen.stringAtPoint(u.frameRate.asString, Point(10, 10));
};
)

~spd= 5;
~spd= 15;


//各円の位置をずらす
(
var width= 400, height=400;
var d= 20;
var cx= width-d/2, cy= height-d/2;
var x, y, radian=1, cnt= 0;
 
w= Window.new("w", Rect(100, 200, width, height), false).front;
u= UserView(w, w.view.bounds);
u.background= Color.black;
u.animate= true;

~spd= 3;
~pos= 0.2pi; //各円の位置を決めるのに使用する

u.drawFunc= {
 Pen.translate(cx, cy);
 Pen.strokeColor= Color.white;
 Pen.width= 4;
 cnt= cnt + ~spd % 360;
 Array.series(8, 0, d).do {|rds|
  radian= pi/180*cnt+(rds/~pos); //各円のradianを少しずつずらす
  x= cos(radian)*rds;
  y= sin(radian)*rds;
  Pen.strokeOval(Rect(x, y, d, d));
 };
 Pen.translate(cx.neg, cy.neg);
 Pen.color= Color.red;
 Pen.stringAtPoint(u.frameRate.asString, Point(10, 10));
};
)

~pos= 1;
~pos= 0.5pi;
~pos= pi/9;
~spd= 5;
~spd= 10;


//放射状の円アニメーション
(
var width= 400, height=400;
var d= 20;
var cx= width-d/2, cy= height-d/2;
var x, y, radian, cnt= 0;
 
w= Window.new("w", Rect(100, 200, width, height), false).front;
u= UserView(w, w.view.bounds);
u.background= Color.black;
u.animate= true;

~spd= 1;

u.drawFunc= {
 Pen.translate(cx, cy);
 Pen.strokeColor= Color.white;
 Pen.width= 1;
 cnt= cnt + ~spd % 360;
 Array.series(30, 0, 12).do{|z|
  radian= pi/180*z+cnt; //各円のradianを均等に更新
  Array.series(20, 0, 20).do {|rds|
   x= cos(radian)*rds;
   y= sin(radian)*rds;
   Pen.strokeOval(Rect(x, y, d, d));
  };
 };
 Pen.translate(cx.neg, cy.neg);
 Pen.color= Color.red;
 Pen.stringAtPoint(u.frameRate.asString, Point(10, 10));
};
)


＊Penのヘルプファイルや、以下のリンク先にたくさんの描画サンプルがあります。
ぜひいろいろと実行してみてください。

Pen
http://swiki.hfbk-hamburg.de:8888/MusicTechnology/833
http://swiki.hfbk-hamburg.de:8888/MusicTechnology/901
http://swiki.hfbk-hamburg.de:8888/MusicTechnology/902


//---------------------------------
100905 / itaru yasuda - i@itaru.org
