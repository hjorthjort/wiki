The REPL
========

Start with `erl`

Load a module (if ncessary): `l(MODULE_NAME)`

Compile a moduel from source


Modules
=======

Compile a module from source
```erlang
-module(foo).
-compile(export_all)


% Function defintions ... 
```

Load module in REPL with `c(foo)`.

Calling across language barriers
================================

It's easy to call external programs and get their output back as a message, using `open_port`.

<http://erlang.org/doc/reference_manual/ports.html>
<http://erlang.org/doc/tutorial/c_port.html>


Multiple nodes
==============

Start a named node, and (for fun) have it ping itself:

```sh
$ erl -sname foo
1> net_adm:ping(node())
pong
```

Start another node, and have it ping the first one:

```sh
$ erl -sname bar
```
```erl
1> nodes().
[]
2> net_adm:ping(foo@my_host_name).
pong
3> nodes().
[foo@my_host_name]
```

Nodes find out about each other by communicating.
When two nodes come in contact, they also tell each other of all other nodes they know of.
Start a third node, and have it ping the first one:

```sh
$ erl -sname baz
```
```erl
1> nodes().
[]
2> net_adm:ping(foo@my_host_name).
pong
3> nodes().
[foo@alladin,bar@my_host_name]
```

Across hosts: Magic cookie
--------------------------

If you want to communicate across hosts (and across machines), there are some things to be aware of.
1. There's no security built-in. Messages fly around in the open, so make sure you only ever connect nodes behind firewalls, and use proper internet protocols for communication on untrusted networks.
2. All hosts in the same network need to have the same ["magic cookie"](http://erlang.org/doc/reference_manual/distributed.html#security), which is an Erlang atom in the file `$HOME/.erlang.cookie`. If you have not set it yourself, it was set at random, with access 400 (-r--------). Just change this to have the same value on all your machines. You can also do some custom setting and assuming cookies with `erlang:set_cookie\2`.
