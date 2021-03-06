FluentDOM.js
============

[![Build Status](https://travis-ci.org/FluentDOM/FluentDOM.js.svg?branch=master)](https://travis-ci.org/FluentDOM/FluentDOM.js)

FluentDOM.js is a small library for DOM and XML handling.

It brings concepts from the original [FluentDOM](http://fluentdom.org) project back to JavaScript.

Basic usage
-----------

```javascript
fd = FluentDOM(true, { ns : 'urn:ns' }, 'text/xml')
```

The function `FluentDOM()` returns a new FluentDOM instance with a document a namespace resolver.

The first argument is the source. If no argument is provided, or it is `false` the global variable
`document` will be used. If it is `true` a new Document instance will be created. Other possibilities depend on the
mimetype. A string will be parsed as xml by default.

The second argument is used to resolve namespace prefixes. It can be an namespace resolver, a function
or an key-value object.

The third argument is the mimetype. The default value is `text/xml`.

FluentDOM()::create
-------------------

This function allows for an simple syntax to create XML nodes. Just store the function into a local variable.
The example uses `_`, but it is your decision.

```javascript
var _ = FluentDOM().create;
document.querySelector('#demo').appendChild(
  _(
    'ul',
    _('li', 'one', {class : 'first'}),
    _('li', 'two'),
    _('li', 'three')
  )
);
```

The function can use the namespace resolver, and has another functions as properties to create special nodes.

```javascript
var fd = FluentDOM(true, {'atom' : 'http://www.w3.org/2005/Atom'});
var _ = fd.create;
document.querySelector('#demo').textContent = fd.xml(
  _(
    'atom:feed',
    _('atom:title', 'Example Feed'),
    _('atom:link', {'href' : 'http://example.org/'}),
    _('atom:updated', '2003-12-13T18:30:02Z'),
      _(
        'atom:author',
        _('atom:name', 'John Doe')
      ),
      _('atom:id', 'urn:uuid:60a76c80-d399-11d9-b93C-0003939e0af6'),
      _(
        'atom:entry',
        _('atom:title', 'Atom-Powered Robots Run Amok'),
        _('atom:link', {'href' : 'http://example.org/2003/12/13/atom03'}),
    _('atom:id', 'urn:uuid:1225c695-cfb8-4ebb-aaaa-80da344efa6a'),
      _('atom:updated', '2003-12-13T18:30:02Z'),
      _('atom:summary', 'Some text.')
    )
  )
);
```

FluentDOM()::evaluate
---------------------

Allows to fetch nodes and scalar values from an document. If the result is a scalar value the
value is returned. Otherwise it is a NodeList object that provides toArray() and each().

```javascript
var read = FluentDOM(xml, {'atom' : 'http://www.w3.org/2005/Atom'}).evaluate;
var write = FluentDOM().create;

document.querySelector('#demo').appendChild(
  write('h1', read('string(/atom:feed/atom:title)'))
);
document.querySelector('#demo').appendChild(
  write.each(
    read('/atom:feed/atom:entry').toArray(),
    function(entry) {
      return write(
        'details',
        write(
          'summary',
          write(
            'h2',
            write(
              'a',
              {
                href : read('string(.//atom:link[@rel = "alternate" and @type = "text/html"]/@href)')
              },
              read('string(atom:title)', entry)
            )
          )
        ),
        write(
          'div',
          read('string(atom:summary)', entry)
        )
      );
    }
  )
);
```

Node.js
-------

FluentDOM.js supports Node.js. It uses the [xmldom](https://github.com/bigeasy/xmldom) and 
[xpath](https://github.com/goto100/xpath) packages. 
 
Internet Explorer
-----------------

Internet Explorer has no native support for `Document:evaluate()`. Like for node.js 
the [xpath](https://github.com/goto100/xpath) library is used. `xpath.min.js` is a minified version of it.

