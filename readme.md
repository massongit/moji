Moji.js [![npm version](https://badge.fury.io/js/moji.svg)](http://badge.fury.io/js/moji)
=========================================================================================

<a href="https://saucelabs.com/u/niwaringo_moji"> <img src="https://saucelabs.com/browser-matrix/niwaringo_moji.svg" alt="Sauce Test Status"/></a>

半角英数↔全角英数、半角カナ↔全角カナ、ひらがな↔カタカナ変換等のJavaScriptライブラリ

出来る事
--------

-	文字種の変換 (convert)
-	文字種の絞込 (fileter)
-	文字種の排除 (reject)
-	空白削除 (trim)

インストール
------------

### ブラウザ

[ダウンロード](https://raw.githubusercontent.com/niwaringo/moji/master/src/moji.standalone.js)

```javascript
<script src="/path/to/script/moji.standalone.js"></script>
<script>
$('hoge input')
  .val(new Moji($('hoge input').val()).convert('ZE', 'HE').toString());
</script>
```

### npm & require

```javascript
npm i moji
```

```javascript
var Moji = require('moji');
```

使い方
------

###convert()

`convert('変換元文字種', '変換先文字種')`

```javascript
/** 全角英数 → 半角英数 **/
new Moji('ＡＢＣＤ０１２３４').convert('ZE', 'HE').toString();
// -> ABCD01234

/** 半角英数 → 全角英数 **/
new Moji('ABCD01234').convert('HE', 'ZE').toString();
// -> ＡＢＣＤ０１２３４

/** 全角スペース → 全角スペース **/
new Moji('　').convert('ZS', 'HS').toString();
// -> ' '

/** ひらがな → カタカナ **/
new Moji('あいうえお').convert('HG', 'KK').toString();
// -> アイウエオ

/** カタカナ → ひらがな **/
new Moji('アイウエオ').convert('KK', 'HG').toString();
// -> あいうえお

/** 全角カナ → 半角カナ **/
new Moji('アイウエオ').convert('ZK', 'HK').toString();
// -> ｱｲｳｴｵ

/** 半角カナ → 全角カナ **/
new Moji('ｱｲｳｴｵ').convert('HK', 'ZK').toString(),
// -> アイウエオ
```

配列指定で一括変換が可能です。

```javascript
/** [半角カナ] → [全角カナ] → [ひらがな] **/
new Moji('ｱｲｳｴｵ').convert(['HK', 'ZK'], ['KK', 'HG']).toString();
// -> あいうえお
```

---

### trim()

`trim()`\`

---

### toString()

`toString()`

基本的にメソッドはメソッドチェーンでつなぐ事を想定しているため、文字列の取得は最後に`toString`で文字列に変換してください。

---

### filter()

指定した文字種で絞込ます。

`filter('絞り込みたい文字種')`

```javascript
/** ひらがなを絞込 **/
new Moji('abcあいうアイウ123').filter('HG');
// -> あいう
```

### reject()

指定した文字種を排除します。

`reject('排除したい文字種')`

```javascript
/** ひらがなを排除 **/
new Moji('abcあいうアイウ123').reject('HG');
// -> abcアイウ123
```

---

文字種
------

変換などに使う文字種です。 標準では以下の文字種が登録されています。

`ZE`: 全角英数 // スペース含まず  
`HE`: 半角英数 // スペース含まず  
`HG`: ひらがな  
`KK`: カタカナ

`ZS`: 全角スペース  
`HS`: 半角スペース

`HK`: 半角カタカナ  
`ZK`: 全角カタカナ(半角カタカナとの変換用)  
※ 半角カナは`ｶﾞｷﾞｸﾞｹﾞｺﾞ`の用に`ﾊﾟﾋﾟﾌﾟﾍﾟﾎﾟ`のように、濁点、半濁点が別文字列になるなどの特殊性があるため、半角カナ、全角カナ変換は特別に文字種を設定しています。

文字種の追加
------------

文字種は後から独自に追加する事が可能です。

追加例)

```javascript
Moji.mojisyu('文字種名', {start: 開始文字コード, end: 終了文字コード});
Moji.mojisyu('文字種名', {regxp: 正規表現, list: 文字列の配列});
```

```javascript
var Moji = require('Moji');
Moji.mojisyu('ZE', {start:0xff01, end:0xff5e}); // 全角英数
Moji.mojisyu('HK', {
  regexp: /([\uff66-\uff9c]\uff9e)|([\uff8a-\uff8e]\uff9f)|([\uff61-\uff9f])/g,
  list: ["｡", "｢", "｣"]
  });
```

### 文字種のパターンについて

文字種は二つのパターンがあります。これは、文字列の中から該当文字種を検索して置換する方法に依存します。

#### 範囲で指定する文字種

全角英数や半角英数のように、文字コードが連続している場合に仕様します。

#### 正規表現で指定する文字種

半角カナのように連続する文字列では変換できない場合などに利用します。  
正規表現(rebexp)で範囲を指定して、その範囲内の文字列がlistにあるかチェックします。

これは、listだけで検索すると文字数に応じてパフォーマンスの劣化が予想されるためです。

この辺りはFHconvertを参考にさせて頂いています。

感謝
----

FHconverterを多く参考させて頂いています。

ライセンス
----------

MIT
