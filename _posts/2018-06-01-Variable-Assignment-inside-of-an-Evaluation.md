---
layout: post
title: "Variable Assignment inside of an Evaluation"
excerpt: "Extra Parentheses Save the day"
tags: [
  PowerShell,
  VariableAssignment,
  Loops,
  Quick
]
modified: 2018-06-01 01:10:00 +0500
date: 2018-06-01 01:10:00 +0500
comments: true
image:
  feature: banner.jpg
  thumb: /quicktips/postthumb.png
published: true
---
{% include _toc.html %}

## To evaluate something and also store it in a variable in the most concise way possible

{% gist 36646bdc100f2746ef812173cd3bef69 AssignmentInEvaluation.ps1 %}

The extra set of Parentheses causes the statement to return it's value as well as storing it in the variable, in this case `$a`

This is especially useful in `while` loops. The example below is taken from a script which I wrote to listen for a machine to come back from a reboot.

{% gist 36646bdc100f2746ef812173cd3bef69 WhileLoop.ps1 %}
