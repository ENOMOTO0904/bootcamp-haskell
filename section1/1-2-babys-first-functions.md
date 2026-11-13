# 1.2 赤ちゃんの最初の関数

『すごいHaskellたのしく学ぼう！』第 1 章「はじめの第一歩」の 1.2 節の要約。

## 関数の定義

- 関数は、呼び出すときと同じ形で定義する。関数名、スペース区切りの引数、`=`、関数の本体の順に書く。

```haskell
doubleMe x = x + x
```

- ファイル（`baby.hs`）に書いて保存し、`ghci` で `:l baby` と入力して読み込む。

```haskell
ghci> doubleMe 9
18
ghci> doubleMe 8.3
16.6
```

- `+` は整数にも小数にも使えるので、`doubleMe` はどの数に対しても動く。

<details>
<summary>x の値は変わっていないのか（原著外の補足）</summary>

変わっていない。`doubleMe x = x + x` は、「入力に `x` を入れたら、戻り値は `x + x` です」という意味である。

| 部分 | 意味 |
|---|---|
| `doubleMe` | 関数の名前 |
| `x`（`=` の左） | 入力 |
| `x + x`（`=` の右） | 戻り値 |

Ruby の次の定義と同じである。

```ruby
def double_me(x)
  x + x
end
```

`doubleMe 9` と呼ぶと、入力の `x` は `9` になり、戻り値は `9 + 9` の `18` になる。そのあいだ `x` はずっと `9` のままで、書き換えられない。

`doubleMe` を読み飛ばすと `x = x + x` に見えるので、「`x` を 2 倍の値で上書きしている」と読み違えやすい。Haskell の `=` は代入ではなく、入力と戻り値の対応を定義する記号である。

</details>

## 関数を組み合わせる

- 引数は、スペースで区切って複数並べられる。

```haskell
doubleUs x y = x*2 + y*2
```

```haskell
ghci> doubleUs 4 9
26
ghci> doubleUs 2.3 34.2
73.0
ghci> doubleUs 28 88 + doubleMe 123
478
```

- 自分で定義した関数から、自分で定義した別の関数を呼び出せる。

```haskell
doubleUs x y = doubleMe x + doubleMe y
```

- 明らかに正しい基本的な関数を作り、それを組み合わせて複雑な関数を作る。これは Haskell でよく使う書き方である。
- 繰り返しも避けられる。`doubleMe` の定義を変えれば、それを呼んでいる `doubleUs` の動作も一緒に変わる。
- 関数を定義する順番は自由である。`doubleMe` と `doubleUs` のどちらを先に書いてもよい。

<details>
<summary>Ruby のメソッド定義との比較（原著外の補足）</summary>

`doubleUs x y` の `x` と `y` は、Ruby のメソッドの第 1 引数と第 2 引数に当たる。違いは書き方だけで、Haskell は括弧もカンマも使わない。

| | Ruby | Haskell |
|---|---|---|
| 定義 | `def double_us(x, y)` | `doubleUs x y =` |
| 呼び出し | `double_us(4, 9)` | `doubleUs 4 9` |
| 引数の区切り | カンマ | スペース |

ほかの関数を呼び出して組み合わせる書き方も、同じ形になる。

```ruby
def double_me(x)
  x + x
end

def double_us(x, y)
  double_me(x) + double_me(y)
end

double_us(4, 9)   # => 26
```

```haskell
doubleMe x = x + x

doubleUs x y = doubleMe x + doubleMe y
```

Ruby では戻り値を得るために `def` と `end` で囲んだ本体を実行するが、Haskell では `=` の右に戻り値の式を 1 つ書くだけで済む。

</details>

## if は式である

> [!IMPORTANT]
> **Haskell では、すべての式と関数が必ず値を返す。例外はない。**
> 「何も返さない」「条件に合わなければ何もしない」という書き方は、Haskell には存在しない。

この節でいちばん大事なのは、この一点である。`if` の書き方は、ここから決まる。

- 100 以下の数だけを 2 倍にする関数。

```haskell
doubleSmallNumber x = if x <= 100
                        then x*2
                        else x
```

### else は絶対に省略できない

- 命令型言語の `if` は「条件が成り立てば、この処理を実行する」という**命令**である。成り立たなければ何もしないで済ませられるので、`else` は書かなくてもよい。
- Haskell の `if` は「条件が成り立てばこの値、成り立たなければこの値」という**値の選択**である。どちらの場合にも、返す値がなければならない。
- `else` を省略すると、条件が成り立たないときに返す値がなくなる。**値を返さない関数は Haskell では許されない**ので、`else` のない `if` はコンパイルエラーになる。

### だから if は「式」である

- **式**とは、値を返すコードのことである。`5`、`4 + 8`、`x + y` はどれも式である。
- `else` が必須なので、`if` は**どんな場合でも必ず値を返す**。つまり `if` も、`5` や `x + y` と同じ式である。
- 式なので、`if` 全体をほかの式の一部として、数と同じように使える。

```haskell
doubleSmallNumber' x = (if x > 100 then x else x*2) + 1
```

```haskell
ghci> doubleSmallNumber' 50
101
ghci> doubleSmallNumber' 150
151
```

- `(if ...)` の部分が必ず数になるからこそ、その結果に `+ 1` を足せる。
- 括弧を外すと、`+ 1` は `else` の側にだけ付く。`x` が 100 より大きいときには 1 が足されなくなる。

## 名前の付け方

- `doubleSmallNumber'` の末尾にある `'`（アポストロフィ）に、構文上の特別な意味はない。関数名に使える文字の 1 つである。
- 慣習として、`'` は次のどちらかを表す。
  - 正格版（遅延評価しない版）の関数
  - 元の関数や変数を少し変えた版

> 柄本のつぶやき: A' とか書きたくなるからええやん

- 関数名を大文字で始めることはできない。

## 引数のない関数は「定義」と呼ぶ

```haskell
conanO'Brien = "It's a-me, Conan O'Brien!"
```

- 引数を取らない関数は、ふつう**定義**（または**名前**）と呼ぶ。
- 名前も関数も、一度定義したら意味を変えられない。そのため、`conanO'Brien` と文字列 `"It's a-me, Conan O'Brien!"` は、どこでも互いに置き換えて使える。
