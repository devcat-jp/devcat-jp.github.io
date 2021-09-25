---
title: "Picoのcmakelistメモ"
date: 2021-09-19T15:11:12+09:00
draft: false
summary: "CMakelistのフラグとかの情報をメモ"
tags: [Raspberry Pi, pico]
---

# 目的
picoのCMakeListに記載する情報で
調べた情報を整理・記録すること

## TinyUSB関係
### ライブラリが存在するかチェック
```
if (TARGET tinyusb_device)
    ～～～
endif()
```
TinyUSBはpico-sdkで
```
$ git submodule update --init
```
が必要
  
### 標準出力切り替え
printfの先をuartからusbに変更
```
pico_enable_stdio_usb(target_name 1)
pico_enable_stdio_uart(target_name 0)
```

## target_link_libraries
|  ライブラリ名  |  概要  |
| ---- | ---- |
|  pico_stdlib  |  commonly used features  |



