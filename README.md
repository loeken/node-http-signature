This is a fork of the _node-http-signature_ module, which uses the forked
version of _node-sshpk_ as dependency.

This fork of _node-http-signature_ allows signing of HTTP headers
with secp256k1 private keys, the mostly used EC curve 
in Bitcoin and Ethereum, enabling on-chain verification of HTTP response
for the latter.

# node-http-signature

node-http-signature is a node.js library that has client and server components
for Joyent's [HTTP Signature Scheme](http_signing.md).

## Usage

Note the example below signs a request with the same key/cert used to start an
HTTP server. This is almost certainly not what you actually want, but is just
used to illustrate the API calls; you will need to provide your own key
management in addition to this library.

### Client

```js
var fs = require('fs');
var https = require('https');
var httpSignature = require('http-signature');

var key = fs.readFileSync('./key.pem', 'ascii');

var options = {
  host: 'localhost',
  port: 8443,
  path: '/',
  method: 'GET',
  headers: {}
};

// Adds a 'Date' header in, signs it, and adds the
// 'Authorization' header in.
var req = https.request(options, function(res) {
  console.log(res.statusCode);
});


httpSignature.sign(req, {
  key: key,
  keyId: './cert.pem'
});

req.end();
```

### Server
```js
var fs = require('fs');
var httpSignature = require('http-signature');
var key = fs.readFileSync('./key.pem', 'ascii');
var http = require('http')
var sshpk = require('sshpk')
const crypto = require('crypto')

const requestHandler = (req, res) => {
 var body = "Hello!";
 const hash = crypto.createHash('sha256')
 hash.update(body)
 res.setHeader('Digest', 'SHA-256=' + hash.digest('base64'))
	
 httpSignature.sign(res, {
 	key: key,
	keyId: '047b0e85dd1c08ee6722cbe132be9e73bf531c7882965cb08eec2be17e6408514a323a27d0ab38bdcc35ca1d63c1beeb1f3236e24a50538634f166bfc4e3c1258d',
	headers: ['Digest']	
 })
 res.writeHead(200);
 res.end('Hello!')
};
const server = http.createServer(requestHandler)

server.listen(3000, (err) => {
	
	httpOptions = {
		headers: {
			'x-foo': 'false'
		}
	}
	
	signOptions = {
		key: key,
		keyId: 'oracize1'
	}

	if (err) {
		return console.log('Server Error: ', err)
	}

	console.log('server listening')
})


```


## Installation

    npm install http-signature

## License

MIT.

## Bugs

See <https://github.com/joyent/node-http-signature/issues>.
