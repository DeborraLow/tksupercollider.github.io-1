---
layout: post
title: "Tokyo SuperCollider #4 資料1"
description: ""
category: 
tags: []
---
{% include JB/setup %}

<span class="Apple-style-span" style="font-family: inherit;">Tokyo SuperCollider #4の資料です。</span>

<div>ファイルのダウンロードは以下のどちらかからどうぞ。</div>


[http://supercollider.jp/modules/bwiki/index.php?TKSC04](http://supercollider.jp/modules/bwiki/index.php?TKSC04)

[https://docs.google.com/viewer?a=v&pid=explorer&chrome=true&srcid=0B9hVaOZN5-J4ODM4ZjMwODktMTAyMC00NzlmLWJmZjctMWQzMjZhOTEyOGQ2&hl=ja](https://docs.google.com/viewer?a=v&pid=explorer&chrome=true&srcid=0B9hVaOZN5-J4ODM4ZjMwODktMTAyMC00NzlmLWJmZjctMWQzMjZhOTEyOGQ2&hl=ja)

{% highlight Ruby %}

/*
 tokyo supercollider #4 - 110730
 SCQuartzComposerView tiny demo
 itaru yasuda
*/


// SCQuartzComposerView : SCのCocoaGUIクラス
// SuperColliderでQuartz Compserを使える
// *Quartz Composer OSXのビジュアルパッチプログラミングツール

// 何ができるのか？
// ヘルプにひと通りのデモがある
SCQuartzComposerView


//-- qtzファイルの読み込み、表示、再生

// ウィンドウ作成・表示
w = SCWindow("SCQCView", Rect(20, 250, 600, 300)).front;

// ウィンドウ内にSCQCView作成
v = SCQuartzComposerView(w, Rect(0, 0, 600, 300));

// qtzファイルのパスを指定
v.path = Document.current.dir ++ "/qtz/p0.qtz";

// レンダリング開始
v.start;


// qtzファイルをQCで開く
v.openInQC

// 停止
v.stop

// ウィンドウ閉じる
w.close


//-- QCパッチのパブリッシュされた入力・出力ポートの名前と値の操作・取得
(
  w = SCWindow("SCQCView", Rect(20, 250, 600, 300)).front;
  v = SCQuartzComposerView(w, Rect(0, 0, 600, 300));
  v.path = Document.current.dir ++ "/qtz/p1.qtz"; // ファイル変更
  v.start;
)

// QCで開く
v.openInQC

/*
QCのエディタで"Publish"してあるポート（みどり色のポート）のみ有効
QCエディタの編集結果をSCQCViewに反映させるには、QCを保存して再度SCQCViewを読み込む必要あり
*/

// 入力ポート名取得
v.inputKeys

// 出力ポート名を取得
v.outputKeys

// 出力ポートの値を取得
v.getOutputValue(\Result)

// 入力ポートの値を操作
v.setInputValue(\X_Position, 0.5)
v.setInputValue(\X_Position, -0.5)
v.setInputValue(\X_Position, 0)

v.setInputValue(\Y_Position, 0.5)
v.setInputValue(\Y_Position, -0.5)

v.setInputValue(\Amplitude, 10);
v.setInputValue(\Amplitude, 100);
v.setInputValue(\Amplitude, 5);

// シーケンス操作
(
  a = 0.1;
  b = -0.5;
  {
    300.do{
      v.setInputValue(\Amplitude, a);
      v.setInputValue(\Y_Position, b);
      a = a + 0.2; 
      b = b + 0.003;
      0.05.wait;
    }
  }.fork(AppClock);
)

w.close

// フルスクリーン表示
(
  w = SCWindow("SCQCView", Rect(20, 250, 600, 300)).front;
  //w = SCWindow("SCQCView", Rect(20, 250, 600, 300), border:false).front; // 枠なし、完全なフルスクリーンになるが、閉じるボタンがなくcmd+wも効かないので、実行後はcmd+kでSCをリコンパイルして閉じる（あまり行儀良くない）
  v = SCQuartzComposerView(w, Rect(0, 0, 600, 300));
  v.path = Document.current.dir ++ "/qtz/p1.qtz"; // ファイル変更
  v.start;

  v.resize = 5; // 5 - horizontally elastic, vertically elastic resize
  w.fullScreen
)


/*
他に"Structure"も使える
詳しくはヘルプ参照
*/

//-- おまけ
// 3Dやパフォーマンスにこだわらなければ、Penというクラスもある
c=0;b=200;w=Window().front.drawHook_({1e3.do{|i| Pen.fillRect(Rect(c%i.cos*b+b,c*i.tan%b*2,2,2))};c=c+0.1});AppClock.play{w.refresh;1e-3}


{% endhighlight %}