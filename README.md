# windows-system-optimization-scripts
📫 Contact &amp; Inquiries:      Email: shainahum12@gmail.com      GitHub: shainahum12-cloud  ☕ Crypto Donations &amp; Support  If you find this tool useful for your infrastructure, support further open-source development:      EVM / ETH / ERC-20 / USDT / USDC:      0x943c9cb2b90ed538772f9a7f5729918777e5cd9c

# ⚡ Windows System Cleanup & Performance Optimization Tool

![PowerShell](https://img.shields.io/badge/PowerShell-5.1%2B-blue.svg)
![Platform](https://img.shields.io/badge/Platform-Windows%2010%20%7C%2011%20%7C%20Server-0078D6.svg)
![License](https://img.shields.io/badge/License-Apache--2.0-orange.svg)

An automated PowerShell maintenance tool designed to clear system clutter, reset corrupt network stacks, purge bloated update caches, and optimize Windows performance.

---

## ✨ Features

* **🧹 Deep Temp Cleanup:** Safely purges user temp files, system temp directories, and Prefetch data.
* **🌐 Network Reset:** Flushes DNS resolver cache, resets Winsock, and refreshes TCP/IP stack configuration.
* **📦 Windows Update Maintenance:** Safely stops update services to clear corrupted/bloated `SoftwareDistribution` cache.
* **⚙️ DISM Component Cleanup:** Reduces `WinSxS` folder footprint using native DISM flags.

---

## ⚡ Quick Start

### One-Line Execution (PowerShell Admin)
Open PowerShell as **Administrator** and run:

```powershell
Set-ExecutionPolicy Unrestricted -Scope Process -Force
Invoke-WebRequest -Uri "[https://raw.githubusercontent.com/shainahum12-cloud/windows-system-optimization-scripts/main/Optimize-Windows.ps1](https://raw.githubusercontent.com/shainahum12-cloud/windows-system-optimization-scripts/main/Optimize-Windows.ps1)" -OutFile "Optimize-Windows.ps1"
.\Optimize-Windows.ps1
<#
.SYNOPSIS
    Automated Windows Performance Optimization & System Maintenance Script
.DESCRIPTION
    Cleans system/user temporary files, flushes DNS cache, resets network stack,
    clears Windows Update cache, and cleans component store.
.AUTHOR
    Shai Nahum (GitHub: shainahum12-cloud)
.LICENSE
    Apache-2.0
#>

# Ensure script is run with Administrator privileges
if (-not ([Security.Principal.WindowsPrincipal][Security.Principal.WindowsIdentity]::GetCurrent()).IsInRole([Security.Principal.WindowsBuiltInRole]::Administrator)) {
    Write-Host "[!] Error: This script must be run as Administrator!" -ForegroundColor Red
    Exit
}

Write-Host "==================================================" -ForegroundColor Cyan
Write-Host "   Windows Optimization Suite by Shai Nahum       " -ForegroundColor Cyan
Write-Host "==================================================" -ForegroundColor Cyan

# 1. Clear Temporary Files & Prefetch
Write-Host "`n[*] Cleaning System & User Temporary Files..." -ForegroundColor Yellow
$TempFolders = @(
    "$env:TEMP",
    "C:\Windows\Temp",
    "C:\Windows\Prefetch"
)

foreach ($Folder in$TempFolders) {
    if (Test-Path $Folder) {
        Remove-Item -Path "$Folder\*" -Recurse -Force -ErrorAction SilentlyContinue
        Write-Host "[+] Cleared: $Folder" -ForegroundColor Green
    }
}

# 2. Network Optimization & DNS Flush
Write-Host "`n[*] Flushing DNS Cache & Resetting Network Stack..." -ForegroundColor Yellow
Clear-DnsClientCache
netsh int ip reset > $null
netsh winsock reset > $null
Write-Host "[+] Network stack reset and DNS cache flushed!" -ForegroundColor Green

# 3. Clean Windows Update Cache
Write-Host "`n[*] Maintenance: Windows Update Cache..." -ForegroundColor Yellow
Stop-Service -Name wuauserv -Force -ErrorAction SilentlyContinue
Stop-Service -Name bits -Force -ErrorAction SilentlyContinue

$UpdatePath = "C:\Windows\SoftwareDistribution\Download"
if (Test-Path $UpdatePath) {
    Remove-Item -Path "$UpdatePath\*" -Recurse -Force -ErrorAction SilentlyContinue
    Write-Host "[+] Windows Update download cache cleared!" -ForegroundColor Green
}

Start-Service -Name wuauserv -ErrorAction SilentlyContinue
Start-Service -Name bits -ErrorAction SilentlyContinue

# 4. Component Store Cleanup (DISM)
Write-Host "`n[*] Running DISM Component Store Cleanup..." -ForegroundColor Yellow
Dism.exe /Online /Cleanup-Image /StartComponentCleanup /ResetBase
Write-Host "[+] Component store cleanup completed!" -ForegroundColor Green

Write-Host "`n==================================================" -ForegroundColor Cyan
Write-Host "   Optimization Complete! System is fully primed.  " -ForegroundColor Cyan
Write-Host "==================================================" -ForegroundColor Cyan

