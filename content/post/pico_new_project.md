---
title: "picoの新しいプロジェクト作成"
summary: "自分で用意したフォルダでpicoのプロジェクト作成する手法を整理"
date: 2021-09-26T11:00:53+09:00
draft: false
tags: 
  - "Raspberry Pi"
  - "pico"
categories:
  - "電子工作"

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

## 目的
cloneしたサンプルをビルドすることはできたので  
作成したコードをビルドするためのプロジェクトを作成する  

[参考： Getting started with Raspberry Pi Pico ](https://vha3.github.io/Pico/Setup/Getting_started_c.pdf)

## フォルダ構成
サンプルを確認するとルートディレクトリに共通の「CMakeList.txt」があり  
1階層下に個別コード毎の「CMakeList.txt」がある。  
またルートディレクトリの「pico_sdk_import.cmake」と「example_auto_set_url.cmake」をコピーする。  
以上を踏まえて以下のようなフォルダ構成とする

```
~/works/
    ┣ build/　　・・・ makeとかを実行するためのフォルダ
    ┣ ex_01/　　 ・・・ 自前コード
    ┃   ┣ ex_01.cpp
    ┃   ┗ CMakeList.txt
    ┣ CMakeList.txt
    ┣ example_auto_set_url.cmake　　　 ・・・ サンプルからコピーしたもの
    ┗ pico_sdk_import.cmake　　　・・・ サンプルからコピーしたもの    
```

## コード類
###### ルートディレクトリの「CMakeList.txt」
コードを増やす場合はサブディレクトリを追加する
```
cmake_minimum_required(VERSION 3.12)

# Pull in SDK (must be before project)
include(pico_sdk_import.cmake)

project(pico_examples C CXX ASM)
set(CMAKE_C_STANDARD 11)
set(CMAKE_CXX_STANDARD 17)

set(PICO_EXAMPLES_PATH ${PROJECT_SOURCE_DIR})

# Initialize the SDK
pico_sdk_init()
include(example_auto_set_url.cmake)

# sub_directory
add_subdirectory(ex01)
```

###### ex_01の「CMakeList.txt」
ここでは簡単のためにサンプルの「blink」を修正したものをする
```
add_executable(ex_01
        ex_01.cpp
        )

# Pull in our pico_stdlib which pulls in commonly used features
target_link_libraries(ex_01 pico_stdlib)

# create map/bin/hex file etc.
pico_add_extra_outputs(ex_01)

# add url via pico_set_program_url
example_auto_set_url(ex_01)
```

###### ex_01の「コード」
ここでは簡単のためにサンプルの「blink」を引用する
```
/**
 * Copyright (c) 2020 Raspberry Pi (Trading) Ltd.
 *
 * SPDX-License-Identifier: BSD-3-Clause
 */

#include "pico/stdlib.h"

int main() {
#ifndef PICO_DEFAULT_LED_PIN
#warning blink example requires a board with a regular LED
#else
    const uint LED_PIN = PICO_DEFAULT_LED_PIN;
    gpio_init(LED_PIN);
    gpio_set_dir(LED_PIN, GPIO_OUT);
    while (true) {
        gpio_put(LED_PIN, 1);
        sleep_ms(250);
        gpio_put(LED_PIN, 0);
        sleep_ms(250);
    }
#endif
}
```

## build
以下の手順でビルドする
```
$ cd ~/works/build
$ cmake ..
$ make
　　～～
　　[100%] Linking CXX executable ex_01.elf
　　[100%] Built target ex_01
```
無事ビルドが成功すると
```
~/works/build/ex_01/
                ┗ ex_01.elf
```
が生成されている  
