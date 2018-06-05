---
layout: post
title: "Calculated Properties"
excerpt: "Computed Columns for PowerShell"
tags: [
    PowerShell,
    CalculatedProperties,
    SelectStatements,
    Pipeline,
    101
]
modified: 2018-06-05 12:09:00
date: 2018-06-05 12:09:00
comments: true
image:
  feature: banner.jpg
  thumb: /quicktips/postthumb.png
published: true
---
{% include _toc.html %}

## Intro

Today lets talk about calculated properties in PowerShell which can be thought of like computed columns in SQL if you're familiar with that, or like a column made up of equation cells in Excel.

The most comprehensible way I can think of to do this is to work through an example of where this would be useful in a real world scenario and along the way I will touch on various ways to improve the readability and maintainability of your code.

## Scenario

Take the below scenario in which I am attempting to get the sum and average of the file sizes for all files in `C:\Temp`

{% gist 567767abc08a8d610f6db44256ee12eb ComputedColumns01.ps1 %}

    Count    : 41
    Average  : 776383349.731707
    Sum      : 31831717339
    Maximum  :
    Minimum  :
    Property : length

This output is OK but not great. I'd rather have the size in megabytes and trim off any decimal places beyond two. How would I go about this?

One way would be to store the values one at a time in a variable, perform some math on them and then return them. That's OK but it breaks down when you start trying to check for multiple folders one after another and you lose the output format that you're accustomed to. Additionally if you were to add another property to consideration such as **Maximum** it would be complicated to add and would require an another new variable.

The better option is to use some calculated properties. 

## Select Statements

First begin by testing explicit selection of the properties you want without modifying them

{% gist 567767abc08a8d610f6db44256ee12eb ComputedColumns02.ps1 %}

### Calculated Property Basics

Then we will replace one property with a calculated property which will result in an identical output.

This is done by replacing the name-based column selection with a hashtable containing exactly two properties, one named **Name** with a string value which will become the column header and the other called **Expression** which must contain a scriptblock value which can be created literally by using curly braces. The scriptblock will be executed once per record to generate the value for this column for the row in question.

Remember that `$_` is how you can easily refer to 'this' or 'this record' in PowerShell and thus how you would refer to the record being processed inside of the expression block. To select a specific property use `$_.DesiredPropertyName`

{% gist 567767abc08a8d610f6db44256ee12eb ComputedColumns03.ps1 %}

### Numerical Conversion

Now that we have our basic column to work from we can begin to do math, dividing the average byte count of the files by one megabyte. PowerShell makes this easy by providing a shorthand allowing you to simply put an integer followed by the two digit abbreviation for the unit in question (e.g. `1kb`, `100mb`, `2gb`, `0.4tb`, `1pb`)

{% gist 567767abc08a8d610f6db44256ee12eb ComputedColumns04.ps1 %}

    Count    : 41
    Average  : 740.416860324581
    Sum      : 31831717339
    Maximum  :
    Minimum  :
    Property : length

That works great, now we need to copy this logic to the Sum column and trim the decimal places from both.

### Rounding

The rounding will be done using `[math]::Round($ValueToBeRounded,$NumberOfPlacesToRoundTo)`

In this case we'll round to two places.

{% gist 567767abc08a8d610f6db44256ee12eb ComputedColumns05.ps1 %}

    Count    : 41
    Average  : 740.42
    Sum      : 30357.09
    Maximum  :
    Minimum  :
    Property : length

All clean! But maybe we should annotate what unit we're using now though.

### Reflecting New Units in Output

My preference is to reflect this in the column heading so that I can keep the values clean in case I want to do anything further with them. To do that we just need to modify the **Name** value in the definition of out computed properties.

{% gist 567767abc08a8d610f6db44256ee12eb ComputedColumns06.ps1 %}

    Count        : 41
    Average (MB) : 740.42
    Sum (MB)     : 30357.09
    Maximum      :
    Minimum      :
    Property     : length

## Readability Improvements

That pipeline is getting a bit long and messy now though, we should do something to clean that up! Luckily PowerShell select statements, even ones including calculated properties, can be stored in variables as arrays!

{% gist 567767abc08a8d610f6db44256ee12eb ComputedColumns07.ps1 %}

Cool! Though those computed column lines are a bit long. Luckily we know that they're just hashtables and that `;` is just a stand-in for a newline. With that knowledge we can unwrap it a little further. We can also linebreak after each pipe to aid in readability.

{% gist 567767abc08a8d610f6db44256ee12eb ComputedColumns08.ps1 %}