---
title: Rust 簡介
tags: rust
---

最近花了不少時間在試 Rust 這個標榜有極佳的效率以及安全性的新語言。
這裡稍微做一些簡介。

<!--more-->

## Borrow checker

C/C++ 有不少使 programmer 容易犯的錯誤，比如像是

```cpp
int calc(vector<int> &v) {
    int t = v.back();
    v.push_back(t);
    return t;
}

int &x = vec[0];
...
x = calc(vec);
```

因為 `calc` 塞了一個元素進 `v`，從而導致 `v` 可能被
reallocate，進而使 `x` 這個指向 `vec[0]` 的 reference 失效了。
甚至在 C++17 前沒有規定等號左右 evaluate 的順序，連
`vec[0] = calc(vec);` 都是 undefined behavior。

這個錯誤的根本是因為 `vec` 已經「借給」`x` 了，他就不該再被借去另一個
地方改值了。Rust 在編譯的時候用 borrow checker 檢查了這類形的錯誤。

Rust 的變數共有三種形態，分別是

1. Owned: 
    - 這個資源是被你所擁有的。
    - 既然是你的，在還沒有借給任何人前你可以任意修改。並且由你負責資源的 destruction。
    - 對應到的是以 `mut var` 宣告的變數。 [^1]
2. Shared reference:
    - 這個資源是向別人借來的，並且這個資源還可能分享給其它人使用。
    - 因為如此，你不能做任何修改或其他可能會影響他人的事情。
    - 對應到 `&var`。
3. Exclusive reference: 
    - 這個資源是向別人借來的，但可以確信只借給你而已。
    - 你可以對這個資源做任何事情。
    - 對應到 `&mut var`。

重點是一個變數

- 要嘛借給至多一個 exclusive reference
- 要嘛借給許多 shared reference，但不借給任何 exclusive reference

以先前的例子來說
```rust
fn calc(v: &mut Vec<i32>) -> i32 {
    let t = *v.last().unwrap();
    v.push(t);
    return t;
}

let x = vec.get_mut(0).unwrap();
*x = calc(&mut vec);
```
Rust 編譯器會報錯
```
error[E0499]: cannot borrow `vec` as mutable more than once at a time

   |     let x = vec.get_mut(0).unwrap();
   |             --- first mutable borrow occurs here
   |     *x = calc(&mut vec);
   |                    ^^^ second mutable borrow occurs here
```

再舉一個例子，複製 `Vec` 一遍放在尾端常常會寫出以下錯誤的代碼：
```rust
for x in vec.iter() {
    vec.push(*x);
}
```
Rust 也會把錯誤抓出來
```
error[E0502]: cannot borrow `vec` as mutable because it is also borrowed as immutable

  |     for x in vec.iter() {
  |              --- immutable borrow occurs here
  |         vec.push(*x);
  |         ^^^ mutable borrow occurs here
```

## Lifetime

在 [Non-lexical lifetimes](https://github.com/rust-lang/rfcs/pull/2094) 還沒有被實作以前，
Rust 的 Owned variable 都會在 scope 結束的時候被釋放：
```
let a = ...;
let b = ...;

// Equivalent to 

{
    let a = ...;    ---------|
    {                        |
        let b = ...;  ---|   |
                <--------|   |
    }                        |
            <----------------|
}
```

同樣的，一個 reference 也會在 scope 結束後將借來的還回去：

```
let a = 1;
{
    let b = &a; // Borrow begin
    ...
    // Borrow end
}
```
變數和 borrowing 有效的時間就稱作他的 lifetime。

而理所當然的

## Move by default 


[^1]: Rust 也有 Immutable owned 的變數，但在本質上來說和 Mutable owned
沒有差別，只是編譯器會幫你檢查有沒有被修改而已。沒有任何人能阻止你這麼做：
```rust
let x = 3;
let mut x = x;
```
    也就是說，把所有 `let var = ...` 換成 `let mut var = ...` 不會造成任何編譯錯誤。

