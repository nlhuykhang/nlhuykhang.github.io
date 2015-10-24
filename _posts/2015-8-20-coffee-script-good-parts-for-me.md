---
layout: post
title: Coffeescript - Good parts for me
categories: [coffee, language]
tags: [study]
published: false
fullview: true
---

+
myFunction(item) for item in array

+
re = (item.name for item in array)

+
re =(item for item in array when item.name is 'khang')

+
pass = []
fail = []

(if score > 10 then pass else fail).push score for score in [1,1,1,1,1]

+
included = 'test' in array

included = 'test string'.indexOf('test') isnt -1

+
for key in object
  return object[key]

for key, value of object
  return

+
z or= {}
z ?= {}
z ? {}

+
do ->
  return 'something'

((a,b) ->
  console.log a
  console.log b
)(1, 2)

+
[1, 2, 3, 4][1..2] -> [2, 3]
'string string'[1..3]

+
a if b?

a = b ? 10

a.b()?.c

a.b().c?()