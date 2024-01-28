# 文字列操作
## 置換・結合系
### 改行(\n)
```python
s = 'abc\ndef\nghi'
print(s)
# abc
# def
# ghi
```
### 改行して結合(join)
```python
l = ['abc', 'def', 'ghi']
s_n = '\n'.join(l)
print(s_n)
# abc
# def
# ghi
```
### 区切り文字で分割(split)
```python
s_blank = 'one two   three\nfour\tfive'
print(s_blank.split())
# ['one', 'two', 'three', 'four', 'five']
```
### 改行ごとに分割、リスト化(splitlines)
```python
s = 'abc\ndef\nghi'
print(s.splitlines())
# ['abc', 'def', 'ghi']
```
### 改行コードの置換・削除(joinとsplitlines)
```python
s = 'abc\ndef\nghi'
print(''.join(s.splitlines()))
# abcdefghi
```
### 区切り文字と前後で3分割(partition)
```python
s = 'abc@def'
print(s.partition('@'))
# ('abc', '@', 'def')
```
### 文字列の余分な文字を削除(strip)
```python
s = '  one  '
print(s.strip())
# one
```

### リスト内包表記の使用
```python
s = 'one, two,  three'
l = [x.strip() for x in s.split(',')]
print(l)
# ['one', 'two', 'three']
```

## 比較
### 完全一致(==)
- 大文字と小文字は別物として判定される。揃えるには`lower()`や`upper()`を使う
    ```python
    s1 = 'abc'
    s2 = 'ABC'
    print(s1 == s2)
    # False

    # 小文字に揃える
    print(s1.lower() == s2.lower())
    # True
    ```
### 含む(in)
```python
print('bbb' in 'aaa-bbb-ccc')
# True
```
### 前方一致(startswith), 後方一致(endswith)
```python
s = 'aaa-bbb-ccc'
print(s.startswith('aaa'))
# True
print(s.endswith('ccc'))
# True
```
### 大小(<, >)
- 文字列も大小判定ができる。
    - アルファベット、ひらがな、カタカナの順

## 正規表現
### 
a = 2
X = aE
detX = 4

<br><br>

# リスト操作
### 要素追加
- `append`は末尾に要素を追加する
    ```python
    l = [0, 1, 2]
    l.append(100)
    print(l)
    # [0, 1, 2, 100]
    ```
- `extend`, `+`, `+=`はリストを結合する
    ```python
    l = [0, 1, 2]
    l.extend([10, 11, 12])
    or 
    l += [10, 11, 12]
    print(l)
    # [0, 1, 2, 10, 11, 12]
    ```
- `insert(位置, 要素)`にて、指定位置に要素を追加できる。(負の数は後ろから)
    ```python
    l = [0, 1, 2]
    l.insert(1, 100)
    print(l)
    # ['a', 100, 'b', 'c']
    ```

# リスト内法表記
### 基本形
```python
リスト = [式 for 任意の変数名 in イテラブルオブジェクト]

squares = [i**2     for i in range(5)]
# [0, 1, 4, 9, 16]
```

### ifの追加
```python
リスト = [式 for 任意の変数名 in イテラブルオブジェクト if 条件式]

odds = [i   for i in range(10)  if i % 2 == 1]
# [1, 3, 5, 7, 9]
```

### 三項演算子の追加(elseも使いたい場合)
```python
リスト = [真のときの値 if 条件式 else 偽のときの値 for 任意の変数名 in イテラブルオブジェクト]

odd_even = ['odd'   if i % 2 == 1 else 'even'  for i in range(5)]
# ['even', 'odd', 'even', 'odd', 'even']
```


### forのネスト
```python
リスト = [式 for 変数名1 in イテラブルオブジェクト1 for 変数名2 in イテラブルオブジェクト2 ... ]

matrix = [[1, 2, 3], [4, 5, 6], [7, 8, 9]]
flat = [x   for row in matrix   for x in row]
# [1, 2, 3, 4, 5, 6, 7, 8, 9]
```

### forのネスト + 条件分岐
```python
リスト = [式 for 変数名1 in イテラブルオブジェクト1 if 条件式 for 変数名2 in イテラブルオブジェクト2 if 条件式 ... ]

cells = [(row, col)     for row in range(3) if row % 2 == 0     for col in range(2) if col % 2 == 0]
# [(0, 0), (2, 0)]
```

### 集合内包表記(重複しない要素)
```python
集合 = {式 for 任意の変数名 in イテラブルオブジェクト}

s = {i**2   for i in range(5)}
# {0, 1, 4, 9, 16}
```

### 辞書内包表記(key:value)
```python
辞書 = {キー: 値 for 任意の変数名 in イテラブルオブジェクト}

l = ['Alice', 'Bob', 'Charlie']
d = {s: len(s) for s in l}
# {'Alice': 5, 'Bob': 3, 'Charlie': 7}
```

### 辞書内包表記(key:value)　ket、valueを別リストから取得：zipを使用
```python
辞書 = {キー: 値 for 任意の変数名 in イテラブルオブジェクト}

keys = ['k1', 'k2', 'k3']
values = [1, 2, 3]
d = {k: v for k, v in zip(keys, values)}
# {'k1': 1, 'k2': 2, 'k3': 3}
```

### ジェネレータ式
- forを利用しないと中身が表示されない。
- リスト内法表記に比べてメモリ使用量が少ない。
    - ループが進むごとに次の要素を生成するため、メモリは一度に1つの要素しか保持されない。

```python
ジェネレータ = (キー: 値 for 任意の変数名 in イテラブルオブジェクト)

g = (i**2 for i in range(5))
print(g)
# <generator object <genexpr> at 0x10af944f8>

for i in g:
    print(i)
# 0
# 1
# 4
# 9
# 16
```

### ジェネレータ式　タプル表記

```python
t = tuple(i**2 for i in range(5))

print(t)
# (0, 1, 4, 9, 16)

print(type(t))
# <class 'tuple'>
```
<br><br>

# コマンドライン関連

### __ name __ と __ main __
- 他のファイルからインポートされた時
    - `__name__`はモジュール名が格納される。
- そのファイル自体がpythonコマンドでスクリプト実行された時
    - `__name__`は`__main__`
- `if __name__ == __main__`
    - このファイルがコマンドラインからスクリプトとして実行された時にのみ以降の処理を実行する。

### コマンドライン引数の扱い
- `sys.argv`
    - シンプルだが、引数の個数に応じた処理や型変換を自分で行う。
    - sys.argvはリスト
    - 1つ目の要素にスクリプトファイルのパスが格納されている。
    - 要素はインプットを数値にしても文字列で出てくる。
- `argparse`
    - 設定のためのコードが必要だが、オプションを使ったり任意の個数の引数を扱える。
    - コマンドラインからの引数の型指定をできる。
        ```python
        parser = argparse.ArgumentParser()
        parser.add_argument('arg_int', type=int)
        ```
    - これは裏側でint()に引数が渡される処理となっている。
    - bool()は以下の場合以外をTrueと判定するため、type=boolは上手くいかない。
        - NoneとFalse
        - 数値型の0
        - 空のオブジェクト系
    - boolを使いたい場合は、以下の通りとする。
        ```python
        parser = argparse.ArgumentParser()
        parser.add_argument('--〇〇', action='store_true')
        ```
        - --〇〇が付くとtrue、つかないとfalse。
        - 逆にするには`store_false`にする。
- `input()`
    - プログラムの中でキーボードからの入力を取得する場合に使用する。
    - `input()`で記述すると、入力待ち状態になり、キーボード入力が可能になる。
    - `input(文字列)`で記述すると、その文字列を表示して入力待ち状態を作れる。
    - 入力値を常にstrで受け取る。
    - 受け取る値の個数が決まっている場合、input()をその分だけ記載する。
- `iter(オブジェクト, キーワード)`
    - オブジェクトがキーワードを返すまで繰り返す。
    - list()化せずそのままjoinで改行入りにできる。
        ```python
        '\n'.join(iter(input, ''))
        print(s)
        # Line1
        # Line2
        # Line3
        ```
<br><br>

# ラムダ式
- 普通はdefで関数指定をするが、lambdaで名前を持たない無名関数を作成できる。
### 基本形
```python
def 名前(引数, 引数, ...):
    return 式

↓

名前 = lambda 引数, 引数: 式
```
### if文
```python
lambda 引数, 引数: if 〇〇 else ○○
```
### 具体的な使い方
- `sorted()`, `sort()`, `max()`, `min()`などの引数のkeyに指定する。
    - 例
        ```python
        l = ['Charle', 'Bob', 'Alice']

        ## len()をkeyに指定すると、文字列が少ない順にソートされる。
        l_sorted_len = sorted(l, key=len)
        print(l_sorted_len)
        # ['Bob', 'Alice', 'Charle']
        
        ↓ ラムダを使用

        # 2文字目を取得するラムダを引数に渡して、2文字目のアルファベット順にソートする。
        l_sorted_second = sorted(l, key=lambda x: x[1])
        print(l_sorted_second)
        # ['Charle', 'Alice', 'Bob']
        ```
- `map()`や`filter()`の第一引数に指定する。(基本的にはリスト内包表記を使う)
    - 例
        ```python
        l = [0, 1, 2, 3]

        ## 第一引数に2乗するラムダ式を指定。
        map_square = map(lambda x: x**2, l)
        print(map_square)
        # <map object at 0x1072fd128>
        print(list(map_square))
        # [0, 1, 4, 9]
        
        ↓ リスト内包表記

        l_square = [x**2 for x in l]
        print(l_square)
        # [0, 1, 4, 9]
        ```

<br><br>

# ソート
## 基本メソッド
- `リスト.sort()`
    - 元のリストを変更する。
    - デフォルトは昇順、降順にソートしたい場合は`sort(reverse=True)`とする。
- `sorted(オブジェクト)`
    - 元のリストは変更せず、ソートされた新しいリストを作成する。
    - デフォルトは昇順、降順にソートしたい場合は`sorted(オブジェクト, reverse=True)`とする。
    - リスト以外にも文字列やタプルなどのイミュータブルなオブジェクトもソートかけられる。

## 辞書型のソート
- 辞書がリストを普通にソートしようするとTypeErrorになる。
### ラムダの使用
- keyにラムダを使って指定する。
    ```python
    l = [{'Name': 'Alice', 'Age': 40, 'Point': 80},
        {'Name': 'Bob', 'Age': 20},
        {'Name': 'Charlie', 'Age': 30, 'Point': 70}]
    
    # Ageでソートする    
    pprint.pprint(sorted(l, key=lambda x: x['Age']))
    # [{'Age': 20, 'Name': 'Bob'},
    #  {'Age': 30, 'Name': 'Charlie', 'Point': 70},
    #  {'Age': 40, 'Name': 'Alice', 'Point': 80}]
    ```

- 共通のキーを持たない要素があってもエラーにしないためにはgetを使う。
- getの第二引数はキーを持たない場合のデフォルト値を指定する。
    - そこにinfを使うと、他の数値より大きいと判定されるのでキーが存在しない要素を末尾に置くことができる。
    ```python
    # Pointでソートする    
    pprint.pprint(sorted(l, key=lambda x: x.get('Point', float('inf'))))
    # [{'Age': 30, 'Name': 'Charlie', 'Point': 70},
    #  {'Age': 40, 'Name': 'Alice', 'Point': 80},
    #  {'Age': 20, 'Name': 'Bob'}]
    ```
### operator.itemgetter()の使用(ラムダより高速)
- 共通のキーを持たない場合エラーになる。
```python
import operator
pprint.pprint(sorted(l, key=operator.itemgetter('Age')))
# [{'Age': 20, 'Name': 'Bob'},
#  {'Age': 30, 'Name': 'Charlie', 'Point': 70},
#  {'Age': 40, 'Name': 'Alice', 'Point': 80}]
```

- 複数の指定も可能。
```python
import operator
pprint.pprint(sorted(l, key=operator.itemgetter('Age', 'Name')))
# [{'Age': 20, 'Name': 'Bob'},
#  {'Age': 20, 'Name': 'Charlie', 'Point': 70},
#  {'Age': 40, 'Name': 'Alice', 'Point': 80}]
```

### 最大値の取得
```python
print(max(l, key=operator.itemgetter('Age')))
# {'Name': 'Alice', 'Age': 40, 'Point': 80}
```

## 2次元配列のソート
### デフォルト
- 最初の等しくない要素に対して行われる。
```python
l_2d = [[20, 3, 100], [1, 200, 30], [300, 10, 2]]
pprint.pprint(sorted(l_2d), width=20)
# [[1, 200, 30],
#  [20, 3, 100],
#  [300, 10, 2]]
```

### 行ごと
- リスト内包表記を使用。配列内で昇順に並び替える。
```python
l_2d = [[20, 3, 100], [1, 200, 30], [300, 10, 2]]
pprint.pprint([sorted(l) for l in l_2d], width=20)
# [[3, 20, 100],
#  [1, 30, 200],
#  [2, 10, 300]]
```

### 列ごと
- リスト内包表記を使用。各要素の一番値が小さいものが一番最初に来る。
    ```python
    l_2d = [[20, 3, 100], [1, 200, 30], [300, 10, 2]]
    pprint.pprint([list(x) for x in zip(*[sorted(l) for l in zip(*l_2d)])], width=20)
    # [[1, 3, 2],
    #  [20, 10, 30],
    #  [300, 200, 100]]
    ```
### NumPyを利用する。(各次元の要素数が等しい場合のみ使用可能。)
- デフォルトは行ごと、axis=0を使用すると列ごとにソートされる。
- リストに変換する場合は`tolist()`を使う。
    ```python
    import numpy
    print(numpy.sort(l_2d))
    # [[  3  20 100]
    #  [  1  30 200]
    #  [  2  10 300]]

    print(numpy.sort(l_2d, axis=0))
    # [[  1   3   2]
    #  [ 20  10  30]
    #  [300 200 100]]

    print(numpy.sort(l_2d).tolist())
    # [[3, 20, 100], [1, 30, 200], [2, 10, 300]]
    ```

### 特定の行・列でソート
```python
import operator
""" 1つ """
pprint.pprint(sorted(l_2d, key=operator.itemgetter(1)), width=20)
or
pprint.pprint(sorted(l_2d, key=lambda x: x[1]), width=20)
# [[20, 3, 100],
#  [300, 10, 2],
#  [1, 200, 30]]


""" 複数 """
l_2d_dup = [[1, 3, 100], [1, 200, 30], [1, 3, 2]]
pprint.pprint(sorted(l_2d_dup, key=operator.itemgetter(0, 2)), width=20)
or
pprint.pprint(sorted(l_2d_dup, key=lambda x: (x[0], x[2])), width=20)
# [[1, 3, 2],
#  [1, 200, 30],
#  [1, 3, 100]]
```


### 行と列を入れ替える
- NumPy, Pandas, zip()の3パターンで行える。
```python
import numpy
import pandas
l_2d = [[0, 1, 2], [3, 4, 5]]

""" Numpy """
l_2d_t = numpy.array(l_2d).T.tolist()
print(l_2d_t)
# [[0, 3], [1, 4], [2, 5]]

""" Pandas """
l_2d_t = pandas.DataFrame(l_2d).T.values.tolist()
print(l_2d_t)
# [[0, 3], [1, 4], [2, 5]]

""" zip """
l_2d_t = [list(x) for x in zip(*l_2d)]
print(l_2d_t)
# [[0, 3], [1, 4], [2, 5]]
```