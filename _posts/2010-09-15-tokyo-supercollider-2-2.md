---
layout: post
title: "Tokyo SuperCollider #2 資料2"
description: ""
category: 
tags: []
---
{% include JB/setup %}

<span class="Apple-style-span" style="font-size: small;">SCとJamming Gear Kitの連係に使用したコードのサンプルと解説です。</span><br /><span class="Apple-style-span" style="font-size: small;">テキストファイルのダウンロードは以下のどちらかからどうぞ。</span><br /><span class="Apple-style-span" style="font-size: small;"><br /></span><br /><a href="http://supercollider.jp/modules/bwiki/index.php?TKSC02"><span class="Apple-style-span" style="font-size: small;">http://supercollider.jp/modules/bwiki/index.php?TKSC02</span></a><br /><span class="Apple-style-span" style="font-size: small;"><br /></span><br /><a href="https://docs.google.com/leaf?id=0B9hVaOZN5-J4YmU5NzdmNDEtNjUzOS00MmQzLWI3YjUtYTI0ZTExNjE4Yzk1&amp;hl=ja"><span class="Apple-style-span" style="font-size: small;">https://docs.google.com/leaf?id=0B9hVaOZN5-J4YmU5NzdmNDEtNjUzOS00MmQzLWI3YjUtYTI0ZTExNjE4Yzk1&amp;hl=ja</span></a><br /><br /><br /><div style="color: #bf0000; font: 12.0px Monaco; margin: 0.0px 0.0px 0.0px 0.0px;">//Tokyo SuperCollider #2</div><div style="color: #bf0000; font: 12.0px Monaco; margin: 0.0px 0.0px 0.0px 0.0px;">//Bullet's 100911</div><div style="font: 12.0px Monaco; margin: 0.0px 0.0px 0.0px 0.0px; min-height: 16.0px;"><br /></div><div style="color: #bf0000; font: 12.0px Monaco; margin: 0.0px 0.0px 0.0px 0.0px;">//--Jamming Gear Kit serial sample</div><div style="font: 12.0px Monaco; margin: 0.0px 0.0px 0.0px 0.0px; min-height: 16.0px;"><br /></div><div style="font: 12.0px Monaco; margin: 0.0px 0.0px 0.0px 0.0px;"><span style="color: #0000bf;">Jamming</span> <span style="color: #0000bf;">Gear</span> <span style="color: #0000bf;">Kit</span><span style="font: 12.0px Hiragino Kaku Gothic ProN;">は</span><span style="color: #0000bf;">Arduino</span><span style="font: 12.0px Hiragino Kaku Gothic ProN;">から</span><span style="color: #0000bf;">USB</span><span style="font: 12.0px Hiragino Kaku Gothic ProN;">シリアル通信で歯車の回転情報を送信しいて</span></div><div style="font: 12.0px Hiragino Kaku Gothic ProN; margin: 0.0px 0.0px 0.0px 0.0px;">これを受信するのに、<span style="color: #0000bf; font: 12.0px Monaco;">SC</span>で<span style="color: #606060; font: 12.0px Monaco;">"Arduino"</span>という<span style="color: #0000bf; font: 12.0px Monaco;">Quarks</span>（<span style="color: #0000bf; font: 12.0px Monaco;">SC</span>の拡張機能管理システム）を使用します。</div><div style="font: 12.0px Monaco; margin: 0.0px 0.0px 0.0px 0.0px; min-height: 16.0px;"></div><a name='more'></a><br /><br /><div style="font: 12.0px Hiragino Kaku Gothic ProN; margin: 0.0px 0.0px 0.0px 0.0px;">まずは<span style="color: #0000bf; font: 12.0px Monaco;">USB</span>ドライバを以下のリンクからダウンロードしてインストールします。</div><div style="color: #bf0000; font: 12.0px Monaco; margin: 0.0px 0.0px 0.0px 0.0px;"><span style="color: black;"><a href="http://www.ftdichip.com/Drivers/VCP.htm">http://www.ftdichip.com/Drivers/VCP.htm</a></span></div><div style="font: 12.0px Monaco; margin: 0.0px 0.0px 0.0px 0.0px; min-height: 16.0px;"><br /></div><div style="font: 12.0px Hiragino Kaku Gothic ProN; margin: 0.0px 0.0px 0.0px 0.0px;">インストール方法は<span style="color: #0000bf; font: 12.0px Monaco;">Gainer</span>のサイトでわかりやすく解説されています。</div><div style="color: #bf0000; font: 12.0px Monaco; margin: 0.0px 0.0px 0.0px 0.0px;"><span style="color: black;"><a href="http://gainer.cc/Download/Download">http://gainer.cc/Download/Download</a></span></div><div style="font: 12.0px Monaco; margin: 0.0px 0.0px 0.0px 0.0px; min-height: 16.0px;"><br /></div><div style="font: 12.0px Monaco; margin: 0.0px 0.0px 0.0px 0.0px; min-height: 16.0px;"><br /></div><div style="font: 12.0px Hiragino Kaku Gothic ProN; margin: 0.0px 0.0px 0.0px 0.0px;">次に<span style="color: #0000bf; font: 12.0px Monaco;">Quarks</span>の<span style="color: #606060; font: 12.0px Monaco;">"Arduino"</span>クラスをインストールします。</div><div style="font: 12.0px Hiragino Kaku Gothic ProN; margin: 0.0px 0.0px 0.0px 0.0px;">はじめに次のコードを<span style="color: #0000bf; font: 12.0px Monaco;">SC</span>で実行して、最新の<span style="color: #0000bf; font: 12.0px Monaco;">Quarks</span>をインストールします。</div><div style="font: 12.0px Monaco; margin: 0.0px 0.0px 0.0px 0.0px; min-height: 16.0px;"><br /></div><div style="font: 12.0px Monaco; margin: 0.0px 0.0px 0.0px 0.0px;"><span style="color: #0000bf;">Quarks</span>.checkoutAll;</div><div style="font: 12.0px Monaco; margin: 0.0px 0.0px 0.0px 0.0px; min-height: 16.0px;"><br /></div><div style="font: 12.0px Monaco; margin: 0.0px 0.0px 0.0px 0.0px; min-height: 16.0px;"><br /></div><div style="font: 12.0px Hiragino Kaku Gothic ProN; margin: 0.0px 0.0px 0.0px 0.0px;">実行するとターミナルが起動して自動的に最新のバージョンの<span style="color: #0000bf; font: 12.0px Monaco;">Quarks</span>を<span style="color: #0000bf; font: 12.0px Monaco;">SC</span>にインストールしてくれます。それが終わったら続いて以下のコードを実行して下さい。</div><div style="font: 12.0px Monaco; margin: 0.0px 0.0px 0.0px 0.0px; min-height: 16.0px;"><br /></div><div style="color: #0000bf; font: 12.0px Monaco; margin: 0.0px 0.0px 0.0px 0.0px;">Quarks<span style="color: black;">.gui;</span></div><div style="font: 12.0px Monaco; margin: 0.0px 0.0px 0.0px 0.0px; min-height: 16.0px;"><br /></div><div style="font: 12.0px Monaco; margin: 0.0px 0.0px 0.0px 0.0px; min-height: 16.0px;"><br /></div><div style="font: 12.0px Hiragino Kaku Gothic ProN; margin: 0.0px 0.0px 0.0px 0.0px;">実行すると<span style="color: #0000bf; font: 12.0px Monaco;">Quarks</span>のウィンドウが開きます。</div><div style="font: 12.0px Hiragino Kaku Gothic ProN; margin: 0.0px 0.0px 0.0px 0.0px;"><br /></div><div class="separator" style="clear: both; text-align: center;"><a href="http://1.bp.blogspot.com/_U4BOPjrie10/TI-0rNHjGnI/AAAAAAAAADA/kmOmLCWcQew/s1600/quarks.png" imageanchor="1" style="margin-left: 1em; margin-right: 1em;"><img border="0" height="212" src="http://1.bp.blogspot.com/_U4BOPjrie10/TI-0rNHjGnI/AAAAAAAAADA/kmOmLCWcQew/s320/quarks.png" width="320" /></a></div><div style="font: 12.0px Hiragino Kaku Gothic ProN; margin: 0.0px 0.0px 0.0px 0.0px;"><br /></div><div style="font: 12.0px Hiragino Kaku Gothic ProN; margin: 0.0px 0.0px 0.0px 0.0px;">ウィンドウの項目上から二つ目にある<span style="color: #606060; font: 12.0px Monaco;">"Arduino"</span>の左ボタンを選択して、右上の青い<span style="font: 12.0px Monaco;">save</span>ボタンを押してください。</div><div style="font: 12.0px Hiragino Kaku Gothic ProN; margin: 0.0px 0.0px 0.0px 0.0px;">その後<span style="font: 12.0px Monaco;">cmd+k</span>で<span style="color: #0000bf; font: 12.0px Monaco;">SC</span>をリコンパイルします。これで<span style="color: #606060; font: 12.0px Monaco;">"Arduino"</span>のインストールは完了です。</div><div style="font: 12.0px Monaco; margin: 0.0px 0.0px 0.0px 0.0px; min-height: 16.0px;"><br /></div><div style="font: 12.0px Hiragino Kaku Gothic ProN; margin: 0.0px 0.0px 0.0px 0.0px;"><span style="color: #0000bf; font: 12.0px Monaco;">Quarks</span>についてはこちらも参照してみてください。</div><div style="color: #bf0000; font: 12.0px Monaco; margin: 0.0px 0.0px 0.0px 0.0px;"><span style="color: black;"><a href="http://supercollider.jp/modules/bwiki/index.php?SCSG006">http://supercollider.jp/modules/bwiki/index.php?SCSG006</a></span></div><div style="font: 12.0px Monaco; margin: 0.0px 0.0px 0.0px 0.0px; min-height: 16.0px;"><br /></div><div style="font: 12.0px Monaco; margin: 0.0px 0.0px 0.0px 0.0px; min-height: 16.0px;"><br /></div><div style="font: 12.0px Monaco; margin: 0.0px 0.0px 0.0px 0.0px; min-height: 16.0px;"><br /></div><div style="font: 12.0px Hiragino Kaku Gothic ProN; margin: 0.0px 0.0px 0.0px 0.0px;">では、実際に<span style="color: #0000bf; font: 12.0px Monaco;">Jamming</span><span style="font: 12.0px Monaco;"> </span><span style="color: #0000bf; font: 12.0px Monaco;">Gear</span><span style="font: 12.0px Monaco;"> </span><span style="color: #0000bf; font: 12.0px Monaco;">Kit</span><span style="font: 12.0px Monaco;"> </span>と<span style="font: 12.0px Monaco;"> </span><span style="color: #0000bf; font: 12.0px Monaco;">SC</span>の連係に使用したコードをみていきます。</div><div style="font: 12.0px Monaco; margin: 0.0px 0.0px 0.0px 0.0px; min-height: 16.0px;"><br /></div><div style="color: #bf0000; font: 12.0px Monaco; margin: 0.0px 0.0px 0.0px 0.0px;">//jg-live 100911</div><div style="font: 12.0px Monaco; margin: 0.0px 0.0px 0.0px 0.0px; min-height: 16.0px;"><br /></div><div style="color: #bf0000; font: 12.0px Hiragino Kaku Gothic ProN; margin: 0.0px 0.0px 0.0px 0.0px;"><span style="font: 12.0px Monaco;">//Jamming Gear Kit </span>と<span style="font: 12.0px Monaco;">PC</span>を接続し、シリアルデバイスのリストをポストする</div><div style="font: 12.0px Monaco; margin: 0.0px 0.0px 0.0px 0.0px;"><span style="color: #0000bf;">SerialPort</span>.listDevices;</div><div style="font: 12.0px Monaco; margin: 0.0px 0.0px 0.0px 0.0px; min-height: 16.0px;"><br /></div><div style="font: 12.0px Monaco; margin: 0.0px 0.0px 0.0px 0.0px;">post window<span style="font: 12.0px Hiragino Kaku Gothic ProN;">にポストされた中で</span></div><div style="color: #606060; font: 12.0px Monaco; margin: 0.0px 0.0px 0.0px 0.0px;">"/dev/tty.usbserial~"<span style="color: black; font: 12.0px Hiragino Kaku Gothic ProN;">が</span><span style="color: #0000bf; font: 12.0px Hiragino Kaku Gothic ProN;">USB</span><span style="color: black; font: 12.0px Hiragino Kaku Gothic ProN;">シリアルポートの番号です。</span></div><div style="font: 12.0px Monaco; margin: 0.0px 0.0px 0.0px 0.0px; min-height: 16.0px;"><br /></div><div style="font: 12.0px Monaco; margin: 0.0px 0.0px 0.0px 0.0px; min-height: 16.0px;"><br /></div><div style="color: #bf0000; font: 12.0px Hiragino Kaku Gothic ProN; margin: 0.0px 0.0px 0.0px 0.0px;"><span style="font: 12.0px Monaco;">//Arduino</span>からの通信を受け取るための変数を宣言します。</div><div style="color: #bf0000; font: 12.0px Monaco; margin: 0.0px 0.0px 0.0px 0.0px;"><span style="color: black;">p = </span><span style="color: #0000bf;">ArduinoSMS</span><span style="color: black;">(</span><span style="color: #606060;">"/dev/tty.usbserial-A900aevy"</span><span style="color: black;">, 57600); </span>//<span style="font: 12.0px Hiragino Kaku Gothic ProN;">この</span>ArdinoSMS<span style="font: 12.0px Hiragino Kaku Gothic ProN;">というクラスが</span>Quarks<span style="font: 12.0px Hiragino Kaku Gothic ProN;">でインストールしたものです。引数は</span>("USB<span style="font: 12.0px Hiragino Kaku Gothic ProN;">シリアルポートの番号</span>",&nbsp; <span style="font: 12.0px Hiragino Kaku Gothic ProN;">ボーレート（通信のレート）</span>)</div><div style="font: 12.0px Monaco; margin: 0.0px 0.0px 0.0px 0.0px; min-height: 16.0px;"><br /></div><div style="font: 12.0px Monaco; margin: 0.0px 0.0px 0.0px 0.0px; min-height: 16.0px;"><br /></div><div style="color: #0000bf; font: 12.0px Monaco; margin: 0.0px 0.0px 0.0px 0.0px;">TKSC<span style="color: black;">#2</span><span style="color: black; font: 12.0px Hiragino Kaku Gothic ProN;">で使用した</span>Jamming<span style="color: black;"> </span>Gear<span style="color: black;"> </span>Kit<span style="color: black;"> </span><span style="color: black; font: 12.0px Hiragino Kaku Gothic ProN;">では</span><span style="font: 12.0px Hiragino Kaku Gothic ProN;">Arduino</span><span style="color: black; font: 12.0px Hiragino Kaku Gothic ProN;">側で</span></div><div style="font: 12.0px Hiragino Kaku Gothic ProN; margin: 0.0px 0.0px 0.0px 0.0px;">12個の歯車が回転するごとに固有の番号を出力するようにプログラムされていました。</div><div style="font: 12.0px Hiragino Kaku Gothic ProN; margin: 0.0px 0.0px 0.0px 0.0px; min-height: 18.0px;"><br /></div><div style="font: 12.0px Hiragino Kaku Gothic ProN; margin: 0.0px 0.0px 0.0px 0.0px;"><span style="color: #0000bf;">SC</span>側では個々の番号を受信した時に、あらかじめstoreしておいた固有の<span style="color: #0000bf;">SynthDef</span>(音源)が鳴るようにプログラムし、パート毎に<span style="color: #0000bf;">SynthDef</span>を入れ替えてパフォーマンスを行いました。</div><div style="font: 12.0px Monaco; margin: 0.0px 0.0px 0.0px 0.0px; min-height: 16.0px;"><br /></div><div style="font: 12.0px Monaco; margin: 0.0px 0.0px 0.0px 0.0px; min-height: 16.0px;"><br /></div><div style="color: #bf0000; font: 12.0px Hiragino Kaku Gothic ProN; margin: 0.0px 0.0px 0.0px 0.0px;"><span style="font: 12.0px Monaco;">//</span>使用したプログラムの一例</div><div style="font: 12.0px Monaco; margin: 0.0px 0.0px 0.0px 0.0px;">(</div><div style="font: 12.0px Monaco; margin: 0.0px 0.0px 0.0px 0.0px;">p.action = { <span style="color: #0000bf;">|... msg|</span></div><div style="color: #bf0000; font: 12.0px Hiragino Kaku Gothic ProN; margin: 0.0px 0.0px 0.0px 0.0px;"><span style="color: black; font: 12.0px Monaco;"><span class="Apple-tab-span" style="white-space: pre;"> </span>msg[0].postln;<span class="Apple-tab-span" style="white-space: pre;"> </span></span><span style="font: 12.0px Monaco;">//</span>受け取ったシリアル通信をpost windowにポスト</div><div style="color: #bf0000; font: 12.0px Monaco; margin: 0.0px 0.0px 0.0px 0.0px;"><span style="color: black;"><span class="Apple-tab-span" style="white-space: pre;"> </span>if(msg[0]== 1, {</span><span style="color: #0000bf;">Synth</span><span style="color: black;">(</span><span style="color: #007300;">\a1</span><span style="color: black;">)}); </span>//msg[0]<span style="font: 12.0px Hiragino Kaku Gothic ProN;">が</span>1<span style="font: 12.0px Hiragino Kaku Gothic ProN;">の時SynthDef </span>\a1 <span style="font: 12.0px Hiragino Kaku Gothic ProN;">を鳴らす</span></div><div style="font: 12.0px Monaco; margin: 0.0px 0.0px 0.0px 0.0px;"><span class="Apple-tab-span" style="white-space: pre;"> </span>if(msg[0]== 2, {<span style="color: #0000bf;">Synth</span>(<span style="color: #007300;">\a2</span>)});</div><div style="font: 12.0px Monaco; margin: 0.0px 0.0px 0.0px 0.0px;"><span class="Apple-tab-span" style="white-space: pre;"> </span>if(msg[0]== 3, {<span style="color: #0000bf;">Synth</span>(<span style="color: #007300;">\a3</span>)});</div><div style="font: 12.0px Monaco; margin: 0.0px 0.0px 0.0px 0.0px;"><span class="Apple-tab-span" style="white-space: pre;"> </span>if(msg[0]== 4, {<span style="color: #0000bf;">Synth</span>(<span style="color: #007300;">\a4</span>)});</div><div style="font: 12.0px Monaco; margin: 0.0px 0.0px 0.0px 0.0px;"><span class="Apple-tab-span" style="white-space: pre;"> </span>if(msg[0]== 5, {<span style="color: #0000bf;">Synth</span>(<span style="color: #007300;">\a5</span>)});</div><div style="font: 12.0px Monaco; margin: 0.0px 0.0px 0.0px 0.0px;"><span class="Apple-tab-span" style="white-space: pre;"> </span>if(msg[0]== 6, {<span style="color: #0000bf;">Synth</span>(<span style="color: #007300;">\a6</span>)});</div><div style="font: 12.0px Monaco; margin: 0.0px 0.0px 0.0px 0.0px;"><span class="Apple-tab-span" style="white-space: pre;"> </span>if(msg[0]== 7, {<span style="color: #0000bf;">Synth</span>(<span style="color: #007300;">\a7</span>)});</div><div style="font: 12.0px Monaco; margin: 0.0px 0.0px 0.0px 0.0px;"><span class="Apple-tab-span" style="white-space: pre;"> </span>if(msg[0]== 8, {<span style="color: #0000bf;">Synth</span>(<span style="color: #007300;">\a8</span>)});</div><div style="font: 12.0px Monaco; margin: 0.0px 0.0px 0.0px 0.0px;"><span class="Apple-tab-span" style="white-space: pre;"> </span>if(msg[0]== 9, {<span style="color: #0000bf;">Synth</span>(<span style="color: #007300;">\a9</span>)});</div><div style="font: 12.0px Monaco; margin: 0.0px 0.0px 0.0px 0.0px;"><span class="Apple-tab-span" style="white-space: pre;"> </span>if(msg[0]== 10, {<span style="color: #0000bf;">Synth</span>(<span style="color: #007300;">\a10</span>)});</div><div style="font: 12.0px Monaco; margin: 0.0px 0.0px 0.0px 0.0px;"><span class="Apple-tab-span" style="white-space: pre;"> </span>if(msg[0]== 11, {<span style="color: #0000bf;">Synth</span>(<span style="color: #007300;">\a11</span>)});</div><div style="font: 12.0px Monaco; margin: 0.0px 0.0px 0.0px 0.0px;"><span class="Apple-tab-span" style="white-space: pre;"> </span>if(msg[0]== 12, {<span style="color: #0000bf;">Synth</span>(<span style="color: #007300;">\a12</span>)});</div><div style="font: 12.0px Monaco; margin: 0.0px 0.0px 0.0px 0.0px;">};</div><div style="font: 12.0px Monaco; margin: 0.0px 0.0px 0.0px 0.0px;">)</div><div style="font: 12.0px Monaco; margin: 0.0px 0.0px 0.0px 0.0px; min-height: 16.0px;"><br /></div><div style="font: 12.0px Hiragino Kaku Gothic ProN; margin: 0.0px 0.0px 0.0px 0.0px;">使用した<span style="color: #0000bf; font: 12.0px Monaco;">SynthDef</span>とシリアル通信のプログラムは</div><div style="font: 12.0px Hiragino Kaku Gothic ProN; margin: 0.0px 0.0px 0.0px 0.0px;">以下のリンクからダウンロード可能です。</div><div style="color: #bf0000; font: 12.0px Hiragino Kaku Gothic ProN; margin: 0.0px 0.0px 0.0px 0.0px; min-height: 18.0px;"><br /></div><div style="color: #bf0000; font: 12.0px Hiragino Kaku Gothic ProN; margin: 0.0px 0.0px 0.0px 0.0px;">//jg-live-synthdefs.rtf</div><div style="color: #bf0000; font: 12.0px Hiragino Kaku Gothic ProN; margin: 0.0px 0.0px 0.0px 0.0px;"><a href="https://docs.google.com/leaf?id=0B9hVaOZN5-J4YWVkZjkxNzMtNjg2Yi00YzAzLWFlNzctNWQxYjQ1Mjg3ZDJm&amp;hl=ja">https://docs.google.com/leaf?id=0B9hVaOZN5-J4YWVkZjkxNzMtNjg2Yi00YzAzLWFlNzctNWQxYjQ1Mjg3ZDJm&amp;hl=ja</a></div><div style="color: #bf0000; font: 12.0px Hiragino Kaku Gothic ProN; margin: 0.0px 0.0px 0.0px 0.0px; min-height: 18.0px;"><br /></div><div style="color: #bf0000; font: 12.0px Hiragino Kaku Gothic ProN; margin: 0.0px 0.0px 0.0px 0.0px;">//jg-live-serial.rtf</div><div style="color: #bf0000; font: 12.0px Hiragino Kaku Gothic ProN; margin: 0.0px 0.0px 0.0px 0.0px;"><span style="color: black;"><a href="https://docs.google.com/leaf?id=0B9hVaOZN5-J4Y2NhNGE3ZDEtMjYyZC00NmFjLTgzZjktNDAyZDE2NTk4ZDg1&amp;hl=ja">https:<span style="font: 12.0px Hiragino Kaku Gothic ProN;">//docs.google.com/leaf?id=0B9hVaOZN5-J4Y2NhNGE3ZDEtMjYyZC00NmFjLTgzZjktNDAyZDE2NTk4ZDg1&amp;hl=ja</span></a></span></div><div style="font: 12.0px Hiragino Kaku Gothic ProN; margin: 0.0px 0.0px 0.0px 0.0px; min-height: 18.0px;"><br /></div>