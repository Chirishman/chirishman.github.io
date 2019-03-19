---
layout: post
title: "Bypass WSUS"
excerpt: "Apply A registry tweak to skip WSUS using PowerShell"
tags: [
    PowerShell,
    Registry,
    Tweaks,
    WSUS,
    Temporary,
    Quick
]
modified: 2019-03-19 10:45:00
date: 2019-03-19 11:30:00
comments: true
image:
  feature: banner.jpg
  thumb: /quicktips/postthumb.png
published: true
---
{% include _toc.html %}

## Intro

I have mostly found myself needing to do this when prepping to install SQL server on a machine where WSUS is for some reason refusing to serve DotNet 3.5 which at this point is an on-demand feature rather than a standalone installer.

## Scripts

### To Bypass WSUS

This sets a registry value which allows windows to skip trying to contact the WSUS server.

```PowerShell
Set-ItemProperty -Path HKLM:\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate\AU -Name UseWUServer -Value 0
```

### To Reenable WSUS

And this reverses the value change to resume directing Windows Update to poll the WSUS server. If you group policy is set to enforce this then you can simply leave this step off if desired as it should be corrected on the next consistency check.

```PowerShell
Set-ItemProperty -Path HKLM:\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate\AU -Name UseWUServer -Value 1
```