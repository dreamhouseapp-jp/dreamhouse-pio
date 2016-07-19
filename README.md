DreamHouse PredictionIO レコメンデーションエンジン
---------------------------------------------

このアプリでは、PredictionIO を使用して、ユーザーのお気に入り情報をもとに物件をおすすめします。

デモをご覧ください。

[![Demo](http://img.youtube.com/vi/w4060vJhxig/0.jpg)](http://www.youtube.com/watch?v=w4060vJhxig)

Heroku で実行：

1. [無料の Heroku アカウントの登録](https://heroku.com/signup)を行います。
1. [Heroku Toolbelt](https://toolbelt.heroku.com)をインストールします。
1. Heroku で PredictionIO イベントサーバーをデプロイします。[![Deploy on Heroku](https://www.herokucdn.com/deploy/button.png)](https://heroku.com/deploy?template=https://github.com/jamesward/pio-eventserver-heroku)
1. PredictionIO イベントサーバーで新しいアプリを作成します。

        heroku run console app new dreamhouse -a <イベントサーバーのアプリ名>

1. DreamHouse Web アプリ（pio ブランチ）をデプロイします。[![Deploy on Heroku](https://www.herokucdn.com/deploy/button.png)](https://heroku.com/deploy?template=https://github.com/dreamhouseapp/dreamhouse-web-app/tree/pio)
1. 推奨エンジンをデプロイします。[![Deploy on Heroku](https://www.herokucdn.com/deploy/button.svg)](https://heroku.com/deploy?template=https://github.com/dreamhouseapp/dreamhouse-pio)
1. PredictionIO イベントサーバーの Postgres を推奨エンジンアプリにアタッチします。

    自動付加される Heroku Postgres アドオンを削除します。

        heroku addons:destroy heroku-postgresql -a <エンジンアプリ名>

    イベントサーバーの Postgres の Heroku Postgres アドオン ID を確認します。

        heroku addons -a <イベントサーバーの Heroku アプリ名>

    Postgres アドオンをエンジンにアタッチします。

        heroku addons:attach <アドオン ID> -a <エンジンのアプリ名>

1. DreamHouse Web アプリを構成して、おすすめ情報をどこから引き出すかを指定します。

        heroku config:set PIO_ENGINE_URL=<推奨サーバーの URL> -a <DreamHouse Web アプリの名前>

1. DreamHouse Web アプリでおすすめをチェックします。


ローカルで実行：

1. ローカルの PredictionIO イベントサーバーをセットアップします。
https://github.com/jamesward/pio-eventserver-heroku
1. `pio` ブランチを使用してローカルの DreamHouse Web アプリをセットアップします。
https://github.com/dreamhouseapp-jp/dreamhousejp-web-app/tree/pio
1. ローカルの PredictionIO 推奨エンジンをセットアップします。
https://github.com/dreamhouseapp-jp/dreamhousejp-pio
1. アプリのトレーニングを行い、推奨エンジンを実行します。

        cd dreamhouse-pio
        source bin/env.sh && DREAMHOUSE_WEB_APP_URL=http://localhost:8200 ACCESS_KEY=<アクセスキー> ./sbt "runMain ServerApp"

1. エンジンのステータスを確認します。

    http://localhost:8000

1. アイテムのおすすめをチェックします。

        curl -H "Content-Type: application/json" -d '{"userId": "c1", "numResults": 3 }' -k http://localhost:8000/queries.json
