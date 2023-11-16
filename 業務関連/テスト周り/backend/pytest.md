# pystet周りの整理

## 理解
- @pytest.fixtureを使用することで、テストの前処理を記述できる。
    - fixtureにはスコープが存在する。

        | スコープ名 | 粒度 |
        | ---- | ---- |
        | function | テストケース |
        | class  | テストクラス |
        | module  | テストファイル |
        | class  | テスト全体 |

- ユーザなど複数のテストから呼び出す処理は「conftest.py」に記述をする。
    <br>※「conftest.py」は同階層と親階層を利用できる。

- @pytest.mark.parametrizeを使用すると、同じテストを複数のパラメータで簡単に行える。

- mocker.patchを使用することで、メソッドや外部API接続をmock化できる。




## 疑問
- プロジェクト内に複数のappが存在する場合、testsフォルダはどう作るべきか？
- テストDBを作成する際、マスタデータの入れ方は？