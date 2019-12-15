◇SSHポートの変更

- firewalldの確認

```bash
[kiyota@tk2-241-30361 ~]$ systemctl status firewalld

   Active: active (running) since Wed 2018-10-31 11:49:02 JST; 6h ago

[kiyota@tk2-241-30361 ~]$ sudo firewall-cmd --state
running

```

- デフォルトゾーンの設定の確認

```bash
[kiyota@tk2-241-30361 ~]$ sudo firewall-cmd --list-all
public (active)
  target: default
  icmp-block-inversion: no
  interfaces: eth0
  sources:
  services: dhcpv6-client ssh
  ports:
  protocols:
  masquerade: no
  forward-ports:
  source-ports:
  icmp-blocks:
  rich rules:
```

- sshdの設定ファイルを編集、再起動

```bash

# 設定を間違えた時のために、sshクライアントでもう一台分入っておく
[kiyota@tk2-241-30361 ~]$ sudo vi /etc/ssh/sshd_config

     17 #Port 22
     18 Port 19940

[kiyota@tk2-241-30361 ~]$ sudo systemctl restart sshd
```

- firewalldの設定からsshを削除

```bash
[kiyota@tk2-241-30361 ~]$ sudo firewall-cmd --permanent --remove-service=ssh
success

```

- sshに関するfirewalldの設定ファイルの変更

```bash
[kiyota@tk2-241-30361 ~]$ sudo cp /usr/lib/firewalld/services/ssh.xml /etc/firewalld/services/ssh-19940.xml

[kiyota@tk2-241-30361 ~]$ sudo vi /etc/firewalld/services/ssh-19940.xml

  <port protocol="tcp" port="19940"/>

```

- firewalldの設定に追加、設定を読み込む

```bash
[kiyota@tk2-241-30361 ~]$ sudo firewall-cmd --permanent --add-service=ssh-19940
success
[kiyota@tk2-241-30361 ~]$ sudo firewall-cmd --reload
success
```

- 設定が反映されたか確認

```bash
[kiyota@tk2-241-30361 ~]$ sudo firewall-cmd --list-all
public (active)
  target: default
  icmp-block-inversion: no
  interfaces: eth0
  sources:
  services: dhcpv6-client ssh-19940
  ports:
  protocols:
  masquerade: no
  forward-ports:
  source-ports:
  icmp-blocks:
  rich rules:

# もしくは下記のコマンドで確認
[kiyota@tk2-241-30361 ~]$ sudo firewall-cmd --list-services
dhcpv6-client ssh-19940
```

- sshクライアントで変更したポートを指定してログインする
