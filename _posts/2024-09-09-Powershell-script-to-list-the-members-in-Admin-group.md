---
layout: post
date: 2024-09-09 15:51:42
title: POershell script to list the Members in the Administrateur Group
category: powershell 
tags: powershell
---

```powershell
# Define the list of computers
$computers = @("PC1", "PC2", "PC3")  # Replace with your actual computer names

# Script block to run on each remote machine
$scriptBlock = {
    Get-LocalGroupMember -Group "Administrators" | Select-Object -Property Name, PrincipalSource
}

# Create an array to store the results
$results = @()

# Loop through each computer and invoke the command
foreach ($computer in $computers) {
    try {
        # Invoke the command on the remote computer
        $groupMembers = Invoke-Command -ComputerName $computer -ScriptBlock $scriptBlock

        # Aggregate the results as a single string
        $groupMembersList = ($groupMembers | ForEach-Object {
            $_.Name + " (Source: " + $_.PrincipalSource + ")"
        }) -join "; "  # Combine members with a semicolon delimiter

        # Add the result for this computer
        $results += [PSCustomObject]@{
            "Computer Name" = $computer
            "Result"        = $groupMembersList
        }
    } catch {
        # Handle errors and add to results
        $results += [PSCustomObject]@{
            "Computer Name" = $computer
            "Result"        = "Failed to get Administrators group members: $_"
        }
    }
}

# Output the results in a table format
$results | Format-Table -AutoSize

```
Output

```powershell
Computer Name   Result
-------------   ------
PC1             Administrator (Source: Local); JohnDoe (Source: Domain)
PC2             Administrator (Source: Local)
PC3             Failed to get Administrators group members: [Error]

```
