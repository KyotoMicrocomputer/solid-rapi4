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
#define TNUM_AID_ISR    0           // ISR (割込みサービスルーチン)
#define TNUM_AID_RWL    30000       // RWロック
```

> **注意:** 動的生成資源数の上限は使用に支障が生じないよう多めに設定してありますが、将来のバージョンで予告なく変更される可能性があります。


### 割込みハンドラ

割込みハンドラはSOLID-OSが提供する [`SOLID_INTC_Register`][6] 関数を使用して登録してください。このシステムではRTOSカーネルで扱うものとして静的に[定義][7]された割込みラインは存在しないため `acre_isr` などの関数は機能しません。


### その他

TOPPERSカーネルのシステムログ機能は使用できません。


## SOLID-IDE

[アドレスサニタイザ][4]、[関数トレース][2]、[コードカバレッジ][8]、および[ハードウェアトレース][3]などの使用できない機能があります。 <!-- RM #3371 #3374 -->

メモリマップは固定です。

<!-- TODO: check if XRay is really unavailable -->


## OS間通信 (OSCOM RPC)

- ファイルシステムAPI
    - SOLIDファイルシステムAPIでサポートされていない種類のファイル (e.g., シンボリックリンク) に関する操作は正常に行われないことがあります。

- ソケットAPI
    - Ancillary dataはサポートされていません。
    - データグラムメッセージのサイズは約4KBに制限されており、制限を超える送信メッセージは `SOLID_NET_ERR_EMSGSIZE` エラーを起こし、受信メッセージは `SOLID_NET_Recv` 等のAPI呼出しを処理する過程で破棄されます。
    - `SOLID_NET_IOCTL_FIONREAD` で次回受信メッセージのサイズを取得するとき、上の理由により破棄されるメッセージのサイズが返されることがあります。
    - `SOLID_NET_GetAddrInfo` の入出力サイズには上限があり、これを超えると `SOLID_NET_ERR_EAI_FAIL` エラーを返して失敗します。
    - `SOLID_NET_Poll` に渡せる要素数は64個に制限されています。<!-- OSCOM_MAX_NFDS -->`SOLID_NET_Select` は内部で `SOLID_NET_Poll` を使用しており、同様の制限を受けます。EPollはこの制限を受けません。
    - `SOLID_NET_Send*` は `WAITALL` フラグをサポートしていません。<!-- RM #3457 -->

## モニタデバッガ

本開発システムではネットワーク越しにプログラムをデバッグするために、[モニタデバッガ][1]という仕組みを利用しています。動作原理上、以下の場合はソフトリセットでは復旧できず、システムの再起動を要する状況になります。SOLID-IDEからデバッグ開始したときにエラーが起こる場合、Raspberry Piを再起動してください。

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
[8]: http://solid.kmckk.com/doc/skit/current/user_guide/code_coverage.html
