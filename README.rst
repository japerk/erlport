Erlang port protocol for Python
===============================

.. contents::

Project URLs:

- http://github.com/hdima/erlport
- http://pypi.python.org/pypi/erlport/


Description
-----------

The erlport Python library implements `Erlang external term format
<http://www.erlang.org/doc/apps/erts/erl_ext_dist.html>`_ and `Erlang port
protocol <http://erlang.org/doc/man/erlang.html#open_port-2>`_ for easier
integration of Python and Erlang.

The library exports the following classes and functions:

- ``Port(packet=1, use_stdio=False)`` - class implementing port which connects
  with the corresponding Erlang port. See `open_port/2
  <http://erlang.org/doc/man/erlang.html#open_port-2>`_ for description of
  ``packet`` and ``use_stdio`` arguments.

- ``Protocol()`` - class which simplifies creation of request-response
  protocols.

- ``Atom(str)`` - class which represents an Erlang atom.

- ``String(unicode)`` - class representing an Erlang string which also can be
  an array of integers.

- ``decode(str)`` - function to convert binary data into a term.

- ``encode(term)`` - function to convert a term into the external format.

- ``IncompleteData`` - exception raised by ``decode()`` in case of incomplete
  input data.


Installation
------------

Prerequisites:

- Erlang >= R11B-4

- Python >= 2.4

To install the library use ``easy_install`` from `setuptools
<http://pypi.python.org/pypi/setuptools>`_ package like this::

    $ easy_install erlport


Examples
--------

See ``examples`` directory in the source distribution for additional examples.

For simple request-response protocol use ``Port`` and ``Protocol`` on the
Python side like this::

    from erlport import Port, Protocol

    class HelloProtocol(Protocol):

        def handle_hello(self, name):
            return "Hello, %s" % name

    if __name__ == "__main__":
        proto = HelloProtocol()
        proto.run(Port())

On the Erlang side function ``hello()`` can be called like this::

    -module(hello).
    -export([hello/1]).

    hello(Name) ->
        Port = open_port({spawn, "python hello.py"},
            [{packet, 1}, nouse_stdio, binary]),
        port_command(Port, term_to_binary({hello, Name})),
        receive
            {Port, {data, Data}} ->
                binary_to_term(Data)
        end.

Test it in the Erlang shell::

    1> c(hello).
    {ok,hello}
    2> hello:hello("Bob").
    "Hello, Bob"


Feedback
--------

Please report bugs, offer suggestions or feedback at:

- Report bugs at http://github.com/hdima/erlport/issues

- Email me at <dima at hlabs.spb.ru>

- Write or follow me at http://twitter.com/hdima