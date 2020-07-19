全体像
===============

10分でSymfonyを使い始めましょう！最も重要な概念を理解し、プロジェクトの構築を開始するには本当にこれだけです。

以前にWebフレームワークを利用していた場合は、Symfonyに親しみやすいはずです。
そうでない場合は、全く新しいWebアプリケーションの開発方法へようこそ。
Symfonyはベストプラクティスを採用し、安全簡単にアップグレードができる後方互換性、そして長期的なサポートを提供します。

.. _installing-symfony2:

Symfonyのダウンロード
---------------------

まずは `Composer`_ がインストールされており、PHP7.1.3以降がインストールされていることを確認してください。

準備が整ったらターミナルで次を実行:

.. code-block:: terminal

    $ composer create-project symfony/skeleton quick_tour

これにより、最小で強力なSymfonyアプリケーションの ``quick_tour/`` ディレクトリが作成されます。:

.. code-block:: text

    quick_tour/
    ├─ .env
    ├─ bin/console
    ├─ composer.json
    ├─ composer.lock
    ├─ config/
    ├─ public/index.php
    ├─ src/
    ├─ symfony.lock
    ├─ var/
    └─ vendor/

すでにブラウザでプロジェクトをロードできますか? はい!
:doc:`Nginx またはApache </setup/web_server_configuration>` をセットアップし、 ``public/`` ディレクトリをドキュメントルートに設定します。
しかし、開発のためには :doc:`SymfonyのローカルWebサーバをインストール </setup/symfony_server>` して次のように実行することをお勧めします。:

.. code-block:: terminal

    $ symfony server:start

ブラウザで ``http://localhost:8000`` へアクセスして新しいアプリケーションを試してみましょう！:

.. image:: /_images/quick_tour/no_routes_page.png
   :align: center
   :class: with-browser

基礎: ルート、コントローラ、レスポンス
-----------------------------------------

プロジェクトには約15個のファイルしかありませんが、洗練されたAPI、堅牢なWebアプリ、またはマイクロサービスの準備ができています。
Symfonyは小さなものから始まりますが、あなたと共にスケールします。

しかし先に進む前に、最初のページを作成して基本を掘り下げましょう。

``config/routes.yaml`` で始めます。ここで新しいページのURLを定義できます。
ファイルに既に存在する例のコメントアウトを外します。:

.. code-block:: yaml

    # config/routes.yaml
    index:
        path: /
        controller: 'App\Controller\DefaultController::index'

これは *ルート* と呼ばれます。
それはページ (``/``) のURLと "controller" を定義します。
このURLにアクセスしたときに呼び出される *関数* です。
その関数はまだ存在しないので、作成しましょう！

``src/Controller`` に新しい ``DefaultController`` クラスと ``index`` メソッドを作成します。::

    // src/Controller/DefaultController.php
    namespace App\Controller;

    use Symfony\Component\HttpFoundation\Response;

    class DefaultController
    {
        public function index()
        {
            return new Response('Hello!');
        }
    }

これだけです！ホームページ ``http://localhost:8000/`` にアクセスしてみましょう。
SymfonyはURLがルートと一致することを確認し、新しい ``index()`` メソッドを実行します。

コントローラは、1つのルールを持つ通常の関数で、Symfony ``Response`` オブジェクトを返す必要があります。
ただし、そのレスポンスには、単純なテキスト、JSON、または完全なHTMLページなど、何でも含めることができます。

しかし、ルーティングシステムはるかに強力です。ルートをもっと面白くしましょう。:

.. code-block:: diff

    # config/routes.yaml
    index:
    -     path: /
    +     path: /hello/{name}
        controller: 'App\Controller\DefaultController::index'

このページのURLが変更され、今 ``/hello/*`` となり、 ``{name}`` は全てに一致するワイルドカードのように機能します。
そしてそれは良くなります！コントローラも更新します。:

.. code-block:: diff

    // src/Controller/DefaultController.php
    namespace App\Controller;

    use Symfony\Component\HttpFoundation\Response;

    class DefaultController
    {
    -     public function index()
    +     public function index($name)
        {
    -         return new Response('Hello!');
    +         return new Response("Hello $name!");
        }
    }

``http://localhost:8000/hello/Symfony`` にアクセスして、ページを試してください。
あなたは ``Hello Symfony!`` を確認できるでしょう。
URLの ``{name}`` の値は、コントローラーの ``$name`` 引数として使用できます。

しかし、さらに簡単になる可能性があります。アノテーションサポートをインストールしましょう。:

.. code-block:: terminal

    $ composer require annotations

次に、 ``#`` の文字を追加してYAMLルートをコメントアウトします。:

.. code-block:: yaml

    # config/routes.yaml
    # index:
    #     path: /hello/{name}
    #     controller: 'App\Controller\DefaultController::index'

代わりに、コントローラーメソッドの *真上* にルートを追加します。:

.. code-block:: diff

    // src/Controller/DefaultController.php
    namespace App\Controller;

    use Symfony\Component\HttpFoundation\Response;
    + use Symfony\Component\Routing\Annotation\Route;

    class DefaultController
    {
    +    /**
    +     * @Route("/hello/{name}")
    +     */
         public function index($name) {
             // ...
         }
    }

これは以前と同じように機能します！
しかし、アノテーションを使用することにより、ルートとコントローラーは互いに隣接して存在します。
別のページが必要ですか？
``DefaultController`` に別のルートとメソッドを追加します。::

    // src/Controller/DefaultController.php
    namespace App\Controller;

    use Symfony\Component\HttpFoundation\Response;
    use Symfony\Component\Routing\Annotation\Route;

    class DefaultController
    {
        // ...

        /**
         * @Route("/simplicity")
         */
        public function simple()
        {
            return new Response('Simple! Easy! Great!');
        }
    }

ルーティングは *さらに* いろいろなことができますが、それは別の機会にとっておきましょう！
まさに今、アプリにはさらに多くの機能が必要です！
テンプレートエンジンやロギング、デバッグツールなどのように。

:doc:`/quick_tour/flex_recipes` へ読み進めてください。

.. _`Composer`: https://getcomposer.org/
