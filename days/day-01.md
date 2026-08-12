# Day 01 – File system basics

## Exercise 1: List large files

**Question**  
Write a script that lists all files larger than a given size (in MB) under a specified directory, including subdirectories. Output the full path and size in MB.

**Hints**  
Use `Get-ChildItem -Recurse -File` and filter on `Length`.

**Solution**
```powershell
param(
    [string]$Path = ".",
    [double]$MinSizeMB = 10
)

Get-ChildItem -Path $Path -Recurse -File -ErrorAction SilentlyContinue |
    Where-Object { $_.Length -gt ($MinSizeMB * 1MB) } |
    Select-Object FullName, @{Name="SizeMB"; Expression={[math]::Round($_.Length / 1MB, 2)}}
```

---

## Exercise 2: Count files by extension

**Question**  
Write a script that counts how many files of each extension exist in a directory tree and displays the results sorted by count descending.

**Solution**
```powershell
param([string]$Path = ".")

Get-ChildItem -Path $Path -Recurse -File -ErrorAction SilentlyContinue |
    Group-Object Extension |
    Sort-Object Count -Descending |
    Select-Object Name, Count
```

---

## Exercise 3: Find empty directories

**Question**  
Write a script that finds and lists all empty directories under a given path.

**Solution**
```powershell
param([string]$Path = ".")

Get-ChildItem -Path $Path -Recurse -Directory -ErrorAction SilentlyContinue |
    Where-Object { (Get-ChildItem $_.FullName -Force -ErrorAction SilentlyContinue | Measure-Object).Count -eq 0 } |
    Select-Object FullName
```

---

## Exercise 4: Copy files modified in the last N days

**Question**  
Write a script that copies all files modified in the last N days from a source folder to a destination folder, preserving the relative directory structure.

**Solution**
```powershell
param(
    [string]$Source,
    [string]$Destination,
    [int]$Days = 7
)

$cutoff = (Get-Date).AddDays(-$Days)

Get-ChildItem -Path $Source -Recurse -File |
    Where-Object { $_.LastWriteTime -ge $cutoff } |
    ForEach-Object {
        $relative = $_.FullName.Substring($Source.Length).TrimStart('\')
        $target = Join-Path $Destination $relative
        $targetDir = Split-Path $target -Parent
        if (-not (Test-Path $targetDir)) {
            New-Item -ItemType Directory -Path $targetDir -Force | Out-Null
        }
        Copy-Item $_.FullName -Destination $target -Force
    }
```
