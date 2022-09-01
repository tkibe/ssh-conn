# ssh-conn
SSH接続共有

# 0. 事前準備

iTerm2をインストールしてください。

環境に合わせて以下の読み替えをしてください。

[任意の場所] = "~/bin"

# 1. Bashファイルの設置

[任意の場所]に以下のBashファイルを設置します。

$ vi [任意の場所]/ssh-change-profile.sh
```
#!/bin/bash

# set profile
if [ `echo $@|grep prod` ]; then
  echo -ne "\033]1337;SetProfile=prod\a"
else
  echo -ne "\033]1337;SetProfile=stg\a"
fi

# ssh login
/usr/bin/ssh -A "${@/@/}"

# set profile(default)
echo -ne "\033]1337;SetProfile=Default\a"
```

# 2. エイリアスを張る

$ vi ~/.bash_profile
```
alias conn="[任意の場所]/ssh-change-profile.sh"
```

# 3. hostsを設定する

$ vi /etc/hosts
```
##
# Host Database
#
# localhost is used to configure the loopback interface
# when the system is booting.  Do not change this entry.
##
127.0.0.1	localhost
example-prod-server
example-stg-server
test-server
demo-prod-server- 
```

> ここでは `@` で入力補完出来るようにホスト名を設定しておくだけにしておいた方が、 `.ssh/config` に集約出来るのでオススメです。

> `demo-server-` のような設定をしておくと `demo-server-1` や `demo-server-2` のようなホスト名に対して簡単に接続出来ます。

# 4. SSHの接続設定をおこなう

$ vi ~/.ssh/config
```
Host example-stg-server
  Hostname 192.0.2.1
  User [接続ユーザ]
  IdentityFile [秘密鍵のパス]
  TCPKeepAlive yes
  IdentitiesOnly yes

Host demo-prod-server-1
  Hostname 198.51.100.1
  User [接続ユーザ]
  IdentityFile [秘密鍵のパス]
  TCPKeepAlive yes
  IdentitiesOnly yes
```

# 5. 再接続
ターミナルを一度ログアウトして、再接続し `conn @` コマンドを発行する。

```
$ conn @[TAB Key]
```
