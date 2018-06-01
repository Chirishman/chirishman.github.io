---
layout: post
title: "Code Performance Measurement"
excerpt: "To quickly measure snippets or create permanent measurement features in your scripts"
tags: [
    PowerShell,
    Performance,
    Metrics,
    Loops,
    DotNet,
    Quick
]
modified: 2018-06-01 01:23:00 +0500
date: 2018-06-01 01:23:00 +0500
comments: true
image:
  feature: banner.jpg
  thumb: /quicktips/postthumb.png
published: true
---
{% include _toc.html %}

## Single Commands or Short Sections

To measure a single command or section of code when developing use `Measure-Command` to quickly get some performance information

{% gist 24e802b620f4e6e54e12eeff5ae4969d Measure-Command.ps1 %}

#### Sample Output

    Days              : 0
    Hours             : 0
    Minutes           : 0
    Seconds           : 15
    Milliseconds      : 14
    Ticks             : 150149931
    TotalDays         : 0.000173784642361111
    TotalHours        : 0.00417083141666667
    TotalMinutes      : 0.250249885
    TotalSeconds      : 15.0149931
    TotalMilliseconds : 15014.9931

## Long or complex scripts and structures

To measure specific sections of code in a production script leverage dotnet stopwatch objects which are much more flexible. They can be stopped, started, and cleared at will.

{% gist 24e802b620f4e6e54e12eeff5ae4969d Test-FunctionPerformance.ps1 %}

#### Sample Output

    VERBOSE: Step 1 Took 00:00:03.0023588
    VERBOSE: Step 2 Took 00:00:05.0012098
    VERBOSE: Step 3 Took 00:00:10.0010250
    VERBOSE: Odd Numbered Steps Took 00:00:13.0025344
    VERBOSE: Entire Function Took 00:00:18.0079912

## Rich metrics on loop performance

Stopwatch objects are also especially good for measuring loops

{% gist 24e802b620f4e6e54e12eeff5ae4969d LoopMetrics.ps1 %}

#### Sample Output

    VERBOSE: Loop Performance Metrics 
    
    Count     Average       Sum   Maximum  Minimum Property    
    -----     -------       ---   -------  ------- --------    
      100 0.057919116 5.7919116 0.1153261 0.006223 TotalSeconds
    
    
    
    VERBOSE: Entire Loop Took 00:00:05.8833751
