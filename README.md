# rlite

Tiny, light-weight JavaScript routing with zero dependencies.

## Usage
Rlite does not come with any explicit tie into HTML5 push state or hash-change events, but these are easy enough to tie in based on your needs. Here's an example:

    var r = Rlite();

    // Default route
    r.add('', function () {
        document.title = 'Home';
    });

    // #inbox
    r.add('inbox', function () {
        document.title = 'In';
    });

    // #sent?to=john -> r.params.to will equal 'john'
    r.add('sent', function (r) {
        document.title = 'Out ' + r.params.to;
    });

    // #users/chris -> r.params.name will equal 'chris'
    r.add('users/:name', function (r) {
        document.title = 'User ' + r.params.name;
    });

    // #logout
    r.add('logout', function () {
        document.title = 'Logout';
    });

    // Hash-based routing
    function processHash() {
        var hash = location.hash || '#';
        r.run(hash.substr(1));
    }

    window.addEventListener('hashchange', processHash);
    processHash();

The previous examples should be relatively self-explantatory. Simple, parameterized routes are supported. Only relative URLs are supported. (So, instead of passing: 'http://example.com/users/1', pass '/users/1').

One other non-obvious thing is this: if there is a query parameter with the same name as a route parameter, it will override the route parameter. So given the following route definition:

    /users/:name

If you pass the following URL:

    /users/chris?name=joe

The value of params.name will be 'joe', not 'chris'.

### Browserify
This library is [CommonJS](http://www.commonjs.org/) compatible, so you can use it in this way:

```javascript
var Rlite = require('rlite-router'),
routes = new Rlite();

routes.add('', function () {
...
```

## Handling 404s

Rlite's run method returns true if the route matched and false if it didn't.

So, you could do a 404 like this:

    if (!r.run(hash.substr(1))) {
      show404Page();
    }

## Multiple Handlers

By default, Rlite doesn't allow more than one handler per route. So, if
you were to do this:

    r.add('hey/:name', function (r) {
        // This will never run because the
        // next registration overwrites this one
    });

    r.add('hey/:name', function(r) {
        // This will run. Last one in wins!
    });

If you want to have multiple handlers for the same route, you can
include plugins/rlite-handlers.min.js, so your scripts might look like:

    <script src="rlite.min.js"></script>
    <script src="plugins/rlite-handlers.min.js"></scripts>

You'd use it like this:

    var r = Rlite();

    function setTitle(r) {
      document.title = r.params.name;
    }

    function logName(r) {
      console.log(r.params.name);
    }

    r.add('example/:name', Rlite.handlers(setTitle, logName));

## Installation

Just download rlite.min.js, or use bower:

    bower install rlite

Or use npm:
https://www.npmjs.com/package/rlite-router

    npm install --save rlite-router

## Minified using

    uglifyjs rlite.js --source-map rlite.min.js.map -m -c -o rlite.min.js

## License MIT

Copyright (c) 2015 Chris Davies

Permission is hereby granted, free of charge, to any person
obtaining a copy of this software and associated documentation
files (the "Software"), to deal in the Software without
restriction, including without limitation the rights to use,
copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the
Software is furnished to do so, subject to the following
conditions:

The above copyright notice and this permission notice shall be
included in all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND,
EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES
OF MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND
NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT
HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY,
WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING
FROM, OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR
OTHER DEALINGS IN THE SOFTWARE.
