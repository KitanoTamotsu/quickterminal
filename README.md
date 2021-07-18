## 　　Lesson19.　ホットキーツールを作成する／後続フローを二股にする  
#### 開発メモ
### 1.実装までの試行錯誤
　Key Comboオブジェクトのサンプルを考えていました
<br>　初めは、選択中のテキストを⌘Cでクリップボードにコピーして、それをスクリプトでターミナルに送るみたいなイメージ
<br>　でも、この場合、⌘Cを送ってもクリップできないことがありました
<br>　結局、HotkeyオブジェクトのSelection in macOSを利用することにしたら
<br>　けっこう思い通りに稼働するようになりました
### 2.実装後のちょい足し
　試作したワークフローはHotkeyオブジェクトからTerminal Commandオブジェクトを結ぶだけのものでした
<br>　実際の利用を考えると、ターミナルにコピペで利用するコマンドは、別途、保存するようなことが想定されるので、
<br>　クリップボードに追加する部分をつけ達しました。Copy To Clipboardオブジェクトです
<br>　
<br>　さらに、たんなる思いつきですが、URLを選択している場合は、そのURLを開くようにしたり
<br>　URLでも、コマンドでもないものは、検索するようにしてみました
<br>　ターミナルとは無関係になっちゃいました
<br>
<br>　選択テキストの判定はConditionalオブジェクトで制御しています
<br>　正規表現で、URLっぽいものは、Open URLオブジェクトでオープン
<br>　英数字・記号・スペースの場合はTerminal Commandオブジェクトと、Copy to Clipboardオブジェクトで処理
<br>　それ以外はDefault Web Searchで検索、という具合です　
<br>
<br>　結果的にselecitonを{query}にして、内容に応じて処理を行うワークフローになりました
<br>　選択文字で処理を判断できるのであれば、ご自身がコピペなどでよく使う処理を追加すると良いでしょう
<br>　文字選択＋Hotkeyだけなので便利かも
#### 背景
　シェルスクリプトを描くようになってターミナルを使うことも増えたので、思いつきました
<br>　コマンドのクリップからターミナルを立ち上げてペースト＆実行がHOTKEYひとつでできるようになります
<br>　省力化というほどには大したことではないですね　
　<br>　
#### 取扱説明
### 機能:
　現在選択しているテキストを判断して、ターミナル実行、URLオープン、検索を実施する

<br>　
### インストール:
　1.[Alfredworkflow](https://github.com/KitanoTamotsu/quickterminal/releases/download/1.0/Quick.Terminal.alfredworkflow.zip)をダウンロード 
<br>　2.ファイルをダブルクリックしてワークフローに登録
### 使い方:
　テキストを選択してHotkey『⌘T』で起動
<br>　（Hotkeyはご自身で設定が必要です）
<br>　以下のサンプルを試してみてください
```
テキストを選択して、Quickterminalワークフローに設定したホットキーで起動させてみて！


1.カレンダー
 cal -A 1 -B 1


2.お天気
 curl wttr.in 


3.花文字　（readはzshとbashで異なります。下記はzshです。ターミナルがzshになってました。。）
 echo "What's your name? "; read name; banner -w 48 $name


4.吹き出しつきアスキーアート
 まずはコマンドのインストール
 brew install cowsay

 その後で試してね（全部入り）
 cowsay -l | grep -v "^Cow" | sed -e "s% %\n%g" | sed -e "s%\(.*\)%cowsay -f \1 Now I am a \1%" | sh

 例えばこんな使い方
 cowsay -f cheese はい！チーズ 


5.超有名。ls打ち間違い時のジョークコマンド
 まずはコマンドのインストール
 brew install sl

 その後で試してね（想像どおりかな）
 sl -F


6.顔文字の雨
 ruby -e 'C=`stty size`.scan(/\d+/)[1].to_i;S=[*0x1F600..0x1F640];a={};puts "\033[2J";loop{a[rand(C)]=0;a.each{|x,o|;a[x]+=1;print "\033[#{o};#{x}H \033[#{a[x]};#{x}H#{S.sample.chr("utf-8")} \033[0;0H"};$stdout.flush;sleep 0.2}'


7.日本語の読み上げ 
 Say -v Otoya '昔々、あるところに、お爺さんと　お婆さんが　住んでいました。'

 上記コマンドでOtoya君がいない場合に確認
 say -v '?'


8.グー<font color=red>ル</font>グル。googleのイースターエッグですが、safariではできないので、chrome指定で起動しています
 open 'http://www.google.co.jp/search?q=%E4%B8%80%E5%9B%9E%E8%BB%A2' -a 'Google Chrome'

9.ストリートビューのURL（海底や空や宇宙にも行けちゃいます。ドーローン撮影ですかね）
 https://www.google.com/maps/@33.9323743,136.2118284,3a,75y,61.24h,62.57t/data=!3m8!1e1!3m6!1sAF1QipPHR2Zdfn_8NiCu4TIgMjT4pzp4zgtYSXKeqsMK!2e10!3e11!6shttps:%2F%2Flh5.googleusercontent.com%2Fp%2FAF1QipPHR2Zdfn_8NiCu4TIgMjT4pzp4zgtYSXKeqsMK%3Dw203-h100-k-no-pi-0-ya17.522667-ro-0-fo100!7i4096!8i2048

10.幸福のグラフのURL
 https://www.google.com/search?q=%28sqrt%28cos%28x%29%29*cos%28200*x%29%2Bsqrt%28abs%28x%29%29-0.5%29*%284-x*x%29%5E0.14&client=safari&rls=en&sxsrf=ALeKk02DCqeCk9WQqXqfyYnRCzi7z6HYcw%3A1616457541304&ei=RS9ZYNPyEcnXhwOh_JuADg&oq=%28sqrt%28cos%28x%29%29*cos%28200*x%29%2Bsqrt%28abs%28x%29%29-0.5%29*%284-x*x%29%5E0.14&gs_lcp=Cgdnd3Mtd2l6EAxQAFgAYLWlBGgBcAB4AIABSogBSpIBATGYAQCqAQdnd3Mtd2l6wAEB&sclient=gws-wiz&ved=0ahUKEwiTqazMjcXvAhXJ62EKHSH-BuAQ4dUDCAw


11.検索（サンプルを作るまでもないですが）
訳せない言葉

```
#### 修正履歴

### ver1.1(2021-04-04)：
　シェルスクリプトをbashからzshに変更しました

<br>
<br>
[トップページに戻る](https://kitanotamotsu.github.io/)

