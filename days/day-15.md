# Day 15 – Mini automation projects

## Exercise 1: Disk cleanup report

**Question**  
Write a script that finds temporary files older than 7 days under common temp locations and produces a summary of space that could be freed (do not delete in the sample).

**Solution**
```powershell
$locations = @($env:TEMP, "C:\Windows\Temp")
$cutoff = (Get-Date).AddDays(-7)
$total = 0

foreach ($loc in $locations) {
    if (Test-Path $loc) {
        Get-ChildItem $loc -Recurse -File -ErrorAction SilentlyContinue |
            Where-Object { $_.LastWriteTime -lt $cutoff } |
            ForEach-Object { $total += $_.Length }
    }
}
Write-Host ("Potential cleanup: {0:N2} MB" -f ($total / 1MB))
```

---

## Exercise 2: Inventory script

**Question**  
Create a simple hardware/software inventory that outputs computer name, OS version, total memory, and a list of installed hotfixes (basic).

**Solution**
```powershell
[PSCustomObject]@{
    ComputerName = $env:COMPUTERNAME
    OS = (Get-CimInstance Win32_OperatingSystem).Caption
    TotalMemoryGB = [math]::Round((Get-CimInstance Win32_ComputerSystem).TotalPhysicalMemory / 1GB, 1)
    HotfixCount = (Get-HotFix).Count
}
```

---

## Exercise 3: Backup important files

**Question**  
Write a script that copies a set of important folders to a dated backup directory.

**Solution**
```powershell
param(
    [string[]]$Sources,
    [string]$BackupRoot = ".\Backups"
)

$date = Get-Date -Format "yyyy-MM-dd"
$dest = Join-Path $BackupRoot $date
New-Item -ItemType Directory -Path $dest -Force | Out-Null

foreach ($src in $Sources) {
    if (Test-Path $src) {
        $name = Split-Path $src -Leaf
        Copy-Item -Path $src -Destination (Join-Path $dest $name) -Recurse -Force
    }
}
Write-Host "Backup completed to $dest"
```

---

## Exercise 4: Health check script

**Question**  
Write a small health-check script that tests disk space, memory pressure, and a couple of critical services, then prints a simple pass/fail summary.

**Solution**
```powershell
$results = @()

# Disk
$disk = Get-PSDrive C
$freePct = [math]::Round(($disk.Free / ($disk.Used + $disk.Free)) * 100, 1)
$results += [PSCustomObject]@{Check="Disk Free %"; Value=$freePct; Status=$(if ($freePct -gt 15) {"OK"} else {"LOW"})}

# Memory (basic)
$os = Get-CimInstance Win32_OperatingSystem
$freeMemPct = [math]::Round(($os.FreePhysicalMemory / $os.TotalVisibleMemorySize) * 100, 1)
$results += [PSCustomObject]@{Check="Free Memory %"; Value=$freeMemPct; Status=$(if ($freeMemPct -gt 10) {"OK"} else {"LOW"})}

# Services
$critical = @("Spooler", "EventLog")
foreach ($svcName in $critical) {
    $svc = Get-Service -Name $svcName -ErrorAction SilentlyContinue
    $status = if ($svc -and $svc.Status -eq "Running") {"OK"} else {"FAIL"}
    $results += [PSCustomObject]@{Check="Service $svcName"; Value=$svc.Status; Status=$status}
}

$results | Format-Table -AutoSize
```
