## ライセンスの有効化

### キーの作成

ホストPCで以下のコマンドを実行し、キーを生成します。

```
ssh-keygen -t rsa

Enter file in which to save the key (C:\Users\<Username>/.ssh/id_rsa): # Enter
Enter passphrase (empty for no passphrase): # Enter
Enter same passphrase again: # Enter
```

### キーの転送と書き込み

SCPなどで Raspberry Pi4 側に生成した `id_rsa.pub` を転送してください。

```
scp id_rsa.pub user@hostname:
```

Raspberry Pi4 側で `authorized_keys` に書き込みます。

```
mkdir .ssh
chmod 700 .ssh
cat id_rsa.pub >> .ssh/authorized_keys
chmod 600 .ssh/authorized_keys
rm id_rsa.pub
```

### `Raspbery Pi Selector`の起動とライセンスの取得

ソリューションを開くと、`Raspbery Pi Selector` が自動で起動します。
`Search` ボタンを押すとネットワーク内に存在する Raspberry Pi 4 の IPアドレスリストが
表示されるので、キーを書き込んだターゲットを選択して、`Connect` を押してください。

   ![select-rpi4](img/rpi4-selector.png)
