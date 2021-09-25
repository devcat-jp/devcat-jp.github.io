---
title: "picoのC/C++開発環境構築"
date: 2021-09-12T15:31:32+09:00
draft: false
summary: "WSL2_Ubuntu20.04を使用したpicoの開発環境構築とLチカサンプルの実行を行う"
tags: [Raspberry Pi, pico]
---

## 使用環境
* Win10 Pro
* WSL2 + Ubuntu20.04
* VScode

## 環境構築手順
1.Ubuntu20.04に必要なパッケージのインストール
```
$ sudo apt update
$ sudo apt install build-essential cmake gcc-arm-none-eabi git curl
```

2.pico-sdkのインストール
```
$ mkdir ~/pico
$ cd ~/pico
$ git clone https://github.com/raspberrypi/pico-sdk
$ cd ~/pico/pico-sdk
$ git submodule update --init
```

3.pico-sdkのパス通し
```
$ echo export PICO_SDK_PATH=~/pico/pico-sdk >> ~/.bashrc
$ source ~/.bashrc
```

4.picoサンプルの入手
```
$ cd ~/pico
$ git clone https://github.com/raspberrypi/pico-examples
```
5.Lチカサンプル（blink）のビルド
```

$ cd ~/pico/pico-examples
$ mkdir build
$ cd build
$ cmake ..
　　成功すると末尾に以下のような表示
　　-- Configuring done
　　-- Generating done
　　-- Build files have been written to: 作業パス
$ cd blink
$ make
$ ls | grep blink.uf2
    blink.uf2
```

6．生成プログラムのコピー  
pico本体の「BOOTSEL」を押下しながらWinPCとUSBケーブルで接続する。  
するとUSBストレージとして認識されるので先ほどの「blink.uf2」をコピーする  

7.実行  
コピーした段階で自動的に実行開始する  
正しく作業できていた場合はpico本体のLEDがチカチカしているのを確認できるはず