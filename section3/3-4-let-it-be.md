# 3.4 let It Be

『すごいHaskellたのしく学ぼう！』第 3 章「関数の構文」の 3.4 節の要約。

## let 式とは

- `let` は `where` によく似ている。`where` は関数の終わりで名前を束縛し、ガードを含む関数全体から見える。`let` はどこにでも書けて、それ自体が式である。ただし、見える範囲はとても狭く、ガードをまたげない。
- 形は `let 束縛 in 式` である。`let` で定義した名前は、`in` のあとの式からだけ見える。

```haskell
cylinder :: Double -> Double -> Double
cylinder r h =
    let sideArea = 2 * pi * r * h
        topArea = pi * r ^ 2
    in  sideArea + 2 * topArea
```

- `where` と同じく、名前は先頭の列をそろえて書く。

## let は式である

- `where` は構文にすぎないが、`let` は値を持つ式である。`if` と同じように、式を書けるところならどこにでも書ける。

```haskell
ghci> 4 * (if 10 > 5 then 10 else 0) + 2
42
ghci> 4 * (let a = 9 in a + 1) + 2
42
```

- その場かぎりの関数も定義できる。

```haskell
ghci> [let square x = x * x in (square 5, square 3, square 2)]
[(25,9,4)]
```

- 1 行に複数の束縛を書くときは、セミコロンで区切る。

```haskell
ghci> (let a = 100; b = 200; c = 300 in a*b*c, let foo="Hey "; bar = "there!" in foo ++ bar)
(6000000,"Hey there!")
```

- `let` の束縛でもパターンマッチが使える。タプルを分解して名前を付けるのに便利である。

```haskell
ghci> (let (a,b,c) = (1,2,3) in a+b+c) * 100
600
```

## リスト内包表記での let

- リスト内包表記の中にも `let` を書ける。条件と同じ場所に書くが、要素を絞り込むのではなく、名前を束縛するだけである。

```haskell
calcBmis :: [(Double, Double)] -> [Double]
calcBmis xs = [bmi | (w, h) <- xs, let bmi = w / h ^ 2, bmi > 25.0]
```

```haskell
ghci> calcBmis [(85,1.90),(100,1.70)]
[34.602076124567475]
```

- `let` で定義した名前は、出力（`|` の左）と、`let` よりあとに書いた条件から見える。`(w, h) <- xs` の部分は `let` より前にあるので、そこでは `bmi` を使えない。
- リスト内包表記の中では、名前の見える範囲がすでに決まっているので、`in` を書かない。

## GHCi での let

- GHCi で関数や定数を直接定義するときも、`in` を省ける。省くと、その名前は対話セッションの終わりまで見える。

```haskell
ghci> let zoot x y z = x * y + z
ghci> zoot 3 9 2
29
ghci> let boot x y z = x * y + z in boot 3 4 2
14
ghci> boot
error: Variable not in scope: boot
```

## let と where の使い分け

| | `where` | `let` |
|---|---|---|
| 種類 | 構文 | 式 |
| 書く位置 | 使う場所のあと | 使う場所の前 |
| 見える範囲 | ガードを含む関数全体 | `in` のあとの式だけ |
| ガードをまたげるか | またげる | またげない |

- `let` は範囲が狭いので、複数のガードで同じ名前を使いたいときには向かない。
- `where` は、関数の本体が名前と型宣言のすぐ近くに来るので、そのほうが読みやすいと考える人もいる。

## Ruby との違い

<details>
<summary>Ruby の代入と、Haskell の let（原著外の補足）</summary>

Ruby の代入も、値を返す式である。だから、式の途中に書ける。

```ruby
4 * (a = 9; a + 1) + 2   # => 42
```

```haskell
ghci> 4 * (let a = 9 in a + 1) + 2
42
```

違いは、名前が見える範囲にある。

- Ruby では、代入した変数は式が終わっても残る。上の例のあとでも `a` は `9` のままで、メソッドの終わりまで使える。
- Haskell の `let` では、`a` は `in` のあとの式の中でしか見えない。式の外には漏れない。

範囲を限りたいとき、Ruby では `then` とブロックを使う。ブロック引数は、ブロックの外からは見えない。

```ruby
9.then { |b| b + 1 } * 4 + 2   # => 42
```

| | Ruby | Haskell |
|---|---|---|
| 名前を付ける | `a = 9` | `let a = 9 in ...` |
| 見える範囲 | メソッドの終わりまで | `in` のあとの式だけ |
| 再代入 | できる | できない |
| 分解して名前を付ける | `a, b, c = [1, 2, 3]` | `let (a,b,c) = (1,2,3) in ...` |

</details>
