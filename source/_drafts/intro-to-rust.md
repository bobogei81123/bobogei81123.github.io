---
title: Rust 簡介
tags: rust
---

最近花了不少時間在試 Rust 這個標榜有極佳的效率以及安全性的新語言。
這裡稍微做一些簡介。

## Resource

Rust 的 Resource 有三種形態，分別是

1. Owned: 對應到的是以 `mut var` 宣告的變數，表示這個資源是被你所擁有的，
    因此你可以任意修改，而你也需要負責資源的 destruction。
2. (Immutable) Borrowed: 對應到 `&var` ，表示這個資源是向別人借去的，
    你只能讀取，不能做任何修改。
3. Exclusive Borrowed: 對應到 `&mut var` ，代表這個資源是只借給你的，
    因此你可以任意修改，但最終仍要把這個資源還回去。
