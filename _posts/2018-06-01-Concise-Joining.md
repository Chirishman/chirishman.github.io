---
layout: post
title: "Concise Joining"
excerpt: "To concatenate an array very concisely"
tags: [
    PowerShell,
    Joining,
    CodeGolf,
    Concatenation,
    Quick
]  
modified: {2018-06-01 01:15:00 +0500}
date: {2018-06-01 01:15:00 +0500}
comments: true
image:
  feature: banner.jpg
  thumb: /quicktips/postthumb.png
published: true
---
{% include _toc.html %}

## To join the entries in an array together in the most concise way possible

If you don't need a delimiter between the entries

```PowerShell
-join(0,1,2,3,4)
```

If you do need a delimiter (such as a comma) the long way is:

```PowerShell
@(0,1,2,3,4) -join ','
```

[More on PowerShell Operators](https://ss64.com/ps/syntax-operators.html)