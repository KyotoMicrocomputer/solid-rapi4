# トラブルシューティング

本ドキュメントではSOLID for Raspberry Pi 4を使用中に起こる可能性のある問題と解決策を示します。

## ハードウェア

<details>
<summary><b>手元にあるRaspberry Pi 4 Model Bのメモリサイズが分からない。</b></summary>

Raspberry Pi 4上で `/proc/cpuinfo` から[モデルリビジョンコード][4]を取得して判別できます。2022年の時点では以下の規則に従っているようです。

| リビジョンコード | モデル | メモリサイズ |
| ---------------- | ------ | ------------ |
| `a0311*`         | 4B     | 1GB          |
| `b0311*`         | 4B     | 2GB          |
| `c0311*`         | 4B     | 4GB          |
| `d0311*`         | 4B     | 8GB          |

例えば、以下の例ではRaspberry Pi 4 Model B 8GBモデルであると分かります。

```
$ cat /proc/cpuinfo | grep Revision
Revision        : d03114
```

</details>

## ネットワーク

<details>
<summary><b>Raspberry Piに接続できなくなった。</b></summary>

Linuxカーネルがハングアップしている可能性が疑われます。UARTコンソールでエンターキーを入力し、Linuxのログインプロンプトが表示されるか確認してください。UARTコンソールも反応しない場合、Raspberry Piの電源を切断して再起動することが必要です。

SOLID-OSとLinuxは完全に分離されていないため、モニタデバッガやロードしたアプリケーションの誤作動が原因でLinuxがハングアップする可能性があります。例えば、Linuxが使用しているペリフェラルの割込みラインの設定をSOLID-OSから変更することは推奨されません。

</details>

<details>
<summary><b>インターネットの接続の調子が悪くなった。</b></summary>

Raspberry Piがハングアップしていないか確認してください。Linuxカーネルがハングアップするとハードウェアが大量の[Pauseフレーム][2]を送信し、一部のネットワーク機器の動作に一時的な支障を来す場合があることを確認しています。

</details>

<details>
<summary><b>Raspberry Piのファイアウォールを有効化したらRaspberry Pi Selectorに表示されなくなった。</b></summary>

Raspberry PiがUDPポート51590のパケットが受信できるように設定してください。

</details>

## ビルド

<details>
<summary><b>Rustプロジェクトのビルドが "No such file or directory" エラーで失敗する</b></summary>

Rust MinGWツールチェーンの[制約][3]です。ソリューションを非ASCII文字を含まない短いパスの場所に移動して再度お試しください。ソリューションの完全パスが110文字以内に収まるようにすることを推奨します。

</details>

## デバッグ

<details>
<summary><b>"アプリケーションホストからの応答がありません。ターゲットシステムの再起動が必要な可能性があります。" というエラーが出た。</b></summary>

デバッグモニタがハングアップしていることが考えられます。この場合、 `sudo reboot` コマンドなどの方法でRaspberry Piを再起動することが必要です。これはモニタデバッガの[技術的制限](limitations.md)です。

</details>

<details>
<summary><b>"ターゲットにアタッチ中のデバッガが既に存在します。" というエラーが出た。</b></summary>

他にデバッガのインスタンスが存在しないのにもかかわらずこのメッセージが表示される場合、次のコマンドをRaspberry Pi上で実行することで、Ghostdを強制的に再起動し、デバッグモニタへの接続をすべて切断することができます。

```
$ sudo systemctl restart ghostd
```

</details>

<details>
<summary><b>ステップ実行が非常に遅い。</b></summary>

[RTOSビューア][5]が表示中の場合、実行を停止するたびにウィンドウの内容を更新するため、 ステップ実行などの動作が極端に遅くなります。ステップ実行を行う際は非表示にすることをお勧めします。

</details>

## アプリケーション開発

<details>
<summary><b><code>acre_isr</code> 関数が <code>E_OBJ</code> を返して失敗する。</b></summary>

割込みハンドラはSOLID-OSが提供する [`SOLID_INTC_Register`][1] 関数を使用して登録してください。[`acre_isr` 関数は使用できません。](limitations.md#割込みハンドラ)

</details>

<details>
<summary><b><code>main</code> 関数を作ったが呼び出されない。</b></summary>

SOLID for Raspberry Pi 4アプリケーションはSOLID独自のローダブル形式を使用しており、 `slo_main` がRTOSメインタスクから呼び出されるエントリポイント (入り口関数) となります。このエントリポイントは各言語で以下のようにして定義できます。

```c
void slo_main() { /* ... */ }                   // C
```

```c++
extern "C" void slo_main() { /* ... */ }        // C++
```

```rust
#[no_mangle]
pub extern "C" fn slo_main() { /* ... */ }      // Rust
```

</details>

<details>
<summary><b>メインタスクのスタックを大きくしたい。</b></summary>

メインタスクのスタックサイズは変更できないため、独自のスタックサイズを指定した新しいタスクを起動してください。

```c++
// C++
const T_CTSK inner_main_task_opts = {
    .tskatr = 0,
    .exinf = 0,
    .task = inner_main_task,
    .itskpri = 5,
    .stksz = 1024 * 256, // 256KiB
    .stk = NULL,
    .iprcid = 1,
    .affinity = 0b01,
};
acre_tsk(&inner_main_task_opts);
```

```rust
// Rust
std::thread::Builder::new()
    .stack_size(256 * 1024) // 256KiB
    .spawn(inner_main_task)
    .expect("failed to spawn an inner main task")
    .join()
    .expect("inner main task panicked");
```

</details>

## Linux

<details>
<summary><b>実行可能ファイルを起動すると "そのようなファイルはありません" 旨のエラーが出て失敗する</b></summary>

`file` コマンドを使用して実行可能ファイルのターゲットCPUアーキテクチャを確認してください。SOLID for Raspberry Pi 4のOSイメージのRaspberry Pi OSは **64-bit Arm (AArch64)** バージョンを使用しており、32-bit Arm向けにビルドされたプログラムは初期状態では起動できない場合があります。

```
$ file xxx
xxx: ELF 64-bit LSB pie executable, ARM aarch64, version 1 (SYSV), dynamically linked, interpreter /lib/ld-linux-aarch64.so.1, ...
         ^^^^^^                     ^^^^^^^^^^^
            '---------------+-------------'
                            |
                           good     

$ file xxx
xxx: ELF 32-bit LSB pie executable, ARM, EABI5 version 1 (SYSV), dynamically linked, interpreter /lib/ld-linux-armhf.so.3, ...
         ^^^^^^                     ^^^
            '-------------+----------'
                          |
                         BAD       
```

32-bit Arm向けにビルドされたプログラムを起動するためには32-bit Arm向けのシステムライブラリを別途インストールする必要があります。(Stack Exchange, "[How to run 32-bit (armhf) binaries on 64-bit (arm64) Debian OS on Raspberry Pi?](https://unix.stackexchange.com/a/683867)")

</details>


[1]: http://solid.kmckk.com/doc/skit/current/os/cs/intc.html#c.SOLID_INTC_Register
[2]: https://en.wikipedia.org/wiki/Ethernet_flow_control#Pause_frame
[3]: http://solid.kmckk.com/doc/skit/current/troubleshooting/rust-path-length.html
[4]: https://www.raspberrypi.com/documentation/computers/raspberry-pi.html#new-style-revision-codes-in-use
[5]: http://solid.kmckk.com/doc/skit/current/user_guide/rtos_viewer.html
