# Sia API

[![dependencies Status](https://david-dm.org/aspectron/sia-api.svg)](https://david-dm.org/aspectron/sia-api#info=dependencies)
[![license:mit](https://img.shields.io/badge/license-mit-blue.svg)](https://opensource.org/licenses/MIT)

NodeJs API for [Sia](http://sia.tech) - the decentralized cloud storage platform.

This module converts HTTP API paths exposed by the Sia REST API to NodeJs function calls arranged in an object hierarchy.

## Installation

`npm install sia-api`


## Documentation

Each Sia REST API call is enveloped into a function call using `request` module. API paths are converted to hierarchy of objects containing API function calls as follows:

```
GET: /render/allowance

becomes

sia.renter.allowance(<callback>)
```

By default, each function call is bount to `HTTP GET` method.  So `sia.renter.allowance()` executes `HTTP GET /renter/allowance`.  Each function call, however, can be overridden by explicitly calling `.get()` or `.post()` on the function as follows:

```
sia.renter.allowance(<callback>)  		// default GET
sia.renter.allowance.get(<callback>)	// explicit GET
sia.renter.allowance.post(<post args object>, <callback>)	// explicit POST
```

If an API call requires a sub-path to be provided to the HTTP call, the first argument to the function must be the string containing this sub-path.  For example:

```
GET: /wallet/transaction/bcf9922a9be02b0b4d1b3a2379054fb1c7113b1ac7006c03f93d7af6b440d172

```
must be called as follows:
```
sia.wallet.transaction("bcf9922a9be02b0b4d1b3a2379054fb1c7113b1ac7006c03f93d7af6b440d172", 
	function(err, resp) { ... })
```

`headers` and `timeout` arguments to the underlying `request` calls can be specified by an additional argument to the function before the callback argument:

```
sia.wallet.unlock({ timeout : 5 * 60 * 1000 }, <callback>)
```

## Creating Sia Module Instance

To create Sia module instance you must do the following:

```
var Sia = require("sia-api");

var sia = new Sia({
	host : "http://localhost:9980",
	// other constructor arguments
})
```

Other constructor arguments:

* `verbose` - Outputs request arguments and return data to console
* `timeout` - Default timeout value for request HTTP calls (milliseconds)
* `rpcServer` - iris-rpc server object (for iris-rpc relay)
* `rpcClient` - iris-rpc client object (for iris-rpc relay)
* `rpcUUID` - iris-rpc UUID when using iris-rpc multiplexer

This Sia API module supports call routing through iris-rpc, an RPC library that is a part of an [IRIS Application Framework](https://github.com/aspectron/iris-app)


## Usage Example

```
var sia = new Sia({
	host : "http://localhost:9980",
})


sia.daemon.version(function(err, resp) {
	if(err)
		console.log(err);
	else
		console.log("Sia daemon version:", resp.version);
})

```

Sample script listing active Sia hosts and average network pricing: https://github.com/aspectron/sia-hosts

