# 2.2 一般的な Haskell の型

『すごいHaskellたのしく学ぼう！』第 2 章「型を信じろ！」の 2.2 節の要約。

## 型の一覧

| 型 | 意味 | 例 |
|---|---|---|
| `Int` | 整数。上限と下限がある | `7` |
| `Integer` | 整数。上限と下限がなく、どれだけ大きな数も扱える。`Int` より遅い | `factorial 50` の結果 |
| `Float` | 単精度の小数 | `25.132742` |
| `Double` | 倍精度の小数。`Float` の 2 倍の精度 | `25.132741228718345` |
| `Bool` | 真偽値。`True` と `False` の 2 つだけ | `True` |
| `Char` | 1 文字。シングルクォートで囲む | `'a'` |

```haskell
factorial :: Integer -> Integer
factorial n = product [1..n]
```

```haskell
ghci> factorial 50
30414093201713378043612608166064768844377641568960512000000000000
```

- 同じ計算でも、型によって精度が変わる。

```haskell
circumference :: Float -> Float
circumference r = 2 * pi * r

circumference' :: Double -> Double
circumference' r = 2 * pi * r
```

```haskell
ghci> circumference 4.0
25.132742
ghci> circumference' 4.0
25.132741228718345
```

- 空のタプル `()` も型であり、その値は `()` の 1 つだけである。

<details>
<summary>リストとタプルは、中身まで決まって初めて型になる（Ruby の配列との比較、原著外の補足）</summary>

上の一覧にリストがないのは、「リスト」だけでは型として完成していないからである。

Ruby には配列という型（`Array` クラス）があり、中身は問わない。何を入れても、空でも、同じ `Array` である。

```ruby
[1, 2].class          # => Array
["a", "b"].class      # => Array
[1, "a", nil].class   # => Array
[].class              # => Array
```

Haskell では、中身の型まで含めて 1 つの型になる。中身が違えば、リストとしても別の型である。

```haskell
ghci> :t [True, False]
[True, False] :: [Bool]
ghci> :t "abc"
"abc" :: String
```

| | Ruby | Haskell |
|---|---|---|
| 配列（リスト）の型 | `Array` の 1 種類 | 要素の型ごとに別の型。`[Bool]`、`[Char]`、`[Int]` ... |
| 型が表すもの | 入れ物の種類だけ | 入れ物の種類と、中身の型 |
| 中身 | 問わない。混ぜてもよい | 型に含まれる。混ぜられない |

リストとタプルでは、型を決めるのに必要なものが違う。

| | 型を決めるのに必要なもの | 型の例 |
|---|---|---|
| リスト | 要素の型（1 つ）。長さは含まない | `[Int]`、`[Char]` |
| タプル | 要素の数と、それぞれの位置の型 | `(String, Int)`、`(Bool, Char, Int)` |

`Int` や `Bool` はそれだけで完成した型であり、リストとタプルは、それらを材料にして新しい型を作る部品に当たる。組み合わせることもできる。

```haskell
ghci> :t [("a", True)]
[("a", True)] :: [(String, Bool)]
ghci> :t [[True]]
[[True]] :: [[Bool]]
```

材料にする型は何でもよいので、リストとタプルの型は無限に作れる。だから一覧には書ききれない。

### 空のリストの型

空のリストには要素がないので、コンパイラは中身から型を割り出せない。

```haskell
ghci> :t []
[] :: [a]
```

`[a]` は、「要素の型がまだ決まっていないリスト」という意味である。`a` は型変数で、「どんな型でもよい」ことを表す（2.3 節）。`[]` の型は、使われた場所に合わせて決まる。

```haskell
ghci> :t [] ++ "abc"
[] ++ "abc" :: [Char]
ghci> :t 1 : []
1 : [] :: Num a => [a]
```

Ruby の `[]` は、中身があってもなくても `Array` である。Haskell では、空のリストも「何かのリスト」でなければならないので、決まるまでは型変数で保留しておく。

### なぜ中身まで型に含めるのか

型が `[Int]` と決まっていれば、`head` で取り出した値が `Int` であることを、実行する前に確かめられる。型が単に「リスト」だと、中から何が出てくるかがわからず、その先の計算の型を確かめられなくなる。

Ruby でも、型を書く仕組み（RBS など）では `Array[Integer]` のように中身の型まで書く。入れ物の種類と中身の型を組み合わせて 1 つの型にする考え方は、型を実行前に確かめる言語に共通している。

</details>

## Ruby との違い

<details>
<summary>数の型の違い（原著外の補足）</summary>

Ruby の整数は `Integer` クラスの 1 種類で、大きさに上限がない。小数は `Float` クラスで、倍精度である。

| | Ruby | Haskell |
|---|---|---|
| 整数 | `Integer` のみ。上限なし | `Int`（上限あり、速い）と `Integer`（上限なし） |
| 小数 | `Float` のみ。倍精度 | `Float`（単精度）と `Double`（倍精度） |
| 整数と小数の計算 | 自動で小数に変換される | 自動では変換されない。`fromIntegral` を使う（2.4 節） |

名前が紛らわしいので注意が要る。Ruby の `Integer` は Haskell の `Integer` に当たり、Ruby の `Float` は Haskell の `Double` に当たる。

```ruby
[1, 2, 3, 4].length + 3.2   # => 7.2   整数が自動で小数になる
```

```haskell
ghci> fromIntegral (length [1,2,3,4]) + 3.2
7.2
```

Haskell が自動で変換しないのは、型の誤りを見逃さないためである。変換が必要な場所には、プログラマが変換を書く。

</details>
