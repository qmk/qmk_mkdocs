# コーディング規約 (Python)

<!---
  original document: 0.9.19:docs/coding_conventions_python.md
  git diff 0.9.19 HEAD -- docs/coding_conventions_python.md | cat
-->

私たちのスタイルの大部分は PEP8 に従いますが、神経質にならないように幾つかのローカルな変更を加えています。

* サポートされる全てのプラットフォームとの互換性のために、Python 3.6 を対象にしています。
* 4つのスペース (ソフトタブ) を使ってインデントします
* 充分なコメントを書くことを推奨します
   * コメントを機能を説明するストーリーと考えて下さい
   * 特定の決定がなされた理由を充分なコメントで説明してください。
   * 分かり切ったコメントは書かないでください
   * 分かり切ったコメントであるか確信できない場合は、コメントを含めてください。
* 全ての関数について、役に立つ docstring を必要とします。
* 一般的に、行を折り返さないで、必要なだけ長くすることができます。行を折り返すことを選択した場合は、76列を超えて折り返さないでください。
* 私たちの慣習の幾つかは、Python 使いでは無い人にコードベースをより身近にするために、python コミュニティに広まっているものとは競合しています。

# YAPF

コードを整形するために [yapf](https://github.com/google/yapf) を使うことができます。[setup.cfg](https://github.com/qmk/qmk_firmware/blob/master/setup.cfg) で設定を提供しています。

# インポート

`import ...` や `from ... import ...` をいつ使うかについての厳密なルールはありません。理解しやすさと保守性が究極の目的です。

一般的に、コードを短く理解しやすくするためにモジュールから特定の関数とクラス名をインポートする方が望ましいです。これにより、名前が曖昧になることがあります。代わりにモジュールをインポートするようにします。互換性のあるモジュールをインポートする時を除いて、インポートする時は "as" キーワードを避けるべきです。

インポートは各モジュール1行にする必要があります。標準的な python ルールに従って、インポート文をシステム、サードパーティ、ローカルにグループ化します。

`from foo import *` を使わないでください。代わりにインポートしたいオブジェクトのリストを指定するか、モジュール全体をインポートします。

## インポートの例

良い:

```
from qmk import effects

effects.echo()
```

悪い:

```
from qmk.effects import echo

echo()  # echoがどこから来たのかが不明瞭です
```

良い:

```
from qmk.keymap import compile_firmware

compile_firmware()
```

良いですが、上の方がより良いです:

```
import qmk.keymap

qmk.keymap.compile_firmware()
```

# 命令文

各行1文としてください。

可能な場合(例えば `if foo: bar`)でも、2つの文を1行にまとめないでください。

# 命名

`module_name`, `package_name`, `ClassName`, `method_name`, `ExceptionName`, `function_name`, `GLOBAL_CONSTANT_NAME`, `global_var_name`, `instance_var_name`, `function_parameter_name`, `local_var_name`.

関数名、変数名 およびファイル名は説明的でなければなりません; 略語を避けます。特に、プロジェクト外の読み手に曖昧あるいは馴染みのない略語を使わず、単語内の文字を削除して略さないでください。

常に .py のファイル名の拡張子を使います。ダッシュを使わないでください。

## 避けるべき名前

* カウンタあるいはイテレータ以外の1文字の名前。try/except 文では例外の識別子として `e` を使うことができます。
* パッケージ/モジュール名内のダッシュ (`-`)
* `__double_leading_and_trailing_underscore__` (2つのアンダースコアで始まる名前と終わる名前、Python で予約済み)

# Docstring

docstring の一貫性を維持するために、以下のガイドラインを設定しました。

* マークダウン(Markdown)形式の使用
* 常に少なくとも1つの改行を含む3つのダブルクォートの docstring を使ってください: `"""\n"""`
* 最初の行は、関数が行うことの短い (70文字未満) 説明です。
* docstring が更に必要な場合は、説明と残りの間に空白行を入れます。
* 開始の3つのダブルクォートと同じインデントレベルでインデント行を始めます
* 以下で説明する形式を使って全ての関数の引数について記述します
* Args:、Returns: および Raises: が存在する場合、それらは docstring の最後の3つの要素で、それぞれ空白行で区切られなければなりません。

## 簡単な docstring の例

```
def my_awesome_function():
    """1970 Jan 1 00:00 UTC からの秒数を返します。
    """
    return int(time.time())
```

## 複雑な docstring の例

```
def my_awesome_function():
    """1970 Jan 1 00:00 UTC からの秒数を返します。

    この関数は常に整数の秒数を返します。
    """
    return int(time.time())
```

## 関数の引数の docstring の例

```
def my_awesome_function(start=None, offset=0):
    """1970 Jan 1 00:00 UTC からの秒数を返します。

    この関数は常に整数の秒数を返します。


    Args:
        start
            1970 Jan 1 00:00 UTC の代わりの開始時間

        offset
            最初の引数からこの秒数が引かれた答えを返します

    Returns:
        秒数を表す整数。

    Raises:
        ValueError
            `start` あるいは `offset` が正の数ではない場合
    """
    if start < 0 or offset < 0:
        raise ValueError('start and offset must be positive numbers.')

    if not start:
        start = time.time()

    return int(start - offset)
```

# 例外

例外は例外的な状況を処理するために使われます。フローの制御のために使われるべきではありません。これは Python の「許しを請う」という規範からの逸脱です。例外をキャッチする場合、異常な状況を処理する必要があります。

何らかの理由で全ての例外のキャッチを使う場合は、cli.log を使って例外とスタックトレースを記録する必要があります。

try/except ブロックをできるだけ短くします。多数の try 文が必要な場合は、コードを再構成する必要があるかもしれません。

# タプル

1項目のタプルを定義する場合、タプルを使用していることが明らかになるように、常に末尾のカンマを含めます。暗黙的な1項目のタプルのアンパックに頼らないでください。明確なリストを使う方が良いです。

これはよく使用される printf 形式の書式文字列を使う場合に、特に重要です。

# リストと辞書

シーケンス形式と末尾のカンマとを区別するように YAPF を設定しました。末尾のカンマが省略されると、YAPF はシーケンスを1つの行として整形します。末尾のカンマがある場合、YAPF はシーケンスを1行1項目で整形します。

一般的に1行が短い定義になるようにすべきです。読みやすさと保守性を向上させるために、後からではなく早めに複数の行を分割してください。

# 括弧

過度な括弧は避けますが、括弧を使ってコードを理解しやすくします。タプルを明示的に返すか、あるいは数式の一部である場合を除き、return 文で括弧を使わないでください。

# 書式文字列

一般的に printf 形式の書式文字列を用います。例:

```
name = 'World'
print('Hello, %s!' % (name,))
```

このスタイルはログモジュールで使われており、私たちはそれを広範囲で利用しており、一貫性を保つために他の場所でも採用しています。これは、私たちの気まぐれな読者の大部分である C プログラマにもおなじみのスタイルです。

付属の CLI モジュールは、パーセント (%) 演算子を使わずにこれらを使うことをサポートしています。詳細は、`cli.echo()` と様々な `cli.log` 関数 (例えば、`cli.log.info()`) を見てください。

# 内包表記とジェネレータ表記

内包表記とジェネレータの自由な使用を推奨しますが、あまりに複雑にしないでください。複雑になる場合は、理解しやすい for ループで代替します。

# ラムダ

使っても問題ありませんが、おそらく避けるべきです。内包表記とジェネレータを使えば、ラムダの必要性は以前ほど強くありません。

# 条件式

変数の割り当てでは問題ありませんが、そうでなければ避けるべきです。

条件式はコードに続く if 文です。例えば:

```
x = 1 if cond else 2
```

一般にこれらを関数の引数、シーケンス項目などとして使用することはお勧めできません。見落としやすくなります。

# デフォルト引数

推奨されていますが、値は不変オブジェクトでなければなりません。

デフォルト値に引数リストを指定する場合は、その場で変更できないオブジェクトを指定するように常に注意してください。可変オブジェクトを使うと変更は呼び出しの間で持続しますが、これは通常あなたの望むものではありませんそれがあなたのやろうとしていることであっても、他の人にとっては混乱するもので理解を妨げます。

悪い:

```
def my_func(foo={}):
    pass
```

良い:

```
def my_func(foo=None):
    if not foo:
        foo = {}
```

# プロパティ

getter および setter 関数の代わりにプロパティを常に使います。

```
class Foo(object):
    def __init__(self):
        self._bar = None

    @property
    def bar(self):
        return self._bar

    @bar.setter
    def bar(self, bar):
        self._bar = bar
```

# True/False の評価

一般的に、if 文で等価性を調べるのではなく、暗黙的な True/False 評価を行うべきです。

悪い:

```
if foo == True:
    pass

if bar == False:
    pass
```

良い:

```
if foo:
    pass

if not bar:
    pass
```

# デコレータ

適切な時に使ってください。理解に役立つ時を除き、魔法の(ように見える技巧の)使いすぎは避けるようにしてください。

# スレッドとマルチプロセス

避けるべきです。これが必要な場合は、私たちがコードをマージする前に十分な理由を述べる必要があります。

# 強力な機能

Python は非常に柔軟な言語で、独自のメタクラス、バイトコードへのアクセス、実行中コンパイル、動的な継承、オブジェクトの親の変更、インポートハック、リフレクション、システム内部の変更など、多くの素晴らしい機能を提供します。

これらを使わないでください。

パフォーマンスは私たちにとって重要な関心ごとではなく、コードのわかりやすさに関心があります。私たちは、コードベースを1日か2日しかいじっていない人が利用できるようにしたいです。これらの機能は一般的に理解のしやすさを犠牲にするため、より高速あるいはよりコンパクトなコードよりも、容易に理解できるコードの方が望ましいです。

一部の標準ライブラリモジュールはこれらの手法を使っており、これらのモジュールを利用しても問題ありません。ただし、それらを使う時には、読みやすさと理解のしやすさを忘れないでください。

# 型アノテーション付きコード

今のところ型アノテーションシステムを使っていないため、コードにアノテーションをつけないようにしてください。将来的にはこれを再検討する可能性があります。

# 関数の長さ

小さくて焦点のあった関数にしてください。

長い関数が時には適切であることを理解しているので、関数の長さには厳密な制限はありません。関数が約40行を超える場合は、プログラムの構造を損なわずに分割できるかどうかを検討してください。

今のところ長い関数が完全に機能するとしても、数か月でそれを変更する人が新しい挙動を追加するかもしれません。これにより見つけにくいバグが発生するかもしれません。関数を短くかつシンプルにすることで、他の人がコードを読んで修正しやすくします。

幾つかのコードで作業をすると、長く複雑な関数を見つけるかもしれません。既存コードを変更することを怖がらないでください: もし、難しいことが判明したり、エラーがデバッグしづらいとわかったり、いくつかの異なるコンテキストで一部を使いたいような関数を扱っている場合、関数を小さくてより扱いやすい単位に分割することを検討してください。

# FIXME

FIXME をコードに残しても構いません。なぜでしょうか？このコードを文章化しないままにするよりも、少なくとも考え抜く必要がある(あるいは混乱している)コードの一部を文章化するように奨励する方が、このコードを文章化しないままにするよりも良いです。

全ての FIXME は以下のように書式化されるべきです:

```
FIXME(username): 何々機能が完了したらこのコードを再検討する。
```

...username はあなたの GitHub のユーザ名です。

# テスト

統合テストと単体テストの組み合わせを使ってコードが可能な限りバグが無いようにします。全てのテストは `lib/python/qmk/tests/` にあります。`qmk pytest` を使って全てのテストを実行することができます。

これを書いている時点では、テストは全く完全なものではありません。現在のテストを見て、テストされていない状況のための新しいテストケースを書くことは、コードベースに精通し、QMK に貢献するという両方の点で素晴らしい方法です。

## 統合テスト

統合テストは `lib/python/qmk/tests/test_cli_commands.py` にあります。ここで実際に CLI コマンドが実行され、全体的な動作が検証されます。[`subprocess`](https://docs.python.org/3.6/library/subprocess.html#module-subprocess) を使って各 CLI コマンドを起動し、正しく動作するかを判断するために出力とリターンコードの組み合わせを使います。

## ユニットテスト

`lib/python/qmk/tests/` 内の他の `test_*.py` ファイルはユニットテストを含みます。`lib/python/qmk/` 内の個々の関数のテストをここに書くことができます。一般的にこれらのファイルはモジュールに基づいて名前を付けられ、ドットはアンダースコアで置き換えられます。

これを書いている時点では、テストのためのモックを作っていません。これを変更する手伝いをしたい場合は、[issue を開く](https://github.com/qmk/qmk_firmware/issues/new?assignees=&labels=cli%2C+python&template=other_issues.md&title=) か [Discord の #cli に参加](https://discord.gg/heQPAgy)し、そこで会話を開始してください。
