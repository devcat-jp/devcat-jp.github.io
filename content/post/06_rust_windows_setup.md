---
title: "WindowsでのRust開発環境構築"
summary: "ツールチェインや設定などのメモ"
date: 2022-01-08T11:24:11+09:00
draft: false

tags: 
  - "Rust"
categories:
  - "Rust"

comments: false # Enable Disqus comments for specific page
authorbox: false # Enable authorbox for specific page
pager: true # Enable pager navigation (prev/next) for specific page
toc: true # Enable Table of Contents for specific page
mathjax: true # Enable MathJax for specific page
sidebar: "right" # Enable sidebar (on the right side) per page
widgets: # Enable sidebar widgets in given order per page
  - "search"
  - "recent"
  - "taglist"
---

## ■ 目的
Windows環境でのRust環境構築手順を記録として残すこと

  
## ■ リンカのインストール
RustコンパイラはCコンパイラのリンカを使用するとの情報。
一般的な推奨はVisual StudioまたはMicrosoft C++ Build Toolsをインストールすることになっている。
今回は後者のMicrosoft C++ Build Toolsを選択する。

  
[Microsoft C++ Build Tools　ダウンロードページ](https://visualstudio.microsoft.com/ja/visual-cpp-build-tools/)  
{{< figure src="/images/06_01.png" alt="" width=350px >}}

「Build Toolsのダウンロード」をクリックすると自動的にダウンロードが開始する
ダウンロードしたものを起動し、下図を選択して「インストール」をクリックする。
{{< figure src="/images/06_02.png" alt="" width=650px >}}

インストールが完了すると再起動を要求するので「再開」で再起動する
{{< figure src="/images/06_03.png" alt="" width=350px >}}
  

## ■ Rustツールチェインのインストール
公式ページからインスーラを入手する

[Rust　ダウンロードページ](https://www.rust-lang.org/ja/learn/get-started)  
{{< figure src="/images/06_04.png" alt="">}}

ダウンロードしたものを起動し、「1」を入力してEnter
{{< figure src="/images/06_05.png" alt="" width=350px >}}
インストールが完了すると以下のような表記になるのでEnterを入力して終了
{{< figure src="/images/06_06.png" alt="" >}}

「PowerShell」を起動し、Rustのバージョン確認ができるか確認する。
```
PS C:\> rustup --version
rustup 1.24.3 (ce5817a94 2021-05-31)
info: This is the version for the rustup toolchain manager, not the rustc compiler.
info: The currently active `rustc` version is `rustc 1.57.0 (f1edd0429 2021-11-29)`
```