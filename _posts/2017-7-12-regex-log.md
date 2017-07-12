---
layout: post
title: Regex Log
categories: [regex, logs]
tags: [regex, logs]
published: false
fullview: true
---


\w stands for any latin letter or digit

\d matches any decimal digit

+ after a symbol will match one or more repetitions of that symbol

* matches zero or more occurences of the symbol

[] match any of some given set of characters

[^abc] match any character except a,b or c

If you would like to match symbols that bear special meaning for regex, you need to escape them like this: \., \[, \+, etc.

a|b will match a or b. This is similar to the square braces, however you can apply | to more than single symbols

. will match any single symbol

? which makes the symbol optional

^ and $ stand for start and end of line

\b means a word boundary

^\w+ match the first word in line
\w+$ match the last word in each line

\b\w match the first letter in word
\w\b match the last letter in word

{min, max}
{exact}

You can group pieces of the match by putting them in braces (). Afterwards you reference the groups in the regex by \1, \2, etc
(\w)\1 matches any letter repeated two times
Most importantly you can use groups for replacing, referencing them by numbers: $1, $2, etc. in the replace expression

(?=), (?!) are called a lookahead
abc(?=d) will match abc only if it is followed by d
abc(?!d) will match abc only if it is not followed by d
