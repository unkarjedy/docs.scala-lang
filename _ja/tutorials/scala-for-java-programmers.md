---
layout: singlepage-overview
title: JavaプログラマーのためのScalaチュートリアル

partof: scala-for-java-programmers

discourse: false
language: ja
---

原文 Michel Schinz and Philipp Haller<br>
翻訳 TATSUNO Yasuhiro

## はじめに

この文書は、Scala 言語とコンパイラを手短に紹介します。
ある程度プログラミング経験を持ち、Scala で何ができるかについて概要を知りたい方向けです。
オブジェクト指向プログラミング、とりわけ Java についての基本的な知識を前提とします。

## 最初の例

最初の例として、基本的な **Hello World** プログラムを用います。
興味をそそられるものではありませんが、言語についてほとんど知らなくてもScala ツールの使い方を実演しやすいです。
こんな感じです:

    object HelloWorld {
      def main(args: Array[String]): Unit = {
        println("Hello, world!")
      }
    }

このプログラムの構造は、Java プログラマにはなじみ深いはずです。
`main` と呼ばれるメソッドがあり、それはパラメータとしてコマンドライン引数（文字列の配列）を受け取ります。
このメソッドの本体は、事前に定義されたメソッド `println` に友好的な挨拶を引数にして、1回だけ呼び出しています。
`main` メソッドは値を返しません（手続きメソッド）。
そのため、戻り値の型を宣言する必要はありません。

Java プログラマにあまりなじみがないのは、`main` メソッドを含む `object` という宣言です。
Scala はそのような宣言によって、一般に**シングルトンオブジェクト**として知られる、インスタンスを１つだけ有するクラスを取り入れています。
そのため先の宣言は、`HelloWorld` という名前のクラスと、そのクラスのただ1つのインスタンス（これまた `HelloWorld` という名前）両方を宣言しています。
このインスタンスは必要に応じ、初めて使われるときに生成されます。

鋭い読者なら `main` メソッドが `static` として宣言されていないことに気づいたかもしれません。
これは Scala には static メンバー（メソッドまたはフィールド）が存在しないからです。
static メンバーを定義するよりも、Scala プログラマーはシングルトンオブジェクトにそれらメンバーを宣言します。

### 例をコンパイルする

先の例をコンパイルするために、Scala コンパイラー `scalac` を使います。
`scalac` はほとんどのコンパイラーと同様に動きます。
ソースファイルといくつかのオプションを引数として受け取り、１つか複数のオブジェクトファイルを作ります。
作られるオブジェクトファイルは、標準的な Java クラスファイルです。

上記のプログラムを `HelloWorld.scala` というファイルに保存したら、次のコマンドを発行することでコンパイルできます
（大なり記号 `>` はシェルプロンプトを表しており、入力しないでください）。

    > scalac HelloWorld.scala

これにより、カレントディレクトリに数個のクラスファイルが生成されます。
そのひとつは `HelloWorld.class` というもので、`scala` コマンドを使って直接実行可能（次の章で説明）なクラスを含んでいます。

### 例を実行する

一度コンパイルされると、Scala プログラムは `scala` コマンドを使って実行できます。
その使い方は、Java プログラムの実行に使われる `java` コマンドとよく似ており、同じオプションを受け入れます。
上の例は以下のコマンドを使って実行でき、期待される結果を生成します。

    > scala -classpath . HelloWorld

    Hello, world!

## Java とのインタラクション

Scala の強みの１つは、Java コードと相互作用するのがとても簡単なことです。
`java.lang` パッケージのすべてのクラスは初期設定でインポートされています。
他のクラスは明示的にインポートされる必要があります。

これを実演する例を見てみましょう。
現在の日付を取得し、特定の国たとえばフランスの慣例に従って書式設定したいとします
（他の地域、例えばスイスのフランス語を話す範囲も同じ慣例を使います）。

Java のクラスライブラリは、強力なユーティリティクラス、例えば `Date` や `DateFormat` を定義しています.
Scala は Java とシームレスに相互運用できるので、Scala クラスライブラリに同等のクラスを実装する必要はありません。
対応する Java パッケージのクラスをただインポートするだけです。

    import java.util.{Date, Locale}
    import java.text.DateFormat._

    object FrenchDate {
      def main(args: Array[String]): Unit = {
        val now = new Date
        val df = getDateInstance(LONG, Locale.FRANCE)
        println(df format now)
      }
    }

Scala のインポート文は Java と同等のものととてもよく似ていますが、ずっと強力です。
1行目にあるように、波括弧で囲むことで同じパッケージから複数のクラスをインポートできます。
また別の違いとして、パッケージやクラスのすべての名前をインポートするときは、アスタリスク `*` の代わりにアンダースコア文字 `_` を使います。
というのもアスタリスクは、あとで見るように Scala の識別子（例えばメソッド名）として有効だからです。

それゆえ2行目のインポート文は、`DateForat` クラスのすべてのメンバーをインポートします。
これによって static メソッド `getDateInstance` や static フィールド `LONG` が直接利用可能になります。

`main` メソッドの中では、まず Java の `Date` クラスのインスタンスを作成します。
このインスタンスはデフォルトで現在の日時を含んでいます。
次に、先ほどインポートした static の `getDateInstance` メソッドを使って日付の書式を定義します。
最後に、ローカライズされた `DateFormat` インスタンスによって書式設定された現在の日付を表示します。
この最後の行は Scala の構文の興味深い特性を表しています。
1引数メソッドには、中置（infix）記法を用いることができます。
つまり、式

    df format now

は、次の式

    df.format(now)

よりも少しだけ冗長でなくなった書き方です。

これは大したことのない構文上の詳細に思えるかもしれませんが、重要な影響をもたらします。
その１つについては次の節で詳しく取り上げます。

Java との相互作用についての本節を終える前に、Scala では Java クラスを継承したり、Java インターフェースを実装できることも記しておきます。

## すべてがオブジェクト

Scala は、数値や関数を含む**あらゆるもの**がオブジェクトであるという意味で、純粋なオブジェクト指向言語です。
この点において、プリミティブ型（例えば `boolean` や `int`）を参照型と区別する Java とは異なります。

### 数値はオブジェクト

数値もまたオブジェクトなのでメソッドを持っています。
実のところ、以下のような数値計算

    1 + 2 * 3 / x

は、メソッド呼び出しのみから成り立っています。
というのもこの式は、前節で見たように以下の式に相当するからです。

    1.+(2.*(3)./(x))

これは `+` や `*` などが Scala では識別子として有効であることを意味します。

### 関数はオブジェクト

Scala では関数もオブジェクトです。
それゆえ関数を引数として渡すこと、関数を変数に格納すること、他の関数から関数を返すことができます。
関数を値として操作できるこの能力は、**関数プログラミング**と呼ばれるとても興味深いプログラミングパラダイムにとって不可欠なものの1つです。

関数を値として使えることが便利であるとてもシンプルな例として、タイマー関数を考えてみましょう。
それは毎秒何らかの動作を実行するためのものです。
実行したい動作をどうやって渡せるでしょうか？
当然、関数としてです。
関数渡しのとてもシンプルな例は、多くのプログラマーになじみ深いはずです。
ユーザーインターフェースのコードで、何かイベントが起きたら呼ばれるコールバック関数を登録するのに、よく用いられています。

次のプログラムでは、`oncePerSecond` というタイマー関数がコールバック関数を引数として受取ります。
この関数の型は `() => Unit` 、つまり、引数を受け取らず何も返さない（`Unit` 型は C/C++/Java の `void` と同様）すべての関数を表す型です。
このプログラムの `main` 関数は、ターミナルに文を印字するコールバックを与えてタイマー関数を呼ぶだけのものです。
つまりこのプログラムは延々と `"time flies like an array"` という文を毎秒印字します。

    object Timer {
      def oncePerSecond(callback: () => Unit): Unit = {
        while (true) { callback(); Thread sleep 1000 }
      }
      def timeFlies(): Unit = {
        println("time flies like an arrow...")
      }
      def main(args: Array[String]): Unit = {
        oncePerSecond(timeFlies)
      }
    }

文字列を印字するために使われているのが事前定義された `println` メソッドで、`System.out` の `println` ではないことに注意してください。

#### 匿名関数

このプログラムは理解しやすいですが、もう少し洗練できます。
まず第一に、関数 `timeFlies` はあとで `oncePerSecond` 関数に渡すためだけに定義されていることに気づきます。
たった一度しか使われない関数に名前をつけるのは不必要に思えるので、要は `oncePerSecoond` に渡す関数を作れさえすればよいでしょう。
Scala では、まさにそんな名前を持たない関数、**匿名関数**を使えます。
`timeFlies` の代わりに匿名関数を使って改正したタイマープログラムはこんな風になります。

    object TimerAnonymous {
      def oncePerSecond(callback: () => Unit): Unit = {
        while (true) { callback(); Thread sleep 1000 }
      }
      def main(args: Array[String]): Unit = {
        oncePerSecond(() =>
          println("time flies like an arrow..."))
      }
    }

この例で匿名関数が登場することは、関数の引数リストと本体を分ける右矢印 `=>` から分かります。
この例では、引数リストは空で、矢印の左にある中身のない括弧によって表されています。
関数の本体は上記の `timeFlies` のものと同じです。

## クラス

上で見てきたように、Scala はクラスという概念を持っているように、オブジェクト指向言語です
（正確には、クラスという概念のないオブジェクト指向言語もありますが、Scala はそうではありません）。
Scala におけるクラスは Java の構文に近い構文を使って宣言されます。
1つ重要な違いは、Scala におけるクラスはパラメータを持つことができることです。
これは以下の素数の定義に示されています。

    class Complex(real: Double, imaginary: Double) {
      def re() = real
      def im() = imaginary
    }

この `Complex` クラスは2つの引数、素数の実部と虚部を受け取ります。
これら引数は `Complex` クラスのインスタンスを作成するときに必ず、`new　Complex(1.5, 2.3)` というように渡さなければなりません
このクラスは2つのメソッド `re` と `im` を持ち、これら2つの部分へアクセスできるようにします。

これら2つのメソッドの戻り値の型が明示されていないことに注意してください。
コンパイラによって自動で推論されます。
コンパイラはこれらメソッドの右側を調べ、どちらも `Double` 型の値を返すと推論します。

コンパイラはここでのように型をいつでも推論できるわけではありません。
残念ながらいつできて、いつできないかを正確に分かる簡単な規則はありません。
実際には、コンパイラは明示的に与えられていない方を推論できないときに訴えてくるので、あまり問題になりません。
簡単な規則として、初心者 Scala プログラマーは、文脈から推定しやすそうな型宣言を省略してみてコンパイラーが認めてくれるか試してみてください。
しばらくすれば、いつ型を省略するか、いつ明示的に指定するか、良い感触をつかむはずです。

### 引数なしのメソッド

`re` と `im` メソッドにはちょっとした問題があります。
次の例が示すように、それらを呼ぶには名前のあとに中身のない括弧を置かねばなりません。

    object ComplexNumbers {
      def main(args: Array[String]): Unit = {
        val c = new Complex(1.2, 3.4)
        println("imaginary part: " + c.im())
      }
    }

実部と虚部があたかもフィールドのようにアクセスできたらすばらしいですね。
Scala では完全にこれが可能で、**引数なしのメソッド**としてそれらを定義するだけです。
そのようなメソッドは、名前のあとに括弧を持たない（定義場所でも使用場所でも）という点で、0引数のメソッドと区別されます。
`Commplex` クラスは次のように書き直せます。

    class Complex(real: Double, imaginary: Double) {
      def re = real
      def im = imaginary
    }


### 継承とオーバーライド

Scala におけるすべてのクラスはスーパークラスを継承します。
前節の `Complex` の例のように、スーパークラスが指定されていないときは、暗黙的に `scala.AnyRef` が使われます。

Scalaでは、スーパークラスから継承されたメソッドをオーバーライドできます。
予想外のオーバーライドを防ぐため、メソッドがオーバーライドしていることを`override` 修飾子を使って明に指定することが必須です。
その例として、`Object` から継承した `toString` メソッドの再定義を `Complex` クラスに追加してみます。

    class Complex(real: Double, imaginary: Double) {
      def re = real
      def im = imaginary
      override def toString() =
        "" + re + (if (im < 0) "-" else "+") + im + "i"
    }


## ケースクラスとパターンマッチ

プログラムによく現れる一種のデータ構造は、木（訳注：ツリー）です。
例えば、インタープリターやコンパイラーは通例、プログラムを木として内部的に表現します。
XMLドキュメントもツリーです。
何種類ものコンテナーが木、例えば赤黒木をベースとしています。

そのような木が Scala ではどのように表現され、操作されるかを、小さい計算機プログラムを通じて見ていきます。
このプログラムの目的は、足し算と整数の定数と変数からなる、とても単純な演算式の操作です。
`1+2` や `(x+x)+(7+y)` は、そのような式の2つの例です。

はじめにそのような式の表現を決めなければいけません。
もっとも自然なのは木構造で、ノードは操作（ここでは加算）、葉は値（ここでは定数か変数）です。

Java では、そのような木構造はそのための抽象スーパークラス、ノードと葉それぞれの具象クラスで表現されるでしょう。
関数プログラミング言語では、そのような目的には代数的データ型を用います。
Scala は、それら2つの中間のような**ケースクラス**という概念を提供します。
ここでは、ケースクラスが例題のツリー型を定義するためにどう使えるかを示しています。

    abstract class Tree
    case class Sum(l: Tree, r: Tree) extends Tree
    case class Var(n: String) extends Tree
    case class Const(v: Int) extends Tree

クラス `Sum`、`Var`、`Const` がケースクラスとして宣言されているということは、それらが通常のクラスといくつかの点で違うことを意味します。

- クラスインスタンスを作るにあたり `new` キーワードは必須ではありません
  （例えば、`new Const(5)` の代わりに `Const(5)` と書けます)
- コンストラクターパラメーターに対するゲッター関数は自動で定義されます
  （つまり、クラス `Const` のインスタンス `c` のコンストラクターパラメーター `v` の値を `c.v` と書くだけで取得できます）
- メソッド `equals` と `hashCode` のデフォルト定義が提供されます。
  それらはインスタンスの同一性ではなく**構造**をもとに動きます。
- メソッド `toString` のデフォルト定義が提供されます。
  「ソースの外」で値を印字します（つまり、式 `x+1` のツリーは `Sum(Var(x),Const(1))` を印字します)
- これらクラスのインスタンスは、以下で見るようにパターンマッチを使って分解できます。

算術式を表すデータ型を定義できたので、それらを操作する演算の定義を始められます。
ある**環境**における式を評価する関数から始めます。
環境の目的は、変数に値を与えるためです。
例えば、値 `5` が変数 `x` に結び付けられた環境を`{ x -> 5 }` と表すとして、そこでは式 `x+1` は `6` という結果を出します。　

そのため環境を表す方法を見つけなければいけません。
もちろん、ハッシュテーブルのような連想データ構造を使うことができますが、関数を直接使うこともできます！
環境とは実のところ値を（変数の）名前に結びつける関数に過ぎません。
上記の環境 `{ x -> 5 }` は Scala ではシンプルに書けます。

    { case "x" => 5 }

この記法は、引数として文字列 `"x"` を受けとったら整数 `5` を返し、それ以外のときは例外とともに失敗する関数を定義します。

評価関数を書く前に、環境の型に名前を付けましょう。
もちろん環境には型 `String => Int` をいつでも使えますが、この型に名前を付けておけばプログラムが単純になり、将来変更しやすくなります。
これは Scala では次の記法で達成できます。

    type Environment = String => Int

以降、型 `Environment` は `String` から `Int` への関数の型の別名（訳注：エイリアス）として使えます。

これで評価関数の定義を与えることができます。
概念上はとても簡単です。
2式の和の値は、単にそれら式の値の和です。
変数の値は環境から直接得られます。
そして定数の値はそれ自身です。
Scala でこれを表すと、これ以上難しくならないでしょう。

    def eval(t: Tree, env: Environment): Int = t match {
      case Sum(l, r) => eval(l, env) + eval(r, env)
      case Var(n)    => env(n)
      case Const(v)  => v
    }

この評価関数は、木 `t` に**パターンマッチ**を実行することで動作します。
直感的に、上記定義の意味は明らかです。

1. まず `t` が `Sum` であるかをチェックします。
   もしそうであれば、左部分木を変数 `l` 、右部分木を変数 `r` に束縛します。
   それから矢印に続く式の評価を進めます。
   この式では矢印の左側に現れるパターンに束縛された変数 `l` と `r` を使うことができ、実際にそうしています。
2. もし最初のチェックが成功しなければ、つまり木が `Sum` でなければ、続けて `t` が `Var` であるかチェックします。
   もしそうであれば、`Var` ノードに含まれる名前を変数 `n` に束縛し、右側の式に進みます。
3. もし2つ目のチェックが失敗すれば、つまり `t` が `Sum` でも `Var` でもなければ、`Const` であるかどうかチェックします。
   もしそうであれば、`Const` ノードに含まれる値を変数 `v` に束縛し、右側の式に進みます。
4. 最後に、すべてのチェックが失敗すれば、パターンマッチ式の失敗を知らせるために例外が発生します。
   これは `Tree` のサブクラスが他にも宣言されているときにのみ起こりえます。

パターンマッチの基本的なアイデアを見てきました。
それは連続するパターンを値にマッチさせてみて、パターンがマッチすると値を抽出してその各部に名前をつけて、通常それら名前のついた部分を活用する何らかのコードを最終的に評価するものです。

経験豊富なプログラマであれば、`Tree` クラスやサブクラスの**メソッド**として `eval` を定義しなかったことを不思議がっているかもしれません。
Scala は普通のクラスと同様にケースクラスにメソッド定義を許しているので、実際にはそうすることもできました。
それゆえ、パターンマッチを使うかメソッドを使うかを決めるのは好みの問題ではありますが、拡張性について重要な示唆があります。

- メソッドを使うときは、`Tree` サブクラスを定義するだけでできるので、新種のノードを足すのは簡単です。
  一方で、木を操作する新しい処理を追加するのは、`Tree` の全サブクラスの変更が必要になってしまうので、面倒です。
- パターンマッチを使うときは、状況が逆になります。
  新種のノード追加は、木に対するパターンマッチを使ったすべての関数で、新しいノードを考慮した修正を必要とします。
  一方で、新しい処理を追加するのは、独立した関数を定義するだけなので簡単です。

パターンマッチについてさらに知るために、演算式に対する別の処理、数式微分を定義してみましょう。
この処理についての次のような規則をご存知かもしれません。

1. 和の微分係数は、微分係数の和です。
2. 何らかの変数 `v` の微分係数は、変数 `v` に対して微分されるなら1、そうでなければ0です。
3. 定数の微分係数は0です。

これら規則はほぼ文字通り Scala コードに翻訳でき、次の定義が得られます。

    def derive(t: Tree, v: String): Tree = t match {
      case Sum(l, r) => Sum(derive(l, v), derive(r, v))
      case Var(n) if (v == n) => Const(1)
      case _ => Const(0)
    }

この関数はパターンマッチに関連して2つの新しい概念を紹介しています。
はじめに、変数への `case` 式が、`if` キーワードに続く式、**ガード**を持っています。
このガードは、その式が真でない限りパターンマッチが成功するのを防ぎます。
ここでのガードの使用は、微分される変数の名前が微分の変数 `v` と同じときにだけ、定数`1` を返すことを保証するためです。
ここで使われているパターンマッチの新しい特徴2つ目は、`_` で書かれている**ワイルドカード**です。
それはどんな値にもマッチして、名前をつけないパターンです。

パターンマッチの全力をまだ探っていませんが、この文章を短くするためにここで止めておきます。
実例について上の2つの関数がどのように実行するかをまだ見たいですね。
そのために、式 `(x+x)+(7+y)` にいくつかの処理を実行する簡単な `main` 関数を書いてみましょう。
まず環境における値 `{ x -> 5, y -> 7 }` を計算し、次に `x` そして `y` についての微分係数を計算します。

    def main(args: Array[String]): Unit = {
      val exp: Tree = Sum(Sum(Var("x"),Var("x")),Sum(Const(7),Var("y")))
      val env: Environment = { case "x" => 5 case "y" => 7 }
      println("Expression: " + exp)
      println("Evaluation with x=5, y=7: " + eval(exp, env))
      println("Derivative relative to x:\n " + derive(exp, "x"))
      println("Derivative relative to y:\n " + derive(exp, "y"))
    }

コンパイルする前に、`Environment` 型、`eval`、`derive` そして `main` メソッドを `Calc` オブジェクトで包む必要があります。
このプログラムを実行することで、期待する結果が得られます。

    Expression: Sum(Sum(Var(x),Var(x)),Sum(Const(7),Var(y)))
    Evaluation with x=5, y=7: 24
    Derivative relative to x:
     Sum(Sum(Const(1),Const(1)),Sum(Const(0),Const(0)))
    Derivative relative to y:
     Sum(Sum(Const(0),Const(0)),Sum(Const(0),Const(1)))

出力を観察することで、微分係数の結果はユーザーに示されるより前にシンプルにすべきことに気づきます。
パターンマッチを使った基本的なシンプル化の定義は興味深い（ですが驚くほど一筋縄ではいかない）問題なので、読者への練習問題として残しておきます。

## トレイト

スーパークラスからコードを継承するのとは別に、Scala クラスは1つ以上の**トレイト**からコードを取り込めます。

おそらく Java プログラマーにとってトレイトを理解するもっとも簡単な方法は、コードを含むことができるインターフェースとしてとらえることでしょう
Scala では、クラスがトレイトから継承するとき、トレイトのインターフェースを実装し、トレイトに含まれるコードをすべて継承します。

（注：Java 8 からは、Java インターフェースも同様にコードを含めるようになりました。`default` キーワードをつけるか、または static メソッドとして)

トレイトの有用性を見るため、古典的な例、順序付きオブジェクトを見てみましょう。
あるクラスのオブジェクト同士を順序比較できると、例えば並び替え（ソート）できて、便利なことが多いです。
Java では、比較できるオブジェクトは `Comparable` インターフェースを実装します。
Scala では、`Comparable` 相当を `Ord` という名前のトレイトとして定義することで、Java よりも少しだけ良くなっています。

オブジェクトを比較するとき、6つの異なる述語（より小さい、より小さいか等しい、等しい、等しくない、より大きいか等しい、より大きい）が役に立ちます。
しかし、これらすべてを定義するのは、これら6つのうち4つは残りの2つを使って表現できるので、退屈です。
つまり、例えば「等しい」と「より小さい」の述語があれば、他を表現できるということです。
Scala では、これらの観察結果は以下のトレイト宣言にうまくとらえられます。

    trait Ord {
      def < (that: Any): Boolean
      def <=(that: Any): Boolean =  (this < that) || (this == that)
      def > (that: Any): Boolean = !(this <= that)
      def >=(that: Any): Boolean = !(this < that)
    }

この定義は、Java の `Comparable` インターフェースと同じ働きをする `Ord` という新しい型と、3つの述語のデフォルト実装を4つ目の抽象述語を使うことで作成しています。
等式と不等式の述語はすべてのオブジェクトにデフォルトで存在するので、ここには現れていません。

上で使われた型 `Any` は、Scala における `Any` 以外すべてのクラスのスーパー型です。
Java の `Object` 型のより一般的なものとしてとらえられます。
というのも `Int` や `Float` など基本型のスーパー型でもあるからです
（訳注：Java では `int` や `float` などのプリミティブ型は `Object` ではない）。

よって、あるクラスのオブジェクトを比較可能にするには、相等・不等を検査する述語を定義し、上の `Ord` クラスを混ぜ合わせれば十分です。
例として、グレゴリオ暦の日付を `Date` クラスを定義してみましょう。
その日付は、日、月、年から構成され、それぞれ整数で表しましょう。
そのため、`Date` クラスの定義は次のように始めます。

    class Date(y: Int, m: Int, d: Int) extends Ord {
      def year = y
      def month = m
      def day = d
      override def toString(): String = year + "-" + month + "-" + day

ここで重要なのは、クラス名とパラメータのあとに続く `extends Ord` という宣言です。
`Date` クラスが `Ord` トレイトを継承していることを宣言しています。

次に、日付がそれらの個々のフィールドで適切に比較されるように、`Object` から継承された `equals` メソッドを再定義します。
Java のデフォルト実装はオブジェクトを物理的に比較するので、`equals` のデフォルト実装は使えません。
次の定義に到着します。

    override def equals(that: Any): Boolean =
      that.isInstanceOf[Date] && {
        val o = that.asInstanceOf[Date]
        o.day == day && o.month == month && o.year == year
      }

このメソッドはあらかじめ定義されたメソッド `isInstanceOf` と `asInstanceOf` を使っています。
1つ目の `isInstanceOf` は Java の `instanceof` 演算子に相当し、メソッドを適用したオブジェクトが与えられた型のインスタンスであるときに限り、true を返します。
2つ目の `asInstaceOf` は Java のキャスト演算子に相当し、オブジェクトが与えられた型のインスタンスであればその型としてみなせるようにし、そうでなければ `ClassCastException` を発生させます。

最後に、最後に定義するメソッドは、次のとおり下級かどうかを調べる述語です。
`scala.sys` パッケージオブジェクトの別のメソッド `error` を使っており、これは与えられたエラーメッセージつきの例外を発生させます。

    def <(that: Any): Boolean = {
      if (!that.isInstanceOf[Date])
        sys.error("cannot compare " + that + " and a Date")

      val o = that.asInstanceOf[Date]
      (year < o.year) ||
      (year == o.year && (month < o.month ||
                         (month == o.month && day < o.day)))
    }

これで `Date` クラスの定義は完成です。
このクラスのインスタンスは、日付としても比較可能なオブジェクトとしても見ることができます。
さらに、それらはすべて上で述べた6つの比較述語を定義しています。
`equals` と `<` は `Date` クラスの定義に直接現れているから、他はそれらが `Ord` トレイトから継承されているからです。

ここで示した以外の状況でもトレイトはもちろん便利ですが、その応用を長々と説明するのはこの文章の範囲外です。

## ジェネリクス

（訳注：原文では genericity が使われていますが、日本語ではジェネリクスという用語が広く使われているので、ジェネリクスとします）

このチュートリアルで探る Scala の最後の特徴はジェネリクスです。
Java プログラマーであれば、Java 言語におけるジェネリクスの欠如がもたらした問題、Java 5 で対応された欠点について、承知しているはずでしょう。

ジェネリクスとは、型によってパラメータ化されたコードを書ける能力です。
例えば、連結リストのライブラリを書くプログラマーは、リストの要素にどのような型を与えるべきかという問題に直面します。
このリストは多くの様々な文脈で使われはずなので、要素の型が例えば `Int` であると決めつけることはできません。
これは完全に恣意的で、あまりにも抑圧的です。

Java プログラマーは、すべてのオブジェクトのスーパー型 `Object` の使用に頼ります。
しかしこの解決方法は理想からかけ離れています。
というのも基本型（`int`、`long`、`float`など）では動かないし、プログラマー自身によって動的な型キャストをたくさん挿入することになるからです。

Scala は、ジェネリッククラス（とメソッド）を定義できるようにすることで、この問題を解決しています。
もっとも単純なコンテナークラス（空、または何らかの型のオブジェクトを指し示す、参照）を例に見てみましょう。

    class Reference[T] {
      private var contents: T = _
      def set(value: T) { contents = value }
      def get: T = contents
    }

クラス `Reference` は、その要素の型である `T` という型でパラメーター化されています。
この型は `contents` 変数の型として、`set` メソッドの引数の型として、`get` メソッドの戻り値の型として、クラス本体で使われています。

上のコード例は、Scala における変数を紹介しましたが、さらなる説明は必要ないでしょう。
とはいえ、デフォルトの値を表す `_` によって変数の初期値を与えられることは興味深いでしょう。
このデフォルト値は、数値型については0、`Boolean` 型には `false`、`Unit` 型には `()`、すべてのオブジェクト型には `null` です。

`Reference` クラスを使うには、型パラメーター `T` つまり `cell` によって格納される要素の型について、どの型を使うか宣言する必要があります、
例えば、整数を保持する `cell` を作成して使うには、以下のように書くことができます。

    object IntegerReference {
      def main(args: Array[String]): Unit = {
        val cell = new Reference[Int]
        cell.set(13)
        println("Reference contains the half of " + (cell.get * 2))
      }
    }

この例から分かるように、`get` メソッドから返って来た値を整数として使う前に、キャストする必要はありません。
この特定のセルは整数を保持すると宣言されているので、整数以外を格納できません。

## 結び

この文書は Scala 言語の短い概要を説明し、基本的なコード例を示しました。
興味のある読者は、例えば、さらなる説明やコード例がある **[Scala ツアー](https://docs.scala-lang.org/ja/tour/tour-of-scala.html)** に進んでみたり、必要なら **[Scala 言語仕様](https://www.scala-lang.org/files/archive/spec/2.13/)** を調べられます。
