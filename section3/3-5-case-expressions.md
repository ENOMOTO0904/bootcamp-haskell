# 3.5 case 式

『すごいHaskellたのしく学ぼう！』第 3 章「関数の構文」の 3.5 節の要約。

## case 式とは

- 多くの命令型言語の `case` は、変数の値に応じて別々のコードを実行する構文である。Haskell の `case` はそれを一歩進めたもので、値を持つ式であり、パターンマッチもできる。
- 関数の引数でのパターンマッチは、`case` 式の糖衣構文である。次の 2 つは同じ意味で、入れ替えられる。

```haskell
head' :: [a] -> a
head' [] = error "No head for empty lists!"
head' (x:_) = x
```

<details>
<summary><code>(x:_)</code> の読み方（原著外の補足）</summary>

`(x:_)` は、要素が 1 個以上あるリストに合うパターンである。先頭の要素に `x` という名前を付け、残りは捨てる。

| 部品 | 意味 |
|---|---|
| `:` | リストを「先頭」と「残り」に分ける |
| `x` | 先頭の要素に付ける名前 |
| `_` | 残りのリスト。使わないので名前を付けない |

`[1,2,3]` は `1:2:3:[]` の糖衣構文で、`:` は先頭に 1 個くっつける演算子である。パターンでは、これを逆向きに使って分解する。

| 渡すリスト | `x` | `_` に当たる部分 |
|---|---|---|
| `[1,2,3]` | `1` | `[2,3]` |
| `[1]` | `1` | `[]` |
| `[]` | 合わない | |

- 空リストは先頭と残りに分けられないので、合わない。だから `[]` の場合を別に書いている。
- `(x:xs)` と書いても同じように合う。残りを使わないときは `_` にすると、使わないことが読み手に伝わる。
- かっこは必要である。`head' x:_ = x` と書くと `(head' x):_` と解釈されて、エラーになる。

Ruby では、配列の分解で `*` を使う書き方が近い。

```ruby
x, * = [1, 2, 3]   # x => 1
```

ただし、Ruby では空配列でも `x` が `nil` になって通る。Haskell では空リストは合わず、次のパターンに進む。

</details>

```haskell
head' :: [a] -> a
head' xs = case xs of [] -> error "No head for empty lists!"
                      (x:_) -> x
```

## 書き方

```haskell
case 式 of パターン -> 結果
           パターン -> 結果
           パターン -> 結果
           ...
```

- 式の値が、上から順にパターンと照らし合わされる。最初に合ったパターンの結果が、`case` 式全体の値になる。
- どのパターンにも合わなければ、実行時にエラーになる。

## 式の途中で使う

- 引数でのパターンマッチは、関数を定義するときにしか使えない。`case` 式は、式を書けるところならどこにでも書ける。式の途中でパターンマッチしたいときに便利である。

```haskell
describeList :: [a] -> String
describeList ls = "The list is " ++ case ls of [] -> "empty."
                                               [x] -> "a singleton list."
                                               xs -> "a longer list."
```

```haskell
ghci> describeList [1]
"The list is a singleton list."
```

- 同じ関数は、`where` と、引数でのパターンマッチを使っても書ける。

```haskell
describeList :: [a] -> String
describeList ls = "The list is " ++ what ls
    where what [] = "empty."
          what [x] = "a singleton list."
          what xs = "a longer list."
```

## Ruby との違い

<details>
<summary>Ruby の case と、Haskell の case 式（原著外の補足）</summary>

Ruby の `case` も、Haskell と同じく値を返す式である。だから、式の途中に書ける。

```ruby
def describe_list(xs)
  "The list is " + case xs
                   in [] then "empty."
                   in [_] then "a singleton list."
                   else "a longer list."
                   end
end
```

Ruby の `case` には 2 種類ある。Haskell の `case` 式に近いのは、`in` のほうである。

| | 調べ方 | どれにも合わないとき |
|---|---|---|
| Ruby の `case`/`when` | `===` で比べる | `nil` を返す |
| Ruby の `case`/`in` | パターンマッチ | `NoMatchingPatternError` |
| Haskell の `case` 式 | パターンマッチ | 実行時にエラーになる |

3.1 節で見たとおり、Ruby ではメソッドの中に `case`/`in` を書く。Haskell ではその書き方が `case` 式に当たり、関数の定義をパターンごとに分ける書き方は、それを短く書くための糖衣構文である。

</details>
