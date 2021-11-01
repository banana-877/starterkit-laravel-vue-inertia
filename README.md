# 開発環境構築手順

## docker install

事前に<a href="https://docs.docker.com/docker-for-mac/install/">docker</a>をインストールする

※Windowsの場合も同様にDockerとDockerComposeのインストールを行う。

## Composer依存関係インストール

```bin/sh
$ docker run --rm \
    -u "$(id -u):$(id -g)" \
    -v $(pwd):/opt \
    -w /opt \
    laravelsail/php80-composer:latest \
    composer install --ignore-platform-reqs
```

## sailコマンドalias設定

ローカルのシェルの種類によって、変更する。

- bashの場合
```bash
$ echo "alias sail='[ -f sail ] && bash sail || bash vendor/bin/sail'" >> ~/.bashrc
$ source ~/.bashrc
```

- zshの場合
```zsh
$ echo "alias sail='[ -f sail ] && zsh sail || zsh vendor/bin/sail'" >> ~/.zshrc
$ source ~/.zshrc
```

## envファイル設定

- envコピー
```bin/sh
$ \cp -rf .env.example .env 
```

- APP_KEY生成
```bin/sh
$ sail artisan key:generate
```

- 生成された文字列を.envのAPP_KEYへ設定 

```.env
APP_KEY=base64:XXXXXXXXXXXXXXXXXXX
```

## コンテナ起動
Laravelのルートディレクトリで以下コマンドを実行してコンテナ起動

（初回は時間がかかる）

```bin/sh
$ sail up -d
```

## NPM更新

```bin/sh
$ sail npm install
$ sail npm dev
```
* 実装中は``` $sail npm dev ```を``` $sail npm watch ```にすれば変更が随時反映される。

## DB更新

```bin/sh
$ sail artisan migrate:refresh --seed
```

## アクセス確認
以下のページへアクセスして問題なければ構築成功

- laravel_app: http://localhost

# PHPUnitテスト環境用DB作成

- mysqlへログイン

```
$ docker exec -it {starterkit-laravel-vue-inertia}_mysql_1 /bin/bash -c 'mysql -h 127.0.0.1 -u root -p'
```
パスワードを求められるので、```password```と入力する

- create databaseのSQLでテスト用DB作成

```mysql
mysql $ CREATE DATABASE {starterkit-laravel-vue-inertia}_test DEFAULT CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;
mysql $ grant ALL PRIVILEGES on {starterkit-laravel-vue-inertia}_test.* to 'sail'@'%';
```

- test用Key作成

```
$ sail artisan key:generate --env=testing
```

# Sailコマンド
マイグレーションやサーバの設定などdocker内で行う作業を効率化するコマンド

詳細は<a> https://readouble.com/laravel/8.x/ja/sail.html </a>を参照

- コンテナへログイン
```bin/sh
$ sail shell
```

- コンテナ起動
```bin/sh
$ sail up
```

- コンテナ起動（バックグラウンド）
```bin/sh
$ sail up -d
```

- コンテナ停止
```bin/sh
$ sail down
```

- phpコマンド実行
```bin/sh
$ sail php --version
$ sail php script.php
```

- Composer
```bin/sh
$ sail composer require laravel/sanctum
```

Artisan
```bin/sh
$ sail artisan queue:work
```

Npm
```bin/sh
$ sail npm run prod
```

テスト
```bin/sh
$ sail test
$ sail test --group orders
$ sail dusk
```

ページ共有公開

```bin/sh
sail share
```

