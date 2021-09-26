---
title: "picoでusbを使用したシリアル通信"
summary: "usbを使用してPCと双方向通信を行う"
date: 2021-09-26T14:47:01+09:00
draft: false

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
簡易的なデバッグ作業やpicoに対する指示を行うために
usbを利用したシリアル通信方法を調査・記録する

[参考： yoshiyuki's blog ](https://ysin1128.hatenablog.com/entry/2021/08/25/143519)

## サンプルコード調査
usbを使用したシリアル通信サンプルは下記に存在している
```
pico-examples\hello_world\usb
```
このコードやネット情報を確認すると重要なポイントとして  
CMakeLists.txtに下記を記載することのようだ  
```
pico_enable_stdio_usb(pj_name 1)
pico_enable_stdio_uart(pj_name 0)
```
この記載を行うことで標準入出力の対象がusbになり逆にuartが無効となる  
実際にサンプルを実行させ、PCのTeraTermを使用して文字列の受信ができる

## PCからのデータ受信
printfでpicoからの文字列を受信できたので  
逆方向はscanfでPCからの文字列を受信できるかと思って試す…が  
ビルドは通るが自分の環境ではusbデバイスが正しく認識されず通信できなかった。  
そのため冒頭の参考サイトを参考に「TinyUSB」のAPIを使用して受信処理を検討する。  
使用するTinyUSBのヘッダファイルは下記場所にある
```
pico\pico-sdk\lib\tinyusb\src\class\cdc\cdc_device.h
```
今回使用するAPIは以下となる
|  API  |  概要  |
| ---- | ---- |
|  uint32_t tud_cdc_available (void)  |  読み込み可能なバイト数の取得  |
|  int32_t tud_cdc_read_char (void);  |  1バイト取得  |

これらを使用することでusbデバイスが認識され  
TeraTermから送信した文字列を受信できることを確認できた。

## 双方向通信サンプル
作成例として、PCから「led」と送信したらpicoのLEDを点灯する例を作成する。

###### CMakeLists.txt
```
if (TARGET tinyusb_device)
    add_executable(ex_02
            ex_02.cpp
            )

    # Pull in our pico_stdlib which aggregates commonly used features
    target_link_libraries(ex_02 pico_stdlib)

    # enable usb output, disable uart output
    pico_enable_stdio_usb(ex_02 1)
    pico_enable_stdio_uart(ex_02 0)

    # create map/bin/hex/uf2 file etc.
    pico_add_extra_outputs(ex_02)

    # add url via pico_set_program_url
    example_auto_set_url(ex_02)
elseif(PICO_ON_DEVICE)
    message(WARNING "not building ex_02 because TinyUSB submodule is not initialized in the SDK")
endif()
```

###### ex_02.cpp
```
#include <stdio.h>
#include "pico/stdlib.h"
#include "class/cdc/cdc_device.h"

int main() {
    stdio_init_all();

    unsigned short cmd_buff_index_ = 0;
    char cmd_buff[256] = {0};
    char tmp_input;
    
    const uint LED_PIN = PICO_DEFAULT_LED_PIN;
    gpio_init(LED_PIN);
    gpio_set_dir(LED_PIN, GPIO_OUT);

    while (true) {
        // RxBuffにデータが存在する
        if(tud_cdc_available() > 0){
            // 1文字受信
            tmp_input = tud_cdc_read_char();

            // 改行文字か？
            if(tmp_input == '\r') {
                // null文字付与
                cmd_buff[cmd_buff_index_] = '\0';
                // コマンド判断
                char* command = &cmd_buff[0];
                if(strcmp(command, "led") == 0) {
                    printf("LED Blink\n");
                    gpio_put(LED_PIN, 1);
                    sleep_ms(250);
                    gpio_put(LED_PIN, 0);
                    sleep_ms(250);
                } else {
                    printf("no such command\n");
                }
                // index clear
                cmd_buff_index_ = 0;
            } else {
                cmd_buff[cmd_buff_index_] = tmp_input;
                cmd_buff_index_++;
            }
        }
        // wait
        sleep_ms(1);
    }
}
```