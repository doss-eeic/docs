# Git 使い方講座

## 基本操作

### リポジトリを初期化

git clone してきた場合は不要

```bash
$ git init
```

### 名前・メールアドレスを設定

ここで設定した名前やメールアドレスはリポジトリを clone できる人なら確認できるので注意すること。

```bash
$ git config --local user.email "<メールアドレス>"
$ git config --local user.name "<名前>"
```

### 現在のリポジトリの状態を確認

どのファイルが編集されているか、どのファイルがステージされているかを確認できる

```bash
# 基本
$ git status
# 圧縮表示（見やすい）
$ git status -s
```

### 過去のコミット履歴を確認

```bash
# 基本
$ git log
# 圧縮表示（たくさん見たい時）
$ git log --oneline
```

### ファイルをステージ

```bash
# 変更されたすべてのファイルをステージ
$ git add -A
# 特定のファイルをステージ
$ git add hoge.txt
# 今いるフォルダ配下の全体をステージ
$ git add .
```

### コミット

コミットされるのはステージされた変更のみ

```bash
$ git commit -m "メッセージ"
```

### プッシュ・プル

push, pull は本当はもっと奥が深い。

```bash
# 手元の修正をリモートリポジトリに反映
$ git push
# リモートリポジトリの修正を手元に反映
$ git pull
```

## リモート（GitHub）操作

~~元々のリポジトリが GitHub なら Web UI 上で fork するのが簡単だが、~~そうでないなら一度ローカル環境を経由する必要がある。

**追記**: 同じリポジトリの fork を複数作れないので、相手が GitHub であってもこの手順を踏んでください。

### 現在接続しているリモートリポジトリを確認する

```bash
$ git remote -v
origin  https://gitlab.com/inkscape/inkscape (fetch)
origin  https://gitlab.com/inkscape/inkscape (push)
```

この例では、origin という名前で fetch(pull), push した場合に `https://gitlab.com/inkscape/inkscape` に接続することを示す。

### clone したリポジトリを GitHub に登録する

git clone したリポジトリの中にいると仮定

```bash
# 元々のリポジトリに対応する名前を origin から upstream に変更
$ git remote rename origin upstream
# origin という名前で GitHub の URL を登録
# リポジトリ名は「年度-班番号2桁-ソフトウェア名」にする（事前に作成しておく）
$ git remote add origin git@github.com:doss-eeic/2022-01-inkscape.git
# 今いるブランチ（master）を origin（GitHub）に master という名前で登録
# -u をつけるとデフォルト設定にできる（その後このブランチからは git push だけで origin/master に push される）
$ git push -u origin master
```

この設定をすると、接続先リポジトリは以下のようになるはず。

```bash
$ git remote -v
upstream  https://gitlab.com/inkscape/inkscape (fetch)
upstream  https://gitlab.com/inkscape/inkscape (push)
origin  git@github.com:doss-eeic/2022-01-inkscape.git (fetch)
origin  git@github.com:doss-eeic/2022-01-inkscape.git (push)
```

## ブランチ操作

### ブランチを新しく作成する

```bash
# 今いるブランチを元に新しいブランチを作成し、そのブランチに移動する
# この段階では、ブランチは作成した本人の手元にしかない
$ git checkout -b <新ブランチ名>
# 新しく作成したブランチを GitHub に登録
$ git push -u origin <新ブランチ名>
```

### 手元のブランチの一覧を見る

```bash
$ git branch
* master
  hoge
```

この例では、手元の環境に master, hoge という２つのブランチがあり、今は master にいることを示している。

### GitHub 上のものも含め全てのブランチを見る

```bash
# 手元の情報を最新に更新
$ git fetch
# 全てを一覧
$ git branch -a
* master
  hoge
  remotes/origin/master
  remotes/origin/hoge
  remotes/origin/fuga
```

この例では、手元の環境に master, hoge、GitHub 上に master, hoge, fuga というブランチがあることを示している。

### 別のブランチに移動する

```bash
$ git checkout <ブランチ名>
```

### GitHub 上にあるブランチを手元に持ってくる

```bash
# 手元の情報を最新に更新
$ git fetch
# origin（GitHub）上のブランチを元に、新たに手元にブランチを作るイメージ
$ git checkout -b <ブランチ名> origin/<ブランチ名>
```

手元のブランチ名と対応する GitHub 上のブランチ名を変えることもできるが、混乱するのでやめたほうが良い。

### ２つのブランチを統合する

ブランチ fix をブランチ master に統合する例。コンフリクトすると厄介なので慎重に。

```bash
# マージ先のブランチに移動
$ git checkout master
# マージ
# これを実行するとエディタ（vim とか）が開き、コミットメッセージを入力して確定
$ git merge fix
```

## 本家に Pull Request を出す

Pull Request を出す前に以下を確認すること。

1. リポジトリ内の CONTRIBUTING.md などを読んだか
ある程度の規模のプロジェクトであれば上記のようなファイルが存在するはず。必ず良く読み、その基準を満たしているかどうかを確認すること。
2. ブランチ 1 本にまとめているか
複数のブランチで作業していたとしても、適宜手元でマージして 1 本の Pull Request 用ブランチを作成すること。
3. コミット内容は適切か
複数の問題に対する変更が混ざったりしていないか。コミットメッセージはわかりやすいものになっているか（ルールが存在する場合もある）。
4. 本家の最新に追従しているか
開発が活発なリポジトリであれば、自分たちが作業している間にも master が伸びているはず。以前の章で示したように本家リポジトリを upstream として残していれば、作業ブランチ上で

    ```bash
    $ git pull --rebase upstream master
    ```

    とすれば本家レポジトリの変更を取り込んだ上でその最新のコミットからブランチをはやしたような形になる。

## 取り消し・やり直し系

### 最新のコミットの状態に戻す

コミットしていない修正を破棄するということ

```bash
# すべてのファイルを戻す
$ git checkout .
# 特定のファイルを戻す
$ git checkout <ファイル名>
```

### git add を取り消す

ステージの取り消しだけで、ファイルの変更は残る

```bash
# 全ての add を取り消す
$ git reset .
# 特定のファイルの add を取り消す
$ git reset <ファイル名>
```

### 直前のコミットメッセージを修正する

まだ push してないとき。

```bash
# エディタが開く
$ git commit --amend
```

既に push してしまったものを書き換えるのは避けたほうが良い（force push が必要になるので）。

### 直前のコミットに別のファイルを含める

まだ push してないとき。

```bash
$ git add <追加したいファイル>
$ git commit --amend
```

既に push してしまったものを書き換えるのは避けたほうが良い。

### 直前のコミットを取り消す

#### まだ push していない場合

**【危険】push していないことを確認すること**。push 後のコミットに対しこれをやってはならない。

```bash
# 直前のコミットを「なかったこと」にする（履歴から消える）
# コミットに含まれていた変更はステージング状態に戻る
$ git reset --soft HEAD^
```

#### push 済みの場合

`git revert` は、指定したコミットと逆の操作をするコミットを作ることでコミットを取り消す。

```bash
# 直前のコミット前の状態にする
$ git revert HEAD
```
