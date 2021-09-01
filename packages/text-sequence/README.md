# JSON8 Text Sequence

## Introduction

JSON Text Sequence [RFC 7464](https://tools.ietf.org/html/rfc7464) toolkit for JavaScript.

---

- [JSON8 Text Sequence](#json8-text-sequence)
  - [Introduction](#introduction)
  - [Getting started](#getting-started)
  - [SerializeStream](#serializestream)
  - [ParseStream](#parsestream)
  - [Benchmark](#benchmark)

## Getting started

`npm install json8-text-sequence`

---

```js
const textSequence = require("json8-text-sequence");
```

## SerializeStream

Implements [Node.js Writable Stream](https://nodejs.org/api/stream.html#stream_writable_streams).

Example

```js
const { createWriteStream } = require("fs");
const textSequence = require("json8-text-sequence");

const writeStream = createWriteStream("example.log", { flags: "a" });
const serializeStream = new textSequence.SerializeStream();

serializeStream.pipe(writeStream);

setInterval(() => {
  // sequence can be any valid JSON value
  // string, boolean, array, object, ...
  const sequence = {
    date: Date.now(),
    value: Math.random().toString().slice(2),
  };
  serializeStream.write(sequence);
}, 500);
```

[↑](#json8-text-sequence)

## ParseStream

Implements [Node.js Readable Stream](https://nodejs.org/api/stream.html#stream_readable_streams).

```js
const { createReadStream } = require("fs");
const textSequence = require("json8-text-sequence");

const readStream = createReadStream("example.log");
const parseStream = new textSequence.ParseStream();

readStream.pipe(parseStream);

// Sequence is truncated
parseStream.on("truncated", (sequence) => {
  console.log("truncated sequence", sequence);
});

// Sequence is not valid JSON
parseStream.on("invalid", (sequence) => {
  console.log("invalid sequence", sequence);
});

parseStream.on("data", (json) => {
  console.log(json);
});
```

## Benchmark

```sh
git clone git@github.com:sonnyp/JSON8.git
npm install
cd JSON8/packages/text-sequence
node benchmark
```

On my computer (read/parse a 900kB application/json-seq file from disk):

```
json-text-sequence x 73 ops/sec ±4.64% (74 runs sampled)
json8-text-sequence x 584 ops/sec ±5.81% (66 runs sampled)
Fastest is json8-text-sequence
```

[↑](#json8-text-sequence)
