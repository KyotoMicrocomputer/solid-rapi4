# ライセンスの有効化

## SSH鍵ペアの作成

ホストPCで以下のコマンドを実行し、SSH鍵ペア (公開鍵と秘密鍵) を生成します。

```
> ssh-keygen -t ed25519

Enter file in which to save the key (C:\Users\<Username>/.ssh/id_ed25519): # Enter
Enter passphrase (empty for no passphrase): # Enter
Enter same passphrase again: # Enter
```

> **注意:** 鍵ペアが既に存在する場合、 `Overwrite (y/n)?` という確認メッセージが表示されます。この場合は新たに鍵ペアを生成する必要はありません。 `n` を入力して中止してください。 **鍵ペアを上書きしてしまった場合、元に戻す方法はありません。**

## SSH公開鍵の転送と書き込み

生成した `id_ed25519.pub` を[SCP](system-linux.md#secure-copy)などのプログラムを使用してRaspberry Pi 4側に転送してください。

```
> scp id_ed25519.pub user@hostname:
```

Raspberry Pi 4に[SSH](system-linux.md#sshリモートログイン)でリモートログインし、転送した `id_ed25519.pub` を `authorized_keys` ファイルに追加してください。

```
> ssh user@hostname

$ mkdir .ssh
$ chmod 700 .ssh
$ cat id_ed25519.pub >> .ssh/authorized_keys
$ chmod 600 .ssh/authorized_keys
$ rm id_ed25519.pub
```
## Raspberry Pi Selectorの起動とライセンスの取得

ソリューションを開くと、Raspberry Pi Selectorが自動で起動します。
**Search** を押すとネットワーク内に存在するRaspberry Pi 4のリストが表示されるので、IPアドレスやホスト名 (HN) などを確認し、SSH公開鍵を書き込んだターゲットを選択して **Save** を押してください。

   ![lm-select-target](img/lm-select-target.png)

任意のプロファイル名を **Name** に、ログイン時のユーザ名を **User** にそれぞれ入力し、 **保存**を押します。

   ![lm-new-profile](img/lm-new-profile.png)

**Home** 画面から保存したプロファイルを選択して、 **接続** を押してください。

   ![lm-connect](img/lm-connect.png)
