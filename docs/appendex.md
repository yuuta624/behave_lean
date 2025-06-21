## Behave チートシート

このチートシートは、BDDツール「Behave」を使ってテストを記述する際の、基本的な要素とよく使う機能をまとめたものです。

### 基礎

* **Featureファイル**： テストしたい機能を記述するファイル。Gherkin記法で記述する。
    * ```gherkin
      Feature: 機能名
        Scenario: シナリオ名
          Given 前提条件
          When 実行する操作
          Then 期待される結果
      ```
* **ステップ定義ファイル**: Featureファイルに記述したステップに対応するPythonコードを記述するファイル。
    * ```python
      from behave import *

      @given('前提条件')
      def step_impl(context):
          # 前提条件を設定する処理

      @when('実行する操作')
      def step_impl(context):
          # 操作を実行する処理

      @then('期待される結果')
      def step_impl(context):
          # 結果を検証する処理
      ```

### よく使う機能

* **ステップパラメーター**: ステップに値を渡す。
    * ```gherkin
      Given ある値が "10" である
      ```
    * ```python
      @given('ある値が "{value}" である')
      def step_impl(context, value):
          context.value = int(value)
      ```
* **シナリオアウトライン**: 同じシナリオを異なるデータセットで実行する。
    * ```gherkin
      Scenario Outline: シナリオアウトライン名
        Given ある値が "<value>" である
        Then 結果は "<result>" になる

        Examples:
          | value | result |
          | 10    | 20    |
          | 20    | 40    |
      ```
* **タグ**: シナリオやフィーチャーにタグを付けて、実行するシナリオを制御する。
    * ```gherkin
      @tag1
      Scenario: タグ付きシナリオ
        # ...
      ```
    * 実行例: `behave --tags=tag1`
* **背景**: 複数のシナリオで共通の前提条件を設定する。
    * ```gherkin
      Background:
        Given 共通の前提条件

      Scenario: シナリオ1
        # ...

      Scenario: シナリオ2
        # ...
      ```
* **フック**: 特定のタイミングで処理を実行する。
    * ```python
      from behave import *

      @before_all(context):
        # 全てのテスト実行前に一度だけ実行される処理

      @after_all(context):
        # 全てのテスト実行後に一度だけ実行される処理
      ```
* **レポート**: テスト結果をさまざまな形式で出力する。
    * JUnit形式: `behave --junit`
    * JSON形式: `behave -f json`
    * Allureレポート: `behave -f allure_behave.formatter:AllureFormatter -o allure-results`

**注意**: 上記はBehaveのごく一部の機能です。詳細については、Behaveの公式ドキュメントを参照してください。

 [Behave](https://behave.readthedocs.io/en/latest)
