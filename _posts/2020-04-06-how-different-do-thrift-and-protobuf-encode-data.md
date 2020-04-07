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
// this string is 59 long, which means it would occupy as least 59 bytes

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

Compare to JSON, Thrift and Protobuf main differences are that they have schema and they encode data to binary rather than string. Take the JSON value above (i.e. `jsonValue`) as an example, to encode that value using Thrift BinaryProtocol, a schema needs to be defined as follow:

```
struct Post {
    1: required string title,
    2: required bool published,
    3: required i32 views
}
```

Using this schema to encode `jsonValue`, the result would be:

```javascript
// this is a continuous byte sequence of length 38, | and space characters are added for readability only

0b|00 01|00 00 00 13|74 68 72 69 66 74 20 61 6e 64 20 70 72 6f 74 6f 62 75 66|02|00 02|01|08|00 03|00 00 00 0d|00

// breakdown:

type 11 (string)        field tag 1       length 19       t  h  r  i  f  t     a  n  d     p  r  o  t  o  b  u  f
0b                      00 01             00 00 00 13     74 68 72 69 66 74 20 61 6e 64 20 70 72 6f 74 6f 62 75 66


type 2 (boolean)        field tag 2       value: true
02                      00 02             01


type 8 (int32)          field tag 3       value: 13
08                      00 03             00 00 00 0d

end of struct
00
```

It is clear that this encoded value is smaller than the encoded string value which is 59 bytes long. This is the result of omitting the key names and other unnecessary characters. Instead of using key name, Thrift uses field tag number to map a key to its value. This is much more compact as field tag only take 2 bytes in the encoded value regardless of the length of the actual key name. And a 2 bytes number can denote 65536 values so it is unlikely there would any problems with not having keys.

### How does Protobuf encode data?

Using Protobuf, a schema for the above value would be defined as follow:

```javascript
message Post {
    required string title = 1;
    required bool published = 2;
    required int32 views = 3;
}
```

The encoded binary value will be 25 bytes long:

```javascript
0a|13|74 68 72 69 66 74 20 61 6e 64 20 70 72 6f 74 6f 62 75 66|10|01|18|0d

// breakdown:
// in protobuf, the field tag value and data type are combined together in one byte. First 5 bits are used for field tag, and the last 3 bits are used for data type

field tag 1     type 2 (string)         length 19       t  h  r  i  f  t     a  n  d     p  r  o  t  o  b  u  f
0 0 0 0 1       0 1 0
0a                                      13              74 68 72 69 66 74 20 61 6e 64 20 70 72 6f 74 6f 62 75 66

// in protobuf, type 0 (varint) is used to denote multiple data types such as int32, int64, boolean...

field tag 2     type 0 (varint)         value: true
0 0 0 1 0       0 0 0
10                                      01

field  tag 3    type 0 (varint)         value: 13
0 0 0 1 1       0 0 0
18                                      0d

```

As we can use, Protobuf is even more compact than Thrift BinaryProtocol. Because it uses only one byte for both field tag value and data type, and variable-length integers for boolean and integers value. Instead of fixed-size four bytes integer to denote 13, Protobuf uses only 1 byte. The idea of variable-length integer is pretty simple, the top bit of each byte is used to indicate whether there are still more bytes to come. If the top bit of the current byte is 1, Protobuf knows that the next byte is also part of the current value. It keeps on reading till the top bit is 0, and concludes that current byte is the last part to the value. This means integers between -64 and 63 are encoded in one byte, integers between -8192 and 8191 are encoded in two bytes, etc. Take `1637` for example, this value would be encoded to:

```javascript

binary value: e5 0c

// breakdown:

e    5    0    c
1110 0101 0000 1100

=> 1 1100101 0 0001100

1 indicates there is one more byte      first 7 bits       0 indicates this is the last byte       last 7 bits
1                                       1100101            0                                       0001100

=> 0001100 1100101 = 1637

```

### Summary

In conclusion, Thrift and Protobuf are much more efficient than JSON to encode/decode data. They have the advantages of being more compact and faster. However, they lack the flexibility of JSON being a schemaless data format. Users of JSON are free to include whatever they want in the value without having to agree on a schema beforehand. JSON is also much more readable which is a huge benefit when working with a third-party web API. For these reasons, JSON is more popular for web services of different parties to communicate on the open web, whereas binary data formats such as Thrift and Protobuf are used between internal services controlled by one party within the private network.
