<!--
title:   【図解】pyenv + venv + poetry による開発環境構築 (Ubuntu 21.10)
tags:    Python,pyenv
id:      a5b5328c93bad615c5b2
private: false
-->
pythonの仮想環境で開発を行うために必要なツールのインストール手順の紹介です。


## Python開発ツール概要

1. [pyenv](#pyenv) :: 複数バージョンの実行環境を構築
2. [venv](#venv) :: 仮想環境を構築
3. [poetry](#poetry) :: 仮想環境のパッケージを管理

<details>
<summary>Python仮想開発環境関連図</summary>
- どのコマンドがどの環境で動作しているのか頭に入っていると、余計な混乱しなくてすみます。
- 開発にはpipは使いません
</details>

![Python仮想環境](https://raw.githubusercontent.com/ryokat3/qiita-articles/main/img/python_dev_env.drawio.png)

## pyenv

<details>
<summary>複数バージョンの実行環境を構築</summary>
- 複数バージョンのpythonを切り替えて使えることができる。
- 各ユーザが使用するpythonのバージョンを切り替える。
- バージョンの切替はOSが使用しているpythonのバージョンに影響はない。
- venvを使って、開発環境毎にバージョンを切り替えられる。
</details>

### 1. python ビルドツールインストール

<details>
<summary>Pythonをソースコードからコンパイル</summary>
- pyenvはpythonをソースコードをダウンロードして、コンパイルして、開発者のhomeディレクトリにインストールする。
- そのためビルドツールをあらかじめインストールする必要がある。
</details>

:::note warn
各OSでの必要パッケージは <a href="https://github.com/pyenv/pyenv/wiki#suggested-build-environment">Suggested build environment</a> を参照
:::

```shell:【参考】ubuntu-21.10
sudo apt-get update; sudo apt-get install make build-essential libssl-dev zlib1g-dev \
libbz2-dev libreadline-dev libsqlite3-dev wget curl llvm \
libncursesw5-dev xz-utils tk-dev libxml2-dev libxmlsec1-dev libffi-dev liblzma-dev
```

### 2. pyenv インストール

<details>
<summary>~/.pyenvに実行環境をダウンロード</summary>
- pyenvは[Shim](https://en.wikipedia.org/wiki/Shim_(computing))と呼ばれる方式で複数バージョンのpythonを切り替える。
- shim版python (`~/.pyenv/bin/python`) が本物のpythonの切替を行う方式。
- 複数バージョンのpythonは`~/.pyenv`配下のディレクトリにインストールされる。
- `~/.pyenv`には、python のインタプリタと標準ライブラリのみインストールし、それ以外のライブラリについては、
  後述のvenvによって各開発環境下にインストールする（ようにすべき）。
</details>

```shell
git clone https://github.com/pyenv/pyenv.git ~/.pyenv
```

### 3. pyenv 最適化

<details>
<summary>実行時間の短縮</summary>
- pythonのshim方式によるruntime時の負担を最適化する。
- 下記コマンドは失敗してもpyenvは動作するのでエラーは無視してよい。
</details>

:::note info
下記コマンドはエラーは無視できる
:::

```shell
cd ~/.pyenv && src/configure && make -C src
```

### 4. pyenv ユーザ環境設定

<details>
<summary>ユーザのシェル環境に追加設定</summary>
- pyenv用のPATHや各種環境変数を各ユーザが設定する必要がある。
- bashの場合、スクリプト用の設定と、ログインシェル用の設定を分けている。
- 設定完了後は、logoutとloginで設定変更を反映させる。
</details>

:::note warn
各OSでの設定方法は <a href="https://github.com/pyenv/pyenv#basic-github-checkout">Basic GitHub Checkout</a> を参照
:::

```shell:【参考】環境変数追加設定（.profile、.bash_profileなど）
export PYENV_ROOT="$HOME/.pyenv"
export PATH="$PYENV_ROOT/bin:$PATH"
# shim版python 用 PATH の設定
eval "$(pyenv init --path)"
```

毎回起動されるので設定は最小限がLinuxの流儀

```shell:【参考】実行時環境追加設定（.bashrcなど）
eval "$(${HOME}/.pyenv/bin/pyenv init -)"
```


## venv

<details>
<summary>仮想環境の構築</summary>
- python (3.4以降) 標準の仮想環境。
- 開発環境毎のpythonのruntime環境の切替、追加ライブラリの管理を行う。
- 標準ではあるが、runtime環境ではないのでOSによっては追加でインストールする必要がある。
</details>

```shell:ubuntu-21.10
sudo apt-get update; sudo apt install python3.9-venv
```

## poetry

<details>
<summary>仮想環境のパッケージを管理</summary>
- 選択肢はたくさんあるが、現時点のbest practiseの模様。
- poetryは`~/.local/bin`にインストールされる。
- Ubunts 21.10 ではデフォルトでPATHが通っているのでインストールすれば則実行可。
- pipでインストールすると、実行環境に依存してしてしまう。
- 独自のpython実行環境をもつので、仮想環境に影響を受けない。
</details>

:::note alert
poetry は pip でインストールしない
:::

:::note warn
各OSでのインストール方法は <a href="https://python-poetry.org/docs/master/#installation">Poetry Installation</a>を参照
:::

```shell:ubuntu-21.10
curl -sSL https://install.python-poetry.org | python3 -
```

:::note warn
~/.local/bin がPATHに含まれていない場合に追加
:::

```shell:【参考】環境変数追加設定（.profile、.bash_profileなど）
export PATH="$HOME/.local/bin:$PATH"
```
