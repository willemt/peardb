.. image:: https://img.shields.io/badge/rsttst-testable-brightgreen.svg
   :target: https://github.com/willemt/rsttst

.. image:: http://badges.github.io/stability-badges/dist/experimental.svg
   :target: http://github.com/badges/stability-badges

.. image:: https://travis-ci.org/willemt/peardb.png
   :target: https://travis-ci.org/willemt/peardb


What?
=====
PearDB is a HTTP Key-Value pair database. It uses `LMDB <http://symas.com/mdb/>`_ for storing data, and `h2o <https://github.com/h2o/h2o>`_ for HTTP.

Persistent connections and pipelining are built-in.

Why?
====
LMBD allows zero copy. h2o is targeted towards low latency. This means PearDB *could be really fast*.

Because the CRUD is RESTful You could hypothetically use an HTTP cache to scale out reads.

Example usage
=============

All examples below make use of the excellent `httpie <https://github.com/jakubroztocil/httpie>`_

Starting the server
-------------------

.. code-block:: bash

   sudo build/pear -d -p 80
   echo daemonizing...
   sleep 10

.. code-block:: bash

   daemonizing...

Get
---
You obtain a value by GET'ng the key. In this case the key is 'mykey':

.. code-block:: bash

   http -v -h --ignore-stdin http://127.0.0.1/mykey/

But you get a 404 if it doesn't exist:

.. code-block:: bash
   :class: dotted

   HTTP/1.1 404 NOT FOUND
   Date: ..., ... .... ........ GMT 
   Server: h2o/1.0.0
   Connection: keep-alive
   content-length: 0

Put
---
We use PUT instead of POST for putting a key-value pair.

.. code-block:: bash

   echo "MY VALUE" | http -h PUT http://127.0.0.1/mykey/

.. code-block:: bash
   :class: dotted

   HTTP/1.1 200 OK
   Date: ..., ... .... ........ GMT 
   Server: h2o/1.0.0
   Connection: keep-alive
   content-type: text/plain; charset=utf-8
   transfer-encoding: chunked

Now we can finally retrieve our data:

.. code-block:: bash

   http --ignore-stdin http://127.0.0.1/mykey/

.. code-block:: bash
   :class: dotted

   MY VALUE


Delete
------
.. code-block:: bash

   http -h --ignore-stdin DELETE http://127.0.0.1/mykey/

.. code-block:: bash
   :class: dotted

   HTTP/1.1 200 OK
   Date: ..., ... .... ........ GMT 
   Server: h2o/1.0.0
   Connection: keep-alive
   transfer-encoding: chunked

Doesn't exist anymore:

.. code-block:: bash

   http -h --ignore-stdin http://127.0.0.1/mykey/

.. code-block:: bash
   :class: dotted

   HTTP/1.1 404 NOT FOUND
   Date: ..., ... .... ........ GMT 
   Server: h2o/1.0.0
   Connection: keep-alive
   content-length: 0

Building
========

Ubuntu
------
$ sudo apt-get install libuv

$ make libh2o

$ make

OSX
---
$ brew install --HEAD libuv

$ make libh2o

$ make
