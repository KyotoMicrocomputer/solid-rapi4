# 制限事項

本ドキュメントでは、SOLID for Raspberry Pi 4のユーザアプリケーションに課される技術的な制限事項について説明します。


## SOLID-OS

### カーネル動的資源生成

[動的生成可能なカーネル資源数][5]は固定されており、現在は以下の値に設定されています。

```c
#define TNUM_AID_TSK    500         // タスク
#define TNUM_AID_SEM    5000        // セマフォ
#define TNUM_AID_FLG    5000        // イベントフラグ
#define TNUM_AID_DTQ    2000        // データキュー
#define TNUM_AID_PDQ    2000        // 優先度データキュー
#define TNUM_AID_MTX    30000       // ミューテックス
#define TNUM_AID_MBF    2000        // メッセージバッファ
#define TNUM_AID_MPF    1000        // 固定長メモリプール
#define TNUM_AID_CYC    2000        // 周期通知
#define TNUM_AID_ALM    2000        // アラーム通知
#define TNUM_AID_ISR    1000        // ISR (割込みサービスルーチン)
#define TNUM_AID_RWL    30000       // RWロック
```

> **注意:** 動的生成資源数の上限は使用に支障が生じないよう多めに設定してありますが、将来のバージョンで予告なく変更される可能性があります。


### 割込みハンドラ

割込みハンドラはSOLID-OSが提供する [`SOLID_INTC_Register`][6] 関数を使用して登録してください。このシステムではRTOSカーネルで扱うものとして静的に[定義][7]された割込みラインは存在しないため `acre_isr` などの関数は機能しません。


## SOLID-IDE

[アドレスサニタイザ][4]、[関数トレース][2]、および[ハードウェアトレース][3]などの使用できない機能があります。

メモリマップは固定です。

<!-- TODO: check if XRay is really unavailable -->


## モニタデバッガ

本開発システムではネットワーク越しにプログラムをデバッグするために、[モニタデバッガ][1]という仕組みを利用しています。動作原理上、以下の場合はソフトリセットでは復旧できず、システムの再起動を要する状況になります。SOLID-IDEからの「デバッグ開始」でエラーになる場合、Raspberry Piを再起動してください。

- いずれかのSOLIDコアが、優先度がタイマ割込みと同じまたはそれを超える割込みを無効化したままハングアップした、あるいは他コアの処理の完了を待つ状態に入った。例えば:
    - カーネル関数内でブレークまたはハングアップした場合。
    - SOLIDクリティカルセクション内でブレークまたはハングアップし、それと同時に別のコアが同じSOLIDクリティカルセクションをロックしようとしている場合。
    - いずれかのコアがCPUロック状態でハングアップした場合。
    - タイマ割込みの処理がハングアップした場合。
- SOLID Core Servicesが使用するメモリ領域を破壊した、またはメモリマップを変更した場合。
- Linuxがハングアップした、またはカーネルの動作が阻害された。例えば:
    - Linuxが使用するメモリ領域を破壊した場合。
    - Linuxが使用する割込みラインの構成を変更した場合。
- その他、特にシステムレジスタの変更など。


[1]: http://solid.kmckk.com/doc/skit/current/os/solution-config.html?highlight=%E3%83%A2%E3%83%8B%E3%82%BF#solid-os
[2]: http://solid.kmckk.com/doc/skit/current/user_guide/function_trace.html
[3]: http://solid.kmckk.com/doc/skit/current/user_guide/hardware_trace.html
[4]: http://solid.kmckk.com/doc/skit/current/user_guide/address_sanitizer.html
[5]: http://solid.kmckk.com/doc/skit/current/os/kernel/kernel_config.html#id68
[6]: http://solid.kmckk.com/doc/skit/current/os/cs/intc.html#c.SOLID_INTC_Register
[7]: http://solid.kmckk.com/doc/skit/current/os/kernel/kernel_config.html#id63