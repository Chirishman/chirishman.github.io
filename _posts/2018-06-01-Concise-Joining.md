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
modified: 2018-06-01 13:15:00
date: 2018-06-01 13:15:00
comments: true
image:
  feature: banner.jpg
  thumb: /quicktips/postthumb.png
published: true
---
{% include _toc.html %}

## To join the entries in an array together in the most concise way possible

If you don't need a delimiter between the entries

{% gist c435cbfdcbaa6dadddaa6f020da0e088 ConciseJoin.ps1 %}

If you do need a delimiter (such as a comma) the long way is:

{% gist c435cbfdcbaa6dadddaa6f020da0e088 DelimitedJoin.ps1 %}

[More on PowerShell Operators](https://ss64.com/ps/syntax-operators.html)