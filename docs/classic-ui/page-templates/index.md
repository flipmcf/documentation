# Page Templates in Plone Classic UI

These instructions and resources will help you develop Classic-UI pages.

When you visit the Plone backend with a web browser, you will reach the Classic-UI.  These pages are generated with Classic Plone Browser Views and Page Templates.

This chapter covers developing Page Templates.   Browser views are documented in-depth in (another chapter)
# Contents

* Page Templates
   * Introduction
   * Overriding existing templates
   * Creating your own templates
* Language Reference
   * Template Expression Language (TAL)
   * Expressions (TALES)
       * Python expressions
       * Path Expressions
   * Macros (METAL)
   * ${..} Operator
   * 

* Chameleon
    
    * blah
    * blah
    * blah
* Skin Layers
* [DTML](dtml.md)


## Page Templates
### Introduction
Plone uses Zope Page Templates.  These templates use well established and time-tested standards:   The *Template Attribute Language* (TAL),  *TAL Expression Syntax* (TALES), *Macro Expansion TAL* (METAL).

From a python programmer's point of view, Page Templates are a tool to display a python object on the web, instead of simply what `__repr__()` shows. These files always have a `.pt` file extension

A normal full Plone HTML page consists of:
* the *master template* defining the overall layout of the page
* *slots* defined by the master template, and filled by the object being published.
* *viewlets* and *viewlet managers*
* *tiles*

Page templates are most frequently describing an HTML document, but they can also render XML (useful for RSS feeds or XML-based API's).

Templates can actually render any mime type you find useful: markdown `.md` files, or latex, or `text/plain`

You could, if you wanted, create a Page Template that rendered `application/json` but there are better ways to do that directly from a browser view.  

The point here is that you are not limited to templates creating only HTML.


### Overriding Existing Templates

Your first journey in page templates may be overriding a Plone Core template or a template from an add-on.  The recommended approach is to use [z3c.jbot](https://pypi.org/project/z3c.jbot/) and to put your customized templates onto the filesystem and version controlled.

This document is meant to simply describe the development of templates.

* `Create your own add-on`_
  which you can use to contain your page templates on the file system.

* Use the `z3c.jbot`_ Plone helper add-on to override existing page
  templates.
  This is provided in the `sane_plone_addon_template`_ add-in, no separate
  set-up needed.

* `z3c.jbot`_ can override page templates (``.pt`` files) for views,
  viewlets, old style page templates and portlets.
  In fact it can override any ``.pt`` file in the Plone source tree.


### Creating your own Templates

!!! admonition 'note'

    Although templates can be 'stand alone' templates that render a Plone object directly, this is not best practice. A combination of a View and Page Template is the correct implementation.



### Language Reference



## Chameleon
Chameleon is an HTML/XML template engine for Python.

It’s designed to generate the document output of a web application, typically HTML markup or XML.

The complete documentation for Chameleon can be found at [https://chameleon.readthedocs.io/](https://chameleon.readthedocs.io/) 

The language used is *page templates*, originally a Zope invention[^1]

The template engine compiles templates into Python byte-code and is optimized for speed. For a complex template language, the performance is very good.

The *page templates* language is used within your document structure as special element attributes and text markup. Using a set of simple language constructs, you control the document flow, element repetition, text replacement and translation.

The basic language (known as the *template attribute language* or TAL)
is simple enough to grasp from an example:

```xml+genshi
<html>
  <body>
    <h1>Hello, ${'world'}!</h1>
    <table>
      <tr tal:repeat="row 'apple', 'banana', 'pineapple'">
        <td tal:repeat="col 'juice', 'muffin', 'pie'">
           ${row.capitalize()} ${col}
        </td>
      </tr>
    </table>
  </body>
</html>
```

The ``${...}`` notation is short-hand for text insertion [3]_. The
Python-expression inside the braces is evaluated and the result
included in the output. By default, the string is escaped before
insertion. To avoid this, use the ``structure:`` prefix:

```xml+genshi
  <div>${structure: ...}</div>
```

Note that if the expression result is an object that implements an
``__html__()`` method [4]_, this method will be called and the result
treated as "structure". An example of such an object is the
``Markup`` class that's included as a utility

```python
  from chameleon.utils import Markup
  username = Markup("<tt>%s</tt>" % username)
```

The macro language (known as the *macro expansion language* or METAL)
provides a means of filling in portions of a generic template.

On the left, the macro template; on the right, a template that loads
and uses the macro, filling in the "content" slot:

```xml+genshi

  <html xmlns="http://www.w3.org/1999/xhtml">             <metal:main use-macro="load: main.pt">
    <head>                                                   <p metal:fill-slot="content">${structure: document.body}<p/>
      <title>Example &mdash; ${document.title}</title>    </metal:main>
    </head>
    <body>
      <h1>${document.title}</h1>

      <div id="content">
        <metal:content define-slot="content" />
      </div>
    </body>
  </html>
```

In the example, the expression type [load](load reference) is
used to retrieve a template from the file system using a path relative
to the calling template.

The METAL system works with TAL such that you can for instance fill in
a slot that appears in a ``tal:repeat`` loop, or refer to variables
defined using ``tal:define``.

The third language subset is the translation system (known as the
*internationalization language* or I18N):

```xml+genshi

  <html i18n:domain="example">

    ...

    <div i18n:translate="">
       You have <span i18n:name="amount">${round(amount, 2)}</span> dollars in your account.
    </div>

    ...

  </html>
```

Each translation message is marked up using ``i18n:translate`` and
values can be mapped using ``i18n:name``. Attributes are marked for
translation using ``i18n:attributes``. The template engine generates
`gettext <http://www.gnu.org/s/gettext/>`_ translation strings from
the markup::

  "You have ${amount} dollars in your account."

If you use a web framework such as `Pyramid <https://trypyramid.com/>`_, the
translation system is set up automatically and will negotiate on a *target
language* based on the HTTP request or other parameter. If not, then
you need to configure this manually.


[^1]: The template language specifications and API for the Page Templates engine are based on Zope Page Templates (see in particular zope.pagetemplate). However, the Chameleon compiler and Page Templates engine is an entirely new codebase, packaged as a standalone distribution. It does not require a Zope software environment.