# 3.2 場合分けして、きっちりガード！

『すごいHaskellたのしく学ぼう！』第 3 章「関数の構文」の 3.2 節の要約。

## ガードとは

- パターンは、値の形を調べて分解する。**ガード**は、値が条件を満たすかどうかを調べる。
- `if` に似ているが、条件が複数あるときにずっと読みやすく、パターンマッチともうまく組み合わせられる。

```haskell
bmiTell :: Double -> String
bmiTell bmi
    | bmi <= 18.5 = "underweight"
    | bmi <= 25.0 = "normal"
    | bmi <= 30.0 = "overweight"
    | otherwise   = "obese"
```

- ガードは、関数名と引数のあとに `|` を書いて始める。ふつうは字下げして縦にそろえる。
- 関数名と引数のすぐあとには `=` を書かない。`=` は、それぞれのガードのあとに書く。
- ガードは真偽値の式である。上から順に評価され、最初に `True` になったガードの本体が使われる。

```haskell
ghci> bmiTell 24.3
"normal"
```

- `24.3` は 1 つ目のガードで `False` になり、2 つ目で `True` になる。

## otherwise

- 最後のガードには、たいてい `otherwise` を書く。`otherwise` は `otherwise = True` と定義されているだけで、すべてを受け止める。
- すべてのガードが `False` で `otherwise` もないときは、次のパターンに移る。合うガードもパターンもなければ、エラーになる。

## 引数が複数のとき

- ガードは、引数がいくつある関数にも使える。

```haskell
bmiTell :: Double -> Double -> String
bmiTell weight height
    | weight / height ^ 2 <= 18.5 = "underweight"
    | weight / height ^ 2 <= 25.0 = "normal"
    | weight / height ^ 2 <= 30.0 = "overweight"
    | otherwise                   = "obese"
```

```haskell
ghci> bmiTell 85 1.90
"normal"
```

- `max` や `compare` も、ガードで自分で書ける。

```haskell
max' :: (Ord a) => a -> a -> a
max' a b
    | a <= b    = b
    | otherwise = a

myCompare :: (Ord a) => a -> a -> Ordering
a `myCompare` b
    | a == b    = EQ
    | a <= b    = LT
    | otherwise = GT
```

```haskell
ghci> 3 `myCompare` 2
GT
```

- 関数は、バッククォートを使って中置で呼び出せるだけでなく、中置で定義もできる。

## Ruby との違い

<details>
<summary>Ruby の if/elsif と、Haskell のガード（原著外の補足）</summary>

ガードに当たるものは、Ruby では `if`/`elsif`、または対象を書かない `case`/`when` である。

```ruby
def bmi_tell(bmi)
  case
  when bmi <= 18.5 then "underweight"
  when bmi <= 25.0 then "normal"
  when bmi <= 30.0 then "overweight"
  else "obese"
  end
end
```

| | Ruby | Haskell |
|---|---|---|
| 条件 | `when 条件 then`、`elsif 条件` | `\| 条件 =` |
| どれにも当てはまらない | `else` | `\| otherwise =` |
| 書く場所 | メソッドの本体の中 | 関数の定義そのもの |
| どれにも当てはまらず、`else` もない | `nil` を返す | 実行時にエラーになる |

- どちらも上から順に調べ、最初に当てはまったものを使う。
- Ruby の `else` は構文である。Haskell の `otherwise` は、`True` に名前を付けただけの値である。
- Ruby のパターンマッチにも、`in x if x > 5` のようにガードを付けられる。パターンと条件を組み合わせるという考え方は同じである。

</details>
