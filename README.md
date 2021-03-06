# OriginDB [![Build Status](https://travis-ci.org/CreaturePhil/origindb.svg?branch=master)](https://travis-ci.org/CreaturePhil/origindb) [![Dependency Status](https://david-dm.org/creaturephil/origindb.svg)](https://david-dm.org/creaturephil/origindb) [![devDependency Status](https://david-dm.org/creaturephil/origindb/dev-status.svg)](https://david-dm.org/creaturephil/origindb#info=devDependencies)

Blazing fast and flexible JSON database.

```js
db(key).set(property, value)
```

Each key in the `db` object **corresponds to a JSON file**.

This can also be view as:

```js
db(file).set(property, value)
```

## Install

```
$ npm install origindb --save
```

## Usage

```js
var db = require('origindb')('db');
db('money').set('phil', 10);
db('money').set('some_user', db('money').get('phil') + 10);
db('seen').set('some_user', Date.now());
db('posts').set('posts', [
  { title: 'OriginDB is awesome!', body: '...', likes: 10 },
  { title: 'flexbility ', body: '...', likes: 3 },
  { title: 'something someting something', body: '...', likes: 8 }
]);
```

In the `db` **folder**:

```json
// money.json
{
  "phil": 10
}

// seen.json
{
  "some_user": 1439674925906
}

// posts.json
{
  "posts": [
    { "title": "OriginDB is awesome!", "body": "...", "likes": 10 },
    { "title": "flexbility ", "body": "...", "likes": 3 },
    { "title": "something someting something", "body": "...", "likes": 8 }
  ]
}
```

## API

### db(databaseDirectory)

Create a new database.

Parameters:

- ``databaseDirectory``: _String_ -
The folder where all the json files will be stored at.

Returns: Function(key)
  - ``key``: _String_ - JSON file in the database directory
  - Returns: _Object_ - Methods of OriginDB.

Examples:

```js
var db = require('origindb')('userdb');
```

```js
var db = require('origindb')('apidb');
```

### save()

Saves the current data in the database to all files. Does not write to a file
if no changes were made.

Parameters: None

Returns: _undefined_

Example:

```js
// main.js
db('money').object().phil = 25;
db.save();

// money.json
{
  'phil': 25
}
```

### get(property, defaultValue)

Get a property and if it does not exist, return the default value instead.

Parameters:

- ``property``: _String_ | _Array_
- ``defaultValue``: _any_

Returns: _any_ - Depends on what is in property.

Example:

```js
// money.json
{
  'phil': 10,
  'stevo': 5
}

// main.js
db('money').get('phil'); // 10
db('money').get('jared', 0); // 0
db('money').get('stevo', 0); // 5
```

```js
// profile.json
{
  'phil': {
    'name': 'Philip La',
    'img': {
      'width': 55,
      'height': 20
    }
  }
}

// main.js
db('profile').get(['phil', 'name']); // 'Philip La'
db('profile').get(['phil', 'img', 'width']); // 55
db('profile').get(['phil', 'img', 'height'], 0); // 20
db('profile').get(['phil', 'img', 'resolution'], '4k'); // '4k'
db('profile').get(['phil', 'join_date']); // undefined
db('profile').get(['phil', 'img']); // { width: 55, height: 20 }
```

### delete(property)

Delete a property.
Shorthand for ``delete db('key').object()['prop']; db.save();``

Parameters:

- ``property``: _String_ | _Array_

Returns: _Object_ - Methods of OriginDB. This is useful for chaining methods
together.

Example:

```js
// food.json before
{
  'soup': true,
  'noodle': true
}

db('food').delete('soup');

// food.json after
{
  'noodle': true
}
```

### set(property, value)

Set a property with a value. This method automatically saves.

Parameters:

- ``property``: _String_ | _Array_
- ``value``: _any_

Returns: _Object_ - Methods of OriginDB. This is useful for chaining methods
together.

Examples:

```js
// main.js
db('money')
  .set('phil', 10)
  .set('josh', 89)
  .set('mike', db.get('mike', 0))
  .set('alex', 17);

// money.json
{
  'phil': 10,
  'josh': 89,
  'mike': 0,
  'alex': 17
}
```

```js
db('tickets').set('phil', db('tickets').get('phil').concat[generateTicket()]);
```

```js
// profile.json before
{
  'phil': {
    'name': 'Steven Hausen',
    'img': {
      'width': 55,
      'height': 20
    }
  }
}

// main.js
db('profile')
  .set(['phil', 'name'], 'Steven Hausen')
  .set(['phil', 'friends'], ['fender', 'a Gryphon'])
  .set(['phil', 'wins', 'game_format'], 9)
  .set(['phil', 'img', 'height'], 200);

// profile.json after
{
  'phil': {
    'name': 'Steven Hausen',
    'img': {
      'width': 55,
      'height': 200
    },
    'friends': ['fender', 'a Gryphon'],
    'wins': {
      'game_format': 9
    }
  }
}
```

### object()

Get the JSON object to directly manipulate it.

Parameters: None

Returns: _Object_

Examples:

```js
// join_date.json
{
  'phil': 1450814470721,
  'micheal': 1394814738429
}

// main.js
var name = 'phil';
console.log(db('join_date').object()[name]); // 1450814470721
console.log(db('join_date').object().micheal); // 1394814738429
db('join_date').object()[name] = new Date();
db.save();

// join_date.json
{
  'phil': 1450815256685,  
  'micheal': 1394814738429
}
```

```js
// money.js
{
  "phil": 10,
  "some_user": 20,
  "john": 10,
  "mike": 23,
}

// main.js
var users = db('money').object();
var total = Object.keys(users).reduce(function(acc, cur) {
  return acc + users[cur];
}, 0);

console.log(total); // 63
```

## License

MIT © [Phil](http://creaturephil.github.io)
