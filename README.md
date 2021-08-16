# pthrift

[![npm version](https://badge.fury.io/js/%40brigade%2Fpthrift.svg)](https://badge.fury.io/js/%40brigade%2Fpthrift)
[![Build Status](https://travis-ci.org/brigade/pthrift.svg?branch=master)](https://travis-ci.org/brigade/pthrift)

Pooled thrift client, a node Thrift client utilising a pool of service connections and improved error handling/recovery

```
npm install pthrift
```

## Features

- proper Promise support in pool instance con/destruction
- correct behaviour on "Internal Error", aka, socket misalignment on
  unhandled server errors
- better/existent timeout support for: connections, pool checkout,
  execution
- builtin retry support using upstream Thrift code
- faster detection and pruning of dead connections
- async/await compatibility and other niceties
- enabled oneway function

## Example usage

Given a thrift file `calculator_service.thrift` with contents:

```thrift
exception OutOfRange {
  1: string message
}
service CalculatorService {
  i32 add(1:i32 num1, 2:i32 num2) throws (1:OutOfRange out_of_range)
}
```

Compile with the `thrift` command (installable via your package manager):

```sh
thrift --gen js:node calculator_service.thrift
```

This will produce JS definitions for your service, which can then be used to create a client:

```js
const CalculatorService = require("./CalculatorService");
const Pool = require("pthrift");

// host and port are mandatory see other config options in comments
const host = "127.0.0.1",
  port = 9000;
const client = Pool(
  CalculatorService,
  { host, port },
  {
    poolOptions: { max: 5 },
    onAcqTimeout: (err) => {},
    onConnTimeout: (err) => {},
    onClosedError: (err) => {},
  }
);

// use the client as you would a regular client, get pooling for free
client.add(1, 2).then((sum) => console.log(sum));
```

## Testing

Tests use the `calculator_service.thrift` example service located in
`spec/support/thrift`. A compiled version is committed to this repository; to
recompile it, install `thrift` using your package manager (e.g., `brew install thrift`), then follow the instructions in the file.

Tests can be run using

```sh
npm test
```

## License

Copyright (2018-2021) <a href="https://www.chatopera.com/" target="_blank">北京华夏春松科技有限公司</a>

[Apache License Version 2.0](./LICENSE)

[![chatoper banner][co-banner-image]][co-url]

[co-banner-image]: https://static-public.chatopera.com/assets/images/42383104-da925942-8168-11e8-8195-868d5fcec170.png
[co-url]: https://www.chatopera.com
