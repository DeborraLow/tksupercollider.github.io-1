---
layout: post
title: "Tokyo SuperCollider #2 資料1"
description: ""
category: 
tags: []
---
{% include JB/setup %}


Tokyo SuperCollider #2 資料1

SCのオーディオビジュアルプログラムサンプルです。
pre-tutorialと合わせてぜひSCで実行してみてください。

テキストファイルのダウンロードは以下のどちらかからどうぞ。
http://supercollider.jp/modules/bwiki/index.php?TKSC02


https://docs.google.com/leaf?id=0B9hVaOZN5-J4NzE4MjdlZjEtZWI1YS00OTZmLTg0OGEtYTUxMjFlYjVhYzU2&sort=name&layout=list&num=50



//Tokyo SuperCollider #2
//Bullet's 100911

//--Audiovisual program sample

以下のサンプルはFredrik Olofssonさんの
"Audiovisuals with SC"に基づいたものです。
http://www.fredrikolofsson.com/f0blog/?q=node/316

元記事は英語ですが、たくさんの面白いサンプルコードがダウンロードできるので
このチュートリアルと合わせてぜひSCで試してみてください。




//--1本の直線とサイン波 
( 
s.waitForBoot{ //サーバーが起動するのを待つ
 //Window設定
 var width= 400, height= 300;
 var w= Window.new("w", Rect(100, 200, width, height), false).front;
 var u= UserView(w, w.view.bounds);
 //アニメーション用の変数
 var cnt= 0;
 var x;
 //SynthDef 音源
 var syn= SynthDef(\sin, {|frq=0, pan=0|
  var snd= SinOsc.ar(frq, 0, 0.7);
  Out.ar(0, Pan2.ar(snd, pan));
 }).play(s);
 
 //グローバル変数、実行中に変更できる
 ~spd= 1; //線の動く速さ
 
 //アニメーション+サウンド
 u.drawFunc= {
  cnt= cnt + ~spd;
  x= cnt;
  if(x > width, {cnt= 0});
  //アニメーション
  Pen.width= 2;
  Pen.color= Color.white;
  Pen.line(x@0, x@height);
  Pen.stroke;
  //サウンド
  syn.set(
   \frq, x+100, //線のx座標の位置と周波数が対応
   \pan, x.linlin(0, width, -1, 1) //線のx座標の位置とパンが対応
  );
 };
 
 //Window設定
 w.onClose= {syn.free}; //Windowが閉じた時にSynthDefを解放する
 u.background= Color.black;
 u.animate= true;
};
)

//グローバル変数の変更
~spd= 2;
~spd= 0.2;
~spd= 10;



//--20本の直線とサイン波
(
s.waitForBoot{
 var width= 400, height= 300;
 var w= Window.new("w", Rect(100, 200, width, height), false).front;
 var u= UserView(w, w.view.bounds);

 var cnt= 0;
 var x;

 var syns= { 
  SynthDef(\sin, {|frq=0, pan=0|
   var snd= SinOsc.ar(frq, 0, 0.05); //ampは合計で1を超えないように、ここでは0.05*20= 1
   Out.ar(0, Pan2.ar(snd, pan));
  }).play(s);
 }.dup(20); //20個のサイン波
 
 ~spd= 1;
 ~spx= 20; //線の間隔
 
 u.drawFunc= {
  cnt= cnt + ~spd;
  Pen.width= 3;
  Pen.color= Color.gray(1, 0.95);
  20.do{|i|
   x= (i*~spx+cnt).fold(0, width);
   Pen.line(x@0, x@height);
   Pen.stroke;
   
   syns[i].set(
    \frq, x+441,
    \pan, x.linlin(0, width, -1, 1)
   );
  }
 };
 w.onClose= {syns.do{|x| x.free}};
 u.background= Color.black;
 u.animate= true;
};
)

//グローバル変数の変更
~spd= 5;
~spx= 10;
~spx= 40;
~spd= 0.5;


//円運動 - より動きのあるサウンド・アニメーション
(
s.waitForBoot{
 var width= 600, height= 400;
 var w= Window.new("w", Rect(20, 300, width, height), false).front;
 var u= UserView(w, w.view.bounds);
 
 var theta= 0; //円の位置を更新するのに使う
 
 var syn= SynthDef(\sin, {|frq=0, amp=0, pan=0|
  var snd= SinOsc.ar(frq, 0, amp);
  Out.ar(0, Pan2.ar(snd, pan));
 }).play(s);
 
 ~spd= 0.02; //円のスピード
 ~dia= 30; //円の直径
 ~xrt= 250; //円のx座標の振れ幅
 ~yrt= 150; //円のy座標の振れ幅
 
 u.drawFunc= {
  //円の位置を計算し変数として宣言
  var x= sin(theta)*~xrt; //円のx座標位置
  var y= cos(theta)*~yrt; //円のy座標位置
  var h= x.hypot(y)/(~xrt+~yrt/2); //円の画面中心からの距離：x.hypot(y)は、直角をはさむ二辺の長さがx,yの直角三角形の斜辺の長さを求める。
  
  Pen.width= 10; //Penの太さ
  Pen.color= Color.white; //Penの色
  Pen.translate(width/2, height/2); //描画開始位置を画面の中心に
  Pen.strokeOval(Rect.aboutPoint(Point(x, y), ~dia*h, ~dia*h)); //あらかじめ計算した位置を中心として円を描く、画面中心から遠くなる程円の大きさが大きくなる
  
  syn.set(
   \frq, y.linlin(height.neg/2, height/2, 800, 120), //周波数と円のy座標が対応
   \amp, h.min(0.95), //音量と円の大きさが対応*min(0.95)で0.95以上大きくはならない
   \pan, x.linlin(width.neg/2, width/2, -1, 1) //円のx座標の位置がパンと対応
  );
 
  theta= theta+~spd%2pi; //円の位置を更新、2piで一周するのでリセット
 };
 
 w.onClose= {syn.free};
 u.background= Color.black;
 u.animate= true;
};
)

//グローバル変数の変更
~spd= 0.1;
~dia= 80;
~xrt= 10;
~yrt= 10;
~xrt= 100;
~yrt= 200;

//--Pattern+Taskでシーケンスをかいてみる
(
var px, py, ps;
// x pattern
px= Pwrand(#[1,2,3], [10,50,100].normalizeSum, inf).asStream;
~t1= Task({
  inf.do({
   ~xrt= px.next*linrand(100, 10, 110); 
   exprand(1.0, 3.0).wait;
   });
 }, AppClock).start;


// y pattern
py=  Pmeanrand(5, 200, inf).asStream;
~t2= Task({
  inf.do({
   ~yrt= py.next;
   exprand(1.0, 3.0).wait;
   });
 }, AppClock).start;


// spd pattern
ps= Plprand(0.001, 3, inf).asStream;
~t3= Task({
  inf.do({
   ~spd= ps.next; 
   rrand(1, 4).wait;
   });
 }, AppClock).start;
)

//ストップ
(
~t1.stop;
~t2.stop;
~t3.stop;
)
