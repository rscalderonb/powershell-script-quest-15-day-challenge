# Day 04 – Working with objects and pipelines

## Exercise 1: Select and rename properties

**Question**  
Write a pipeline that takes the output of `Get-ChildItem` and produces objects with only `Name`, `SizeKB`, and `LastModified` properties.

**Solution**
```powershell
Get-ChildItem -File |
    Select-Object Name,
        @{Name="SizeKB"; Expression={[math]::Round($_.Length / 1KB, 1)}},
        @{Name="LastModified"; Expression={$_.LastWriteTime}}
```

---

## Exercise 2: Group and measure

**Question**  
Write a script that groups processes by company name (if available) and shows the count and total memory usage per company.

**Solution**
```powershell
Get-Process |
    Where-Object { $_.Company } |
    Group-Object Company |
    Select-Object Name, Count,
        @{Name="TotalMemoryMB"; Expression={
            [math]::Round(($_.Group | Measure-Object WorkingSet64 -Sum).Sum / 1MB, 1)
        }}
```

---

## Exercise 3: Custom calculated properties

**Question**  
Create a report of files that includes a property indicating whether the file is older than 30 days.

**Solution**
```powershell
$cutoff = (Get-Date).AddDays(-30)
Get-ChildItem -File |
    Select-Object Name, Length, LastWriteTime,
        @{Name="IsOld"; Expression={$_.LastWriteTime -lt $cutoff}}
```

---

## Exercise 4: Filter and export

**Question**  
Write a script that finds all `.log` files larger than 5 MB, selects relevant properties, and exports the result to CSV.

**Solution**
```powershell
param(
    [string]$Path = ".",
    [string]$Output = "large-logs.csv"
)

Get-ChildItem -Path $Path -Recurse -Filter *.log -File -ErrorAction SilentlyContinue |
    Where-Object { $_.Length -gt 5MB } |
    Select-Object FullName, Length, LastWriteTime |
    Export-Csv -Path $Output -NoTypeInformation
```
