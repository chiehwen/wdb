wdb
===

![](https://raw.github.com/Kozea/wdb/master/wdb.png)

Description
-----------

**wdb** is as for now a proof of concept of an independant web debugger for wsgi project (implemented as a wsgi middleware).

Unlike other web debuggers, **wdb** is based on the [python bdb debugger framework](http://docs.python.org/2/library/bdb.html). (The one used by [pdb](http://docs.python.org/2/library/pdb.html))

This allows step by step debugging during the rendering of the page as well as exception inspection in the very state the exception occured.


How is it possible ?
--------------------

*WebSockets*! 
All debug information between web page and web server transits throught a websocket opened on a random port.
Server request processing is blocked by the websocket and then resumed when the debugging is over.


Warning
-------

This is still far from working perfectly, it has a lot of known issues and can easily break your application but the in page debugging does work. You probably will have to kill everything and start again once something went wrong.

*Random disclaimer warning*

This python thing will probably eat your cat.


Instalation:
------------

```
    $ pip install wdb
```

Usage
-----

To try it you can simply run the `run.py` script which is a flask application which will be accessible at <http://localhost:1984/>,  <http://localhost:1984/wtf> for step by step testing.

To try it on another wsgi application, use the `Wdb` middleware:

```python
    from wdb import Wdb
    wsgi_app = Whathever_wsgi_server_lib()
    my_app = Wdb(wsgi_app)
    my_app.serve_forever()
```

### Using flask:

```python
    from wdb import Wdb
    app = Flask(__name__)
    app.wsgi_app = Wdb(app.wsgi_app)
    app.run()
```

### Using django:

In your `wsgi.py`, add the middleware:

After:

```python
    # This application object is used by any WSGI server configured to use this
    # file. This includes Django's development server, if the WSGI_APPLICATION
    # setting points here.
    from django.core.wsgi import get_wsgi_application
    application = get_wsgi_application()
```

Add this:

```python
    from wdb import Wdb
    application = Wdb(application)
```

In your `settings.py`, activate exception propagation:

```python
    DEBUG = True
    DEBUG_PROPAGATE_EXCEPTIONS = True
```

### Breaking

You can now put some breakpoint in a request code:

```python
    do_something()
    import wdb; wdb.set_trace()
    return
```

Once you are in a breakpoint or in an exception, you can eval all you want in the prompt under the code.
Multi-lines are partially supported using `[Shift] + [Enter]`.

As of now the following special commands are supported during breakpoint:

   * `.s or [Ctrl] + [↓] or [F11]  `: Step into
   * `.n or [Ctrl] + [→] or [F10]  `: Step over (Next)
   * `.r or [Ctrl] + [↑] or [F9]   `: Step out (Return)
   * `.c or [Ctrl] + [←] or [F8]   `: Continue
   * `.u or [F7]                   `: Until (Next over loops)
   * `.b [file:]lineno[, condition]`: Break on file at lineno (file is the current file by default)
   * `.t [file:]lineno[, condition]`: Same as b but break only once
   * `.j lineno                    `: Jump to lineno (Must be at bottom frame and in the same function)
   * `.q                           `: Quit

NB: Hotkeys with arrows are purposedly not triggered in the eval prompt to avoid conflicts when typing.

Author
------

[Florian Mounier](http://github.com/paradoxxxzero) @ [Kozea](http://kozea.fr/)


Licence
-------

This library is licensed under GPLv3

wdb - An improbable web debugger through WebSockets


    wdb Copyright (C) 2012  Florian Mounier, Kozea

    This program is free software: you can redistribute it and/or modify
    it under the terms of the GNU General Public License as published by
    the Free Software Foundation, either version 3 of the License, or
    (at your option) any later version.

    This program is distributed in the hope that it will be useful,
    but WITHOUT ANY WARRANTY; without even the implied warranty of
    MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
    GNU General Public License for more details.

    You should have received a copy of the GNU General Public License
    along with this program.  If not, see <http://www.gnu.org/licenses/>.
