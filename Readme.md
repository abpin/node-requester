## node-requester

A simple network request helper that is geared towards crawling. (a few keywords GZIP, XML, JSON, PROXIES)

## installation

    $ npm install requester

## super simple to use

```javascript
var Requester = require('requester'),
	requester = new Requester();

requester.get(/* URL */, function (body) {
	console.log(this.statusCode, body);
});

requester.get(/* URL */, /* REQUEST_OBJECT */, function (body) {
	console.log(this.statusCode, body);
});
```

## initialization

```javascript
var Requester = ('requester');

var requester = new Requester({
	cookiejar: true, // basic cookie support, currently doesnt care about domain or path rules
	cookies: {},
	headers: {},
	timeout: 4000,
	retries: 3,
	encoding 'utf8',
	// didRequestFail: null, (this has its own section)
	// signRequest: null, (this has its own section)
	dataType: 'RAW' // JSON or XML,
	auth: {username: 'username', password: 'password'}, // basic auth for all requests
	proxies: [{ip: '127.0.0.1', port: 1337}, {ip: '127.0.0.2', port: 1337}, {ip: '127.0.0.3', port: 1337}] // rotating proxy array
});
```

if you initialize the request object with any of the above properties every request will default to those settings, you can over ride them on a per request basis

```javascript
var options = {
	encoding: 'binary',
	proxy: {ip: '127.0.0.1', port: 1337},
	data: {foo: 'bar'},
	cookies: {foo: 'bar'},
	auth: {username: 'username', password: 'password'} // basic auth for request
};

requester.get(/* URL */, options, function (body) {
	console.log(body)
});
```
## request objects

they support the following properties
* {Object} data 
* {String} dataType
* {Object} headers
* {Object} cookies
* {Object} proxy
* {Object} auth
* {String} encoding
* {Number} timeout
* {Number} retries
* {Function} didRequestFail
* {Function} signRequest
* {Boolean} follow
* {Number} followMax

## request response checking

this is a method that gets ran before the actual response callback gets run to ensure that the contents are actually what you're expecting, for example if the content rotates and you're looking for something special you can do

```javascript
requester.get(
	/ * URL */,
	{
		didRequestFail: function (data) {
			return !data.match(/something/);
		},
		retries: 10
	},
	function (data) {
		console.log(data);
	}
);
```

this would request the url until it matches the string 'something' in the response (up to 10 attempts)

## request signatures

you can create a custom request signature function like this

```javascript
var qs = require('querystring');

var requester = new Requester({
	signRequest: function (data) {
		// do something with the data
		return qs.stringify(data);
	}
});
```

## posting

```javascript
requester.post(/* URL */, {data: {foo: 'bar', bar: 'foo'}}, function (body) {
	console.log(body)
});
```

## multipart

the multipart request works a little different, in the data object you can prefix a values key with '@' like this

```javascript
requester.multipart(/* URL */, {data: {'@file': /* FILEPATH */, '@file2': /* FILEPATH */, bar: 'foo'}}, function (body) {
	console.log(body)
});
```

this will create a multipart request and upload files
