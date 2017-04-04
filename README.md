# api documentation for  [cookie (v0.3.1)](https://github.com/jshttp/cookie)  [![npm package](https://img.shields.io/npm/v/npmdoc-cookie.svg?style=flat-square)](https://www.npmjs.org/package/npmdoc-cookie) [![travis-ci.org build-status](https://api.travis-ci.org/npmdoc/node-npmdoc-cookie.svg)](https://travis-ci.org/npmdoc/node-npmdoc-cookie)
#### HTTP server cookie parsing and serialization

[![NPM](https://nodei.co/npm/cookie.png?downloads=true)](https://www.npmjs.com/package/cookie)

[![apidoc](https://npmdoc.github.io/node-npmdoc-cookie/build/screenCapture.buildNpmdoc.browser._2Fhome_2Ftravis_2Fbuild_2Fnpmdoc_2Fnode-npmdoc-cookie_2Ftmp_2Fbuild_2Fapidoc.html.png)](https://npmdoc.github.io/node-npmdoc-cookie/build/apidoc.html)

![npmPackageListing](https://npmdoc.github.io/node-npmdoc-cookie/build/screenCapture.npmPackageListing.svg)

![npmPackageDependencyTree](https://npmdoc.github.io/node-npmdoc-cookie/build/screenCapture.npmPackageDependencyTree.svg)



# package.json

```json

{
    "author": {
        "name": "Roman Shtylman",
        "email": "shtylman@gmail.com"
    },
    "bugs": {
        "url": "https://github.com/jshttp/cookie/issues"
    },
    "contributors": [
        {
            "name": "Douglas Christopher Wilson",
            "email": "doug@somethingdoug.com"
        }
    ],
    "dependencies": {},
    "description": "HTTP server cookie parsing and serialization",
    "devDependencies": {
        "istanbul": "0.4.3",
        "mocha": "1.21.5"
    },
    "directories": {},
    "dist": {
        "shasum": "e7e0a1f9ef43b4c8ba925c5c5a96e806d16873bb",
        "tarball": "https://registry.npmjs.org/cookie/-/cookie-0.3.1.tgz"
    },
    "engines": {
        "node": ">= 0.6"
    },
    "files": [
        "HISTORY.md",
        "LICENSE",
        "README.md",
        "index.js"
    ],
    "gitHead": "e3c77d497d66c8b8d4b677b8954c1b192a09f0b3",
    "homepage": "https://github.com/jshttp/cookie",
    "keywords": [
        "cookie",
        "cookies"
    ],
    "license": "MIT",
    "maintainers": [
        {
            "name": "dougwilson",
            "email": "doug@somethingdoug.com"
        }
    ],
    "name": "cookie",
    "optionalDependencies": {},
    "readme": "ERROR: No README data found!",
    "repository": {
        "type": "git",
        "url": "git+https://github.com/jshttp/cookie.git"
    },
    "scripts": {
        "test": "mocha --reporter spec --bail --check-leaks test/",
        "test-ci": "istanbul cover node_modules/mocha/bin/_mocha --report lcovonly -- --reporter spec --check-leaks test/",
        "test-cov": "istanbul cover node_modules/mocha/bin/_mocha -- --reporter dot --check-leaks test/"
    },
    "version": "0.3.1"
}
```



# <a name="apidoc.tableOfContents"></a>[table of contents](#apidoc.tableOfContents)

#### [module cookie](#apidoc.module.cookie)
1.  [function <span class="apidocSignatureSpan">cookie.</span>parse (str, options)](#apidoc.element.cookie.parse)
1.  [function <span class="apidocSignatureSpan">cookie.</span>serialize (name, val, options)](#apidoc.element.cookie.serialize)



# <a name="apidoc.module.cookie"></a>[module cookie](#apidoc.module.cookie)

#### <a name="apidoc.element.cookie.parse"></a>[function <span class="apidocSignatureSpan">cookie.</span>parse (str, options)](#apidoc.element.cookie.parse)
- description and source-code
```javascript
function parse(str, options) {
  if (typeof str !== 'string') {
    throw new TypeError('argument str must be a string');
  }

  var obj = {}
  var opt = options || {};
  var pairs = str.split(pairSplitRegExp);
  var dec = opt.decode || decode;

  for (var i = 0; i < pairs.length; i++) {
    var pair = pairs[i];
    var eq_idx = pair.indexOf('=');

    // skip things that don't look like key=value
    if (eq_idx < 0) {
      continue;
    }

    var key = pair.substr(0, eq_idx).trim()
    var val = pair.substr(++eq_idx, pair.length).trim();

    // quoted values
    if ('"' == val[0]) {
      val = val.slice(1, -1);
    }

    // only assign once
    if (undefined == obj[key]) {
      obj[key] = tryDecode(val, dec);
    }
  }

  return obj;
}
```
- example usage
```shell
...

## API

'''js
var cookie = require('cookie');
'''

### cookie.parse(str, options)

Parse an HTTP 'Cookie' header string and returning an object of all cookie name-value pairs.
The 'str' argument is the string representing a 'Cookie' header value and 'options' is an
optional object containing additional parsing options.

'''js
var cookies = cookie.parse('foo=bar; equation=E%3Dmc%5E2');
...
```

#### <a name="apidoc.element.cookie.serialize"></a>[function <span class="apidocSignatureSpan">cookie.</span>serialize (name, val, options)](#apidoc.element.cookie.serialize)
- description and source-code
```javascript
function serialize(name, val, options) {
  var opt = options || {};
  var enc = opt.encode || encode;

  if (typeof enc !== 'function') {
    throw new TypeError('option encode is invalid');
  }

  if (!fieldContentRegExp.test(name)) {
    throw new TypeError('argument name is invalid');
  }

  var value = enc(val);

  if (value && !fieldContentRegExp.test(value)) {
    throw new TypeError('argument val is invalid');
  }

  var str = name + '=' + value;

  if (null != opt.maxAge) {
    var maxAge = opt.maxAge - 0;
    if (isNaN(maxAge)) throw new Error('maxAge should be a Number');
    str += '; Max-Age=' + Math.floor(maxAge);
  }

  if (opt.domain) {
    if (!fieldContentRegExp.test(opt.domain)) {
      throw new TypeError('option domain is invalid');
    }

    str += '; Domain=' + opt.domain;
  }

  if (opt.path) {
    if (!fieldContentRegExp.test(opt.path)) {
      throw new TypeError('option path is invalid');
    }

    str += '; Path=' + opt.path;
  }

  if (opt.expires) {
    if (typeof opt.expires.toUTCString !== 'function') {
      throw new TypeError('option expires is invalid');
    }

    str += '; Expires=' + opt.expires.toUTCString();
  }

  if (opt.httpOnly) {
    str += '; HttpOnly';
  }

  if (opt.secure) {
    str += '; Secure';
  }

  if (opt.sameSite) {
    var sameSite = typeof opt.sameSite === 'string'
      ? opt.sameSite.toLowerCase() : opt.sameSite;

    switch (sameSite) {
      case true:
        str += '; SameSite=Strict';
        break;
      case 'lax':
        str += '; SameSite=Lax';
        break;
      case 'strict':
        str += '; SameSite=Strict';
        break;
      default:
        throw new TypeError('option sameSite is invalid');
    }
  }

  return str;
}
```
- example usage
```shell
...

The default function is the global 'decodeURIComponent', which will decode any URL-encoded
sequences into their byte representations.

**note** if an error is thrown from this function, the original, non-decoded cookie value will
be returned as the cookie's value.

### cookie.serialize(name, value, options)

Serialize a cookie name-value pair into a 'Set-Cookie' header string. The 'name' argument is the
name for the cookie, the 'value' argument is the value to set the cookie to, and the 'options'
argument is an optional object containing additional serialization options.

'''js
var setCookie = cookie.serialize('foo', 'bar');
...
```



# misc
- this document was created with [utility2](https://github.com/kaizhu256/node-utility2)
