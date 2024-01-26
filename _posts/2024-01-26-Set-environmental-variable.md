---
layout: post
date: 2024-01-26 10:51:36
title: Set Environment varaiable 
category: Environmentvariable
tags: environmentvariable windows10
---

# To apply settings to the current user session
The set commands sets the variable but its not persistant after reboot

```batch
set [variable_name] “[variable_value]”
```

The setx command sets the variable that is persistant even after reboot

```batch
setx [variable_name] “[variable_value]”
```

# To apply the setting to System wide use the switch /M

```batch
setx [variable_name] “[variable_value]” /M
ex:
setx ADSKFLEX_LICENSE_FILE 27001@server-flexlm /M
```
