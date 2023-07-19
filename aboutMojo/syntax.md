# Base syntax Mojo

## Functions

Mojoの関数定義は`fn`, `def`のどちらかを使用することができる

- `fn` 型指定される
- `def` 型指定されない(pythonと同じ)

## Variables

変数定義は `let`, `var`が使用可能

- `let` 変数の値が変更できない
- `var` 変数の値が何度でも変更できる

実際に以下のようにするとエラーが起きる

``` mojo

fn do_math():
    let x: Int = 1
    x = 2 <!-- <-- compile error -->
    let y = 2
    print(x + y)

do_math()

>>> Error 
```

## Function Argument

`fn`で宣言した時の関数の引数の値は変更することはデフォルトの設定でできないようになっている。

``` mojo
fn add(x: Int,y: Int) -> Int:
    return x + y

print(add(3, 5))

>>> 8
```

また引数の値を変更できないことを明確に表すために`borrowed`が使われている

``` mojo

fn add( borrowed x: Int, borrowed y: Int) -> Int:
    x = x + 3 <!-- <- Compile error -->
    return x + y

print(add(3, 5))

>>> 8
```

ただし`inout`を使用すれば引数の変数の値を変更することができるようになる。変更された値は関数内部だけではない外部でも影響が出る

``` mojo

fn add_inout(inout x: Int, inout y: Int) -> Int:
    x += 1
    y += 1
    return x + y

var a = 3
var b = 4
c = add_inout(a, b)
print(a)
print(b)
print(c)

>>> 4
>>> 5
>>> 9
```

また関数内でのみ引数の値を変更することができるようにする`owned`の機能がある

``` mojo

from String import String

fn set_fire(owned text: String) -> String:
    text += "🔥"
    return text

fn mojo():
    let a: String = "mojo"
    let b = set_fire(a)
    print(a)
    print(b)

mojo()

>>> mojo
>>> mojo🔥
```

関数に値のコピーを作成させたくないときは`^`を使用することができる
`^`を付与された演算子を呼びたそうとするとコンパイルエラーが発生する

``` mojo

from String import String

fn set_fire(owned text: String) -> String:
    text += "🔥"
    return text

fn mojo():
    let a: String = "mojo"
    let b = set_fire(a^)
    print(a) <!-- <- expression faild -->
    print(b)

mojo()

>>> Error

```

## About Structures

Mojoの`struct`はPythonの`class`に近い。
ただしMojoの`struct`は静的で動的なものはエラーが起きる

``` Mojo
struct MyPair:
    var first: Int
    var second: Int

    # This "initializer" behaves like a constructor in other languages
    fn __init__(inout self, first: Int, second: Int):
        self.first = first
        self.second = second
    
    fn dump(inout self):
        print(self.first)
        print(self.second)

```

## Python Integration

MojoはCPythonインタープリターを使用することでPythonコードを実行することができ、すでにPythonでよく使われている`numpy`などのモジュールをそのままインポートすることができる。

``` Mojo

from PythonInterface import Python

let np = Python.import_module("numpy")

ar = np.arange(15).reshape(3, 5)
print(ar)
print(ar.shape)

```
