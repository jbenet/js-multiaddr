js-multiaddr
============

[![](https://img.shields.io/badge/made%20by-Protocol%20Labs-blue.svg?style=flat-square)](http://ipn.io)
[![](https://img.shields.io/badge/freenode-%23ipfs-blue.svg?style=flat-square)](http://webchat.freenode.net/?channels=%23ipfs)
[![Build Status](https://travis-ci.org/jbenet/js-multiaddr.svg?style=flat-square)](https://travis-ci.org/jbenet/js-multiaddr)
![](https://img.shields.io/badge/coverage-%3F-yellow.svg?style=flat-square)
[![Dependency Status](https://david-dm.org/jbenet/js-multiaddr.svg?style=flat-square)](https://david-dm.org/jbenet/js-multiaddr)
[![js-standard-style](https://img.shields.io/badge/code%20style-standard-brightgreen.svg?style=flat-square)](https://github.com/feross/standard)

> [multiaddr](https://github.com/jbenet/multiaddr) implementation in JavaScript.

# What is multiaddr?

A standard way to represent addresses that

- support any standard network protocol
- are self-describing
- have a binary packed format
- have a nice string representation
- encapsulate well

# Example

```js
$ node

> const multiaddr = require('multiaddr')

> const addr = multiaddr("/ip4/127.0.0.1/udp/1234")
<Multiaddr /ip4/127.0.0.1/udp/1234>

> addr.buffer
<Buffer 04 7f 00 00 01 11 04 d2>

> addr.toString()
'/ip4/127.0.0.1/udp/1234'

> addr.protos()
[
  {code: 4, name: 'ip4', size: 32},
  {code: 17, name: 'udp', size: 16}
]

> addr.nodeAddress()
{
  family: "IPv4",
  port: 1234,
  address: "127.0.0.1"
}

> addr.encapsulate('/sctp/5678')
<Multiaddr /ip4/127.0.0.1/udp/1234/sctp/5678>
```

# API

```js
const multiaddr = require('multiaddr')
```

## Create

### const addr = multiaddr(str)

Creates a multiaddress from a string (e.g. `/ip4/127.0.0.1/udp/1234`).

### const addr = multiaddr(buf)

Creates a multiaddress from another multiaddress' raw bytes.

### addr.buffer

The raw bytes representing this multiaddress.

### addr.toString()

The multiaddress in string format (e.g. `/ip4/127.0.0.1/udp/1234`).


## Protocols

### addr.protoCodes()

Returns the codes of the protocols in the multiaddress, in left-to-right order.

```js
addr.protoCodes()
// [4, 6]
```

### addr.protoNames()

Returns the names of the protocols in the multiaddress, in left-to-right order.

```js
addr.protoNames()
// ['ip4', 'tcp']
```

### addr.protos()

Returns description objects of the protocols in the multiaddress, in left-to-right order.

```js
addr.protos()
// [
//   { code: 4, name: 'ip4', size: 32},
//   { code: 17, name: 'udp', size: 16}
// ]
```

Each object contains the protocol code, protocol name, and the size of its
address space in bits.

## Node-Friendly Addresses

Utility functions for getting NodeJS-friendly address information from a
multiaddress.

### addr.nodeAddress()

Returns a NodeJS-friendly object describing the left-most address in the
multiaddress.

```js
addr.nodeAddress()
// { family: "IPv4", port:1234, address: "127.0.0.1" }
```

Note that protocol information is left out: in Node (and most network systems)
the protocol is unknowable given only the address.

### addr.fromNodeAddress(addr)

Constructs a multiaddress, given a NodeJS-friendly address object and a protocol.

```js
addr.fromNodeAddress({family: "IPv4", port:1234, address: "127.0.0.1"}, 'udp')
// <Multiaddr /ip4/127.0.0.1/udp/1234>
```

### addr.fromStupidString(str)

Returns a multiaddress, given a URI in the format `<proto><IPv>://<IP
Addr>[:<proto port>]`

```js
addr = multiaddr.fromStupidString("udp4://127.0.0.1:1234")
// <Multiaddr /ip4/127.0.0.1/udp/1234>
```

*NOT IMPLEMENTED*

### addr.toStupidString()

*NOT IMPLEMENTED*

## En/decapsulate

### addr.encapsulate(str)

Returns a new multiaddress that encapsulates `addr` in a new protocol string,
`str`.

```js
addr.encapsulate('/sctp/5678')
// <Multiaddr /ip4/127.0.0.1/udp/1234/sctp/5678>
```

### addr.decapsulate(str)

Returns a new multiaddress with the right-most protocol string `str` removed.

```js
multiaddress('/ip4/127.0.0.1/udp/1234').decapsulate('/udp')
// <Multiaddr /ip4/127.0.0.1>
```

## Tunneling

Given these encapsulation/decapsulate tools, multiaddresses lend
themselves to expressing tunnels very nicely:

```JavaScript
const printer = multiaddr('/ip4/192.168.0.13/tcp/80')

const proxy = multiaddr('/ip4/10.20.30.40/tcp/443')

const printerOverProxy = proxy.encapsulate(printer)
// <Multiaddr /ip4/10.20.30.40/tcp/443/ip4/192.168.0.13/tcp/80>
```

# Installation

### Node.js + npm

```bash
$ npm install multiaddr --save
```
### Browser + Webpack

Same as in Node.js, you just have to transpile it with Webpack before serving
the code. Follow the [webpack config
example](/webpack.config.js).

### Browser + Browserify

Same as in Node.js, you just have to
[browserify](https://github.com/substack/node-browserify) the code before
serving it.

### Browser + `<script>` Tag

Make the [multiaddr.js](/dist/multiaddr.js) available through your server and
load it using a normal `<script>` tag. This will export the `multiaddr` on the
`window` object:

```JavaScript
const multiaddr = window.multiaddr
```

**NOTE**: You will need access to the Node.js `Buffer` API. If you are running
in the browser, you can access it with `multiaddr.Buffer` or you can install
[feross/buffer](https://github.com/feross/buffer).

# License

MIT
