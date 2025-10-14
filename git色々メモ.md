# Git関連メモ

## SSH鍵の生成

```bash
ssh-keygen -t ed25519 -C "despocampi.foot@gmail.com"
```

## Git設定
特定のディレクトリ内やリポジトリ内でuser.nameやemailを変更したいときは以下のように`--global`を抜いて実行する｡

```bash
git config user.name "ryoya-masuda-unirita"
git config user.email "ryouya_masuda@unirita.co.jp"
```

## SSH Config設定

`~/.ssh/config`ファイルに以下の設定を追加：

```bash
# 個人用GitHubアカウント
Host github.com
  HostName github.com
  User git
  IdentityFile ~/.ssh/id_ed25519

# Unirita PDG用GitHubアカウント
Host github.com-unirita-pdg
  HostName github.com
  User git
  IdentityFile ~/.ssh/id_ed25519_unirita
```

この設定により、リポジトリURLの`@github.com-unirita-pdg:`部分が`github.com-unirita-pdg`というHost設定にマッチし、対応する秘密鍵が使用されます。<br>
<br>
※gitHubCode→SSHの下のURLをコピーすると`github.com`になっているが`~/.ssh/config`で
`Host github.com-unirita-pdg`に設定した場合には`github.com`→`github.com-unirita-pdg`に変更する必要がある｡

## リポジトリURL

### 通常のURL
```
git@github.com:product-design-group/metadata-management-depot.git
```

### Unirita PDG用のURL
`github.com`→`github.com-unirita-pdg`に変更
```
git@github.com-unirita-pdg:product-design-group/metadata-management-depot.git
```
※通常CodeのSSHからコピーすると`github.com:hogehoge/mogemoge ~`になるので`github.com`の部分を書き換える必要がある｡

## SSH設定の変更方法

SSHのURLをコピーして、以下のように書き換える：

- 変更前: `@github.com:`
- 変更後: `@github.com-unirita-pdg:`

