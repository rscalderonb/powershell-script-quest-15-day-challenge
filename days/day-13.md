# Day 13 – Advanced filtering

## Exercise 1: Complex Where-Object

**Question**  
Filter processes that use more than 100 MB of memory and have a company name containing "Microsoft".

**Solution**
```powershell
Get-Process |
    Where-Object {
        $_.WorkingSet64 -gt 100MB -and
        $_.Company -like "*Microsoft*"
    } |
    Select-Object Name, Company, @{N="MemMB";E={[math]::Round($_.WorkingSet64/1MB,1)}}
```

---

## Exercise 2: Calculated filter

**Question**  
List files that were modified on a weekend.

**Solution**
```powershell
Get-ChildItem -Recurse -File -ErrorAction SilentlyContinue |
    Where-Object {
        $day = $_.LastWriteTime.DayOfWeek
        $day -eq "Saturday" -or $day -eq "Sunday"
    } |
    Select-Object FullName, LastWriteTime
```

---

## Exercise 3: Multiple conditions with -and / -or

**Question**  
Find services that are either Stopped or whose name starts with "W".

**Solution**
```powershell
Get-Service |
    Where-Object {
        $_.Status -eq "Stopped" -or $_.Name -like "W*"
    } |
    Select-Object Name, Status, StartType
```

---

## Exercise 4: Exclude system processes

**Question**  
List user processes while excluding common system process names.

**Solution**
```powershell
$exclude = @("Idle", "System", "svchost", "csrss", "smss", "wininit")
Get-Process |
    Where-Object { $exclude -notcontains $_.Name } |
    Select-Object Name, Id, CPU
```
