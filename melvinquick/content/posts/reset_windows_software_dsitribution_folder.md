---
date: "2024-02-22T11:27:18-04:00"
draft: false
title: "Reset the SoftwareDistribution Folder Back to Stock in Windows"
tags: ["windows", "powershell", "disk cleanup"]
categories: ["windows"]
---

## Intro

Sometimes you need to clear space on Windows machine and it's not always obvious where you can save those bytes at. You may look for temp folders/files, games you can/should uninstall, or old profiles you can remove, but sometimes the easiest way is to cleanup the SoftwareDistribution Folder! This folder houses files that may be required for Windows Updates, such as hotfixes or cumulative updates. Sometimes though, this folder doesn't get cleaned up like it shoud and leaves a lot of unnecessary files behind. The following is a step-by-step guide on how to clean up this folder and reset it back to stock so that you may claim that space for something better... like more games!

## Steps

1. Open a PowerShell Prompt as administrator.
   1. Tip: In modern versions of Windows, you can right-click the Windows Icon in your taskbar and select PowerShell as admin or Terminal as admin... both of these will get you where you need to be!
2. Run the following commands in this order:
   1. `Stop-Service wuauserv`
   2. `Stop-Service bits`
   3. `Get-ChildItem -Path "C:\Windows\SoftwareDistribution" | Remove-Item -Recurse -Force`
   4. `Start-Service wuauserv`
   5. `Start-Service bits`

## Conclusion

Resetting the SoftwareDistribution folder back to stock is a great way to reclaim some space for yourself on your Windows Machine. Besides also having the benefit of clearing up some issues when updates get stuck, among other things, it's also simple, fast, and satisfying!
