<h1 align="center">
  <picture>
    <source media="(prefers-color-scheme: light)" srcset="doc/img/solid-rpi4b.svg">
    <source media="(prefers-color-scheme: dark)" srcset="doc/img/solid-rpi4b-dark.svg">
    <img src="doc/img/solid-rpi4b.svg" alt="SOLID for Raspberry Pi 4">
  </picture>
</h1>

<p align="center">
   <a href="doc/download.md"><b>ダウンロード</b></a> |
   <a href="https://solid.kmckk.com/SOLID/doc/latest/"><b>SOLIDドキュメント</b></a> |
   <a href="https://github.com/KyotoMicrocomputer/solid-rapi4-examples"><b>サンプル集</b></a>
</p>

**SOLID for Raspberry Pi 4**は[SOLID組込みソフトウェア開発プラットフォーム](https://solid.kmckk.com/SOLID/)を[Raspberry Pi 4 Model B](https://www.raspberrypi.com/products/raspberry-pi-4-model-b/)上で簡単にお試しいただけるソフトウェアパッケージです。Raspberry Pi OS + SOLID-OSを書き込んだSDカードからRaspberry Piをブートし、ホストPCにインストールした開発環境からネットワーク経由で接続するだけでベアメタル・RTOSアプリケーション開発が始められます。別のコアでLinuxが同時に動作しているため、RTOSとLinuxの連携を活かしたアプリケーションの開発方法を検討するのにも役立ちます。

> 本レポジトリはSOLID for Raspberry Pi 4に関する情報を提供することを目的としています。[Issues](https://github.com/KyotoMicrocomputer/solid-rapi4/issues) (付属のバグトラッカー) はバグ報告の場、[Discussions](https://github.com/KyotoMicrocomputer/solid-rapi4/discussions)は技術サポートフォーラムとして使用することができます。(弊社による返答・対応を保証するものではありません。あらかじめご了承ください。)

## 必要なもの

- [Raspberry Pi 4 Model B](https://www.raspberrypi.com/products/raspberry-pi-4-model-b/) (4GBまたは8GB RAM)
- 容量16GB以上のmicroSDカード
- Windows PC (64-bit Windows 10 or 11)
- ネットワーク接続
  - Raspberry Pi 4 Model BとホストPCがネットワークで通信できる必要があります。
- USB-UARTケーブル ([ログ出力](doc/system-rtos.md#ログ出力)の表示に必要)

-------

## チュートリアル

SOLID for Raspberry Pi 4の基本的な使い方を手順を追って説明します。

### 準備
1. [SDカードの準備](doc/flashing-sd-card.md)
   - [カスタムイメージの選択](doc/flashing-sd-card.md#カスタムイメージの選択)
   - [設定](doc/flashing-sd-card.md#設定)
   - [イメージの書き込み](doc/flashing-sd-card.md#イメージの書き込み)
   - [起動](doc/flashing-sd-card.md#起動)

2. [開発環境のセットアップ](doc/setup-devenv.md)
   - [インストーラのダウンロード](doc/setup-devenv.md#インストーラのダウンロード)
   - [インストール](doc/setup-devenv.md#インストール)

3. [ライセンスの有効化](doc/license.md)
   - [SSH鍵ペアの作成](doc/license.md#SSH鍵ペアの作成)
   - [SSH公開鍵の転送と書き込み](doc/license.md#SSH公開鍵の転送と書き込み)
   - [Raspberry Pi Selectorの起動とライセンスの取得](doc/license.md#raspberry-pi-selectorの起動とライセンスの取得)

### プログラムのビルドと実行

1. [既存プロジェクトの実行](doc/running-sample-program.md)
   - [cpp-blinky-ap804のビルド](doc/running-sample-program.md#cpp-blinky-ap804-のビルド)
   - [cpp-blinky-ap804の実行とデバッグ](doc/running-sample-program.md#cpp-blinky-ap804-の実行とデバッグ)

2. [新規プロジェクトの作成](doc/build-and-run-newproject.md)
   - [プロジェクトの作成](doc/build-and-run-newproject.md#プロジェクトの作成)
   - [スタティックライブラリの追加](doc/add-static-library.md#スタティックライブラリの追加)

-------

## ドキュメンテーション

SOLID for Raspberry Pi 4の仕様と留意事項について説明します。

- [Linuxシステム](doc/system-linux.md)

- [SOLID-OSシステム](doc/system-rtos.md)

- [トラブルシューティング](doc/troubleshooting.md)

- [制限事項](doc/limitations.md)

-------

## リンク集

- [SOLIDドキュメント](https://solid.kmckk.com/SOLID/doc/latest/index.html)
   - [SOLID-OS](https://solid.kmckk.com/SOLID/doc/latest/os/index.html)
   - [SOLID-Rust](https://solid.kmckk.com/SOLID/doc/latest/solid_rust/rust.html) (Rustに関係するIDEの機能やSOLID固有の事項)
- [Nightly Rustドキュメント](https://doc.rust-lang.org/nightly/) (英語)
   - [標準ライブラリのAPIリファレンスマニュアル](https://doc.rust-lang.org/nightly/std/index.html) (英語)
- [Rustの日本語ドキュメント](https://doc.rust-jp.rs/)
- [TOPPERS第3世代カーネル (ITRON系) 統合仕様書 Release 3.5.0](https://toppers.jp/docs/tech/tgki_spec-350.pdf) (SOLID for Raspberry Pi 4のRTOSカーネルは[TOPPERS/FMP3](https://toppers.jp/fmp3-kernel.html)をベースに[機能拡張](https://solid.kmckk.com/SOLID/doc/latest/os/kernel/api_spec.html#toppers)しています)
- [ウィキブックス UNIX/Linux入門](https://ja.wikibooks.org/wiki/UNIX/Linux%E5%85%A5%E9%96%80)
- [Raspberry Pi公式サイト](https://www.raspberrypi.com/) (英語)
   - [BCM2711](https://www.raspberrypi.com/documentation/computers/processors.html#bcm2711) (Raspberry Pi 4 Model BのSoC) (英語)
   - [The config.txt file](https://www.raspberrypi.com/documentation/computers/config_txt.html#what-is-config-txt) (ファームウェアの設定ファイル) (英語)
