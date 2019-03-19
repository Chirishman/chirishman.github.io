---
layout: post
title: "Perform and Report On Long Running Commands"
excerpt: "Perform one or more long running commands and email yourself the results so that you don't have to babysit it"
tags: [
    PowerShell,
    LongJobs,
    chkdsk,
    Quick
]
modified: 2019-03-19 11:06:00
date: 2019-03-20 12:06:00
comments: true
image:
  feature: banner.jpg
  thumb: /quicktips/postthumb.png
published: false
---
{% include _toc.html %}

## Intro

Often times, especially when diagnosing something you'll want to let a long running job or command run for hours, perhaps overnight. In my example here I've used my most frequent flier for this, chkdsk (or in this case, the PowerShell wrapper for same, `Repair-Volume`).

## Scripts

### Perform and Capture your results

Run the command and collect the results in a variable or output it to a file. Here the results of this command are fairly easy to store in a variable, however if you are using things like old command prompt tools or something like a git command you will need to do a little more legwork to capture the results in a usable way. My favorite way to do this is as computed properties in a select statement which I'll be covering in another post.

```PowerShell
$ScanResult = Repair-Volume -DriveLetter c -Scan
```

### Use Send-MailMessage to deliver the scan results to yourself

Make sure to change the **from**, **to**, and **smtpserver** values below to the ones appropriate to your sending/receiving environment.

```PowerShell
Send-MailMessage -From 'a@b.com' -To 'b@a.com' -Subject 'Scan Results' -Body $ScanResult -SmtpServer 'mail.b.com'
```

There are other more complicated and more robust ways to do this kind of thing and I'm not advocating it as a permanent solution to something like a reporting task but it's short, easy to remember and great for ad-hoc use, especially when troubleshooting.