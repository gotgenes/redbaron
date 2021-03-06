# Todo

### Important

- __setattr__ should work as expected when possible (will be a long list of stuff to do for that)
  for eg: RedBaron("{a: 1}")[0].value[0] = "b: c" fails now
- automatic reindentation when attribute a NodeList to an attribute (like adding a new body to a function)
- when dealing with a block, ensure you don't break the indentation of the
  block after you (like when playing with a method, be carreful with the next
  method)
- when set a function/other body block, ensure that it starts with a "\n" + correct indentation
- .at() return the first item starting at line X
- .rename() (name -> value, def/class -> name)

- there is a global problem of some node having some attributes that are keyword in python, eg: trynode.finally, WithContextItemNode.as How to handle that in general? For now I use allow to use $KEYWORD + "\_" but that breaks that completion and probably other things

- to_node -> Node.from_fst, same for NodeList.from_fst
- generate default constructors for nodes using nodes_rendering_order
- if possible try to keep a coherent signature with possibles attributes, set correctly default attributes
- examples:
    * SpaceNode(" ")
    * CommaNode()
    * CommaNode(first_formatting=[" "]) # allow to pass strings again

### Find/Find\_All (comparison)

Magic stuff like:
value\_\_dumps that compare the .dumps() with a string? or use lambda for that instead?

### Generic append\_stuff that take care of the delimiter:

Need: DotList, CommaList, EndlList (.append\_item, .extend, +=)

More general things to do:
- append\_comma doesn't handle the correct delimiter, only takes a comma,
  doesn't takes if their is a space before the comma
- in reality, needs a extend, not an append
- need a specific behavior for Atomtrailer that combine comma/dot/nothing

CommaList need to be done:
* import
* from import
* print
* global
* exec
* with
* subscript list ?
* for a, b, c in ... (comprehension aussi)

### ProxyList

A more sensible approach to those delimiters stuff would be to have ProxyList
that handle separation in the back so you don't have to care about it. This
would be the wrapper arround something like what .filtered is returning right
now. It should implement every expected method of a list (.sort, .extend, .remove, .append etc...).

### More general stuff

* .assign, .map\_concat
* __setitem__ on NodeList
* .swap, .move on a NodeList
* .cut (like .copy but with del self)
* .remove on nodelist
* method to change style of formatting in a datastructure
* .append\_value should take a \*args
* .grep method that test on successive .dumps()

### More refactoring like

- find\_name\_binding -> search assign, def (args && name), class, except, with, for ...
- find\_identifier -> search name + everything up there ^

### Wrappers

It should be easy to wrap statement, expressions or various structure in other
statements, like a statement with a block or an associative parenthesis or
those kind of things.

### Misc

* delegate .filtered on the value if the value is a NodeList? Can quickly become magic behavior

- node.to_python return an instance in python of the node (in a "safe" eval), for eg RedBaron("42")[0].to_python() -> 42 as an int instance
- to_python car accept an argument context which is a context dict to pass to it
