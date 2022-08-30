# Linuxシステム

SOLID for Raspberry Pi 4で使用するLinuxシステムについて説明します。

<div align="center"><img src="img/architecture-linux.svg" alt=""></div>


## OSイメージ

LinuxシステムのOSイメージはRaspberry Pi OSの公式イメージ作成ツール [pi-gen](https://github.com/RPi-Distro/pi-gen) を使用してビルドされています。SOLID for Raspberry Pi 4固有のアプリケーション・デバイスドライバを追加していますが、それ以外は本家Raspberry Pi OS (64-bit) とほとんど変わりません。


## カーネルイメージ

`raspberrypi-kernel` パッケージが提供する公式サポートされたLinuxカーネルを使用しますが、イメージにパッチを当ててブート手順を追加することでCPU0/1で[SOLID-OS](system-rtos.md)を最初に起動するようにしています。Linuxカーネルは残りのCPU2/3で起動します。

パッチは `ghost-rpi4` パッケージが登録する `/etc/kernel/postinst.d` フックによってLinuxカーネルが更新されるたびに自動的に再適用されます。 `/boot/config.txt` で次のように指定することでパッチ済みカーネルからブートしています。

```ini
# Load the special kernel containing a Linux kernel and a SOLID-OS
# loadable application host ("Ghost").
kernel=ghost-rpi4.bin.gz
```

> SOLID-OSのブートを一時的に無効化したい場合、この `kernel` ディレクティブをコメントアウトして再起動してください。 `/boot` はFATパーティションなのでWindowsからでもマウントして編集できます。


## Ghostd

**Ghostd**はモニタデバッガからデバッグモニタへの通信やSOLIDアプリケーションから行われるファイルシステム・ネットワークAPIコールを仲介するデーモンプログラムです。これはsystemd unit `ghostd.service` として登録されており、 `systemctl restart ghostd` などのコマンドで動作を制御することができます。

設定ファイルは `/etc/ghostd.toml` にあり、ファイルシステム・ネットワークAPIコールをLinuシステムコールに変換して処理するサーバー (OSCOM RPCサーバー) をどのユーザ・グループの権限で起動するか (`rpc.{user,group}`) などを指定できます。

> **注意:** SOLID for Raspberry Pi 4では `listen` キー以下の設定を変更することはサポートされておらず、動作に支障を生じる可能性があります。また、OSCOM RPCサーバーの分離は不完全であり、 `rpc.{user,group}` はセキュリティ機構として使用すべきではありません。


## Ghost CLI

**Ghost CLI**はGhostdに付属するツールで、これを使用することで動作中のGhostdにコマンドを送信し、バージョンを取得したり、アプリケーションイメージ (`.slo` ファイル) をデバッガをアタッチせずにロードしたりすることができます。

```bash
  # アプリケーションをロードする
$ ghost load myapp.slo

  # バージョン情報を表示する
$ ghost version
```

`/etc/ghostd-autorun.slo` にアプリケーションイメージを置くとLinux起動時に `ghostd-autorun.service` systemd unitによって自動的にロードされます。


## リモートアクセス

開発中は開発環境からLinuxシステムにリモートアクセスできると便利ですが、これには以下で説明する方法があります。

### SSHリモートログイン

Raspberry Pi SelectorからRaspberry Piに接続するためには [ライセンスの有効化](./license.md) で説明しているようにSSHの設定が必須なため、開発環境からリモートログインするにはSSHが最も自然な方法です。

SSHを使用してリモートログインするには以下のコマンドを実行します。 `pi` と `raspberrypi` はそれぞれ[イメージ書き込み時](./flashing-sd-card.md)にRaspberry Pi Imagerで指定したホスト名とユーザー名で置き換えてください。

```pwsh
> ssh pi@raspberrypi
```


### Secure Copy

Secure Copy (`scp`) はSSHを経由してファイルをコピーするコマンドです。

```pwsh
  # カレントディレクトリにある myfile.txt を
  # リモートホストのホームディレクトリ (/home/pi) にコピーする
> scp myfile.txt pi@raspberrypi:

  # リモートホストのホームディレクトリ (/home/pi) にある myfile.txt を
  # カレントディレクトリにコピーする
> scp pi@raspberrypi:myfile.txt .
```

### Samba

SambaはWindowsの「共有フォルダー」のプロトコルの実装です。LinuxではSambaをインストールすることにより、Linux上のフォルダーを共有したり、共有フォルダーをLinuxファイルシステムにマウントしたりできます。

設定方法に関しては[Raspberry Pi公式ドキュメント](https://www.raspberrypi.com/documentation/computers/remote-access.html#samba-smbcifs) (英語) を参照してください。


### UARTシリアルコンソール

このOSイメージでは `/boot/config.txt` の [`enable_uart`](https://www.raspberrypi.com/documentation/computers/config_txt.html#enable_uart) がデフォルトで有効になっているため、UART (GPIO 14/15) はLinuxコンソールとして使用できます。ボーレートは115200に設定されており、デフォルト文字セットはUTF-8です。

> **注意:** SOLID-OSの出力も同じUARTポートを使用するため、同時に出力が行われると出力内容が壊れることがあります。
