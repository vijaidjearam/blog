---
layout: post
date: 2025-09-15 14:10:53
title: Turn off Bitlocker and check the status of the decrytion.
category: bitlocker
tags: bitlocker windows
---

Here is the powershell script that turns off Bitlocker and checks the status of the decryptions.

```powershell
# Disable BitLocker on C: and monitor decryption progress
$DriveLetter = "C:"

Write-Host "Starting BitLocker decryption on drive $DriveLetter ..." -ForegroundColor Cyan

# Start decryption (ignore errors if already decrypted)
try {
    manage-bde -off $DriveLetter | Out-Null
} catch {
    Write-Host "BitLocker may already be off on $DriveLetter." -ForegroundColor Yellow
}

Write-Host "Monitoring decryption progress. Press Ctrl+C to cancel." -ForegroundColor Green

# Loop until decryption is done
do {
    $status = manage-bde -status $DriveLetter

    $conversion = ($status | Select-String "Conversion Status").ToString().Split(":")[1].Trim()
    $percentEnc = ($status | Select-String "Percentage Encrypted").ToString().Split(":")[1].Trim()

    Write-Host "[$(Get-Date -Format 'HH:mm:ss')] Conversion: $conversion | Encrypted: $percentEnc" -ForegroundColor White

    if ($conversion -eq "Fully Decrypted" -and $percentEnc -eq "0%") {
        Write-Host "✅ Drive $DriveLetter is fully decrypted!" -ForegroundColor Green
        break
    }

    Start-Sleep -Seconds 60  # adjust polling interval if you want faster/slower checks
} while ($true)

```
