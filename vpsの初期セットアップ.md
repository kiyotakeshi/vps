# vpsの初期セットアップ

- 一般ユーザの作成

```bash
$ useradd
$ passwd
```

- rootでログインさせない

```bash
$ vi /etc/ssh/sshd_config

     38 #PermitRootLogin yes
     39 PermitRootLogin no
```

- Sudo権限の設定
    - visudoで編集する(編集が無効だと警告が出るため)

```bash
    101 ## Same thing without a password
    102 # %wheel        ALL=(ALL)       NOPASSWD: ALL
         ### sudo実行時にパスワードを聞かれないように
    103  %wheel ALL=(ALL)       NOPASSWD: ALL
```

- セカンダリグループでsudoが使える「wheel」に追加

```bash
$ usermod -G wheel kiyota
```

- 鍵認証の設定

```bash
### serverでの作業
# 鍵の生成(鍵にパスワードは不要なので、enterを押して生成)
$ ssh-keygen

# 自身の公開鍵を、authorized_keysに登録
# これで秘密鍵を保存したローカルから秘密鍵を指定すれば鍵認証でログインできる
$ cat ~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys
$ chmod 400 /home/kiyota/.ssh/authorized_keys

# パスワード認証できないように設定
$ sudo vi /etc/ssh/sshd_config

     64 #PasswordAuthentication yes
     65 PasswordAuthentication no
     66 #PermitEmptyPasswords no

# 秘密鍵を出力(ローカルに保存するためコピーする)
$ cat ~/.ssh/id_rsa
```

```bash
### ローカル(Mac)での作業
$ mkdir ~/vps

# コピーした秘密鍵をペースト
$ vi ~/vps/vultr.pem

# vpsに接続できることを確認
$ ssh ユーザ名@xxxx.xxxx.xxxx.xxxx
```

- SSHポートの変更

```bash
# お好きなポートに変更(今回は19940)
$ sudo less /etc/ssh/sshd_config

# firewalldの設定ファイルのコピー、ポートの変更
$ sudo cp /usr/lib/firewalld/services/ssh.xml /etc/firewalld/services/ssh-19940.xml
$ sudo vi /etc/firewalld/services/ssh-19940.xml

# firewalldの設定に追加
$ sudo firewall-cmd --permanent --add-service=ssh-19940
success

$ sudo firewall-cmd --reload
success

# 追加できたか確認
$ sudo firewall-cmd --list-services

# デフォルトの22番は閉じる
$ sudo firewall-cmd --permanent --remove-service=ssh
$ sudo firewall-cmd --reload
success

$ sudo firewall-cmd --list-services
```

- hisoryに日時を設定

```bash
$ vi ~/.bash_profile

HISTTIMEFORMAT='%Y/%m/%d %H:%M '
export PATH HISTTIMEFORMAT
```

- プロンプトをカスタム（絶対パスを表示）

```bash
$ vi ~/.bashrc

# PS1はプロンプトの表示内容を設定する環境変数
PS1='[\u@\h \w]\$ '
```

- Aliasの設定
    - rm,mvを確認付きで実行するようにする
    
```bash
$ vi ~/.bash_profile

alias rm='rm -i'
alias mv='mv -i'

# `※「~/.bash_profile」と「~/.bashrc」の違い
# →一般ユーザの設定として「~/etc/bashrc」、「~/.bashrc」、「~/.bash_profile」と読み込まれていく
# →全部「~/.bash_profile」に書いても問題ない
```

- タイムゾーンの設定

```bash
$ sudo yum install ntp
$ sudo ntpdate -s ntp.nc.u-tokyo.ac.jp
```

- アップデート
    - 結構時間がかかる

```bash
$ sudo yum update
```

