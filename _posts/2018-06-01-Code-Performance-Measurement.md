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

```PowerShell
$PerformanceResults = Measure-Command -Expression {
    Start-Sleep -Seconds 15
}

$PerformanceResults
```

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

```PowerShell
Function Test-FunctionPerformance {
    [CmdletBinding()]
    Param()
    $Overall = [system.diagnostics.stopwatch]::startNew()
    $OddSteps = [system.diagnostics.stopwatch]::startNew()
    Start-Sleep -Seconds 3
    $OddSteps.Stop()
    Write-Verbose -Message "Step 1 Took $($Overall.Elapsed)"
    
    $Step2 =  [system.diagnostics.stopwatch]::startNew()
    Start-Sleep -Seconds 5
    $Step2.Stop()
    Write-Verbose -Message "Step 2 Took $($Step2.Elapsed)"
    
    $Step3 =  [system.diagnostics.stopwatch]::startNew()
    $OddSteps.Start()
    Start-Sleep -Seconds 10
    $Step3.Stop()
    $OddSteps.Stop()
    $Overall.Stop()
    Write-Verbose -Message "Step 3 Took $($Step3.Elapsed)"
    
    Write-Verbose -Message "Odd Numbered Steps Took $($OddSteps.Elapsed)"

    Write-Verbose -Message "Entire Function Took $($Overall.Elapsed)"
}

Test-FunctionPerformance -Verbose
```

#### Sample Output

    VERBOSE: Step 1 Took 00:00:03.0023588
    VERBOSE: Step 2 Took 00:00:05.0012098
    VERBOSE: Step 3 Took 00:00:10.0010250
    VERBOSE: Odd Numbered Steps Took 00:00:13.0025344
    VERBOSE: Entire Function Took 00:00:18.0079912

## Rich metrics on loop performance

Stopwatch objects are also especially good for measuring loops

```PowerShell
$Overall = [system.diagnostics.stopwatch]::startNew()
$ThisLoop = [system.diagnostics.stopwatch]::New()
$IndividualLoopPerformance = [System.Collections.ArrayList]::new()
1..100 | %{
    $ThisLoop.Restart()
    Start-Sleep -Milliseconds $_
    [void]$IndividualLoopPerformance.Add($ThisLoop.Elapsed)
}
$ThisLoop.Stop()
$Overall.Stop()

Write-Verbose -Message "Loop Performance Metrics `r`n$(($IndividualLoopPerformance | measure -Average -Property totalseconds -Maximum -Minimum -Sum | ft ) | out-string)" -Verbose

Write-Verbose -Message "Entire Loop Took $($Overall.Elapsed)" -Verbose

```

#### Sample Output

    VERBOSE: Loop Performance Metrics 
    
    Count     Average       Sum   Maximum  Minimum Property    
    -----     -------       ---   -------  ------- --------    
      100 0.057919116 5.7919116 0.1153261 0.006223 TotalSeconds
    
    
    
    VERBOSE: Entire Loop Took 00:00:05.8833751
