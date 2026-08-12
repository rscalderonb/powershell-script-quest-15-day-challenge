# Day 03 – Process and service information

## Exercise 1: Top memory consumers

**Question**  
Write a script that lists the top 10 processes by working set (memory) usage.

**Solution**
```powershell
Get-Process |
    Sort-Object WorkingSet64 -Descending |
    Select-Object -First 10 Name, Id, @{Name="MemoryMB"; Expression={[math]::Round($_.WorkingSet64 / 1MB, 1)}}
```

---

## Exercise 2: Find processes by name pattern

**Question**  
Write a script that finds all processes whose name matches a given pattern (wildcards allowed) and shows name, ID, and start time.

**Solution**
```powershell
param([string]$Pattern = "*")

Get-Process -Name $Pattern -ErrorAction SilentlyContinue |
    Select-Object Name, Id, StartTime
```

---

## Exercise 3: Service status report

**Question**  
Write a script that lists all services that are set to Automatic but are currently not running.

**Solution**
```powershell
Get-Service |
    Where-Object { $_.StartType -eq 'Automatic' -and $_.Status -ne 'Running' } |
    Select-Object Name, DisplayName, Status, StartType
```

---

## Exercise 4: Kill process by port (Windows)

**Question**  
Write a script that finds the process listening on a given TCP port and offers to stop it (or just lists the information safely).

**Solution**
```powershell
param([int]$Port)

$connection = Get-NetTCPConnection -LocalPort $Port -ErrorAction SilentlyContinue |
    Where-Object { $_.State -eq 'Listen' }

if ($connection) {
    $proc = Get-Process -Id $connection.OwningProcess -ErrorAction SilentlyContinue
    $proc | Select-Object Name, Id, Path
} else {
    Write-Output "No process found listening on port $Port"
}
```
