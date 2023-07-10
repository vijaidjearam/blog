---
layout: post
date: 2023-01-11 08:39:45
title: Chocolatey Softwares install commands
category: chocolatey
tags: chocolatey
---

# Chocolatey Interne software install commands

## Chocolatey Features

```
choco feature enable -n=useRememberedArgumentsForUpgrades
```


## Sifac

```
choco install -y sifac sifac-patch sifac-settings
```

## celcat

```
choco install -y celcat
```

## celcat et smartrapport

```
choco install -y celcat ctlicensemanager smartrapport
```

## Fog client

```
choco install fog-client-urca -y --params "'/server:fog-gmp.local.fr'"
```

## Microsoft Teams

```
choco install microsoft-teams.install --params "'/AllUsers /NoAutoStart'"
```
