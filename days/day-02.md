# Day 02 – Text processing

## Exercise 1: Extract unique email addresses

**Question**  
Write a script that reads a text file and extracts all unique email addresses found in it.

**Solution**
```powershell
param([string]$Path)

$content = Get-Content -Path $Path -Raw
$pattern = '\b[A-Za-z0-9._%+-]+@[A-Za-z0-9.-]+\.[A-Za-z]{2,}\b'
[regex]::Matches($content, $pattern) |
    ForEach-Object { $_.Value } |
    Sort-Object -Unique
```

---

## Exercise 2: Replace text across multiple files

**Question**  
Write a script that replaces a given string with another string in all `.txt` files under a directory (including subdirectories).

**Solution**
```powershell
param(
    [string]$Path,
    [string]$Find,
    [string]$Replace
)

Get-ChildItem -Path $Path -Recurse -Filter *.txt |
    ForEach-Object {
        (Get-Content $_.FullName -Raw) -replace [regex]::Escape($Find), $Replace |
            Set-Content -Path $_.FullName -NoNewline
    }
```

---

## Exercise 3: Count word frequency

**Question**  
Write a script that counts the frequency of each word in a text file (case-insensitive) and displays the top 20 most common words.

**Solution**
```powershell
param([string]$Path)

Get-Content -Path $Path |
    ForEach-Object { $_.ToLower() -split '\W+' } |
    Where-Object { $_ -ne '' } |
    Group-Object |
    Sort-Object Count -Descending |
    Select-Object -First 20 Name, Count
```

---

## Exercise 4: Remove blank lines

**Question**  
Write a script that removes all blank lines from a text file and saves the result to a new file.

**Solution**
```powershell
param(
    [string]$InputPath,
    [string]$OutputPath
)

Get-Content -Path $InputPath |
    Where-Object { $_.Trim() -ne '' } |
    Set-Content -Path $OutputPath
```
