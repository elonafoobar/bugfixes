# Others


* *map_init_mainで指定された音楽が再生されない問題 [#x51b38b6]
~>>129の内容は差し戻しておいてくれると助かるます

if cfg_music=false : returnの下に
if music=0 : if mMusic!0 : music=mMusicを書き加え
if music=0{ }内のif mMusic!0 :music=mMusicはコメントアウト
これで基本的には問題がないように思える

個人的には
if (music=0)or(areaType(gArea)=mTypeWorld)の後者の条件の方を切り取ってif music=0{ }内にぶち込んだり
if music=0{ }内にif gArea=areaVernis : music=mcTown1　
と書いておいてヴェルニースのBGMがディスクで書き換わったままにならないようにしたが好みの範疇だと思う

またmMusic:mdata(13)の設定が優先されるようになるので
map_init_mainの方でmMusicの設定をしておくとよい

~>>49のようにゼーム打倒後のBGMを変えたいなら
レシマス最深層で検索して出てくるあたりで
mMusic=mcLastBoss
↓
if npcMemory(0,2)=0:mMusic=mcLastBoss:else:mMusic=mcChaos
のように変更するといいかも

三魔石の試練のボスはすくつで先に倒すことができるので
もし打倒後BGM変えたいならnpcMemoryじゃなくイベントフラグを判定に使った方がいいかも
クルイツゥア倒してもコルゴンが生きてるとBGM変えるのは不自然だし竜窟も別の方法が必要だが
レシマス最深層と違って固定マップなので死亡状態残しておいて判定に使う等いくつか方法はあると思う

またディスク使用時に"再生した。"となっているが"を再生した。"と助詞を入れておくと良い

sound.hsp

*種族の部位設定が正しく反映されていない問題 [#xbbb5100]
580 名前：異形の森の名も無き使者[sage] 投稿日：2011/06/03(金) 17:15:18 ID:hMz9KaxA
援軍で呼んだ仲間にランダムで部位が付与されているといった事はあるのでしょうか。うちのカブの装備部位がwikiの記述と違い最初から腕腰足が付与されていました。
試しにこのカブを牧場で増やしてみても増えた牧畜の装備部位はwikiの記述通りでした。
仲間にしたのはlv10前後の時で遺伝子合成は覚えていませんでしたし、した覚えもありません。
それに合成で増やした部位は矢弾の後に表示されると思うのですが、このカブの装備部位は(頭 胴体 背中 手 手 腰 足 足 腕 腰 足 遠隔 矢弾)と表示されます。
ちなみにver1.22です。

619 名前：異形の森の名も無き使者[sage] 投稿日：2011/06/03(金) 23:23:19 ID:FBWPN4TE
~>>580
遅レスだけど俺もすくつか討伐依頼で支配した高レベル体のカブに見覚えの無い部位がついていて
それとまったく同じ部位だった。もう一度他のカブを支配しても同じ部位がついていたので仕様かなと思ってた


どうもデータベースの部位記述がおかしいらしい

イェルス
頭|首|体|背|手|手|指|指|腕|腰|足|
getstr用にこんな感じで区切り文字で閉じて書かなければならないのだが

インプ
首|体|手|手|指|指指|

ストーカー
首|手|手|指|指|腕||

バイク
頭|体|背|手|手|腰|足|足

この三種族は書き方がおかしく、うまくgetstrできてない
人間型等9部位以上持ったキャラを生成したあとにこれら三種族を生成すると9部位目以降が追加されてしまうようだ
ひとまず書き直しておく

db_race.hsp

*カジノで連勝するほどエーテル抗体の取得率が下がる問題 [#w3061d0c]
カジノ景品おまけのエーテル抗体の取得率修正
　カジノ景品のおまけで貰えるエーテル抗体の取得率が連勝数が４の時が最大で以降
　連勝するごとに下がっていく
　対処
　Noa氏への問い合わせの結果、「連勝すればするほど取得率が上がるように」が意図との
　ことだったので
　txtadv.hsp 737行目
　　if rnd(200)>(winRow*5+5)をif rnd(200)<(winRow*5+5)へ変更
　※エーテル抗体の取得難度が上がると思われるため要調整？

*リングコマンドの表示位置がおかしい問題 [#daabd0e1]
ついでに逆コンソースで申し訳ないがzでのコマンド位置修正
240200行辺り(*label_2699) ty = 345 → ty = windowh - 255

**修正方法 [#k50a26d6]
help.hsp 85行目
 tx=50:ty=345
↓
 tx=50:ty=windowH-255

*ダンジョンで下り階段の前に帰還できる問題 [#u74457dd]
ちょっと小耳に挟んだのですが、

レシマスの洞窟で上り階段を上がった直後、レシマスに帰還をすると、
なんと到達最下層の下り階段の目の前に帰還できるそうですよ！

冒険者 (2010-07-17 (土) 20:28:47)

裏技というか変な挙動なんで、動作を確認してバグ報告予定リストに入れてみた。 -- 2010-07-17 (土) 21:04:46
verはいくつだ -- 2010-07-17 (土) 21:41:42

map.func.hsp

*信仰が既に限界まで高まっているときに捧げものをするとご褒美フラグが立たない問題 [#dfed6bd3]
**修正方法 [#t8988a3c]
処理の順だけ変えておく
god.hsp 6行目

 #deffunc modPiety int a
  if sPrayer(pc)*100<cPiety(pc){
    txt lang("あなたの信仰は既に限界まで高まっている。"," Your God becomes indifferent to your gift.")
    return false
    }
  if gGodRank=4 : if cPiety(pc)>=defGodGift3:gGodRank++:txtGod cGod(pc),txtGodLove
  if gGodRank=2 : if cPiety(pc)>=defGodGift2:gGodRank++:txtGod cGod(pc),txtGodLike
  if gGodRank=0 : if cPiety(pc)>=defGodGift1:gGodRank++:txtGod cGod(pc),txtGodLike
 
  cPiety(pc)+=a/(1+(gArea=areaShowHouse)*9)
  return true
↓
 #deffunc modPiety int a
  if gGodRank=4 : if cPiety(pc)>=defGodGift3:gGodRank++:txtGod cGod(pc),txtGodLove
  if gGodRank=2 : if cPiety(pc)>=defGodGift2:gGodRank++:txtGod cGod(pc),txtGodLike
  if gGodRank=0 : if cPiety(pc)>=defGodGift1:gGodRank++:txtGod cGod(pc),txtGodLike
 
  if sPrayer(pc)*100<cPiety(pc){
    txt lang("あなたの信仰は既に限界まで高まっている。"," Your God becomes indifferent to your gift.")
    return false
    }
  cPiety(pc)+=a/(1+(gArea=areaShowHouse)*9)
  return true


*捧げ物のエフェクトがPC以外に表示される問題 [#k87e5401]
**修正方法 [#j7a11f35]
god.hsp 376行目
  call anime,(animeId=aniOffer)
↓
  call anime,(animeId=aniOffer,tc=pc)

*護衛依頼の報酬が正しく計算されていない問題 [#peebd05b]
**修正方法 [#wd0ebdb0]
quest.hsp 168行目
    p=qMap(qParam1(rq))
↓
    p=qParam1(rq)

*暗記スキル未取得のとき、脳機械化の変異でスキルが上昇する問題 [#h209001d]
**修正方法 [#z2fdf046]
trait.hsp 550-551行目
    if sORG(rsMemorize,pc)=0:return false
を挿入


*護衛依頼失敗時に護衛対象がマップ上に残り続けることがある問題 [#we5cc1d1]
**概要 [#u30fef4b]
詳細な再現手順と現象については http://www.nicovideo.jp/watch/sm32556762 の 7:00頃を参照
動画はoomExであるが、v1.22でも発生することを確認済み
鼓舞などによって護衛依頼失敗時の大ダメージ(999,999)を耐えさせることで護衛対象者が生き残った場合、
扱いとしては対象者が死んだことになり依頼も失敗するが、画面上には残り続け、さらに話しかけるといった動作も行うことができる。
これ以外の簡単な再現方法として、ソースコードを書き換えてdmghp関数に渡すダメージ量を減らす、護衛対象のHPを999,999よりも大きくする、dmghpを呼び出さないようにする、といった方法がある。
**原因 [#ifff84f1]
以下擬似コード
 function 依頼失敗:
    if その依頼が護衛:
        show_message "あなたは護衛の任務を果たせなかった。"
        foreach 全ペット:
            if そのペットが護衛対象で、その依頼が今まさに失敗した依頼:
                護衛対象であるというフラグを折る
                if まだ生きている:
                    show_message "「時間切れだ。こうなったら...」"
                    999,999のダメージを与える
                強制的に殺す (*)
問題なのは(*)の箇所で、cdata(0, X)（Xはキャラクタ番号） をゼロにしているのだが、これだけでは完全に殺しきることができない。
Elonaにおいてキャラクタ番号はmap(X, Y, 1)（X、Yはマップの座標）でも参照されており、こちらもゼロにしておかないと
画面上の描写が残ったり、話しかけることができたりする。cdata(0, X)がゼロになっていてもメモリ上には存在しているので
cdata(0, X)がゼロでないかを確認しない処理ではキャラクタが残り続ける。逆に言えば、cdata(0, X)を確認する場合には
無効なキャラクタと判定される。これによって上述の動画に挙げられているような、動かない、マップ移動すると消えるといった
現象を説明することができる。すなわちこれらの処理ではcdata(0, X)が非ゼロかどうかを確認しているため、死んだキャラクタであると
正確に判断されている。
**修正方法 [#p6424568]
この問題はmap(X, Y, 1)を参照する箇所において参照先のキャラクタのcdata(0, X)がゼロでないかどうかを確認するという方法でも修正可能だが、
(*)の箇所で同時にmap(X, Y, 1)もゼロクリアしておく方が無難と考えられる。すなわち、
 cdata(0, tc) = 0
 cell_removechara cdata(1, tc), cdata(2, tc) ; 追加
**関連する潜在的なバグ [#wd931461]
この問題はキャラクタを強制的に殺す方法としてcdata(0, X) = 0を用いていることである。これだけではマップ上から消し去れないため、
関数を新たに定義して、cdata(0, X)のクリアとmap(X, Y, 1)のクリアを両方行わせるべきである。
なお、chara_vanquishがほぼ同様の動作をするがそれで良いかは未確認。
この件のように「必ず死ぬと思われる処理」の後に「cdata(0, X)のクリア」を行なっている箇所は他にリリーを
殺す処理がある。リリーを何らかの方法でダメージ(9,999)に耐えさせると同様の現象が発生することを確認している。
