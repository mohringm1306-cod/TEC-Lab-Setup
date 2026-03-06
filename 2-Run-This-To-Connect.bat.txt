@echo off
setlocal enabledelayedexpansion
title TEC Lab Remote Desktop - College of DuPage

echo.
echo  ============================================================
echo.
echo         TEC Lab Remote Desktop Setup
echo         College of DuPage
echo.
echo  ============================================================
echo.
echo  STOP! Did you set up Microsoft Authenticator?
echo.
echo  If NOT, open "0-Instructions-With-Pictures.html" first!
echo  It has step-by-step screenshots showing exactly what to do.
echo.
echo  Your sign-in method MUST be set to "notification" (not code)
echo  or HP Anyware will NOT work!
echo.
echo  ============================================================
echo.
echo  Press any key if you have already set up Authenticator...
pause >nul

:: ============================================================
:: Check if HP Anyware is already installed
:: ============================================================
set "CLIENT_PATH="
set "CLI_PATH="

if exist "%ProgramFiles(x86)%\Teradici\PCoIP Client\bin\pcoip_client.exe" (
    set "CLIENT_PATH=%ProgramFiles(x86)%\Teradici\PCoIP Client\bin\pcoip_client.exe"
    set "CLI_PATH=%ProgramFiles(x86)%\Teradici\PCoIP Client\bin\pcoip_client_cli.exe"
)
if exist "%ProgramFiles%\Teradici\PCoIP Client\bin\pcoip_client.exe" (
    set "CLIENT_PATH=%ProgramFiles%\Teradici\PCoIP Client\bin\pcoip_client.exe"
    set "CLI_PATH=%ProgramFiles%\Teradici\PCoIP Client\bin\pcoip_client_cli.exe"
)

if defined CLIENT_PATH (
    echo.
    echo  HP Anyware is already installed. Configuring and connecting...
    goto :configure
)

:: ============================================================
:: Download and install HP Anyware
:: ============================================================
echo.
echo  HP Anyware is not installed. Downloading now...
echo  This may take a few minutes (~60MB download).
echo.

set "INSTALLER=%TEMP%\pcoip-client_latest.exe"
set "DOWNLOAD_URL=https://dl.anyware.hp.com/DeAdBCiUYInHcSTy/pcoip-client/raw/names/pcoip-client-exe/versions/latest/pcoip-client_latest.exe"

:: Use PowerShell to download (handles TLS 1.2 properly)
powershell -NoProfile -ExecutionPolicy Bypass -Command ^
    "[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12; " ^
    "$ProgressPreference = 'SilentlyContinue'; " ^
    "Write-Host '  Downloading from HP...'; " ^
    "try { " ^
    "  Invoke-WebRequest -Uri '%DOWNLOAD_URL%' -OutFile '%INSTALLER%' -UseBasicParsing; " ^
    "  Write-Host '  Download complete!' -ForegroundColor Green " ^
    "} catch { " ^
    "  Write-Host '  Download failed. Check your internet connection.' -ForegroundColor Red; " ^
    "  exit 1 " ^
    "}"

if not exist "%INSTALLER%" (
    echo.
    echo  ERROR: Download failed. Please check your internet connection.
    echo.
    pause
    exit /b 1
)

echo.
echo  Installing HP Anyware...
echo  Please click YES on any administrator prompts that appear.
echo.

:: Run installer silently with elevation
powershell -NoProfile -ExecutionPolicy Bypass -Command ^
    "Start-Process -FilePath '%INSTALLER%' -ArgumentList '/S' -Verb RunAs -Wait"

:: Wait for installation to settle
timeout /t 5 /nobreak >nul

:: Clean up installer
del "%INSTALLER%" >nul 2>&1

:: Re-check installation
if exist "%ProgramFiles(x86)%\Teradici\PCoIP Client\bin\pcoip_client.exe" (
    set "CLIENT_PATH=%ProgramFiles(x86)%\Teradici\PCoIP Client\bin\pcoip_client.exe"
    set "CLI_PATH=%ProgramFiles(x86)%\Teradici\PCoIP Client\bin\pcoip_client_cli.exe"
)
if exist "%ProgramFiles%\Teradici\PCoIP Client\bin\pcoip_client.exe" (
    set "CLIENT_PATH=%ProgramFiles%\Teradici\PCoIP Client\bin\pcoip_client.exe"
    set "CLI_PATH=%ProgramFiles%\Teradici\PCoIP Client\bin\pcoip_client_cli.exe"
)

if not defined CLIENT_PATH (
    echo.
    echo  ERROR: Installation completed but HP Anyware was not found.
    echo  Please restart your computer and try again.
    echo.
    pause
    exit /b 1
)

echo.
echo  HP Anyware installed successfully!
echo.

:: ============================================================
:: Configure security mode and connection
:: ============================================================
:configure

echo  Configuring settings...

:: Set security mode to Low (required for COD) and create TEC Lab connection
powershell -NoProfile -ExecutionPolicy Bypass -Command ^
    "$null = New-Item -Path 'HKCU:\Software\Teradici\PCoIP Client' -Force -EA SilentlyContinue; " ^
    "Set-ItemProperty -Path 'HKCU:\Software\Teradici\PCoIP Client' -Name 'SecurityMode' -Value 2 -Type DWord -Force; " ^
    "$null = New-Item -Path 'HKCU:\Software\Teradici\PCoIP Client\Settings' -Force -EA SilentlyContinue; " ^
    "Set-ItemProperty -Path 'HKCU:\Software\Teradici\PCoIP Client\Settings' -Name 'SecurityMode' -Value 2 -Type DWord -Force; " ^
    "$null = New-Item -Path 'HKCU:\Software\Teradici\PCoIP Client\Connections\TEC Lab' -Force -EA SilentlyContinue; " ^
    "Set-ItemProperty -Path 'HKCU:\Software\Teradici\PCoIP Client\Connections\TEC Lab' -Name 'HostAddress' -Value 'hpanyware.cod.edu' -Type String -Force; " ^
    "Set-ItemProperty -Path 'HKCU:\Software\Teradici\PCoIP Client\Connections\TEC Lab' -Name 'ConnectionName' -Value 'TEC Lab' -Type String -Force; " ^
    "$d = Join-Path ([Environment]::GetFolderPath('ApplicationData')) 'Teradici'; " ^
    "if (-not (Test-Path $d)) { $null = mkdir $d -Force }; " ^
    "[IO.File]::WriteAllText((Join-Path $d 'Teradici PCoIP Client.ini'), [string]::Concat('[General]', [char]13, [char]10, 'security_mode=2', [char]13, [char]10)); " ^
    "Write-Host '  Settings configured.' -ForegroundColor Green"

:: ============================================================
:: Create desktop shortcut
:: ============================================================
powershell -NoProfile -ExecutionPolicy Bypass -Command ^
    "$cli = @('${env:ProgramFiles(x86)}\Teradici\PCoIP Client\bin\pcoip_client_cli.exe','${env:ProgramFiles}\Teradici\PCoIP Client\bin\pcoip_client_cli.exe') | Where-Object { Test-Path $_ } | Select-Object -First 1; " ^
    "if ($cli) { " ^
    "  $lnk = Join-Path ([Environment]::GetFolderPath('Desktop')) 'TEC Lab.lnk'; " ^
    "  if (-not (Test-Path $lnk)) { " ^
    "    $ws = New-Object -ComObject WScript.Shell; " ^
    "    $s = $ws.CreateShortcut($lnk); " ^
    "    $s.TargetPath = $cli; " ^
    "    $s.Arguments = '--connection-broker \"hpanyware.cod.edu\"'; " ^
    "    $s.WorkingDirectory = Split-Path $cli; " ^
    "    $s.Description = 'Connect to TEC Lab'; " ^
    "    $s.Save(); " ^
    "    Write-Host '  Desktop shortcut created.' -ForegroundColor Green " ^
    "  } else { Write-Host '  Desktop shortcut already exists.' -ForegroundColor Green } " ^
    "}"

:: ============================================================
:: Launch HP Anyware
:: ============================================================
echo.
echo  Launching HP Anyware and connecting to TEC Lab...
echo.

if defined CLI_PATH (
    start "" "!CLI_PATH!" --connection-broker "hpanyware.cod.edu"
) else if defined CLIENT_PATH (
    start "" "!CLIENT_PATH!"
)

echo  ============================================================
echo.
echo  Done! A "TEC Lab" shortcut has been created on your desktop.
echo.
echo  Next time, just double-click the "TEC Lab" shortcut to connect.
echo.
echo  ============================================================
echo.
pause
