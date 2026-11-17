# 3.3 where?!

『すごいHaskellたのしく学ぼう！』第 3 章「関数の構文」の 3.3 節の要約。

## where とは

- 3.2 節の `bmiTell` は、`weight / height ^ 2` という同じ式を 3 回書いていた。`where` を使うと、計算の結果に名前を付けて使い回せる。

```haskell
bmiTell :: Double -> Double -> String
bmiTell weight height
    | bmi <= 18.5 = "underweight"
    | bmi <= 25.0 = "normal"
    | bmi <= 30.0 = "overweight"
    | otherwise   = "obese"
    where bmi = weight / height ^ 2
```

- `where` はガードのあとに書き、そこで名前や関数を定義する。定義した名前は、すべてのガードから見える。
- 式を書くのが 1 回で済むので、計算方法を変えるときに直す場所も 1 つになる。名前が付くので読みやすくもなり、計算も 1 回で済む。
- 名前は複数定義できる。

```haskell
    where bmi = weight / height ^ 2
          skinny = 18.5
          normal = 25.0
          fat = 30.0
```

- `where` の中の名前は、先頭の列をそろえて書く。そろっていないと、同じブロックだとコンパイラが判断できない。

## where のスコープ

- `where` で定義した名前は、その関数の中からしか見えない。ほかの関数の名前空間を汚さない。
- パターンごとに本体を分けた関数では、`where` の名前はそのパターンの本体からしか見えない。複数のパターンで同じ名前を使いたいときは、関数の外で定義する。

## パターンマッチと where

- `where` の束縛にも、パターンマッチを使える。

```haskell
    where bmi = weight / height ^ 2
          (skinny, normal, fat) = (18.5, 25.0, 30.0)
```

```haskell
initials :: String -> String -> String
initials firstname lastname = [f] ++ ". " ++ [l] ++ "."
    where (f:_) = firstname
          (l:_) = lastname
```

```haskell
ghci> initials "Haskell" "Curry"
"H. C."
```

- この例は、引数のところで直接パターンマッチするほうが短くてわかりやすい。`where` でもできることを示すための例である。

## where ブロックの中の関数

- `where` では、定数だけでなく関数も定義できる。

```haskell
calcBmis :: [(Double, Double)] -> [Double]
calcBmis xs = [bmi w h | (w, h) <- xs]
    where bmi weight height = weight / height ^ 2
```

```haskell
ghci> calcBmis [(85,1.90),(60,1.60)]
[23.545706371191137,23.437499999999996]
```

- ここで `bmi` を関数にしたのは、リストの組ごとに別の BMI を計算するからである。`calcBmis` の引数から 1 つの値を計算するだけでは足りない。
- `where` は入れ子にできる。補助関数を `where` で定義し、その補助関数がさらに自分の `where` を持つ、という書き方はよく使われる。

## Ruby との違い

<details>
<summary>Ruby のローカル変数と、Haskell の where（原著外の補足）</summary>

Ruby で同じことをするには、ローカル変数に代入する。

```ruby
def bmi_tell(weight, height)
  bmi = weight / height**2
  if bmi <= 18.5 then "underweight"
  elsif bmi <= 25.0 then "normal"
  elsif bmi <= 30.0 then "overweight"
  else "obese"
  end
end
```

いちばんの違いは、書く順番である。

- Ruby は上から順に実行するので、変数は使う前に代入しておく。
- Haskell の `where` は、使う場所のあとに定義を書く。先に「何を返すか」を読ませ、細かい定義はあとに回せる。`where` の中の名前どうしも、どの順に書いてもよい。

| | Ruby | Haskell |
|---|---|---|
| 名前を付ける | `bmi = ...`（使う前に書く） | `where bmi = ...`（使ったあとに書く） |
| 補助の関数 | private メソッド、ラムダ | `where` の中の関数 |
| 補助の関数が見える範囲 | private メソッドはクラス全体 | その関数の中だけ |
| 再代入 | できる | できない |

Ruby の private メソッドは、同じクラスのどのメソッドからも呼べる。`where` の中の関数は、それを定義した関数の中からしか呼べないので、影響の範囲がより狭い。

</details>
