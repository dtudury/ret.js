# Regular Expression Tokenizer [![Build Status](https://secure.travis-ci.org/fent/ret.js.png)](http://travis-ci.org/fent/ret.js)

Tokenizes strings that represent a regular expressions.


# Usage
```js
var ret = require('ret');

var tokens = ret(/foo|bar/.source);
```

`tokens` will contain the following object

```js
{
  "type": ret.types.ROOT
  "options": [
    [ { "type": ret.types.CHAR, "value", 102 }
    , { "type": ret.types.CHAR, "value", 111 }
    , { "type": ret.types.CHAR, "value", 111 } ],
    [ { "type": ret.types.CHAR, "value",  98 }
    , { "type": ret.types.CHAR, "value",  97 }
    , { "type": ret.types.CHAR, "value", 114 } ]
  ]
}
```

# Token Types
`ret.types` is a collection of the various token types exported by ret.

### ROOT

Only used in the root of the regexp. This is needed due to the posibility of the root containing a pipe `|` character. In that case, the token will have an `options` key that will be an array of arrays of tokens. If not, it will contain a `stack` key that is an array of tokens.
[optional]

```js
{
    "type": ret.types.ROOT
  , "stack": [token]
}
```

### GROUP

Groups contain tokens that are inside of a parenthesis. If the group begins with `?` followed by another character, it's a special type of group. A ':' tells the group not to be remembered when `exec` is used. '=' means the previous token matches only if followed by this group, and '!' means the previous token matches only if NOT followed.

Like root, it can contain an `options` key instead of `stack` if there is a pipe.

```js
{
    "type": ret.types.GROUP
  , "remember" true
  , "followedBy": false
  , "notFollowedBy": false
  , "options" [[token]]
}
```

### POSITION

`\b`, `\B`, `^`, and `$` specify positions in the regexp.

```js
{
    "type": ret.types.POSITION
  , "value": "^"
}
```

### SET

Contains a key `set` specifying what tokens are allowed and a key `not` specifying if the set should be negated.

```js
{
    "type": ret.types.SET
  , "set": [token]
  , "not": false
}
```

### RANGE

Used in set tokens to specify a character range. `from` and `to` are character codes.

```js
{
    "type": ret.types.RANGE
  , "from": 97
  , "to": 122
}
```

### REPETITION

```js
{
    "type": ret.types.REPETITION
  , "min": 0
  , "max": Infinity
}
```

### REFERENCE

References a group token. `value` is 1-9.

```js
{
    "type": ret.types.REFERENCE
  , "value": 1
}
```

### CHAR

Represents a single character token. `value` is the character code. This might seem a bit cluttering instead of concatenating characters together. But since repetition tokens only repeat the last token and not the last clause like the pipe, it's simpler to do it this way.

```js
{
    "type": ret.types.CHAR
  , "value": 123
}
```

## Errors
ret.js will throw errors if given a string with an invalid regular expression. All possible errors are

* Invalid group. When a group with an immediate `?` character is followed by an invalid character. It can only be followed by `!`, `=`, or `:`. Example: `/(?$abc)/`
* Nothing to repeat. Thrown when a repetitional token is used as the first token in the current clause, as in right in the beginning of the regexp or right after a pipe. Example: `/foo|?bar/`
* Unmatched ). A group was not closed. Example: `/(1(23)4/`
* Missing ]. A custom character set was not closed. Example: `/[abc/`


# Install

    npm install ret


# Tests
Tests are written with [vows](http://vowsjs.org/)

```bash
npm test
```

# License
MIT
