# Day 08 – CSV and structured data

## Exercise 1: Import and filter CSV

**Question**  
Write a script that imports a CSV and returns only the rows where a numeric column exceeds a threshold.

**Solution**
```powershell
param(
    [string]$Path,
    [string]$Column,
    [double]$Threshold
)

Import-Csv -Path $Path |
    Where-Object { [double]$_.$Column -gt $Threshold }
```

---

## Exercise 2: Merge two CSV files

**Question**  
Write a script that merges two CSV files that share a common key column.

**Solution**
```powershell
param(
    [string]$File1,
    [string]$File2,
    [string]$Key
)

$left = Import-Csv $File1
$right = Import-Csv $File2 | Group-Object $Key -AsHashTable -AsString

$left | ForEach-Object {
    $keyValue = $_.$Key
    $match = $right[$keyValue]
    if ($match) {
        $_ | Add-Member -NotePropertyMembers ($match[0] | Select-Object * -ExcludeProperty $Key) -PassThru
    } else {
        $_
    }
}
```

---

## Exercise 3: Export selected properties

**Question**  
Export a filtered list of processes to CSV with only Name, Id, and CPU.

**Solution**
```powershell
Get-Process |
    Select-Object Name, Id, CPU |
    Export-Csv -Path processes.csv -NoTypeInformation
```

---

## Exercise 4: Convert CSV to JSON

**Question**  
Write a one-liner or short script that converts a CSV file to a JSON file.

**Solution**
```powershell
param(
    [string]$CsvPath,
    [string]$JsonPath
)

Import-Csv $CsvPath | ConvertTo-Json | Set-Content $JsonPath
```
