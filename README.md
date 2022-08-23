# SOLID for Raspberry Pi 4

## 必要なもの

- [Raspberry Pi 4 Model B](https://www.raspberrypi.com/products/raspberry-pi-4-model-b/) (4GBまたは8GB RAM)
- 容量16GB以上のSDカード
- ネットワーク接続
  - `Raspberry Pi 4 Model B` とホストPCがネットワークで通信できる必要があります。
- USB-UARTケーブル <!-- TODO: UARTの接続方法をどこかに記載する -->

-------

## チュートリアル

### 準備
1. [SDカードの準備](doc/flashing-sd-card.md)
   - [カスタムイメージの選択](doc/flashing-sd-card.md#カスタムイメージの選択)
   - [設定](doc/flashing-sd-card.md#設定)
   - [イメージの書き込み](doc/flashing-sd-card.md#イメージの書き込み)

2. [開発環境のセットアップ](doc/setup-devenv.md)
   - [インストーラのダウンロード](doc/setup-devenv.md#インストーラのダウンロード)
   - [インストール](doc/setup-devenv.md#インストール)

3. [ライセンスの有効化](doc/license.md)
   - [SSH鍵ペアの作成](doc/license.md#SSH鍵ペアの作成)
   - [SSH公開鍵の転送と書き込み](doc/license.md#SSH公開鍵の転送と書き込み)
   - [`Raspberry Pi Selector`の起動とライセンスの取得](doc/license.md#raspbery-pi-selectorの起動とライセンスの取得)

### プログラムのビルドと実行

1. [既存プロジェクトの実行](doc/running-sample-program.md)
   - [c-blinky-ap804 のビルド](doc/running-sample-program.md#c-blinky-ap804-のビルド)
   - [c-blinky-ap804 の実行とデバッグ](doc/running-sample-program.md#c-blinky-ap804-の実行とデバッグ)

2. [新規プロジェクトの作成](doc/build-and-run-newproject.md)
   - [プロジェクトの作成](doc/build-and-run-newproject.md#プロジェクトの作成)

-------

## マニュアル

- [制限事項](doc/limitations.md)

-------

## リンク集

- [SOLIDドキュメント](http://solid.kmckk.com/doc/skit/current/index.html)
   - [SOLID-OS](http://solid.kmckk.com/doc/skit/current/os/index.html)
   - [SOLID-Rust](http://solid.kmckk.com/doc/skit/current/solid_rust/rust.html) (Rustに関係するIDEの機能やSOLID固有の事項)
- [Nightly Rustドキュメント](https://doc.rust-lang.org/nightly/) (英語)
   - [標準ライブラリのAPIリファレンスマニュアル](https://doc.rust-lang.org/nightly/std/index.html) (英語)
- [Rustの日本語ドキュメント](https://doc.rust-jp.rs/)
- [TOPPERS第3世代カーネル (ITRON系) 統合仕様書 Release 3.5.0](https://toppers.jp/docs/tech/tgki_spec-350.pdf) (SOLID for Raspberry Pi 4のRTOSカーネルは[TOPPERS/FMP3](https://toppers.jp/fmp3-kernel.html)をベースに[機能拡張](http://solid.kmckk.com/doc/skit/current/os/kernel/api_spec.html#toppers)しています)
- [ウィキブックス UNIX/Linux入門](https://ja.wikibooks.org/wiki/UNIX/Linux%E5%85%A5%E9%96%80)
- [Raspberry Pi公式サイト](https://www.raspberrypi.com/) (英語)
   - [BCM2711](https://www.raspberrypi.com/documentation/computers/processors.html#bcm2711) (Raspberry Pi 4 Model BのSoC) (英語)
   - [The config.txt file](https://www.raspberrypi.com/documentation/computers/config_txt.html#what-is-config-txt) (ファームウェアの設定ファイル) (英語)
