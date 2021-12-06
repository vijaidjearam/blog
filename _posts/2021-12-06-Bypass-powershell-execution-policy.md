---
layout: post
date: 2021-12-06 16:29:00
title: Bypass powershell execution policy
category: Powershell
tags: powershell bypass
---
# The following script helps to bypass the execution policy in the powershell session
```
function Disable-ExecutionPolicy {($ctx = $executioncontext.gettype().getfield("_context","nonpublic,instance").getvalue( $executioncontext)).gettype().getfield("_authorizationManager","nonpublic,instance").setvalue($ctx, (new-object System.Management.Automation.AuthorizationManager "Microsoft.PowerShell"))}
Disable-ExecutionPolicy
```


