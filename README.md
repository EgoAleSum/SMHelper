# SMHelper

Collection of JavaScript utilities

This code is licensed under the terms of the BSD (2-clause) license (see LICENSE.md).

## Add to your project

Install from NPM:

````sh
npm install --save smhelper
````

## API Guide

Include the module with:

````js
const SMHelper = require('smhelper')
````

The module exports the following methods:

### SMHelper.buildQuerystring

````js
let obj = {
    search: 'hello world',
    page: 2,
    marked: ['b', 'z']
}
let qs = SMHelper.buildQuerystring(obj)
// Result: search=hello%20world&page=2&marked=b%2Cz
````

This methods takes a dictionary (a set of key-value pairs) and returns a string that can be used as query string when building a URL. This method can be used together with `SMHelper.buildUrl`.

Characters are escaped using the proper HTTP sequence when necessary (spaces are replaced with `%20`). This method is Unicode-safe, and supports arrays .

### SMHelper.buildUrl

````js
let base = 'https://example.com/'
// You can also pass this as a string: 'api/v2/list'
let parts = ['api', 'v2', 'list']
let args = {
    search: 'hello world',
    page: 2
}
let url = SMHelper.buildUrl(base, parts, args)
// Result: 
// https://example.com/api/v2/list?search=hello%20world&page=2
````

This method builds a full URL starting from components above:

- `base` is the protocol and hostname, including `http(s)://`
- `parts` is the path, which can either be a string or an array
- `args` is the dictionary for the query string (passed to `SMHelper.buildQuerystring`)

Returns a full URL, escaping values when necessary.

### SMHelper.cloneObject

````js
let obj = {
    a: [1, 2, 3],
    b: {
        x: 1,
        y: 'hello world'
    }
}
let clone = SMHelper.cloneObject(obj)
````

This method clones deeply any JavaScript object (scalar or non-scalar) that can be encoded as JSON. Since the object is cloned deeply, there is no reference to values in the original object.

### SMHelper.compactObject

````js
let obj = { a: 'b', b: false, c: null }
let onlyNull, result

onlyNull = false
result = SMHelper.compactObject(obj, onlyNull)
// Result: {a: 'b'}

onlyNull = true
result = SMHelper.compactObject(obj, onlyNull)
// Result: {a: 'b', b: false}
````

Removes from a dictionary (deeply) all values that are false-y. When `onlyNull` is true (default: false), only values that are exactly `null` are removed.

### SMHelper.getDescendantProperty

````js
let obj = {
    a: 1,
    b: {
        x1: 10,
        x2: {
            y1: 'Hello world',
            y2: [100, 200, 300]
        }
    }
}
let desc, result

desc = 'b.x2.y1'
result = SMHelper.getDescendantProperty(obj, desc)
// Result: Hello world

desc = 'b.x2.y2.2'
result = SMHelper.getDescendantProperty(obj, desc)
// Result: 300
````

Returns a nested property from a dictionary or array, referenced by a string in "dot notation" (for example: `key1.key2.0`).

### SMHelper.isNumeric

````js
let obj, result

obj = 100
result = SMHelper.isNumeric(obj)
// true

obj = "-100.4e2"
result = SMHelper.isNumeric(obj)
// true

obj = "hello world"
result = SMHelper.isNumeric(obj)
// false
````

Returns true if the passed value (string, number or any other object) is numeric: that is, if it can be safely converted to a float with `parseFloat`.

### SMHelper.isPlainObject

````js
let obj, result

obj = {a: 100}
result = SMHelper.isPlainObject(obj)
// true

obj = new Date()
result = SMHelper.isPlainObject(obj)
// false

obj = {a: new Date()}
result = SMHelper.isPlainObject(obj)
// true
````

Returns true if the passed parameter is a plain JavaScript "Object". This method checks only the passed object, and not its properties.

### SMHelper.isScalar

````js
let obj, result

obj = 100
result = SMHelper.isScalar(obj)
// true

obj = "hello world"
result = SMHelper.isScalar(obj)
// true

obj = {a: 100}
result = SMHelper.isScalar(obj)
// false

obj = new String('Hello world')
result = SMHelper.isScalar(obj)
// false
````

Returns true if the passed value is of a scalar type: string, number or boolean.

### SMHelper.objectToDotNotation

````js
let obj = {
    status: "success",
    auth: {
        code: 23123213,
        name: "John Black",
        keys: [10, 11]
    }
}
let preserveArrays, result

preserveArrays = false
result = SMHelper.objectToDotNotation(obj, preserveArrays)
/*
{
    "status": "success",
    "auth.code": 23123213,
    "auth.name": "John Black",
    "auth.keys.0": 10,
    "auth.keys.1": 11
}
*/

preserveArrays = true
result = SMHelper.objectToDotNotation(obj, preserveArrays)
/*
{
    "status": "success",
    "auth.code": 23123213,
    "auth.name": "John Black",
    "auth.keys": [10, 11]
}
*/
````

Flatten a dictionary to the "dot notation", as used by MongoDB (see example code). If `preserveArrays` is true (default: false), arrays are not transformed to the "dot notation".

### SMHelper.pregQuote

````js
let str = "*RRRING* Hello?"
let result = SMHelper.pregQuote(str)
// Result: \\*RRRING\\* Hello\\?
````

Takes a string and puts a backslash in front of every character that is part of the regular expression syntax. The resulting string can then be safely used as argument for `new RegExp()`.

This is a port of the PHP [preg_quote()](http://php.net/preg_quote) function, and accepts the same arguments.

### SMHelper.strIs

````js
let pattern = 'foo*'
let value = 'foobar'
let result = SMHelper.strIs(pattern, value)
// true
````

Determines if a given string matches a pattern, allowing * as wildcard.

### SMHelper.stringToCamel

````js
let str, result

str = 'hello-world-a-1'
result = SMHelper.stringToCamel(str)
// Result: helloWorldA1

str = 'hello_world_a_1'
result = SMHelper.stringToCamel(str)
// Result: helloWorldA1
````

Converts strings with dashes or underscores (eg. 'foo-bar' or 'foo_bar') to camelCase ('fooBar').

### SMHelper.stripTags

````js
let input = '<p>Hello <img src="image.gif" onmouseover="some()"><i>World</i></p>'
let allowed = '<i>'
let result = SMHelper.stripTags(input, allowed)
// Result: Hello <i>World</i>
````

Strips all non-allowed HTML tags from a string. By default, no HTML tag is allowed.

This is a port of the PHP [strip_tags()](http://php.net/strip_tags) function, and accepts the same arguments.

### SMHelper.toStringSafe

````js
let val, result

val = 0
result = SMHelper.toStringSafe(val)
// Result: "0" (as string)

val = false
result = SMHelper.toStringSafe(val)
// Result: "false" (as string)
````

Converts any value to string, ensuring that the number 0 and the boolean false are treated correctly.

### SMHelper.updatePropertyInObject

````js
let obj = {
    hello: 'aa',
    test: {
        a: 'b',
        c: 0,
        d: {
            x: 1
        }
    }
}
let property = 'test.d.x'
let value = [0, 1, 2]
SMHelper.updatePropertyInObject(obj, property, value)
console.log(obj.test.d.x)
// Output: [ 0, 1, 2 ]
````

Updates a property (represented in the "dot notation") in an object, modifying the original object. If the property doesn't exist in the object, it's created.
