# 3.1 パターンマッチ

『すごいHaskellたのしく学ぼう！』第 3 章「関数の構文」の 3.1 節の要約。

## パターンマッチとは

- **パターンマッチ**は、データが従うべき形（パターン）を書いておき、データがその形に合うかを調べ、合えば中身を取り出す仕組みである。
- 関数を定義するとき、パターンごとに別々の本体を書ける。数、文字、リスト、タプルなど、どんなデータ型にも使える。

```haskell
lucky :: Int -> String
lucky 7 = "LUCKY NUMBER SEVEN!"
lucky x = "Sorry, you're out of luck, pal!"
```

- パターンは上から順に調べられ、最初に合ったパターンの本体が使われる。
- `7` のような値のパターンは、その値にだけ合う。`x` のような小文字の名前のパターンは、どんな値にも合い、その値に名前を付ける。
- 場合分けが多い関数は、`if` を重ねるよりずっと読みやすく書ける。

```haskell
sayMe :: Int -> String
sayMe 1 = "One!"
sayMe 2 = "Two!"
sayMe 3 = "Three!"
sayMe x = "Not between 1 and 3"
```

## 順番が大切

- 何にでも合うパターンを先頭に書くと、ほかのパターンは調べられなくなる。細かいパターンを先に、大まかなパターンをあとに書く。
- 階乗は、パターンマッチと再帰で次のように書ける。関数の定義の中でその関数自身を呼ぶことを、**再帰**という。

```haskell
factorial :: Int -> Int
factorial 0 = 1
factorial n = n * factorial (n - 1)
```

- `factorial 3` は、`3 * (2 * (1 * 1))` と計算される。2 つのパターンを逆の順に書くと、`0` も 2 つ目に合ってしまい、計算が終わらない。

## パターンマッチの失敗

- どのパターンにも合わない値を渡すと、実行時にエラーになる。

```haskell
charName :: Char -> String
charName 'a' = "Albert"
charName 'b' = "Broseph"
charName 'c' = "Cecil"
```

```haskell
ghci> charName 'a'
"Albert"
ghci> charName 'h'
*** Exception: Non-exhaustive patterns in function charName
```

- 想定しない入力でプログラムが落ちないように、最後に何にでも合うパターンを書いておく。

## タプルのパターンマッチ

- タプルの中身は、パターンで直接取り出せる。`fst` と `snd` を使うより読みやすい。

```haskell
-- パターンマッチなし
addVectors :: (Double, Double) -> (Double, Double) -> (Double, Double)
addVectors a b = (fst a + fst b, snd a + snd b)
```

```haskell
-- パターンマッチあり
addVectors :: (Double, Double) -> (Double, Double) -> (Double, Double)
addVectors (x1, y1) (x2, y2) = (x1 + x2, y1 + y2)
```

- トリプルには `fst` や `snd` に当たる関数がないが、パターンマッチで自分で作れる。使わない部分は `_` で受ける。

```haskell
first :: (a, b, c) -> a
first (x, _, _) = x
```

## リストのパターンマッチとリスト内包表記

- リスト内包表記の `<-` の左にも、パターンを書ける。パターンに合わない要素は、エラーにならずに飛ばされる。

```haskell
ghci> let xs = [(1,3),(4,3),(2,4),(5,3),(5,6),(3,1)]
ghci> [a+b | (a,b) <- xs]
[4,7,6,8,11,4]
ghci> [x*100+3 | (x, 3) <- xs]
[103,403,503]
```

- リストには、`[]` と `:` を使ったパターンが書ける。`x:xs` は、先頭の要素を `x` に、残りのリストを `xs` に束縛する。
- `:` を含むパターンは、長さが 1 以上のリストにだけ合う。要素が 1 つなら、`xs` は空リストになる。
- 複数の名前に束縛するパターンは、丸括弧で囲む。

```haskell
head' :: [a] -> a
head' [] = error "Can't call head on an empty list, dummy!"
head' (x:_) = x
```

- `error` は、文字列を受け取って実行時エラーを起こす関数である。プログラムが落ちるので、使いすぎないほうがよい。

| パターン | 合うリスト |
|---|---|
| `[]` | 空リスト |
| `(x:[])` または `[x]` | 要素がちょうど 1 つ |
| `(x:y:[])` または `[x,y]` | 要素がちょうど 2 つ |
| `(x:y:_)` | 要素が 2 つ以上 |
| `(x:xs)` | 要素が 1 つ以上 |

```haskell
tell :: (Show a) => [a] -> String
tell [] = "The list is empty"
tell (x:[]) = "The list has one element: " ++ show x
tell (x:y:[]) = "The list has two elements: " ++ show x ++ " and " ++ show y
tell (x:y:_) = "This list is long. The first two elements are: " ++ show x ++ " and " ++ show y
```

- `tell` は、空リスト、要素 1 つ、要素 2 つ、それ以上のすべてを扱っているので、どんなリストを渡しても失敗しない。
- リストのパターンマッチと再帰を組み合わせると、`length` を自分で書ける。

```haskell
length' :: [a] -> Int
length' [] = 0
length' (_:xs) = 1 + length' xs
```

- パターンの中で `++` は使えない。`(xs ++ ys)` と書いても、どこで分けるかが決まらないからである。

## as パターン

- パターンの前に名前と `@` を書くと、パターンで分解しながら、もとの値全体にも名前を付けられる。これを **as パターン**という。

```haskell
firstLetter :: String -> String
firstLetter "" = "Empty string, whoops!"
firstLetter all@(x:xs) = "The first letter of " ++ all ++ " is " ++ [x]
```

```haskell
ghci> firstLetter "Dracula"
"The first letter of Dracula is D"
```

- `all@(x:xs)` は `x:xs` と同じものに合う。本体で `x:xs` と書き直さなくても、`all` でリスト全体を参照できる。

## Ruby との違い

<details>
<summary>Ruby の case/in と、Haskell のパターンマッチ（原著外の補足）</summary>

Ruby にも、`case`/`in` によるパターンマッチがある（3.0 で正式に入った）。書く場所が違う。

```ruby
def lucky(n)
  case n
  in 7 then "LUCKY NUMBER SEVEN!"
  in _ then "Sorry, you're out of luck, pal!"
  end
end
```

```haskell
lucky 7 = "LUCKY NUMBER SEVEN!"
lucky x = "Sorry, you're out of luck, pal!"
```

- Ruby では、メソッドの定義は 1 つで、その中に `case`/`in` を書く。同じ名前で `def` を 2 回書くと、あとの定義が前の定義を上書きする。
- Haskell では、関数の定義そのものをパターンごとに分けて書く。

| | Ruby | Haskell |
|---|---|---|
| タプルやペアの分解 | `x, y = pair`、`\|(a, b)\|` | `(x, y)` |
| 先頭と残り | `x, *xs = list`、`in [x, *xs]` | `(x:xs)` |
| 使わない部分 | `_`、`*` | `_` |
| 全体にも名前を付ける | `in [x, *] => all` | `all@(x:_)` |
| どれにも合わない | `NoMatchingPatternError` | `Non-exhaustive patterns` |

どちらの言語も、どのパターンにも合わなければ実行時にエラーになる。ただし Ruby の多重代入は、合わなくてもエラーにならない。

```ruby
x, *xs = []   # x は nil、xs は []
```

Haskell の `(x:xs)` は空リストに合わないので、`head' []` のような場合を必ず自分で書くことになる。

</details>
