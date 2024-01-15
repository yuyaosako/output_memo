# pytest周りの整理
## 【全般】
- プロジェクト内に複数のappが存在する場合のフォルダ構成は、<br>testsフォルダを1つ作り、その中にapp毎のディレクトリを作って格納していく形で良さそう。

- pytest.iniにaddoptsセクションを利用することで、コマンドラインで使用するオプションをデフォルトで設定できる。

- pytestを使う理由
    - assert が楽。
    - fixtureの利用。

## 【実行時】
- テスト関数の実行中に例外が発生した場合はFAILED、fixtureの実行中に発生した場合はERRORになる。

- 1ケースだけ実行したい場合は以下の記述をする。
    - クラスベースの場合：`pytest (path)::(TestClass)::(test_method)`
    - 関数ベースの場合　：`pytest (path)::(test_method)`

- 例外になることを確認するケースでは以下の形を用いる
    ~~~python
    with pytest.raises(Exception名):
        処理名
    ~~~
- `@pytest.mark.skip(reason="")`
    - スキップしたい場合に使用。reasonにスキップ理由を入れられる。
- `@pytest.mark.skipif()`
    - 条件付きでスキップしたい場合に使用。
- `@pytest.mark.xfail()`
    - 一時的に失敗を許可する場合に使用。
        - テスト駆動開発などで、先にテストを作る場合
        -   バグが起こっているが、すぐに対応できない場合(忘れないようにstrict=Trueを付けておく。)<br> など

## 【引数・フィクスチャ】
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

- `@pytest.mark.usefixtures(fixture名)`
    - 使用することで、値を返さないfixtureを引数に渡す必要がなくなる。
    - ケースにのみ使用できるため、fixtureからfixtureを呼び出す際は引数に設定する必要がある。

- `caplog` : ログ出力のテストができる。
    - `caplog.record_tuples`を使用すると(logger_name, log_level, message)のリストが返される。
    - `caplog.message`ではメッセージのみがリストで返ってくるため、出力内容のテストに使用できる。
    - の使用でloggingに出されている値のテストができるかも。

- ユーザデータの作成など、複数のテストから呼び出す前処理は「conftest.py」に記述をする。
    <br>※「conftest.py」は同階層と親階層を利用できる。

- pytest-djangoでセットアップより前に何かしたい場合は、以下の設定を加える。
    ~~~python
    @pytest.hookimpl(tryfirst=True)
        def pytest_load_initial_conftests(early_config, parser, args):
    ~~~

## 【DB】
- loaddataで入れているマスタデータはcall_command()を利用することで、テストDBに入れることも可能。<br>但し、フォルダ配下のファイルを*で指定はできずファイル名を指定する必要あり。

- pytest-djangoのDBアクセスについて
    - `@pytest.mark.django_db`
        - pytest-djangoではデフォルトではDBアクセスができず、このmarkを使用することでアクセス可能となる。
        - @pytest.mark.django_dbを使用しないと、各ケースでのDB操作が影響を及ぼし合う。
        - テストケースごとのDB操作は互いに独立し、影響を与えない。
            - デフォルト：テスト開始時にトランザクションを開始し、終了時にロールバックをする
            - transaction=True : テスト実行後DBフラッシュし、コミットする（より高速）
            - reset_sequences=True : オートインクリメントの値をリセットする。transaction=Trueと一緒に使う。
        - 基本的にテスト関数やメソッドに対して使用される。
            - 故に、fixtureに対してもscopeがfunctionであれば使用できるが、それより広いscopeの場合にはセットアップのタイミングが上手くいかなくなる。
    - `--resource-db`
        - DBの再利用を行うことができて初期セットアップ時の時間短縮となる。
        - `if not request.config.getoption("--reuse-db"):` を使うと--resource-dbを設定した時にその処理を行わない。
    - `--create-db`
        - 強制的にDBを新規作成し、テスト実行できる。

        ※デフォルトでは--resource_dbを設定しておき、DBスキーマを変えたい時に--create-dbを使用するのが良さそう。

    - テストケースに応じてfixtureに渡す値を変更する方法
        - ケース側：@pytest.mark.parametrizeの引数にfixture名と値、indirect=[fixture名]を与える。
            ~~~python
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
            ~~~python
            @pytest.fixture
            def create_base_data(request):
                key = request.param["key"]
                value = request.param["value"]
            ~~~
    - `django_db_blocker `
        - 使用することで、テストセッション全体にわたるデータベースアクセスをブロックする。
        - `django_db_blocker.unblock()`を呼ぶことで、一時的にデータベースアクセスを許可する。
        - scopeがclass以上のfixture内でDBアクセスをする際は`with django_db_blocker.unblock():`を利用する。
    - `django_db_setup`
        - Djangoのデフォルトのテストランナーが提供するfixtureであり、テストセッション全体で1回だけデータベースを設定する。
        - デフォルトの実装ではmigrationを実行してDBを作成し、テスト実行後にDBを削除する挙動を取る。
        - 複数のfixtureにdjango_db_setupを引数で与えるのは一般的でない。
        - つまり、scopeがsessionでデータの作成を行うfixtureを実装する際はdjango_db_setupをオーバーライドすると良い。


## 【モック】
- `pytest-mock`
    - unittest.mockをベースに作られているため、ほとんどの機能が同一。
    - pytestの拡張として提供されているため、pytestのテストケース内で簡単にmockerオブジェクトを使用できる。
    - fixtureでmockが使用できるようになる。
    - 毎回固定値を使う場合は`return_value`を、渡される引数に応じて値を変える場合は`side_effect`を使用する。
- envデータの書き換えは以下の2パターンがあるが、monkeypatchを利用する方が一般的。
    - monkeypatchを利用：`monkeypatch.setenv(env_name, value)`
    - pytest-mockを利用：`mocker.patch.dict(os.environ, {env_name: value})`
- pytest-mockは、関数、メソッドの挙動を制御したり、呼び出しの検証等に使用する。また、外部API接続もpytest-mockを使うと良い。
- monkeypatchは、環境変数、グローバル変数、ファイルシステムなど外部の状態を制御するのに利用する。
- datetime.datetimeなどのイミュータブル(変更不可能)なオブジェクトでは、now()などのメソッドをmock化できない。<br>対応方法としては以下のパターンがある。
    1. pytest-freezegunの使用
        - `@pytest.mark.freeze_time(datetime.datetime(2000, 1, 2, 3, 4))` などのマーカー記述で置き換えが可能。
    2. datetime.datetimeをmock化した後、モックのnowメソッドを設定
        ~~~python
        mocker.patch("パス名.datetime", **{
            "now.return_value": datetime(2023, 1, 9, 20, 0, 0)
        })
        ~~~
    3. datetime.datetime.nowを返すメソッドを作っておき、そのメソッドをmock化する。
        ~~~python
        def get_now():
            return datetime.datetime.now()

        def test_func_main(mocker):
            mocker.patch("get_now", return_value=datetime(2023, 1, 9, 20, 0, 0))
        ~~~

## 【API】
- rest_frameworkのAPIClientを利用すると簡単にAPI接続をテストできる。
    - urlを指定する場合にreverseでnameから逆引きができるが、以下の点に注意。
        1. 同プロジェクトにアプリケーションが複数ある場合は、reverse('アプリケーション名:name名')で指定できる。
        2. def createに繋ぐには、name名をname-listとしてPOST通信することで接続可能。
        3. 一方、リソースの詳細情報の取得、更新、削除にはname-detailを使用する。

## 疑問
- APIClient, CoreAPIClient, RequestsClient辺りの違いは？