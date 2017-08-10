---
layout: post
title: Redis Logs
categories: [redis, logs]
tags: [redis, logs]
published: false
fullview: true
---

## Commands

SET
GET
DEL
INCR
EXPIRE
TTL

### List

LPUSH
RPUSH
LLEN
LRANGE
LPOP
RPOP
LINDEX

### Set

SADD
SREM
SISMEMBER
SMEMBERS
SUNION
SINTER
SDIFF
SCARD
SRANDMEMBER
SETEX
del
exists
ping
migrate
select
auth
script kill
shutdown save|nosave
object encoding

### Sorted Set

ZADD
ZRANGE
ZREVRANGE
ZREM
ZSCORE
ZRANK

### Hash Map

HSET
HGET
HGETALL
HINCRBY
HDEL

### Bitmap

SETBIT
GETBIT
BITCOUNT
BITOP OR|AND|XOR|NOT

## Useful

KEYS
FLUSHALL
INFO
DBSIZE
DEBUG SEGFAULT
MONITOR
CLIENT SETNAME
CLIENT KILL
RANDOMKEY
EXPIRE
EXPIREAT
TTL
TTLP
PERSIST

## HyperLogLog

PFADD
PFCOUNT
PFMERGE

## Publish/subscribe

PUBSUB
  CHANNELS
  NUMSUB
  NUMPAT

## Transaction

MULTI
EXEC
DISCARD
WATCH
UNWATCH
