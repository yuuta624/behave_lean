# Tutorial

まず、behaveをインストールします。

次に、「features」というディレクトリを作成します。そのディレクトリに、次の内容を含む「tutorial.feature」というファイルを作成します:

```python
Feature: showing off behave

  Scenario: run a simple test
     Given we have behave installed
      When we implement a test
      Then behave will test it for us!
```
「features/steps」という新しいディレクトリを作成します。そのディレクトリに、次の内容を含む「tutorial.py」というファイルを作成します。

```python
from behave import *

@given('we have behave installed')
def step_impl(context):
    pass

@when('we implement a test')
def step_impl(context):
    assert True is not False

@then('behave will test it for us!')
def step_impl(context):
    assert context.failed is False
```

behaveを実行:

```
% behave
Feature: showing off behave # features/tutorial.feature:1

  Scenario: run a simple test        # features/tutorial.feature:3
    Given we have behave installed   # features/steps/tutorial.py:3
    When we implement a test         # features/steps/tutorial.py:7
    Then behave will test it for us! # features/steps/tutorial.py:11

1 feature passed, 0 failed, 0 skipped
1 scenario passed, 0 failed, 0 skipped
3 steps passed, 0 failed, 0 skipped, 0 undefined
```
Now, continue reading to learn how to make the most of behave.

Features（機能）
behave は、次のディレクトリで動作します:

ビジネス アナリスト / スポンサー / 誰かが作成した、動作シナリオを含む機能ファイル、および
シナリオの Python ステップ実装を含む「steps」ディレクトリ。
オプションで、環境コントロール (ステップ、シナリオ、機能、または射撃マッチ全体の前後に実行するコード) を含めることができます。

機能ディレクトリの最小要件は次のとおりです:
```
features/
features/everything.feature
features/steps/
features/steps/steps.py
```

より複雑なディレクトリは次のようになります：
```
features/
features/signup.feature
features/login.feature
features/account_details.feature
features/environment.py
features/steps/
features/steps/website.py
features/steps/utils.py

```
設定に問題があり、behave が機能を見つけるために何をしているのかを確認したい場合は、「-v」（詳細）コマンドラインスイッチを使用します。

## Feature Files
機能ファイル
機能ファイルは、予想される結果の代表的な例とともに機能または機能の一部を記述する自然言語形式です。プレーンテキスト (UTF-8 でエンコード) で、次のようになります:

```python
Feature: Fight or flight
  In order to increase the ninja survival rate,
  As a ninja commander
  I want my ninjas to decide whether to take on an
  opponent based on their skill levels

  Scenario: Weaker opponent
    Given the ninja has a third level black-belt
     When attacked by a samurai
     Then the ninja should engage the opponent

  Scenario: Stronger opponent
    Given the ninja has a third level black-belt
     When attacked by Chuck Norris
     Then the ninja should run for his life
```

この文章の「Given」、「When」、「Then」の部分は、システムをテストする際に behave が実行する実際の手順です。これらは Python のステップ実装にマップされます。一般的なガイドとして:

**「Given」** では、ユーザー (または外部システム) がシステムと対話を開始する前に (When ステップで) システムを既知の状態にします。ユーザーとの対話について、与えられた条件で話すことは避けてください。

**「When」** では、ユーザー (または外部システム) が実行する主要なアクションを実行します。これは、何らかの状態の変化を引き起こす (または引き起こさない) システムとの対話です。

**「Then」** では、結果を観察します。

ステップとして「And」または「But」を含めることもできます。これらは、behave によって名前が変更され、前のステップの名前が取得されます。つまり、次のようになります。

```python
Scenario: Stronger opponent
  Given the ninja has a third level black-belt
   When attacked by Chuck Norris
   Then the ninja should run for his life
    And fall off a cliff
```


この場合、behaveはステップ定義を探します。

`"Then fall off a cliff".`

## Scenario Outlines

場合によっては、一連の既知の状態、実行するアクション、および予想される結果を示す多数の変数を使用してシナリオを実行する必要がありますが、その場合はすべて同じ基本アクションを使用します。これを実現するには、シナリオ アウトラインを使用できます。

```python
Scenario Outline: Blenders
   Given I put <thing> in a blender,
    when I switch the blender on
    then it should transform into <other thing>

 Examples: Amphibians
   | thing         | other thing |
   | Red Tree Frog | mush        |

 Examples: Consumer Electronics
   | thing         | other thing |
   | iPhone        | toxic waste |
   | Galaxy Nexus  | toxic waste |
```

behave は、サンプル データ テーブルに表示される各 (見出し以外の) 行に対してシナリオを 1 回実行します。

## Step Data
データ テーブルをステップに関連付けると便利な場合があります。

ステップに続く """ 行で囲まれたテキスト ブロックは、ステップに関連付けられます。例:
```python
Scenario: some scenario
  Given a sample text loaded into the frobulator
     """
     Lorem ipsum dolor sit amet, consectetur adipisicing elit, sed do
     eiusmod tempor incididunt ut labore et dolore magna aliqua.
     """
 When we activate the frobulator
 Then we will find it similar to English
```

テキストは、各ステップ関数に渡される  変数の「.text」属性として、Python ステップ コードで使用できます。

ステップの後にインデントして入力するだけで、データ テーブルをステップに関連付けることもできます。これは、必要な特定のデータをモデルに読み込む場合に便利です。

```python
Scenario: some scenario
  Given a set of specific users
     | name      | department  |
     | Barry     | Beer Cans   |
     | Pudey     | Silly Walks |
     | Two-Lumps | Silly Walks |

 When we count the number of people in each department
 Then we will find two people in "Silly Walks"
  But we will find one person in "Beer Cans"
```

テーブルは、各ステップ関数に渡されるコンテキスト変数の「.table」属性として、Python ステップ コードで使用できます。上記の例のテーブルには、次のようにアクセスできます。

```python
@given('a set of specific users')
def step_impl(context):
    for row in context.table:
        model.add_user(name=row['name'], department=row['department'])
```
テーブル データにアクセスする方法はさまざまです。詳細については、Table API ドキュメントを参照してください。

## Python ステップの実装

シナリオで使用されるステップは、「steps」ディレクトリの Python ファイルで実装されています。python `*.py` ファイル拡張子を使用する限り、好きなように呼び出すことができます。behave にどのステップを使用するか指示する必要はありません。behave はすべてを使用します。

behave の Python 側の詳細については、[API ドキュメント](https://behave.readthedocs.io/en/stable/api.html)を参照してください。

ステップは、機能ファイルの述語に一致するデコレータを使用して識別されます。**given** 、**when** 、**then** 、**step** です (必要に応じて、タイトル ケースのバリアントも使用できます)。デコレータは、属するシナリオ ステップで使用されるフレーズの残りの部分を含む文字列を受け入れます。

Given a Scenario:

シナリオが与えられた場合:
```python
Scenario: Search for an account
   Given I search for a valid account
    Then I will see the account details
```

ここでの 2 つのステップを実装するステップ コードは次のようになります (Selenium WebDriver とその他のヘルパーを使用)。

```python
@given('I search for a valid account')
def step_impl(context):
    context.browser.get('http://localhost:8000/index')
    form = get_element(context.browser, tag='form')
    get_element(form, name="msisdn").send_keys('61415551234')
    form.submit()

@then('I will see the account details')
def step_impl(context):
    elements = find_elements(context.browser, id='no-account')
    eq_(elements, [], 'account not found')
    h = get_element(context.browser, id='account-head')
    ok_(h.text.startswith("Account 61415551234"),
        'Heading %r has wrong text' % h.text)
```

 `step `デコレータは、ステップを「given」、「when」、または「then」のいずれかのステップ タイプに一致させます。「and」および「but」ステップ タイプは、先行するステップのキーワードを取得するために内部的に名前が変更されます (したがって、「given」に続く「and」は内部的に「given」になり、指定されたデコレータ ステップを使用します)。

注

ステップ関数名には一意のシンボル名は必要ありません。これは、テキスト マッチングによってステップ関数が匿名関数として呼び出される前にステップ レジストリから選択されるためです。したがって、behave がテスト実行で不足しているステップ実装を出力する場合、デフォルトですべての関数に「step_impl」が使用されます。

ステップ実装で別のステップを呼び出したい場合は、コンテキスト メソッド execute_steps() で実行できます。

この関数を使用すると、たとえば次のことが可能になります。

```python
@when('I do the same thing as before')
def step_impl(context):
    context.execute_steps('''
        when I press the big red button
         and I duck
    ''')
```

これにより、「以前と同じ操作を実行したとき」のステップでは、他の 2 つのステップもシナリオ ファイルに記述されているかのように実行されます。

## Step パラメータ

機能ステップに、ごくわずかなバリエーションしかない非常に一般的なフレーズが含まれている場合があります。例:

```python
Scenario: look up a book
  Given I search for a valid book
   Then the result page will include "success"

Scenario: look up an invalid book
  Given I search for a invalid book
   Then the result page will include "failure"
```

これらの Then 句の両方を処理する単一の Python ステップを定義できます (`context.response` にテキストを配置する Given ステップを使用)。
```python
@then('the result page will include "{text}"')
def step_impl(context, text):
    if text not in context.response:
        fail('%r not in %r' % (text, context.response))
```
behave には、デフォルトでいくつかのパーサーが用意されています:

**parse (デフォルト、[parse](https://pypi.python.org/pypi/parse) に基づく)**

ステップ パラメータの正規表現を `{param:Type}` のような読みやすい構文に置き換えるシンプルなパーサーを提供します。この構文は、Python 組み込みの `string.format()` 関数にヒントを得ています。ステップ パラメータは、ステップ定義で parse の名前付きフィールド構文を使用する必要があります。名前付きフィールドは抽出され、オプションで型変換されてから、ステップ関数の引数として使用されます。

型コンバーターを使用して型変換をサポートします `(register_type()` を参照)。

**cfparse (拡張: parse、必須: parse_type)**

「カーディナリティ フィールド」(CF) をサポートする拡張パーサーを提供します。カーディナリティ = 1 の型コンバーターが提供されている限り、関連するカーディナリティの不足している型コンバーターを自動的に作成します。次のような解析式をサポートします:

`{values:Type+}` (cardinality=1..N, many)

`{values:Type*}` (cardinality=0..N, many0)

`{value:Type?}` (cardinality=0..1, optional).

型変換をサポートします (上記と同じ)。

**re**

これは完全な正規表現を使用して句のテキストを解析します。テキストから取得され、`step()` 関数に渡される変数を定義するには、名前付きグループ「(?P<name>…)」を使用する必要があります。

**型変換はサポートされていません。** ステップ関数の作成者は、ステップ関数内で型変換を実装できます (実装)。

使用するパーサーを指定するには、使用するマッチャーの名前を指定して `use_step_matcher()` を呼び出します。特定のステップ関数に合わせてマッチャーを変更できます。ステップ関数宣言の前の `use_step_matcher` への最後の呼び出しが、使用されるものになります。

注意

関数 `step_matcher()` は廃止される予定です。代わりに `use_step_matcher()` を使用してください。

**Context**

渡される`context`変数に気付いたでしょう。これは、あなたと behave が共有する情報を保存できる賢い場所です。これは 3 つのレベルで実行され、behave によって自動的に管理されます。

behave が新しい機能またはシナリオを開始すると、コンテキストに新しいレイヤーが追加され、そのアクティビティの期間中、新しいアクティビティ レベルで新しい値を追加したり、以前に定義された値を上書きしたりできるようになります。これらはスコープと考えることができます。

環境制御ファイルで値を定義できます。この値は機能レベルで設定され、その後一部のシナリオで上書きされる場合があります。シナリオ レベルで行われた変更は、機能レベルで設定された値に永続的に影響しません。

また、ステップ間で値を共有するために使用することもできます。たとえば、定義する一部のステップでは、次のようになります。

```python
@given('I request a new widget for an account via SOAP')
def step_impl(context):
    client = Client("http://127.0.0.1:8000/soap/")
    context.response = client.Allocate(customer_first='Firstname',
        customer_last='Lastname', colour='red')

@then('I should receive an OK SOAP response')
def step_impl(context):
    eq_(context.response['ok'], 1)
```
behave 自体によってコンテキストに追加される値もいくつかあります:

**table**

ステップに関連付けられたテーブル データを保持します。

**text**

ステップに関連付けられた複数行のテキストを保持します。

**failed**

これは、いずれかのステップが失敗したときにコンテキストのルートに設定されます。これを `--stop` コマンドライン オプションと組み合わせて使用​​すると、動作が不適切なリソースが `after_feature()` などでクリーンアップされるのを防ぐのに役立つ場合があります (たとえば、Selenium によって駆動される Web ブラウザーなど)。
すべての場合のコンテキスト変数は、`behave.runner.Context` のインスタンスです。

## Environmental Controls

environment.py モジュールは、テスト中に特定のイベントの前後に実行するコードを定義できます:

**before_step(context, step)、after_step(context, step)**

これらは各ステップの前後に実行されます。

**before_scenario(context, scene)、after_scenario(context, scene)**

これらは各シナリオの実行前と実行後に実行されます。

**before_feature(context, feature)、after_feature(context, feature)**

これらは、各機能ファイルが実行される前と後に実行されます。

**before_tag(context, tag)、after_tag(context, tag)**

これらは、指定された名前でタグ付けされたセクションの前と後に実行されます。これらは、機能ファイル内で見つかった順序で、遭遇したタグごとに呼び出されます[タグによる制御](https://behave.readthedocs.io/en/stable/tutorial.html#controlling-things-with-tags)を参照してください。

**before_all(context), after_all(context)**

これらは、射撃試合全体の前と後に実行されます。

フィーチャ、シナリオ、およびステップ オブジェクトは、フィーチャ ファイルから解析された情報を表します。これらには、いくつかの属性があります:

**キーワード**

“Feature”, “Scenario”, “Given”, etc.

**name**

ステップの名前 (キーワードの後のテキスト)

**tags**

セクションまたはステップに添付されたタグのリスト。[タグによる制御](https://behave.readthedocs.io/en/stable/tutorial.html#controlling-things-with-tags)を参照してください。

**filename と line**

ステートメントのファイル名 (または「<string>」) と行番号。

環境制御の一般的な使用例としては、すべてのテストを実行するための Web サーバーとブラウザーをセットアップすることが挙げられます。例:

```python
# -- FILE: features/environment.py
from behave import fixture, use_fixture
from behave4my_project.fixtures import wsgi_server
from selenium import webdriver

@fixture
def selenium_browser_chrome(context):
    # -- HINT: @behave.fixture is similar to @contextlib.contextmanager
    context.browser = webdriver.Chrome()
    yield context.browser
    # -- CLEANUP-FIXTURE PART:
    context.browser.quit()

def before_all(context):
    use_fixture(wsgi_server, context, port=8000)
    use_fixture(selenium_browser_chrome, context)
    # -- HINT: CLEANUP-FIXTURE is performed after after_all() hook is called.

def before_feature(context, feature):
    model.init(environment='test')
```
```python
# -- FILE: behave4my_project/fixtures.py
# ALTERNATIVE: Place fixture in "features/environment.py" (but reuse is harder)
from behave import fixture
import threading
from wsgiref import simple_server
from my_application import model
from my_application import web_app

@fixture
def wsgi_server(context, port=8000):
    context.server = simple_server.WSGIServer(('', port))
    context.server.set_app(web_app.main(environment='test'))
    context.thread = threading.Thread(target=context.server.serve_forever)
    context.thread.start()
    yield context.server
    # -- CLEANUP-FIXTURE PART:
    context.server.shutdown()
    context.thread.join()

```
もちろん、必要に応じて、機能ごとに新しいブラウザを用意したり、機能間でデータベースの状態を保持したり、シナリオごとにデータベースを初期化したりすることもできます。

## タグによる制御

機能ファイルの一部に「タグ」を付けることもできます。最も単純なレベルでは、これにより Behave が機能セットの一部を選択的にチェックできるようになります。

Given a feature file with:

```python
Feature: Fight or flight
  In order to increase the ninja survival rate,
  As a ninja commander
  I want my ninjas to decide whether to take on an
  opponent based on their skill levels

  @slow
  Scenario: Weaker opponent
    Given the ninja has a third level black-belt
    When attacked by a samurai
    Then the ninja should engage the opponent

  Scenario: Stronger opponent
    Given the ninja has a third level black-belt
    When attacked by Chuck Norris
    Then the ninja should run for his life
```

次に、`behave --tags=slow` を実行すると、`@slow` のタグが付けられたシナリオのみが実行されます。遅いシナリオ以外をすべてチェックしたい場合は、`behave --tags=-slow` を実行できます。

もう 1 つの一般的な使用例は、作業中のシナリオに `@wip` のタグを付け、`behave --tags=wip` を実行してその 1 つのケースだけをテストすることです。

コマンド ラインでのタグ選択は組み合わせることができます:

- `--tags=wip,slow`

これにより、「wip」または「slow」のタグが付けられたすべてのケースが選択されます。

- `--tags=wip --tags=slow`

これにより、「wip」と「slow」の両方のタグが付けられたすべてのケースが選択されます。

機能またはシナリオにタグが付けられ、その後コマンドライン制御によってスキップされた場合、その機能またはシナリオに対して before_ および after_ 環境関数は呼び出されません。behave には、進行中の作業をテストするための追加サポートがあることに注意してください。

機能とシナリオに添付されたタグは、渡された「feature」または「scenario」オブジェクトを介して環境関数で使用できます。これらのオブジェクトには、「tags」という属性があり、これは、features ファイルで見つかった順序で添付されたタグ名のリストです。

タグに固有の環境制御もあるため、上記の例では、behave は、`@slow` でタグ付けされたシナリオの前後に `environment.py` 関数 `before_tag` および `after_tag` を呼び出して、名前「slow」を渡そうとします。複数のタグが存在する場合、関数は、機能ファイルで定義されている順序で各タグを使用して複数回呼び出されます。

上記の例をもう一度見てみましょう。一部の機能のみがブラウザーと Web サーバーを必要とする場合は、`@browser` でタグ付けできます。

```python
# -- FILE: features/environment.py
# HINT: Reusing some code parts from above.
...

def before_feature(context, feature):
    model.init(environment='test')
    if 'browser' in feature.tags:
        use_fixture(wsgi_server, context)
        use_fixture(selenium_browser_chrome, context)
```
## 進行中の作業

behave は、現在開発中の非常に不安定な「進行中の作業」シナリオの概念をサポートします。このシナリオでは、奇妙なログや stdout への奇妙な出力が生成されたり、behave のシナリオ ランナーと予期しない方法で単純に相互作用したりする可能性があります。

このようなシナリオのテストを簡単にするために、「-w」コマンドライン フラグを実装しました。このフラグは、次のようになります:

1. stdout キャプチャをオフにします
1. ログ キャプチャをオフにします。独自のログ ハンドラーを構成する必要があります。before_all() を次のように構成することをお勧めします:
```python
if not context.config.log_capture:
    logging.basicConfig(level=logging.DEBUG)
```
3. きれいな出力をオフにします - シナリオの出力を混乱させる ANSI エスケープ シーケンスはありません
1. 「@wip」タグのシナリオのみを実行します
1. 最初のエラーで停止します

## Fixtures

フィクスチャは、テスト実行中に頻繁に必要となるセットアップ/クリーンアップ タスクを簡素化します。

```python
# -- FILE: behave4my_project/fixtures.py  (or in: features/environment.py)
from behave import fixture
from somewhere.browser.firefox import FirefoxBrowser

# -- FIXTURE: Use generator-function
@fixture
def browser_firefox(context, timeout=30, **kwargs):
    # -- SETUP-FIXTURE PART:
    context.browser = FirefoxBrowser(timeout, **kwargs)
    yield context.browser
    # -- CLEANUP-FIXTURE PART:
    context.browser.shutdown()
```
詳細については、Fixtures を参照してください。

## エラー時のデバッグ (ステップが失敗した場合)

`after_step()` フックを使用すると、「エラー/失敗時のデバッグ」機能を簡単に提供できます。ステップが失敗するとデバッガーが起動します。

一般的に、この機能は必要なときだけ (対話モードで) 有効にするのがよいでしょう。この機能は (この例では) ユーザー固有の構成データを使用して有効になります。ユーザーは次の操作を実行できます。

- コマンドラインでユーザーデータ定義を提供する
- behave の構成ファイルの「behave.userdata」セクションに値を保存する

```python
# -- FILE: features/environment.py
# USE: behave -D BEHAVE_DEBUG_ON_ERROR         (to enable  debug-on-error)
# USE: behave -D BEHAVE_DEBUG_ON_ERROR=yes     (to enable  debug-on-error)
# USE: behave -D BEHAVE_DEBUG_ON_ERROR=no      (to disable debug-on-error)

BEHAVE_DEBUG_ON_ERROR = False

def setup_debug_on_error(userdata):
    global BEHAVE_DEBUG_ON_ERROR
    BEHAVE_DEBUG_ON_ERROR = userdata.getbool("BEHAVE_DEBUG_ON_ERROR")

def before_all(context):
    setup_debug_on_error(context.config.userdata)

def after_step(context, step):
    if BEHAVE_DEBUG_ON_ERROR and step.status == "failed":
        # -- ENTER DEBUGGER: Zoom in on failure location.
        # NOTE: Use IPython debugger, same for pdb (basic python debugger).
        import ipdb
        ipdb.post_mortem(step.exc_traceback)
```
