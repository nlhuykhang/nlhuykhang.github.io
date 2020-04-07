---
layout: post
title: How different do Thrift and Protobuf encode data?
categories: [server]
tags: [server]
published: true
fullview: true
---

### What is Thrift and Protobuf?

In short, they are data serialization (encode/decode data) tools. They are used to encode data before sending or storing somewhere, and decode encoded data back to original format. A more popular data format is JSON that comes with Javascript. `JSON.stringify` and `JSON.parse` are two functions used to encode/decode JSON to/from string value. Being a text-base data format, JSON is easy to use and understand, which is one of the reason why it has become ubiquitous in the web. However, JSON has its own limitations, and that is where other options like Thrift and Protobuf come in.

### What is the problem with JSON?

The main problem with JSON is that it is schemaless, which means encoded JSON value needs to contain both keys and values. For example, say we need to encode the below JSON value to string:

```javascript

const jsonValue = {
    title: "thrift and protobuf",
    published: true,
    views: 13
};

const encodedString = JSON.stringify(jsonValue);

// => encodedString would be '{"title":"thrift and protobuf","published":true,"views":13}'

```

As shown in the example, `encodedString` contains both keys and values of the JSON value, which is quite inefficient as the things we care about are the values not keys. However, if keys are omiteed, it would be impossible to decode the string value back to JSON value because the decoder does not know what keys a JSON value ought to has. In fact, this is not only the problem of JSON, but also all other schemaless data formats like XML.

To solve this problem, schemas are introduced to the encoding/decoding process. The idea is simple, both encoder and decoder will agree beforehand on the format of the data, things like what keys there are, what formats they are in, how to map a value to a key etc... With these information at hand, encoder and decoder can safely omit keys in the encoded data. For example, below is a rudimentary implementation of this idea:

```javascript

const jsonValue = {
    title: "thrift and protobuf",
    published: true,
    views: 13
};

// encode
const encodedString = jsonValue.title + "," + jsonValue.published + "," jsonValue.views;
// => encodedString would be 'thrift and protobuf,true,13'

// decode
const values = encodedString.split(",");
const originalValue = {
    title: values[0],
    published: values[1] === "true",
    views: parseInt(values[2])
};
```

This example is merely a demonstration of the idea. It is actually far from being usable and efficient. For example, it would not work properly if `title` value contains a comma.

Another problem with `JSON.stringify` is that it enccodes all values to string regardless of their types. For instance, in the above example, the boolean value at key `published` is literally encoded to a string `true`. This is far from effective as we know there are only two values of type boolean which is true or false. Therefore, a mere one bit either 0 or 1 could be enough to denote this value.

To solve all these problems, people have worked out multiple data formats having schema. Thrift and Protobuf are two of these solutions.

### How does Thrift encode data?

Compare to JSON, Thrift and Protobuf main differences are that they have schema and they encode data to binary rather than string.

### How does Protobuf encode data?

### Why are JSON still used despite of being inefficient?
