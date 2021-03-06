``pprint++``: a drop-in replacement for ``pprint`` that's actually pretty
=========================================================================

.. image:: https://travis-ci.org/wolever/pprintpp.svg?branch=master
    :target: https://travis-ci.org/wolever/pprintpp

Now with Python 3 support!

Installation
------------


``pprint++`` can be installed with Python 2 or Python 3 using ``pip`` or
``easy_install``::

    $ pip install pprintpp
    - OR -
    $ easy_install pprintpp

Usage
-----

``pprint++`` can be used in three ways:

1. As a regular module::

   >>> import pprintpp
   >>> pprintpp.pprint(...)

2. As a command-line program, which will read Python literals from standard in
   and pretty-print them::

    $ echo "{'hello': 'world'}" | pypprint
    {'hello': 'world'}

3. To monkeypatch ``pprint``::

    >>> import pprintpp
    >>> pprintpp.monkeypatch()
    >>> import pprint
    >>> pprint.pprint(...)

   Note: the original ``pprint`` will be available with ``import
   pprint_original``.

4. Through the separate ``pp`` package::

    $ pip install pp-ez
    $ python
    ...
    >>> import pp
    >>> pp(["Hello", "world"])
    ["Hello", "world"]

   For more, see https://pypi.python.org/pypi/pp-ez

Usability Protips
-----------------

``pp``
~~~~~~

For bonus code aesthetics, ``pprintpp.pprint`` can be imported as ``pp``:

.. code:: pycon

    >>> from pprintpp import pprint as pp
    >>> pp(...)

And if that is just too many letters, the ``pp-ez`` package can be installed
from PyPI, ensuring that pretty-printing is never more than an ``import pp``
away::

    $ pip install pp-ez
    $ python
    ...
    >>> import pp
    >>> pp(["Hello", "world"])
    ["Hello", "world"]

For more, see https://pypi.python.org/pypi/pp-ez


Why is it prettier?
-------------------

Unlike ``pprint``, ``pprint++`` strives to emit a readable, largely
PEP8-compliant, representation of its input.

It also has explicit support for: the ``collections`` module (``defaultdict``
and ``Counter``) and ``numpy`` arrays:

.. code:: pycon

    >>> import numpy as np
    >>> from collections import defaultdict, Counter
    >>> pprint([np.array([[1,2],[3,4]]), defaultdict(int, {"foo": 1}), Counter("aaabbc")])
    [
        array([[1, 2],
               [3, 4]]),
        defaultdict(<type 'int'>, {'foo': 1}),
        Counter({'a': 3, 'b': 2, 'c': 1}),
    ]

Unicode characters, when possible, will be printed un-escaped. This is done by
checking both the output stream's encoding (defaulting to ``utf-8``) and the
character's Unicode category. An effort is made to print only characters which
will be visually unambiguous: letters and numbers will be printed un-escaped,
spaces, combining characters, and control characters will be escaped:

.. code:: pycon

    >>> unistr = u"\xe9e\u0301"
    >>> print unistr
    éé
    >>> pprint(unistr)
    u'ée\u0301'

The output stream's encoding will be considered too:

.. code:: pycon

    >>> import io
    >>> stream = io.BytesIO()
    >>> stream.encoding = "ascii"
    >>> pprint(unistr, stream=stream)
    >>> print stream.getvalue()
    u'\xe9e\u0301'

**Note**: ``pprint++`` is still under development, so the format *will* change
and improve over time.

Example
~~~~~~~

With ``printpp``:

.. code:: pycon

    >>> import pprintpp
    >>> pprintpp.pprint(["Hello", np.array([[1,2],[3,4]])])
    [
        'Hello',
        array([[1, 2],
               [3, 4]]),
    ]
    >>> pprintpp.pprint(tweet)
    {
        'coordinates': None,
        'created_at': 'Mon Jun 27 19:32:19 +0000 2011',
        'entities': {
            'hashtags': [],
            'urls': [
                {
                    'display_url': 'tumblr.com/xnr37hf0yz',
                    'expanded_url': 'http://tumblr.com/xnr37hf0yz',
                    'indices': [107, 126],
                    'url': 'http://t.co/cCIWIwg',
                },
            ],
            'user_mentions': [],
        },
        'place': None,
        'source': '<a href="http://www.tumblr.com/" rel="nofollow">Tumblr</a>',
        'truncated': False,
        'user': {
            'contributors_enabled': True,
            'default_profile': False,
            'entities': {'hashtags': [], 'urls': [], 'user_mentions': []},
            'favourites_count': 20,
            'id_str': '6253282',
            'profile_link_color': '0094C2',
        },
    }

Without ``printpp``::

    >>> import pprint
    >>> import numpy as np
    >>> pprint.pprint(["Hello", np.array([[1,2],[3,4]])])
    ['Hello', array([[1, 2],
           [3, 4]])]
    >>> tweet = {'coordinates': None, 'created_at': 'Mon Jun 27 19:32:19 +0000 2011', 'entities': {'hashtags': [], 'urls': [{'display_url': 'tumblr.com/xnr37hf0yz', 'expanded_url': 'http://tumblr.com/xnr37hf0yz', 'indices': [107, 126], 'url': 'http://t.co/cCIWIwg'}], 'user_mentions': []}, 'place': None, 'source': '<a href="http://www.tumblr.com/" rel="nofollow">Tumblr</a>', 'truncated': False, 'user': {'contributors_enabled': True, 'default_profile': False, 'entities': {'hashtags': [], 'urls': [], 'user_mentions': []}, 'favourites_count': 20, 'id_str': '6253282', 'profile_link_color': '0094C2'}} 
    >>> pprint.pprint(tweet)
    {'coordinates': None,
     'created_at': 'Mon Jun 27 19:32:19 +0000 2011',
     'entities': {'hashtags': [],
                  'urls': [{'display_url': 'tumblr.com/xnr37hf0yz',
                            'expanded_url': 'http://tumblr.com/xnr37hf0yz',
                            'indices': [107, 126],
                            'url': 'http://t.co/cCIWIwg'}],
                  'user_mentions': []},
     'place': None,
     'source': '<a href="http://www.tumblr.com/" rel="nofollow">Tumblr</a>',
     'truncated': False,
     'user': {'contributors_enabled': True,
              'default_profile': False,
              'entities': {'hashtags': [], 'urls': [], 'user_mentions': []},
              'favourites_count': 20,
              'id_str': '6253282',
              'profile_link_color': '0094C2'}}
