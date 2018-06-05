---
layout: post
title: "Check for Lockouts And Logon Times in all DCs"
excerpt: "To quickly poll the domain for information without needing to wait for replication"
tags: [
    PowerShell,
    ActiveDirectory,
    Replication,
    AccountLockout,
    LastLogon,
    Quick
]
modified: 2018-06-04 14:00:00
date: 2018-06-04 14:00:00
comments: true
image:
  feature: banner.jpg
  thumb: /quicktips/postthumb.png
published: true
---
{% include _toc.html %}

## Intro

I've found that certain types of information are regularly more important to get immediately than others and that this need does not always align with Active Directory's synchronization cadence.

In an effort to work around this constraint I have written these functions which allow you to quickly and easily poll all of the domain controllers in your domain in order to quickly check the time of a specific user's Last Logon and for the lockout state of a user in all DCs.

In particular I began this because the LastLogon value (which is the most specific) does not sync between DCs, only the LastLogonTimestamp value which has a window of plus or minus 14 days.

The below scripts depend on Warren Frame's excellent [Invoke-Parallel function](https://github.com/RamblingCookieMonster/Invoke-Parallel) which can be easily installed as part of [the WFTools module](https://www.powershellgallery.com/packages/WFTools/)

## Scripts

A function to create a `System.DirectoryServices.ActiveDirectory.Domain` object based on a domain name.

* Defaults to the domain of which the computer it is run on is a member

{% gist 2089d116947b243f0c13b3d091554f18 Get-DirectoryServicesDomain.ps1 %}

A function to query the domain object to retrieve a list of all domain controllers for the selected domain.

* Defaults to the domain of which the computer it is run on is a member.
* Uses
  * `Get-DirectoryServicesDomain`

{% gist 2089d116947b243f0c13b3d091554f18 Get-DirectoryServicesDomainController.ps1 %}

A function to query all domain controllers for the lockout state of a user.

* Defaults to the domain of which the computer it is run on is a member.
* Uses
  * `Get-DirectoryServicesDomain`
  * `Get-DirectoryServicesDomainController`

{% gist 2089d116947b243f0c13b3d091554f18 Get-RealLockoutState.ps1 %}

A function to query all domain controllers for the latest logon date of a user.

* Defaults to the domain of which the computer it is run on is a member.
* Uses
  * `Get-DirectoryServicesDomain`
  * `Get-DirectoryServicesDomainController`

{% gist 2089d116947b243f0c13b3d091554f18 Get-RealLogonDate.ps1 %}

## Usage

By default these will check these items relative to the domain which the computer that they are running on is a member of. This can be overridden by setting an explicit domain name value in the `-Domain` parameter of either function.

### To check for an AD lockout in all Domain Controllers

{% gist 2089d116947b243f0c13b3d091554f18 Get-RealLockoutState_UsageExample.ps1 %}

### To check for latest logon date in all Domain Controllers

{% gist 2089d116947b243f0c13b3d091554f18 Get-RealLogonDate_UsageExample.ps1 %}