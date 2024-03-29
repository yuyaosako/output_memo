# Devcontainer
## 概要
- Vscodeの開発環境構築支援ツール
- コンテナをフル機能の開発環境として使用できる。

## 前提
- Dockerやkubernetesなどのコンテナ環境を利用していること。

## メリット
- ソースコード・各種ライブラリ・vscodeの拡張機能を1まとめにでき、各人で揃える必要がない。
- vscode上でのデバッグが容易に行える。
- Dockerの立ち上げコマンドが不要。
- 新規開発者のオンボーディングが容易
- ホスト環境とは隔離された開発環境を用意できるため、プロジェクト間での依存関係が生じない。
- リモート開発にも対応しており、リモートサーバー上でも開発可能。
- Docker Composeで管理されるマルチコンテナ構成をサポートしているため、複数のコンテナ管理も可能。(docker-compose.ymlを使用する)

## 追加知識
- devcontainerの中でのGitの利用


- 環境変数の利用
    - 個別に変数を記述
        - containerEnv
        - remoteEnv
            - Docker Composeはこちらのみ利用可能。
    - envファイルを利用
        - `"runArgs": ["--env-file","envファイルのパス"]`
- コンテナ起動時のコマンド実行
    - `"postStartCommand": "実行コマンド"`

- ローカルフォルダにバインドされたマウントを追加する
    - `"mounts": ["source=,"target=,type=,consistency="]`
    - `source="パス"`
        - マウント元のパスを指定する。
        - `${localEnv:HOME}${localEnv:USERPROFILE}`でホストマシンのユーザーホームディレクトリを示す。
    - `target="パス"`
        - マウント先のディレクトリ
    - `type="文字列"`
        - マウントの種類を指定する。
        - bind を指定することで、ボリュームではなくホストマシン上の実際のディレクトリをマウントする。
    - `consistency="文字列"`
        - パラメータはマウントの一貫性を指定する。
        - cached を指定すると、ファイルの変更が最初にアクセスされたときにのみ同期する。

- デフォルトのソースコードマウントを変更する
    - `"workspaceMount": "source=,"target=,type="`
    - `"workspaceFolder": "/workspace"`

- 