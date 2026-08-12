# Day 12 – Logging and reporting

## Exercise 1: Simple rotating log

**Question**  
Write a function that appends a message to a log file and, if the file exceeds a size limit, renames it with a timestamp and starts a new one.

**Solution**
```powershell
function Write-RotatingLog {
    param(
        [string]$Message,
        [string]$LogPath = "app.log",
        [int]$MaxSizeMB = 5
    )
    if ((Test-Path $LogPath) -and ((Get-Item $LogPath).Length -gt $MaxSizeMB * 1MB)) {
        $archive = "$LogPath.$(Get-Date -Format 'yyyyMMddHHmmss')"
        Rename-Item $LogPath $archive
    }
    $entry = "$(Get-Date -Format o) $Message"
    Add-Content -Path $LogPath -Value $entry
}
```

---

## Exercise 2: HTML report

**Question**  
Generate a simple HTML table report from a list of objects.

**Solution**
```powershell
$processes = Get-Process | Select-Object -First 10 Name, Id, CPU
$html = $processes | ConvertTo-Html -Title "Process Report" -PreContent "<h2>Top Processes</h2>"
$html | Out-File report.html
```

---

## Exercise 3: Summary statistics

**Question**  
Write a script that produces a summary of disk space on all fixed drives.

**Solution**
```powershell
Get-PSDrive -PSProvider FileSystem |
    Where-Object { $_.Used -ne $null } |
    Select-Object Name,
        @{Name="UsedGB"; Expression={[math]::Round($_.Used / 1GB, 2)}},
        @{Name="FreeGB"; Expression={[math]::Round($_.Free / 1GB, 2)}},
        @{Name="TotalGB"; Expression={[math]::Round(($_.Used + $_.Free) / 1GB, 2)}}
```

---

## Exercise 4: Event log query (basic)

**Question**  
Retrieve the most recent 20 Error entries from the System event log.

**Solution**
```powershell
Get-WinEvent -LogName System -MaxEvents 50 |
    Where-Object { $_.LevelDisplayName -eq "Error" } |
    Select-Object -First 20 TimeCreated, Id, Message
```
