$ErrorActionPreference = 'SilentlyContinue'
Write-Host 'Starting HP dm1-2010nr Maintenance Protocol...' -ForegroundColor Cyan

# 1. System Info Export
Write-Host 'Exporting System Specs...' -ForegroundColor Yellow
Get-WmiObject Win32_ComputerSystem | Select-Object Manufacturer, Model, TotalPhysicalMemory | Out-File 'C:\dm1_specs.txt'
Get-WmiObject Win32_Processor | Select-Object Name, MaxClockSpeed >> 'C:\dm1_specs.txt'

# 2. Power Plan Optimization for AMD Neo
Write-Host 'Optimizing Power Plan...' -ForegroundColor Yellow
POWERCFG /SETARVALUEDC 0
POWERCFG /X STANDBY-TIMEOUT-DC 15

# 3. Disk Check (Vital for spinning HDDs of this era)
Write-Host 'Checking HDD Smart Status...' -ForegroundColor Yellow
$disk = Get-WmiObject -namespace root\wmi -class MSStorageDriver_FailurePredictStatus
if ($disk.PredictFailure) { Write-Host 'CRITICAL: HDD FAILURE PREDICTED' -ForegroundColor Red } else { Write-Host 'HDD Status: OK' -ForegroundColor Green }

# 4. Temp File Cleanup
Write-Host 'Cleaning Temp Files...' -ForegroundColor Yellow
Remove-Item -Path $env:TEMP\* -Recurse -Force

# 5. Network Reset (Fixes common legacy wifi driver hang)
Write-Host 'Resetting TCP/IP Stack...' -ForegroundColor Yellow
netsh int ip reset
netsh winsock reset

Write-Host 'Protocol Complete. Reboot recommended.' -ForegroundColor Cyan