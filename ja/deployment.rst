デプロイ
########

アプリケーションが完成したら、またはその前に、それをデプロイしたいと思うことでしょう。
CakePHP アプリケーションをデプロイする際には、いくつかやっておかなければならないことがあります。

セキュリティの確認
==================

もしあなたが荒野にアプリケーションを解き放とうとするなら、何か抜け穴がないかを確認しておくことを
お勧めします。 CSRF 攻撃や、フォームの改ざんなどを防ぐために :doc:`/core-libraries/components/security-component`
をチェックしてください。 :doc:`/models/data-validation` を行うこと、そして
:doc:`/core-utility-libraries/sanitize` は、
XSS 攻撃からデータベースを保護する素晴らしいアイデアです。 ``webroot`` ディレクトリだけが
公開され、あなたの秘密 (アプリケーションの salt とセキュリティキーなど) は
プライベートでユニークなことを確認してください!

ドキュメントルートの指定
========================

アプリケーションでドキュメントルートを正しく指定することはコードをセキュアに、
またアプリケーションを安全に保つために重要なステップの内の一つです。CakePHP のアプリケーションは、
アプリケーションの ``app/webroot`` にドキュメントルートを指定する必要があります。
これによってアプリケーション、設定のファイルが URL を通してアクセスすることができなくなります。
ドキュメントルートの指定の仕方はウェブサーバーごとに異なります。
WEBサーバー特有の情報についていは :doc:`/installation/url-rewriting` ドキュメントを見てください。

どの場合においても ``app/webroot/`` をバーチャルホスト（バーチャルドメイン）のドキュメントルートに
設定すべきでしょう。これは webroot ディレクトリの外側のファイルを実行される可能性を取り除きます。


core.php の更新
===============

core.php の更新、とりわけ ``debug`` 値は非常に重要です。 debug = 0 にすることで、
広くインターネットに晒されることがあってはならない多くの開発用の機能を無効にできます。
debug の無効化は以下の種々のものごとに変更を与えます:

* :php:func:`pr()` と :php:func:`debug()` で生成されるデバッグメッセージが無効化されます。
* CakePHP コアのキャッシュが、開発時の 10 秒ごとの代わりに 999 日ごとに破棄されるようになります。
* Error ビューが吐く情報が少なくなり、代わりに共通のエラーメッセージが与えられます。
* エラーが表示されなくなります。
* 例外のスタックトレースが無効になります。

上記に加えて、プラグインやアプリケーションの拡張が ``debug`` を用いてその挙動を変えます。

あなたは環境間で動的にデバッグレベルを設定する環境変数に対してチェックすることができます。
これは、 本番環境へのデプロイ前に debug > 0 でアプリケーションをデプロイすることを回避して、
毎回デバッグレベルを変更することを省略できます。

例えば、Apache の設定で環境変数を設定します。 ::

	SetEnv CAKEPHP_DEBUG 2

そうすると ``core.php`` デバッグレベルを動的に設定できます。 ::

	if (getenv('CAKEPHP_DEBUG')) {
		Configure::write('debug', 2);
	} else {
		Configure::write('debug', 0);
	}

アプリケーションのパフォーマンスの向上
======================================

プラグインの画像や JavaScript、CSS ファイルなどの静的なアセットを扱う場合、
ディスパッチャを通すことはかなりの非効率です。
本番環境においては、次のようにシンボリックリンクを張ることを強くお勧めします。 ::

    ln -s app/Plugin/YourPlugin/webroot/css/yourplugin.css app/webroot/css/yourplugin.css


.. meta::
    :title lang=ja: Deployment
    :keywords lang=ja: stack traces,application extensions,set document,installation documentation,development features,generic error,document root,func,debug,caches,error messages,configuration files,webroot,deployment,cakephp,applications
