=================================
 celery - 分散型タスクキュー
=================================

.. image:: http://cloud.github.com/downloads/celery/celery/celery_128.png

|build-status| |coverage-status|

:Version: 3.2.0a1 (Cipater)
:Web: http://celeryproject.org/
:Download: http://pypi.python.org/pypi/celery/
:Source: http://github.com/celery/celery/
:Keywords: task queue, job queue, asynchronous, async, rabbitmq, amqp, redis,
  python, webhooks, queue, distributed

--

タスクキューとは?
=====================

タスクキューはスレッド　または　マシンの分散処理として使われている

Task queues are used as a mechanism to distribute work across threads or machines.

タスクキューのインプットは、タスクと呼ばれる作業単位であり、専用のワーカープロセス　が常に新しい作業を実行するためのキューを監視します。

A task queue's input is a unit of work, called a task, dedicated worker processes then constantly monitor the queue for new work to perform.

通常ではCeleryは、ワーカーとクライアントの中継にブローカーを用いてメッセージの通信を行います。タスクを開始する場合は、クライアントがキューにメッセージを置き、ブローカーがワーカーにメッセージを配信します。

Celery communicates via messages, usually using a broker to mediate between clients and workers.  To initiate a task a client puts a
message on the queue, the broker then delivers the message to a worker.

Celeryは複数のワーカーやブローカーを用いることにより、高可用性と水平方向のスケーリングを可能とします。

A Celery system can consist of multiple workers and brokers, giving way to high availability and horizontal scaling.

CeleryはPythonで書かれたライブラリですが、プロトコルは任意言語で実装することができます。Ruby用のCeleryは RCelery_ やPHPクライアントがありますが、Webhookを利用することで言語間の運用も可能です。

Celery is a library written in Python, but the protocol can be implemented in any language.  So far there's RCelery_ for the Ruby programming language, and a
`PHP client`, but language interoperability can also be achieved
by using webhooks.

.. _RCelery: https://github.com/leapfrogonline/rcelery
.. _`PHP client`: https://github.com/gjedeer/celery-php
.. _`using webhooks`:
    http://docs.celeryproject.org/en/latest/userguide/remote-tasks.html

必要なもの?
===============

Celery バージョン3.0　実行環境

Celery version 3.0 runs on,

- Python (2.6, 2.7, 3.3, 3.4)
- PyPy (1.8, 1.9)
- Jython (2.5, 2.7).

これは、Pytohn 2.5をサポートする最後のバージョンとなる。Celery 3.1以降は、Python 2.6以降が必要となる。Python 2.4をサポートした最後のバージョンはCelery 2.2となる。

This is the last version to support Python 2.5,
and from Celery 3.1, Python 2.6 or later is required.
The last version to support Python 2.4 was Celery series 2.2.

*Celery*はメッセージブローカーによって、メッセージの送受信をおこないます。RabbitMQ, Redisの転送は完全に機能していますが、ローカル開発のためのSQLiteのように試験的なサポートもあります。

*Celery* is usually used with a message broker to send and receive messages.
The RabbitMQ, Redis transports are feature complete,
but there's also experimental support for a myriad of other solutions, including
using SQLite for local development.

*Celery*は1台や複数のマシン、データセンタをまたいで実行できます。

*Celery* can run on a single machine, on multiple machines, or even
across datacenters.

はじめに
===========

初めてCeleryを使う方や以前のバージョンからCelery 3.0を使う方は、チュートリアルから始めることをおすすめします。

If this is the first time you're trying to use Celery, or you are new to Celery 3.0 coming from previous versions then you should read our
getting started tutorials:

- `Celeryの始めの一歩`_

- `First steps with Celery`_

    チュートリアルは、Celeryを始めるために必要な最小限の最も基本的なことを教えてくれます。

    Tutorial teaching you the bare minimum needed to get started with Celery.

- `次に`_

- `Next steps`_

    より詳細な概要から、機能を知ることができます。

    A more complete overview, showing more features.

.. _`Celeryの始めの一歩`:
    http://docs.celeryproject.org/en/latest/getting-started/first-steps-with-celery.html

.. _`次に`:
    http://docs.celeryproject.org/en/latest/getting-started/next-steps.html

Celeryとは...
==========

- **シンプル**

    Celeryは簡単に使えて、維持も簡単で、*設定ファイルを必要としません。*

    Celery is easy to use and maintain, and does *not need configuration files*.

    アクティブで友好的なコミュニティに、`メーリングリスト`_やIRCチャンネルでサポートを求めることができます。

    It has an active, friendly community you can talk to for support, including a `mailing-list`_ and and an IRC channel.

    ここにあるサンプルは、簡単なアプリケーションの１つです。::

    Here's one of the simplest applications you can make::

        from celery import Celery

        app = Celery('hello', broker='amqp://guest@localhost//')

        @app.task
        def hello():
            return 'hello world'

- **高可用性**

    ワーカーとクライアントは、コネクションの切断や失敗のイベントで自動的にリトライします。またいくつかのブローカーは*マスター/マスター*または、*マスター/スレーブ*のリプリケーションのHAをサポートしています。

    Workers and clients will automatically retry in the event of connection loss or failure, and some brokers support HA in way of *Master/Master* or *Master/Slave* replication.

- **高速性**

    単独のCeleryプロセスは、1分間に数百万のタスクを,1ミリ秒以下のラウンドトリップ・レイテンシーで処理できます。(RabbitMQ, py-librabbitmqを使い、最適な設定の場合)

    A single Celery process can process millions of tasks a minute, with sub-millisecond round-trip latency (using RabbitMQ, py-librabbitmq, and optimized settings).

- **順応性**

    *Celery*のほぼすべて部分は、拡張したり、独自にプールや、シリアライズ、圧縮方式、ロギング、スケジューラ、カスタマ、プロデューサ、オートスケーラ、ブローカ転送などをカスタマイズできます。

    Almost every part of *Celery* can be extended or used on its own, Custom pool implementations, serializers, compression schemes, logging, schedulers, consumers, producers, autoscalers, broker transports and much more.

サポート...
============
    
    - **メッセージ転送**

        - RabbitMQ_, Redis_,
        - MongoDB_ (experimental), Amazon SQS (experimental),
        - CouchDB_ (experimental), SQLAlchemy_ (experimental),
        - Django ORM (experimental), `IronMQ`_
        - など...

    - **並行性**

        - Prefork, Eventlet_, gevent_, threads/single threaded

    - **結果保存**

        - AMQP, Redis
        - memcached, MongoDB
        - SQLAlchemy, Django ORM
        - Apache Cassandra, IronCache

    - **シリアル化**

        - *pickle*, *json*, *yaml*, *msgpack*.
        - *zlib*, *bzip2* compression.
        - Cryptographic message signing.

.. _`Eventlet`: http://eventlet.net/
.. _`gevent`: http://gevent.org/

.. _RabbitMQ: http://rabbitmq.com
.. _Redis: http://redis.io
.. _MongoDB: http://mongodb.org
.. _Beanstalk: http://kr.github.com/beanstalkd
.. _CouchDB: http://couchdb.apache.org
.. _SQLAlchemy: http://sqlalchemy.org
.. _`IronMQ`: http://iron.io

統合Framework
=====================

CeleryはWebフレームワークと簡単に統合することができます。いくつかフレームワークは統合パッケージがあります。

Celery is easy to integrate with web frameworks, some of which even have integration packages:

    +--------------------+------------------------+
    | `Django`_          | not needed             |
    +--------------------+------------------------+
    | `Pyramid`_         | `pyramid_celery`_      |
    +--------------------+------------------------+
    | `Pylons`_          | `celery-pylons`_       |
    +--------------------+------------------------+
    | `Flask`_           | not needed             |
    +--------------------+------------------------+
    | `web2py`_          | `web2py-celery`_       |
    +--------------------+------------------------+
    | `Tornado`_         | `tornado-celery`_      |
    +--------------------+------------------------+

The integration packages are not strictly necessary, but they can make
development easier, and sometimes they add important hooks like closing
database connections at ``fork``.

.. _`Django`: http://djangoproject.com/
.. _`Pylons`: http://www.pylonsproject.org/
.. _`Flask`: http://flask.pocoo.org/
.. _`web2py`: http://web2py.com/
.. _`Bottle`: http://bottlepy.org/
.. _`Pyramid`: http://docs.pylonsproject.org/en/latest/docs/pyramid.html
.. _`pyramid_celery`: http://pypi.python.org/pypi/pyramid_celery/
.. _`django-celery`: http://pypi.python.org/pypi/django-celery
.. _`celery-pylons`: http://pypi.python.org/pypi/celery-pylons
.. _`web2py-celery`: http://code.google.com/p/web2py-celery/
.. _`Tornado`: http://www.tornadoweb.org/
.. _`tornado-celery`: http://github.com/mher/tornado-celery/

.. _celery-documentation:

ドキュメント
=============

ユーザガイドやチュートリアル、APIリファレンスの`最新版`_は、Read The Docsでホスティングされています。

The `latest documentation`_ with user guides, tutorials and API reference
is hosted at Read The Docs.

.. _`最新版`: http://docs.celeryproject.org/en/latest/

.. _celery-installation:

インストール
============

CeleryはPythonパッケージ(PyPI)または、ソースからインストールすることができます。

You can install Celery either via the Python Package Index (PyPI)
or from source.

'pip'でインストールする場合::

To install using `pip`,::

    $ pip install -U Celery

'easy_install'でインストールする場合::

To install using `easy_install`,::

    $ easy_install -U Celery

.. _bundles:

付属
-------

Celeryは、Celeryと依存関係のある特定の機能を、インストールするための付属のグループを定義しています。　

Celery also defines a group of bundles that can be used
to install Celery and the dependencies for a given feature.

要件や''pip''コマンド上で、括弧で指定することができます。複数の付属品をカンマ区切りで複数仕手する事もできます。

You can specify these in your requirements or on the ``pip`` comand-line by using brackets.  Multiple bundles can be specified by separating them by commas.
::

    $ pip install "celery[librabbitmq]"

    $ pip install "celery[librabbitmq,redis,auth,msgpack]"

下記の付属品が使えます:

The following bundles are available:

シリアル化
~~~~~~~~~~~

:celery[auth]:
    for using the auth serializer.

:celery[msgpack]:
    for using the msgpack serializer.

:celery[yaml]:
    for using the yaml serializer.

平行性
~~~~~~~~~~~

:celery[eventlet]:
    for using the eventlet pool.

:celery[gevent]:
    for using the gevent pool.

:celery[threads]:
    for using the thread pool.

転送 と バックエンド
~~~~~~~~~~~~~~~~~~~~~~~

:celery[librabbitmq]:
    for using the librabbitmq C library.

:celery[redis]:
    for using Redis as a message transport or as a result backend.

:celery[mongodb]:
    for using MongoDB as a message transport (*experimental*),
    or as a result backend (*supported*).

:celery[sqs]:
    for using Amazon SQS as a message transport (*experimental*).

:celery[memcache]:
    for using memcached as a result backend.

:celery[cassandra]:
    for using Apache Cassandra as a result backend.

:celery[couchdb]:
    for using CouchDB as a message transport (*experimental*).

:celery[couchbase]:
    for using CouchBase as a result backend.

:celery[beanstalk]:
    for using Beanstalk as a message transport (*experimental*).

:celery[zookeeper]:
    for using Zookeeper as a message transport.

:celery[zeromq]:
    for using ZeroMQ as a message transport (*experimental*).

:celery[sqlalchemy]:
    for using SQLAlchemy as a message transport (*experimental*),
    or as a result backend (*supported*).

:celery[pyro]:
    for using the Pyro4 message transport (*experimental*).

:celery[slmq]:
    for using the SoftLayer Message Queue transport (*experimental*).

.. _celery-installing-from-source:

ソースのダウンロードとインストール
--------------------------------------

最新版のCeleryのダウンロードは、

Download the latest version of Celery from
http://pypi.python.org/pypi/celery/

下記の手順でインストールする::

You can install it by doing the following,::

    $ tar xvfz celery-0.0.0.tar.gz
    $ cd celery-0.0.0
    $ python setup.py build
    # python setup.py install

最後のコマンドでvirtualenvを使わない場合は、特権ユーザで実行する必要がある。

The last command must be executed as a privileged user if
you are not currently using a virtualenv.

.. _celery-installing-from-git:

開発バージョンを使う
-----------------------------

pipの場合
~~~~~~~~

Celeryの開発バージョンは、開発バージョンの``kombu``, ``amqp`` and ``billiard``が必要となります。

The Celery development version also requires the development
versions of ``kombu``, ``amqp`` and ``billiard``.

下記の手順により、最新のスナップショットからインストールできる。
pipコマンド::

You can install the latest snapshot of these using the following
pip commands::

    $ pip install https://github.com/celery/celery/zipball/master#egg=celery
    $ pip install https://github.com/celery/billiard/zipball/master#egg=billiard
    $ pip install https://github.com/celery/py-amqp/zipball/master#egg=amqp
    $ pip install https://github.com/celery/kombu/zipball/master#egg=kombu

gitの場合
~~~~~~~~

コントリビュートセクション

Please the Contributing section.

.. _getting-help:

Getting Help
============

.. _mailing-list:

メーリングリスト
------------

Celeryの使い方や、開発、将来の話し合いは、`celery-users`_のメーリングリストに参加してください。

For discussions about the usage, development, and future of celery,
please join the `celery-users`_ mailing list.

.. _`celery-users`: http://groups.google.com/group/celery-users/

.. _irc-channel:

IRC
---

IRCのチャットに参加してください。**#celery**は`Freenode`_にあります。

Come chat with us on IRC. The **#celery** channel is located at the `Freenode`_
network.

.. _`Freenode`: http://freenode.net

.. _bug-tracker:

バグ・トラッカー
===========

意見や、バグ報告、質問がある場合は、私たちのイシュー・トラッカーの http://github.com/celery/celery/issues/ に報告してください

If you have any suggestions, bug reports or annoyances please report them
to our issue tracker at http://github.com/celery/celery/issues/

.. _wiki:

Wiki
====

http://wiki.github.com/celery/celery/

.. _contributing-short:

貢献
============

`celery`の開発はGithubでおこなわれています。：http://github.com/celery/celery


Development of `celery` happens at Github: http://github.com/celery/celery

`celery`の開発に参加することを非常に推奨いたします。もし(何らかの理由で)Githubが気に入らないのであれば、通常通りパッチを送ることをも歓迎いたします。

You are highly encouraged to participate in the development of `celery`. If you don't like Github (for some reason) you're welcome to send regular patches.

`Celeryに貢献するには`_の章を必ず読んでください。

Be sure to also read the `Contributing to Celery`_ section in the documentation.

.. _`Celeryに貢献するには`:
    http://docs.celeryproject.org/en/master/contributing.html

.. _license:

ライセンス
=======

このソフトウェアは、`New BSD License`にライセンスされています。ライセンステキストの全文は、ディストリビューションの最上位のディレクトリに``LICENSE``ファイルで見ることができます。

日本語訳は、`New BSD License`にライセンスされています。

This software is licensed under the `New BSD License`. See the ``LICENSE``file in the top distribution directory for the full license text.

.. # vim: syntax=rst expandtab tabstop=4 shiftwidth=4 shiftround


.. image:: https://d2weczhvl823v0.cloudfront.net/celery/celery/trend.png
    :alt: Bitdeli badge
    :target: https://bitdeli.com/free

.. |build-status| image:: https://travis-ci.org/celery/celery.svg?branch=master
   :target: https://travis-ci.org/celery/celery
.. |coverage-status| image:: https://coveralls.io/repos/celery/celery/badge.svg
   :target: https://coveralls.io/r/celery/celery


.. image:: https://d2weczhvl823v0.cloudfront.net/windschord/celery-doc-ja/trend.png
   :alt: Bitdeli badge
   :target: https://bitdeli.com/free

