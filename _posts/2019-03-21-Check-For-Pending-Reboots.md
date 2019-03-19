---
layout: post
title: "Bypass WSUS"
excerpt: "Apply A registry tweak to skip WSUS using PowerShell"
tags: [
    PowerShell,
    Registry,
    Reboot,
    Servicing,
    Quick
]
modified: 2019-03-19 11:06:00
date: 2019-03-21 12:30:00
comments: true
image:
  feature: banner.jpg
  thumb: /quicktips/postthumb.png
published: false
---
{% include _toc.html %}

## Intro

Here is a self contained snippet of code which I've assembled to easily check whether a reboot is pending on a given machine for _any_ reason. The list of check locations is based on/captured from xPendingReboot. Full script is at the end.

## Scripts

### Initialize a container for the output

```powershell
$RebootCheck = @{}
```

### Check for pending Component Based Servicing and store the result

```PowerShell
$ComponentBasedServicingKeys = (Get-ChildItem 'HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Component Based Servicing\').Name

$RebootCheck['ComponentBasedServicing'] = $ComponentBasedServicingKeys -Split "\\" -contains "RebootPending"
```

### Check for a pending Windows Update reboot

```
$WindowsUpdateKeys = (Get-ChildItem 'HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\WindowsUpdate\Auto Update\').Name

$RebootCheck['WindowsUpdate'] = $WindowsUpdateKeys -Split "\\" -contains "RebootRequired"
```

### Check for pending file rename

```
$RebootCheck['PendingFileRename'] = (Get-ItemProperty 'HKLM:\SYSTEM\CurrentControlSet\Control\Session Manager\').PendingFileRenameOperations.Length -gt 0
```

### Check for pending Computer Rename

```
$ActiveComputerName = (Get-ItemProperty 'HKLM:\SYSTEM\CurrentControlSet\Control\ComputerName\ActiveComputerName').ComputerName
$PendingComputerName = (Get-ItemProperty 'HKLM:\SYSTEM\CurrentControlSet\Control\ComputerName\ComputerName').ComputerName
$RebootCheck['PendingComputerRename'] = $ActiveComputerName -ne $PendingComputerName
```

### Return Reboot Check Results

```
$RebootCheck
```

## Complete Script

```powershell
$RebootCheck = @{}

$ComponentBasedServicingKeys = (Get-ChildItem 'HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Component Based Servicing\').Name

$RebootCheck['ComponentBasedServicing'] = $ComponentBasedServicingKeys -Split "\\" -contains "RebootPending"

$WindowsUpdateKeys = (Get-ChildItem 'HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\WindowsUpdate\Auto Update\').Name

$RebootCheck['WindowsUpdate'] = $WindowsUpdateKeys -Split "\\" -contains "RebootRequired"

$RebootCheck['PendingFileRename'] = (Get-ItemProperty 'HKLM:\SYSTEM\CurrentControlSet\Control\Session Manager\').PendingFileRenameOperations.Length -gt 0
$ActiveComputerName = (Get-ItemProperty 'HKLM:\SYSTEM\CurrentControlSet\Control\ComputerName\ActiveComputerName').ComputerName
$PendingComputerName = (Get-ItemProperty 'HKLM:\SYSTEM\CurrentControlSet\Control\ComputerName\ComputerName').ComputerName
$RebootCheck['PendingComputerRename'] = $ActiveComputerName -ne $PendingComputerName

$RebootCheck
```