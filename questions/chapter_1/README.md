# Linux 環境

## 1.2

1+1の計算

```
$ echo '1+1' | bc
> 2
```

bc は計算
ruby とかに渡すこともできる。難しそうだったら試しても良いかも

* パイプ（|）とリダイレクト（>）
* ls / mkdir / rm / rmdir
* chmod
  * rwx とかの話
* man

## 1.3

### sed

* `echo 文字列 | sed 's/置換対象/置換後の文字列/g'`
* s = 文字の置換を行う
* g = すべての文字列を対象に
* & = 検索対象を再利用
* \1\2 = 検索対象の部分的な再利用`()`で囲む
  * -E / -r を使わないと括弧をエスケープする必要がある

###  grep

* 検索
* メタ文字
  * `^` 先頭 `$` 末尾
  * `.` なにか一文字
  * `*` 前の文字が0個以上
  * `[]`中の文字どれか一つ
    * `^` があるとそれ以外になる
  * -o = マッチする部分を抜き出すオプション
  * trコマンド 原始的な置換

### awk

* grep がよりプログラマブルになったやつ
* `printf('%s%d', 引数)`
* BEGIN=1行目の処理を始める前、END=最終行の処理を終えたあと
* C言語っぽい何かでたくさんかけるので、おいおい見ていく

### sort と uniq

* sort で並び替えて uniq
* uniq -c オプションをつけるとカウントすできる
* sortされてない状態でuniqする
* sort
  * -n 数字順
  * -k 列指定のオプション

awkの連想配列

a[文字列|数字]
for(key in array)

### xargs

* 引数で与えられたコマンドを実行
* "-n個数" オプション = 入力された文字列を指定した個数ずつコマンドに渡す
* "-I変数" パイプで渡されるものを格納する
* コマンドを指定されなかったときはechoを実行する
  * echo のオプションを渡してしまうと、実行結果が意図しないものになるので注意
  * 即効性を買って利用する

### bash によるメタプログラミング

* shellscript と shebang
* chmod +x

### ファイルを操作する

* 特定の拡張子を持つ文字列を抜き出す
  * . はメタ文字なのでエスケープする
  * sed -n 自動的に出力しない /正規表現/p で 正規表現にマッチした行だけを出力
  * 複雑になれば、grep 以外に awk とかも知っておくべき
* ImageMagickでjpgをpngに変換する
  * time コマンドで処理時間を計測
  * xargs で -P オプションを使うとコマンドを並列実行する
  * nproc 利用可能なCPUの数
  * $(コマンド) => コマンド時間
  * GNU parallel （別途インストールが必要）
  * mogrify （ImageMagick に入っているコマンド）
* ファイル名の一括置換
  * rename コマンド
  * ls -U ソートを省略
  * s/^/0000/ 先頭に 0を4つつける
  * grep -v マッチしなかったものを出力する
  * awk sprintf フォーマットした文字列を返す関数
  * seq -w 0埋めする
* 特定ファイルの削除
  * grep -l ファイル名のみを表示
  * grep -R ファイルの中を検索の対象にして再帰的に読み込み
  * grep の並列化
    * xargs
    * ラインバッファ を気をつける必要がある
  * ripgrep という grep の代替
* awk と sed になれる
  * awk 列を$1,$2で使い分けることができる
  * printf 改行しない、print 改行する
  * tac 出力をひっくり返す
  * NR awk で行番号を取得
  * printf `%*s` *（数字）分になるように左に余白を入れる
  * tr -d 削除
  * sed :a ラベル付け, ta ラベルa に戻る, p print, d 残った文字列を削除

```
seq SYNOPSIS
     seq [-w] [-f format] [-s string] [-t string] [first [incr]] last

DESCRIPTION
     The seq utility prints a sequence of numbers, one per line (default), from first (default 1), to near last as
     possible, in increments of incr (default 1).  When first is larger than last the default incr is -1.
```

* ~ 演算子 => 正規表現と比較
* `cat ./sshellgei160/qdata/7/kakeibo.txt| awk '{tax = ($2~/^\*/)?1.08:1.1;print $0,tax}' | awk '{print int($3*$4)}' | awk '{a+=$1}END{print a}'`
* awk -F 列の区切り文字を空白やタブ空変更するもの
* NF number of fields
* awk でできるものを sed と grep -o でやってみる
* date 
  * -f- 入力から時刻を読み込む
  * -f ファイルから時刻を読み込む