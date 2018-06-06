---
layout: post
title: "Apply Registry Tweaks"
excerpt: "Apply single or bundled registry tweaks using PowerShell"
tags: [
    PowerShell,
    Registry,
    Tweaks,
    Deserialization,
    Tool
]
modified: 2018-06-06 12:30:00
date: 2018-06-06 12:30:00
comments: true
image:
  feature: banner.jpg
  thumb: /quicktips/postthumb.png
published: true
---
{% include _toc.html %}

## Intro

In the process of creating a series of bootstrap scripts for new machines last year I came across the Win10Debloat package and in the process of reworking it to my needs I created a function to easily apply large numbers of registry tweaks and to make it easier to oranize the sets of registry tweaks.

## Scripts

{% gist c97ef3f18d0811faf68ae4d9628a1936 Install-RegistryTweaks.ps1 %}

This function steps through the provided input and makes sure that every part of the path to the desired registry key exists and creates it if it does not. After that it creates and sets the desired value in that registry item.

Here is an example of the structure of the hashtable which the function is expecting to recieve as input:

{% gist 273140340dce05e9fecf001e30252a93 IRT01.ps1 %}

Now that we have an idea of what the structure should be, lets look at this real set of registry tweaks which I was deploying to prevent Edge from *repeatedly* stealing the default association with PDF files from Adobe or Foxit.

{% gist c97ef3f18d0811faf68ae4d9628a1936 AntiEdgeHijack.psd1 %}

You can see that each of those paths in this example contain exactly two properties to be modified or created.

## Usage

These change sets/packages can be fed to the function either as a literal, through the pipeline, or imported from a file and ingested.

### Literal

{% gist 273140340dce05e9fecf001e30252a93 IRT02.ps1 %}

### Pipeline

{% gist 273140340dce05e9fecf001e30252a93 IRT03.ps1 %}

### Imported

{% gist 273140340dce05e9fecf001e30252a93 IRT04.ps1 %}