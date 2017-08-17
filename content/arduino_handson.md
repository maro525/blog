Title: Arduinoの自作について
Date: 2017-08-05
Slug: arduino_handson
Category: 電子工作
Tags: Arduino

#### Arduinoの自作の必要性
Arduinoの自作によって、より商品に最適化された無駄のない回路を作ることができる.

#### そもそものArduinoの構成
- ソフトウェア
	- Arduino IDE
	- Arduino Bootloader
- ハードウェア
	- マイコンボード
		- マイコン（ATmega328P）
			- 32kバイトのフラッシュメモリ
			- 1kバイトのEEPROM
			- 2kバイトのRAM
	- ライター

#### Arduinoのスケッチの書き込み方法
フラッシュメモリに小さなブートローダーを書き込んでおき、パソコンのUSBポートから送られてくるスケッチの情報を、シリアル・インターフェースに書き込み、ブートローダがフラッシュメモリにスケッチを転送して実行する


#### 参考記事
* [Arduinoを自作して量産して販売する(超小型Arduino互換機 8pinoを例に) - Qiita](http://qiita.com/akichika/items/4c282c638b38681050d9)
* [Arduinoのブートローダって何?(1) - しなぷすのハード製作記](https://synapse.kyoto/tips/bootloader/page001.html)
