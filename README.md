<#
.SYNOPSIS
    Downloads and silently installs the Epic Games Launcher (64-bit) on Windows.

.DESCRIPTION
    Intended for use on a freshly imaged / first-boot PC. Uses Epic's own
    "always latest" redirect link, so it always grabs the current installer
    rather than a hardcoded/stale version.
    Requires an active internet connection and admin rights (machine-wide install).

.NOTES
    Run from an elevated PowerShell prompt:
        powershell.exe -ExecutionPolicy Bypass -File .\Install-EpicGamesLauncher.ps1
#>

# Require admin rights for a machine-wide install
$currentPrincipal = New-Object Security.Principal.WindowsPrincipal([Security.Principal.WindowsIdentity]::GetCurrent())
if (-not $currentPrincipal.IsInRole([Security.Principal.WindowsBuiltInRole]::Administrator)) {
    Write-Error "This script must be run as Administrator. Right-click PowerShell and choose 'Run as administrator'."
    exit 1
}

$ErrorActionPreference = "Stop"
[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12

try {
    # Epic's redirect endpoint always points at the current installer MSI
    $installerUrl  = "https://launcher-public-service-prod06.ol.epicgames.com/launcher/api/installer/download/EpicGamesLauncherInstaller.msi"
    $installerPath = Join-Path $env:TEMP "EpicGamesLauncherInstaller.msi"

    Write-Host "Downloading the Epic Games Launcher installer..."
    Invoke-WebRequest -Uri $installerUrl -OutFile $installerPath -UseBasicParsing

    Write-Host "Installing the Epic Games Launcher silently..."
    $process = Start-Process -FilePath "msiexec.exe" -ArgumentList "/i `"$installerPath`" /qn /norestart" -Wait -PassThru

    if ($process.ExitCode -eq 0) {
        Write-Host "Epic Games Launcher installed successfully."
    } else {
        Write-Warning "Installer exited with code $($process.ExitCode)."
    }

    # Clean up the installer
    Remove-Item $installerPath -Force -ErrorAction SilentlyContinue

    # Launch the Epic Games Launcher once installed
    $epicPaths = @(
        "$env:ProgramFiles(x86)\Epic Games\Launcher\Portal\Binaries\Win64\EpicGamesLauncher.exe",
        "$env:ProgramFiles\Epic Games\Launcher\Portal\Binaries\Win64\EpicGamesLauncher.exe",
        "$env:ProgramFiles(x86)\Epic Games\Launcher\Portal\Binaries\Win32\EpicGamesLauncher.exe"
    )
    $epicExe = $epicPaths | Where-Object { Test-Path $_ } | Select-Object -First 1

    if ($epicExe) {
        Write-Host "Launching Epic Games Launcher..."
        Start-Process -FilePath $epicExe
    } else {
        Write-Warning "Could not locate EpicGamesLauncher.exe to launch it automatically."
    }
}
catch {
    Write-Error "Something went wrong: $($_.Exception.Message)"
    exit 1
}
