---
date: 2026-09-01 12:42:00
title: Deploying PowerBGInfo
category: Powershell
tags: script powershell windows 
---
# PowerBGInfo Deployment & Usage Documentation

## Overview

This solution deploys a **code-signed** PowerBGInfo module and configuration script to remote machines, then applies BGInfo (hostname + IP) to both the desktop background and logon screen for all users, running as SYSTEM via a manually-triggered scheduled task.

---

## Prerequisites

- Code-signing certificate (`.pfx` with private key) available on the admin/deployment machine
- Public certificate (`.cer`) trusted on target machines (imported into `Cert:\LocalMachine\Root` and/or `TrustedPublisher`)
- Signed `PowerBGInfo` module and signed `Set-BGInfo.ps1` on the local admin PC
- Admin rights (admin\$ shares) on all target machines
- WinRM enabled on targets (for `Invoke-Command`)
- `PsExec` available if used as an alternative trigger method

---

## 0. Code Signing Procedure (Prerequisite / One-Time Setup)

### 0.1 Create a Code Signing Certificate

**Option A — Self-signed (test/internal lab use)**

```powershell
$cert = New-SelfSignedCertificate -Type CodeSigningCert `
    -Subject "CN=PowerBGInfo Code Signing" `
    -CertStoreLocation Cert:\CurrentUser\My `
    -KeyExportPolicy Exportable `
    -KeyUsage DigitalSignature `
    -KeyAlgorithm RSA -KeyLength 2048 `
    -NotAfter (Get-Date).AddYears(3)
```

**Option B — Enterprise CA (production/domain environments)**

Request a **Code Signing** certificate template from your internal CA (e.g., via `certreq` or the Certificates MMC snap-in), or purchase one from a public CA if binaries/scripts need to be trusted outside your org.

### 0.2 Export the certificate to `.pfx` (private key) and `.cer` (public key)

```powershell
$pwd = Read-Host -Prompt 'Set PFX export password' -AsSecureString

# Private key (keep secure — used for signing)
Export-PfxCertificate -Cert $cert `
    -FilePath 'C:\Secure\PowerBGInfo.pfx' `
    -Password $pwd

# Public key (distributed to targets for trust)
Export-Certificate -Cert $cert `
    -FilePath 'C:\Secure\PowerBGInfo.cer'
```

> Store the `.pfx` somewhere access-controlled (e.g., a vault or restricted-ACL folder) — anyone with it and the password can sign as you.

### 0.3 Trust the public certificate on target machines

The `.cer` must be trusted in **both** stores on every target so Windows accepts the signature and treats the publisher as trusted:

```powershell
foreach ($pc in $ComputerName) {
    Invoke-Command -ComputerName $pc -ScriptBlock {
        param($CerPath)
        Import-Certificate -FilePath $CerPath -CertStoreLocation Cert:\LocalMachine\Root
        Import-Certificate -FilePath $CerPath -CertStoreLocation Cert:\LocalMachine\TrustedPublisher
    } -ArgumentList $using:CerRemoteCopyPath
}
```

Since targets can't reach a local file path directly, copy the `.cer` first:

```powershell
foreach ($pc in $ComputerName) {
    $dest = "\\$pc\C$\Temp\PowerBGInfo.cer"
    Copy-Item 'C:\Secure\PowerBGInfo.cer' -Destination $dest -Force

    Invoke-Command -ComputerName $pc -ScriptBlock {
        Import-Certificate -FilePath 'C:\Temp\PowerBGInfo.cer' -CertStoreLocation Cert:\LocalMachine\Root
        Import-Certificate -FilePath 'C:\Temp\PowerBGInfo.cer' -CertStoreLocation Cert:\LocalMachine\TrustedPublisher
    }
}
```

### 0.4 Set Execution Policy to honor signatures (if not already)

```powershell
foreach ($pc in $ComputerName) {
    Invoke-Command -ComputerName $pc -ScriptBlock {
        Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope LocalMachine -Force
    }
}
```

`RemoteSigned` allows locally-authored (or already-trusted) signed scripts to run — matching the scheduled task's execution policy flag used later.

---

### 0.5 Sign the module and script locally (Updated)

```powershell
$cert = Get-PfxCertificate -FilePath 'C:\Secure\PowerBGInfo.pfx'   # prompts for password
# or on PS7+: Get-PfxCertificate -FilePath ... -Password $pwd

# Sign every script file in the module (psm1, psd1, ps1)
Get-ChildItem 'C:\Program Files\WindowsPowerShell\Modules\PowerBGInfo' -Recurse -Include *.ps1,*.psm1,*.psd1 |
    ForEach-Object { Set-AuthenticodeSignature -FilePath $_.FullName -Certificate $cert }

# Sign the deployment script
Set-AuthenticodeSignature -FilePath 'C:\ProgramData\PowerBGInfo\Set-BGInfo.ps1' -Certificate $cert

Get-AuthenticodeSignature 'C:\ProgramData\PowerBGInfo\Set-BGInfo.ps1' | Format-List Path, Status, StatusMessage
```

Confirm `Status : Valid` for each file before deploying.

> **Only after this signing/trust setup is complete** should you proceed to Sections 1–3 (copy, deploy, trigger) below — otherwise the scheduled task will fail to run the script under `RemoteSigned` policy.

---

> **Only after this signing/trust setup is complete** should you proceed to Sections 1–3 (copy, deploy, trigger) below — otherwise the scheduled task will fail to run the script under `RemoteSigned` policy.

---

## 1. Initial Preparation (One-Time / Per-Deployment)

### 1.1 Copy the signed PowerShell module to remote machines

```powershell
$LocalModulePath = 'C:\Program Files\WindowsPowerShell\Modules\PowerBGInfo'

foreach ($pc in $ComputerName) {
    $destModule = "\\$pc\C$\Program Files\WindowsPowerShell\Modules\PowerBGInfo"
    robocopy $LocalModulePath $destModule /MIR /R:2 /W:2 /NFL /NDL /NJH /NJS
    if ($LASTEXITCODE -ge 8) { throw "robocopy (module) failed on $pc (exit $LASTEXITCODE)" }
}
```

### 1.2 Copy the signed `Set-BGInfo.ps1` script to remote machines

```powershell
$LocalScriptPath = 'C:\ProgramData\PowerBGInfo\Set-BGInfo.ps1'

foreach ($pc in $ComputerName) {
    $destDir = "\\$pc\C$\ProgramData\PowerBGInfo"
    if (-not (Test-Path $destDir)) { New-Item -Path $destDir -ItemType Directory -Force | Out-Null }
    robocopy 'C:\ProgramData\PowerBGInfo' $destDir 'Set-BGInfo.ps1' /R:2 /W:2 /NFL /NDL /NJH /NJS
    if ($LASTEXITCODE -ge 8) { throw "robocopy (script) failed on $pc (exit $LASTEXITCODE)" }
}
```

> Both the module and `Set-BGInfo.ps1` **must already be signed locally** before copying — signatures are preserved by robocopy since file bytes are copied as-is.

### 1.3 Register a manual-trigger Scheduled Task (runs as SYSTEM)

```powershell
foreach ($pc in $ComputerName) {
    Invoke-Command -ComputerName $pc -ScriptBlock {
        $action  = New-ScheduledTaskAction -Execute 'powershell.exe' `
                    -Argument '-NoProfile -ExecutionPolicy RemoteSigned -File "C:\ProgramData\PowerBGInfo\Set-BGInfo.ps1"'
        $principal = New-ScheduledTaskPrincipal -UserId 'SYSTEM' -LogonType ServiceAccount -RunLevel Highest
        $settings  = New-ScheduledTaskSettingsSet -AllowStartIfOnBatteries -DontStopIfGoingOnBatteries

        Register-ScheduledTask -TaskName 'PowerBGInfo' `
            -Action $action -Principal $principal -Settings $settings -Force
    }
}
```

> No `-Trigger` is specified — the task exists but only runs when explicitly started (manual trigger), matching the "apply BGInfo on demand" requirement.

### `Set-BGInfo.ps1` content (deployed to each target)

```powershell
$hostName = $env:COMPUTERNAME
$ip = (Get-NetIPAddress -AddressFamily IPv4 |
        Where-Object { $_.InterfaceAlias -notlike '*Loopback*' -and $_.IPAddress -notlike '169.254.*' } |
        Select-Object -First 1 -ExpandProperty IPAddress)

New-BGInfo -Target Both -AllUsers -MonitorIndex 0 -ConfigurationDirectory 'C:\ProgramData\PowerBGInfo' -TextPosition TopRight {
    New-BGInfoValue -BuiltinValue HostName -Name 'Computer' -Color White -FontSize 20 -FontFamilyName 'Calibri'
    New-BGInfoValue -Name 'IP' -Value $ip -Color White -FontSize 20 -FontFamilyName 'Calibri'
}
```

- `-Target Both` → applies to desktop wallpaper **and** logon screen
- `-AllUsers` → visible for every user profile on the machine
- `-TextPosition TopRight` → hostname/IP rendered top-right instead of default top-left
- Values are computed dynamically at run time (`$env:COMPUTERNAME`, live IP lookup) — no hardcoding per machine

---

## 2. Apply BGInfo (Trigger Display)

Once preparation is complete, trigger the scheduled task remotely to render BGInfo immediately:

```powershell
Invoke-Command -ComputerName $pcs -ScriptBlock {
    Start-ScheduledTask -TaskName 'PowerBGInfo'
}
```

- Runs the task **as SYSTEM** on each target in `$pcs`
- Regenerates the wallpaper and logon screen background using the current hostname/IP
- Can be re-run any time (e.g., after IP changes, DHCP renewal, or hostname change) without redeploying files

### Alternative: PsExec-based trigger (if WinRM is unavailable)

```powershell
foreach ($pc in $pcs) {
    psexec \\$pc -s schtasks /run /tn "PowerBGInfo"
}
```

---

## 3. Full Deployment Script (End-to-End)

```powershell
[CmdletBinding()]
param(
    [Parameter(Mandatory)][string[]]$ComputerName,
    [string]$LocalModulePath = 'C:\Program Files\WindowsPowerShell\Modules\PowerBGInfo',
    [string]$LocalDataPath   = 'C:\ProgramData\PowerBGInfo'
)

$ErrorActionPreference = 'Stop'

$ScriptName = 'Set-BGInfo.ps1'
$CertName   = 'PowerBGInfo.cer'

# --- Pre-flight: verify local files exist and are validly signed ---
$localScript = Join-Path $LocalDataPath $ScriptName
$localCert   = Join-Path $LocalDataPath $CertName

foreach ($f in @($localScript, $localCert)) {
    if (-not (Test-Path -LiteralPath $f)) { throw "Missing local file: $f" }
}

$bad = Get-ChildItem $LocalModulePath -Recurse -Include *.ps1,*.psm1,*.psd1 |
    Get-AuthenticodeSignature |
    Where-Object Status -ne 'Valid'

if ($bad) {
    $bad | Format-Table Path, Status | Out-String | Write-Warning
    throw "Module contains unsigned/invalid files. Sign them before deploying."
}

if ((Get-AuthenticodeSignature $localScript).Status -ne 'Valid') {
    throw "$ScriptName is not validly signed."
}

Write-Host "Pre-flight OK: module + script signatures valid." -ForegroundColor Green


foreach ($pc in $ComputerName) {
    Write-Host "=== $pc ===" -ForegroundColor Cyan
    try {
        if (-not (Test-Connection -ComputerName $pc -Count 1 -Quiet)) {
            throw "Host unreachable"
        }

        $remoteData = "\\$pc\C$\ProgramData\PowerBGInfo"

        # --- 1. Copy signed module to remote PowerShell module directory ---
        robocopy $LocalModulePath `
                 "\\$pc\C$\Program Files\WindowsPowerShell\Modules\PowerBGInfo" `
                 /MIR /R:2 /W:2 /NFL /NDL /NJH /NJS
        if ($LASTEXITCODE -ge 8) { throw "robocopy (module) failed: exit $LASTEXITCODE" }

        # --- 2 & 3. Copy signed Set-BGInfo.ps1 and the .cer ---
        New-Item -Path $remoteData -ItemType Directory -Force | Out-Null
        robocopy $LocalDataPath $remoteData $ScriptName $CertName `
                 /R:2 /W:2 /NFL /NDL /NJH /NJS
        if ($LASTEXITCODE -ge 8) { throw "robocopy (data) failed: exit $LASTEXITCODE" }

        # --- 3. Import cert into LocalMachine Root + TrustedPublisher ---
        $certCmd = "Import-Certificate -FilePath 'C:\ProgramData\PowerBGInfo\$CertName' -CertStoreLocation Cert:\LocalMachine\Root; " +
                   "Import-Certificate -FilePath 'C:\ProgramData\PowerBGInfo\$CertName' -CertStoreLocation Cert:\LocalMachine\TrustedPublisher"

        psexec "\\$pc" -s -accepteula -nobanner powershell.exe -NoProfile -Command $certCmd
        if ($LASTEXITCODE -ne 0) { throw "Certificate import failed: exit $LASTEXITCODE" }

        # --- 4. Clear Mark-of-the-Web, register manual-trigger task ---
        Invoke-Command -ComputerName $pc -ScriptBlock {
            $dir  = 'C:\ProgramData\PowerBGInfo'
            $path = Join-Path $dir 'Set-BGInfo.ps1'

            Unblock-File -Path "$dir\*.ps1" -ErrorAction SilentlyContinue
            Get-ChildItem 'C:\Program Files\WindowsPowerShell\Modules\PowerBGInfo' -Recurse `
                -Include *.ps1,*.psm1,*.psd1 | Unblock-File -ErrorAction SilentlyContinue

            $sig = Get-AuthenticodeSignature $path
            if ($sig.Status -ne 'Valid') { throw "Signature invalid on target: $($sig.Status)" }

            $action = New-ScheduledTaskAction -Execute 'powershell.exe' `
                -Argument "-NoProfile -WindowStyle Hidden -File `"$path`""
            $principal = New-ScheduledTaskPrincipal -UserId 'SYSTEM' `
                -LogonType ServiceAccount -RunLevel Highest

            Register-ScheduledTask -TaskName 'PowerBGInfo' -Action $action `
                -Principal $principal `
                -Description 'Applies BGInfo to desktop and logon screen' -Force | Out-Null

            [pscustomobject]@{
                Computer  = $env:COMPUTERNAME
                Signature = $sig.Status
                Publisher = [bool](Get-ChildItem Cert:\LocalMachine\TrustedPublisher |
                                Where-Object Subject -like '*PowerBGInfo*')
                Task      = 'Registered'
            }
        }
    }
    catch {
        Write-Warning "$pc : $($_.Exception.Message)"
    }
    # --- Apply BGInfo on all successfully deployed machines ---
    Invoke-Command -ComputerName $ComputerName -ScriptBlock {Start-ScheduledTask -TaskName 'PowerBGInfo'}
}


```
To Deploy the script use the following command
```powershell
.\Deploy-PowerBGInfo.ps1 -ComputerName 0123-0A01234001
```
---
## 3.1 Triggering the script on the Remote machine
### --- Apply BGInfo on all successfully deployed machines ---
```powershell
Invoke-Command -ComputerName $ComputerName -ScriptBlock {
    Start-ScheduledTask -TaskName 'PowerBGInfo'
}
```

---

## 4. Re-signing Workflow (After Editing Scripts)

Whenever `Set-BGInfo.ps1` (or the module) is edited:

```powershell
$securePwd = Read-Host -Prompt 'Enter PFX password' -AsSecureString
$cert = Get-PfxCertificate -FilePath 'C:\Secure\PowerBGInfo.pfx'   # or -Password $securePwd on PS7+

Set-AuthenticodeSignature -FilePath 'C:\ProgramData\PowerBGInfo\Set-BGInfo.ps1' -Certificate $cert
Get-AuthenticodeSignature 'C:\ProgramData\PowerBGInfo\Set-BGInfo.ps1' | Format-List Status, StatusMessage
```

Confirm `Status : Valid`, then redeploy using Section 1/3 above. No cert re-trust needed on targets since the thumbprint/public key is unchanged (Section 0.3 is only needed once, or again only if you rotate/renew the certificate).

---

## Summary of Commands

| Step | Command |
|---|---|
| Create cert | `New-SelfSignedCertificate -Type CodeSigningCert ...` |
| Export cert | `Export-PfxCertificate` / `Export-Certificate` |
| Trust cert on targets | `Import-Certificate -CertStoreLocation Cert:\LocalMachine\Root/TrustedPublisher` |
| Set execution policy | `Set-ExecutionPolicy -ExecutionPolicy RemoteSigned` |
| Sign files | `Set-AuthenticodeSignature -FilePath ... -Certificate $cert` |
| Copy module | `robocopy $LocalModulePath \\$pc\C$\...\Modules\PowerBGInfo /MIR` |
| Copy script | `robocopy ... Set-BGInfo.ps1` |
| Register task | `Register-ScheduledTask -TaskName 'PowerBGInfo' ...` (no trigger = manual) |
| **Apply BGInfo** | `Invoke-Command -ComputerName $pcs -ScriptBlock { Start-ScheduledTask -TaskName 'PowerBGInfo' }` |
| Re-sign after edits | `Set-AuthenticodeSignature -FilePath ... -Certificate $cert` |



