.. ipython:: python
    :suppress:

    import sys
    sys.path.append("..")

    import redbaron
    redbaron.ipython_behavior = False

    from redbaron import RedBaron


This is the reference page for every node type encountered in RedBaron and
their specificities.

**This page is still a WIP**.

========
TopClass
========

.. _CodeBlockNode:

CodeBlockNode
=============

CodeBlockNode is a type of node that has a body composed of indented code
like the FuncdefNode or the IfNode. Great care has been taken on the SetAttr of
their value so you don't have to take care about reindenting and other
formatting details.

Demonstration:

.. ipython:: python

    red = RedBaron("def function():\n    pass\n")
    red
    red[0].value = "stuff"  # first '\n' will be added, indentation will be set
    red
    red[0].value = "                    bad_indent"
    red
    red[0].value = " some\n stuff"
    red

Some for indented cases:

.. ipython:: python

    red = RedBaron("class A:\n    def __init__():\n        pass\n\n    def plop():\n        pass")
    red.def_.value = "not_indented"
    red
    red.def_.value = "\n                              badly_indented"
    red
    red.def_.value = "some\nstuff\nfoo\nbar\n\npouet"
    red

=====
Nodes
=====

AssertNode
==========

A node representing the assert statement.

.. ipython:: python

    RedBaron("assert test, message")[0].help(deep=True, with_formatting=True)

SetAttr
-------

.. ipython:: python

    red = RedBaron("assert some_test")
    red
    red[0].value = "1 == caramba()"
    red
    red[0].message = "'foo bar'"
    red
    red[0].message = ""
    red


AssignmentNode
==============

A node representing the assign operation in python (:file:`foo = bar`) and the
"augmented" assign (:file:`foo += bar`).

.. ipython:: python

    RedBaron("a = b")[0].help(deep=True, with_formatting=True)
    RedBaron("a += b")[0].help(deep=True, with_formatting=True)

SetAttr
-------

Works as expected:

.. ipython:: python

    red = RedBaron("a = b")
    red[0].first = "caramba"
    red
    red[0].second = "42"
    red

For the operator part, expected input should work:

.. ipython:: python

    red = RedBaron("a = b")
    red[0].operator = "+="
    red
    red[0].operator = "+" # equivalent to '+='
    red
    red[0].operator = "-" # equivalent to '-='
    red
    red[0].operator = "=" # equivalent to '='
    red
    red[0].operator = "/="
    red
    red[0].operator = "" # equivalent to '='
    red

AssociativeParenthesisNode
==========================

This node represent a statement prioritised other another by being surrounded by
parenthesis. For e.g., the first part of this addition: :file:`(1 + 1) * 2`.

.. ipython:: python

    RedBaron("(foo)")[0].help(with_formatting=True, deep=True)

SetAttr
-------

.. ipython:: python

    red = RedBaron("(foo)")
    red
    red[0].value = "1 + 1"
    red


.. _AtomtrailersNode:

AtomtrailersNode
================

This node represent a combination of :ref:`NameNode`, :ref:`DotNode`,
:ref:`CallNode`, :ref:`GetitemNode` sorted in a list. For e.g.:
:file:`a.b().c[d]`.

.. ipython:: python

    RedBaron("a.b().c[d]")[0].help(with_formatting=True, deep=True)

SetAttr
-------

.. ipython:: python

    red = RedBaron("a.b()")
    red
    red[0].value = "d.be"
    red


BinaryOperatorNode
==================

The node represent a binary operator (an operator (e.g: :file:`+` :file:`-` :file:`/`..) applied to 2 values) with its operands. For e.g.: :file:`1 + 1`.

.. ipython:: python

    RedBaron("1 + 1")[0].help(with_formatting=True, deep=True)

SetAttr
-------

.. ipython:: python

    red = RedBaron("1 + 1")
    red
    red[0].operator = "*"
    red
    red[0].first = "(1 + 1)"
    red
    red[0].second = "caramba"
    red

.. _CallNode:

CallNode
========

A node representing a call (eg: :file:`a()`, here :file:`a` is called with no
arguments). It is always stored in an :ref:`AtomtrailersNode` or a
:ref:`DecoratorNode`.

.. ipython:: python

    RedBaron("a(b, c=d)")[0].value[1].help(deep=True, with_formatting=True)

SetAttr
-------

SetAttr works as expected:

.. ipython:: python

    red = RedBaron("a()")
    red[0].value[1].value = "b, c=d, *e, **f"
    red

Helpers
-------

CallNode comes with one helper to add another item at the end of the value of
the node without having to think about formatting. It is documented here:
:ref:`append_value`.


CallArgumentNode
================

A node representing an argument or a named argument of a :ref:`CallNode` (other
nodes that can be in a CallNode are :ref:`ListArgumentNode` and
:ref:`DictArgumentNode`).

.. ipython:: python

    RedBaron("a(b, c=d)")[0].value[1].value[0].help(deep=True, with_formatting=True)
    RedBaron("a(b, c=d)")[0].value[1].value[2].help(deep=True, with_formatting=True)

SetAttr
-------

.. ipython:: python

    red = RedBaron("a(b)")
    red
    red[0].value[1].value[0] = "stuff=foo"
    red


ClassNode
=========

A node representing a class definition.

.. ipython:: python

    RedBaron("class SomeAwesomeName(A, B, C): pass")[0].help(deep=True, with_formatting=True)

SetAttr
-------

ClassNode is a CodeBlockNode which means its value attribute accepts a wide
range of values, see :ref:`CodeBlockNode` for more informations. Most other
attributes work as expected:

.. ipython:: python

    red = RedBaron("class SomeAwesomeName(A, B, C): pass")
    red[0].name = "AnotherAwesomeName"
    red
    red[0].inherit_from = "object"
    red

Helpers
-------

ClassNode comes with one helper to add another item at the end of the value
of the node without having to think about formatting. It is documented here:
:ref:`append_value`.


CommaNode
=========

A node representing a comma, this is the kind of formatting node that you might
have to deal with if not enough high level helpers are available. They are
generally present in call, function arguments definition and data structure
sugar syntaxic notation.

The comma node is reponsible for holding the formatting arround it.

.. ipython:: python

    RedBaron("[1, 2, 3]")[0].value[1].help(with_formatting=True, deep=True)

ComparisonNode
==============

The node represent a comparison operation, for e.g.: :file:`42 > 30`.

.. ipython:: python

    RedBaron("42 > 30")[0].help(with_formatting=True, deep=True)

SetAttr
-------

.. ipython:: python

    red = RedBaron("42 > 30")
    red
    red[0].operator = "=="
    red
    red[0].first = "(1 + 1)"
    red
    red[0].second = "caramba"
    red


DecoratorNode
=============

A node representing an individual decorator (of a function or a class).

.. ipython:: python

    RedBaron("@stuff.plop(*a)\ndef b(): pass")[0].decorators[0].help(deep=True, with_formatting=True)

SetAttr
-------

.. ipython:: python

    red = RedBaron("@stuff\ndef a(): pass")
    red
    red[0].decorators[0].value = "a.b.c"
    red
    red[0].decorators[0].call = "(*args)"
    red
    red[0].decorators[0].call = ""
    red


DelNode
=======

A node representing a :file:`del` statement.

.. ipython:: python

    RedBaron("del stuff")[0].help(deep=True, with_formatting=True)


SetAttr
-------

.. ipython:: python

    red = RedBaron("del stuff")
    red
    red[0].value = "some, other, stuff"
    red


DictArgumentNode
================

A node representing a 'kwargs' definied in a function definition arguments or
used in a :ref:`CallNode`.

.. ipython:: python

    RedBaron("a(**b)")[0].value[1].value[0].help(with_formatting=True, deep=True)

SetAttr
-------

.. ipython:: python

    red = RedBaron("a(**b)")
    red
    red[0].value[1].value[0].value = "plop"
    red


DictNode
========

A node representing python sugar syntaxic notation for dict.

.. ipython:: python

    RedBaron("{'a': 1, 'b': 2, 'c': 3}")[0].help(deep=True, with_formatting=True)

Helpers
-------

DictNode comes with one helper to add another item at the end of the value of
the node without having to think about formatting. It is documented here:
:ref:`append_value`. **Warning**: :file:`append_value` of DictNode has a
different signature than the append_value of other nodes: it expects 2
arguments: one of the key and one of the value.

.. ipython:: python

    red = RedBaron("{}")
    red[0].append_value(key="'a'", value="42")
    red


DotNode
=======

A node representing a dot '.', generally found in atom trailers (this kind of structure: 'variable.another_variable(call)[getitem]').
This is the kind of formatting node that you might have to deal with if not enough high level helpers are available.

The dot node is responsible for holding the formatting around it.

.. ipython:: python

    RedBaron("a.b")[0].value[1].help(with_formatting=True, deep=True)

.. _ElifNode:

ElifNode
========

A node representing an elif statement.

The ElifNode, like the :ref:`IfNode` or the :ref:`ElseNode` are stored in a :ref:`IfelseblockNode`.

.. ipython:: python

    RedBaron("if a: pass\nelif b: pass")[0].value[1].help(with_formatting=True, deep=True)

SetAttr
-------

ElifNode is a CodeBlockNode which means its value attribute accepts a wide range
of values, see :ref:`CodeBlockNode` for more informations. Other attributes
work as expected:

.. ipython:: python

    red = RedBaron("if a: pass\nelif b: pass")
    red
    red[0].value[1].test = "1 + 1 == 11"
    red

Helpers
-------

ElifNode comes with one helper to add another item at the end of the value of the
node without having to think about formating. It is documented here:
:ref:`append_value`.

.. _ElseNode:

ElseNode
========

A node representing an else statement.

The ElseNode, like the :ref:`IfNode` or the :ref:`ElifNode`, is stored in a :ref:`IfelseblockNode`.

.. ipython:: python

    RedBaron("if a: pass\nelse: pass")[0].value[1].help(with_formatting=True, deep=True)

SetAttr
-------

ElifNode is a CodeBlockNode which means its value attribute accepts a wide range
of values, see :ref:`CodeBlockNode` for more informations.

Helpers
-------

ElifNode comes with one helper to add another item at the end of the value of the
node without having to think about formatting. It is documented here:
:ref:`append_value`.


EndlNode
========

A node for the end line ('\n', '\r\n') component.

**This node is responsible for holding the indentation AFTER itself**. This
node also handles formatting around it, CommentNode **before** an EndlNode will
end up in the formatting key of an EndlNode 99% of the time (the exception is
if the CommentNode is the last node of the file).

.. ipython:: python

    RedBaron("suff\n")[1].help(with_formatting=True)
    RedBaron("# first node of the file\n# last node of the file").help(with_formatting=True)

.. _ExceptNode:

ExceptNode
==========

A node representing a except statement (member of a :ref:`TryNode`).

.. ipython:: python

    RedBaron("try: pass\nexcept FooBar: pass\nexcept Exception: pass\nelse: pass\nfinally: pass\n")[0].excepts[0].help(deep=True, with_formatting=True)

SetAttr
-------

ExceptNode is a CodeBlockNode which means its value attribute accepts a wide range
of values, see :ref:`CodeBlockNode` for more informations. Other attributes
work as expected:

.. ipython:: python

    red = RedBaron("try: pass\nexcept: pass")
    red
    red[0].excepts[0].exception = "plop"
    red
    red[0].excepts[0].target = "stuff"
    red
    red[0].excepts[0].exception = ""
    red
    # red[0].excepts[0].target = "stuff" <- would raise without a target

Helpers
-------

ExceptNode comes with one helper to add another item at the end of the value
of the node without having to think about formatting. It is documented here:
:ref:`append_value`.

.. _FinallyNode:

FinallyNode
===========

A node representing a finally statement (member of a :ref:`TryNode`).

.. ipython:: python

    RedBaron("try: pass\nexcept FooBar: pass\nexcept Exception: pass\nelse: pass\nfinally: pass\n").finally_.help(deep=True, with_formatting=True)

SetAttr
-------

FinallyNode is a CodeBlockNode which means its value attribute accepts a wide range
of values, see :ref:`CodeBlockNode` for more informations.

Helpers
-------

FinallyNode comes with one helper to add another item at the end of the value
of the node without having to think about formatting. It is documented here:
:ref:`append_value`.


ForNode
=======

A node representing a for loop.

.. ipython:: python

    RedBaron("for i in b:\n    pass")[0].help(deep=True, with_formatting=True)

SetAttr
-------

ForNode is a CodeBlockNode which means its value attribute accepts a wide range
of values, see :ref:`CodeBlockNode` for more informations. Other attributes
work as expected:

.. ipython:: python

    red = RedBaron("for i in b: pass")
    red
    red[0].iterator = "i, j, k"
    red
    red[0].target = "[x for x in stuff if condition]"
    red

Helpers
-------

ForNode comes with one helper to add another item at the end of the value
of the node without having to think about formatting. It is documented here:
:ref:`append_value`.


FuncdefNode
===========

A node representing a function definition.

.. ipython:: python

    RedBaron("def stuff():\n    pass\n")[0].help(deep=True, with_formatting=True)

SetAttr
-------

FuncdefNode is a CodeBlockNode which means its value attribute accepts a wide
range of values, see :ref:`CodeBlockNode` for more informations. Most other
attributes works as expected:

.. ipython:: python

    red = RedBaron("def stuff():\n    body\n")
    red[0]
    red[0].name = "awesome_function"
    red[0].arguments = "a, b=None, *c, **d"
    red

Decorators might be a bit less intuitive:

.. ipython:: python

    red =  RedBaron("def stuff():\n    body\n")
    red[0].decorators = "@foo(*plop)"
    red
    red[0].decorators = "@foo\n@bar.baz()"
    red
    red[0].decorators = "    @pouet"  # SetAttr will take care of reindenting everything as expected
    red

Helpers
-------

FuncdefNode comes with one helper to add another item at the end of the value
of the node without having to think about formatting. It is documented here:
:ref:`append_value`.

.. _IfNode:

IfNode
======

A node representing an if statement.

The IfNode, like the :ref:`ElifNode` or the :ref:`ElseNode`, is stored in an :ref:`IfelseblockNode`.

.. ipython:: python

    RedBaron("if a: pass")[0].value[0].help(with_formatting=True, deep=True)

SetAttr
-------

IfNode is a CodeBlockNode which means its value attribute accepts a wide range
of values, see :ref:`CodeBlockNode` for more informations. Other attributes
work as expected:

.. ipython:: python

    red = RedBaron("if a: pass")
    red
    red[0].value[0].test = "1 + 1 == 11"
    red

Helpers
-------

IfNode comes with one helper to add another item at the end of the value of the
node without having to think about formatting. It is documented here:
:ref:`append_value`.


ImportNode
==========

A node representing the import statement of the python language.

*Be careful, this node and its subnodes are way more complex than what you can
expect*.

.. ipython:: python

    RedBaron("import foo")[0].help(with_formatting=True, deep=True)
    RedBaron("import foo.bar.baz as stuff, another_thing.plop")[0].help(with_formatting=True, deep=True)

SetAttr
-------

Works as expected:

.. ipython:: python

    red = RedBaron("import foo")
    red[0].value = "foo.bar.baz as plop, stuff, plop.dot"
    red
    red.help(deep=True)

Helpers
-------

To reduce the complexity, 2 helpers method are provided:

.. ipython:: python

    red = RedBaron("import foo.bar.baz as stuff, another_thing.plop")
    red[0].modules()  # modules imported
    red[0].names()  # names added to the context


IntNode
=======

A python integer.

.. ipython:: python

    RedBaron("42")[0].help(with_formatting=True)


ListNode
========

A node representing python sugar syntaxic notation for list.

.. ipython:: python

    RedBaron("[1, 2, 3]")[0].help(deep=True, with_formatting=True)

Helpers
-------

ListNode comes with one helper to add another item at the end of the value of
the node without having to think about formatting. It is documented here:
:ref:`append_value`.


ReprNode
========

A node representing python sugar syntaxic notation for repr.

.. ipython:: python

    RedBaron("`pouet`")[0].help(deep=True, with_formatting=True)

Helpers
-------

SetNode comes with one helper to add another item at the end of the value of
the node without having to think about formatting. It is documented here:
:ref:`append_value`.


SetNode
========

A node representing python sugar syntaxic notation for set.

.. ipython:: python

    RedBaron("{1, 2, 3}")[0].help(deep=True, with_formatting=True)

Helpers
-------

SetNode comes with one helper to add another item at the end of the value of
the node without having to think about formating. It is documented here:
:ref:`append_value`.


SpaceNode
=========

A formatting node representing a space. You'll probably never have to deal with
it except if you play with the way the file is rendered.

**Those nodes will be hidden in formatting keys 99% of the time** (the only exception is if it's the last node of the file).

.. ipython:: python

    RedBaron("1 + 1")[0].first_formatting[0].help(with_formatting=True)
    RedBaron("1 + 1").help(with_formatting=True)


.. _TryNode:

TryNode
=======

A node representing a try statement. This node is responsible for holding the
:ref:`ExceptNode`, :ref:`FinallyNode` and :ref:`ElseNode`.

.. ipython:: python

    RedBaron("try: pass\nexcept FooBar: pass\nexcept Exception: pass\nelse: pass\nfinally: pass\n")[0].help(deep=True, with_formatting=True)

SetAttr
-------

TryNode is a CodeBlockNode which means its value attribute accepts a wide range
of values, see :ref:`CodeBlockNode` for more informations.

**The other attributes (excepts, finally, else) cannot be setted easily for
now**. It is planned to fix this in a near future.

Helpers
-------

TryNode comes with one helper to add another item at the end of the value
of the node without having to think about formatting. It is documented here:
:ref:`append_value`.


TupleNode
=========

A node representing python sugar syntaxic notation for tuple.

.. ipython:: python

    RedBaron("(1, 2, 3)")[0].help(deep=True, with_formatting=True)

Helpers
-------

TupleNode comes with one helper to add another item at the end of the value of
the node without having to think about formatting. It is documented here:
:ref:`append_value`.


WhileNode
=========

A node representing a while loop.

.. ipython:: python

    RedBaron("while condition:\n    pass")[0].help(deep=True, with_formatting=True)

SetAttr
-------

WhileNode is a CodeBlockNode which means its value attribute accepts a wide range
of values, see :ref:`CodeBlockNode` for more informations. Other attributes
work as expected:

.. ipython:: python

    red = RedBaron("while condition: pass")
    red
    red[0].test = "a is not None"
    red

Helpers
-------

WhileNode comes with one helper to add another item at the end of the value
of the node without having to think about formatting. It is documented here:
:ref:`append_value`.

WithContextItemNode
===================

A node representing a while loop.

.. ipython:: python

    RedBaron("with a as b: pass")[0].contexts[0].help(deep=True, with_formatting=True)

SetAttr
-------

.. ipython:: python

    red = RedBaron("with a: pass")
    red
    red[0].contexts[0].value = "plop"
    red
    red[0].contexts[0].as_ = "stuff"
    red
    red[0].contexts[0].as_ = ""
    red

Helpers
-------

WithContextItemNode comes with one helper to add another item at the end of the
value of the node without having to think about formatting. It is documented
here: :ref:`append_value`.


WithNode
========

A node representing a with statement.

.. ipython:: python

    RedBaron("with a as b, c: pass")[0].help(deep=True, with_formatting=True)

SetAttr
-------

WithNode is a CodeBlockNode which means its value attribute accepts a wide range
of values, see :ref:`CodeBlockNode` for more informations. Other attributes
work as expected:

.. ipython:: python

    red = RedBaron("with a: pass")
    red
    red[0].contexts = "b as plop, stuff()"
    red

Helpers
-------

WithNode comes with one helper to add another item at the end of the value
of the node without having to think about formatting. It is documented here:
:ref:`append_value`.
