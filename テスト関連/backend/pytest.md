# pytest周りの整理
### 【全般】
- プロジェクト内に複数のappが存在する場合のフォルダ構成は、<br>testsフォルダを1つ作り、その中にapp毎のディレクトリを作って格納していく形で良さそう。

- pytest.iniにaddoptsセクションを利用することで、コマンドラインで使用するオプションをデフォルトで設定できる。

- pytestを使う理由
    - assert が楽。
    - fixtureの利用。

### 【実行時】
- テスト関数の実行中に例外が発生した場合はFAILED、fixtureの実行中に発生した場合はERRORになる。
- 例外になることを確認するケースでは以下の形を用いる
    ~~~
    with pytest.raises(Exception名):
        処理名
    ~~~

### 【引数・フィクスチャ】
- フィクスチャを使用するメリット
    - 明示的に呼び出す必要がない。
    - スコープ指定により、特定の範囲で状態を維持できる。
    - テスト後、自動的にクリーンアップされる。

- `@pytest.mark.parametrize`
    - 同じテストを複数のパラメータで簡単に行える。

- `@pytest.fixture`
    - テストの前処理を記述できる。
        - 1つのケースにしか適用されない場合でも前処理であればfixtureに切り出すべき。

    - 複数の引数を渡したい場合は、params=[]に記述する。
        - paramsに複数の値を渡すと、その値毎にテストケースが実行される。
        - fixtureをパラメータ化すると、そのパラメータの数だけテスト関数は実行される。

    - fixtureとテスト関数どちらをパラメータ化するかは以下の通り。
        | 引数同じ | 引数違う | データ同じ | データ違う | 結果 |
        |:-------:|:--------:|:----------:|:---------:|:----:|
        |    ✓    |         |    ✓     |            | パラメータ無し |
        |    ✓    |         |    　     |     ✓     | fixtureをパラメータ化 |
        |    　    |    ✓   |    ✓     |            | 不可能なパターン|
        |    　    |    ✓   |    　     |     ✓     | 関数をパラメータ化|

   - fixtureにはスコープが存在する。
        | スコープ名 | 粒度 |
        | ---- | ---- |
        | function | テストケース |
        | class  | テストクラス |
        | module  | テストファイル |
        | class  | テスト全体 |

    - スコープ内の全てのケースで利用したい場合は、autouse=Trueを追記する。<br>それ以外の場合は、ケースの引数にfixtureのメソッド名を渡す必要がある。

    - 1つのケースで複数のfixtureを呼ぶ際、デフォルトでは呼び出し順序は保証されないが、後から呼ぶfixtureの引数に先に呼ぶfixtureを入れることで順番を制御できる。

    - yieldについて
        - `yield 変数` の記述でケース側で変数を受け取れる
        - データを削除するにはyieldの後にdelete処理を加える。

- `monkeypatch.setenv(env_name, "")`
    - 上記にて、envデータの書き換えができる

- `caplog` : ログ出力のテストができる。
    - `caplog.record_tuples`を使用すると(logger_name, log_level, message)のリストが返される。
    - `caplog.message`ではメッセージのみがリストで返ってくるため、出力内容のテストに使用できる。
    - の使用でloggingに出されている値のテストができるかも。

- ユーザデータの作成など、複数のテストから呼び出す前処理は「conftest.py」に記述をする。
    <br>※「conftest.py」は同階層と親階層を利用できる。

- pytest-djangoでセットアップより前に何かしたい場合は、以下の設定を加える。
    ~~~
    @pytest.hookimpl(tryfirst=True)
        def pytest_load_initial_conftests(early_config, parser, args):
    ~~~

### 【DB】
- loaddataで入れているマスタデータはcall_command()を利用することで、テストDBに入れることも可能。<br>但し、フォルダ配下のファイルを*で指定はできずファイル名を指定する必要あり。


- pytest-djangoのDBアクセスについて
    - `@pytest.mark.django_db`
        - pytest-djangoではデフォルトではDBアクセスができず、このmarkを使用することでアクセス可能となる。
        - テストケースごとのDB操作は互いに独立し、影響を与えない。
            - デフォルト：テスト開始時にトランザクションを開始し、終了時にロールバックをする
            - transaction=True : テスト実行後DBフラッシュし、コミットする（より高速）
            - reset_sequences=True : オートインクリメントの値をリセットする。transaction=Trueと一緒に使う。
    - `--resource-db`
        - DBの再利用を行うことができて初期セットアップ時の時間短縮となる。
        - `if not request.config.getoption("--reuse-db"):` を使うと--resource-dbを設定した時にその処理を行わない。
    - `--create-db`
        - 強制的にDBを新規作成し、テスト実行できる。

        ※デフォルトでは--resource_dbを設定しておき、DBスキーマを変えたい時に--create-dbを使用するのが良さそう。

    - テストケースに応じてfixtureに渡す値を変更する方法
        - ケース側：@pytest.mark.parametrizeの引数にfixture名と値、indirect=[fixture名]を与える。
            ~~~
            @pytest.mark.parametrize(
                "create_base_data",
                [
                    {"key": "key1", "value": "value1"},
                    {"key": "key2", "value": "value2"}
                ],
                indirect=["create_base_data"],
            )
            ~~~
        - fixture側：引数にrequestを設定し、paramを受け取って使用する。
            ~~~
            @pytest.fixture
            def create_base_data(request):
                key = request.param["key"]
                value = request.param["value"]
            ~~~

### 【API】
- rest_frameworkのAPIClientを利用すると簡単にAPI接続をテストできる。
    - urlを指定する場合にreverseでnameから逆引きができるが、以下の点に注意。
        1. 同プロジェクトにアプリケーションが複数ある場合は、reverse('アプリケーション名:name名')で指定できる。
        2. def createに繋ぐには、name名をname-listとしてPOST通信することで接続可能。
        3. 一方、リソースの詳細情報の取得、更新、削除にはname-detailを使用する。

- mocker.patchを使用することで、メソッドや外部API接続をmock化できる。

### 【マーカー】
- `@pytest.mark.skip(reason="")`
    - スキップしたい場合に使用。reasonにスキップ理由を入れられる。
- `@pytest.mark.skipif()`
    - 条件付きでスキップしたい場合に使用。
- `@pytest.mark.xfail()`
    - 一時的に失敗を許可する場合に使用。
        - テスト駆動開発などで、先にテストを作る場合
        -   バグが起こっているが、すぐに対応できない場合(忘れないようにstrict=Trueを付けておく。)<br> など
- `@pytest.mark.usefixtures(fixture名)`
    - 使用することで、値を返さないfixtureを引数に渡す必要がなくなる。
    - ケースにのみ使用できるため、fixtureからfixtureを呼び出す際は引数に設定する必要がある。


## 疑問
- mock, patch周りの違いは？
- 実装した関数だけでなく、datetime.now()などの標準モジュール等の置き換えは可能？
- APIClient, CoreAPIClient, RequestsClient辺りの違いは？
- conftest.pyに記述しているscope="session"のfixtureの引数からdjango_db_setupを削除すると挙動がおかしくなる。
- scopeがclass以上のfixtureでDBアクセスしようとした際、with django_db_blocker.unblock():がないとエラーになる。