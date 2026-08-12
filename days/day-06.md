# Day 06 – Functions and modular scripts

## Exercise 1: Parameterized logging function

**Question**  
Write a function `Write-Log` that accepts a message and a level (Info, Warning, Error) and writes a timestamped entry to both the console and a log file.

**Solution**
```powershell
function Write-Log {
    param(
        [string]$Message,
        [ValidateSet("Info","Warning","Error")]
        [string]$Level = "Info",
        [string]$LogPath = "script.log"
    )
    $timestamp = Get-Date -Format "yyyy-MM-dd HH:mm:ss"
    $entry = "[$timestamp] [$Level] $Message"
    Write-Host $entry
    Add-Content -Path $LogPath -Value $entry
}
```

---

## Exercise 2: Get-FileHash summary

**Question**  
Write a function that calculates the SHA256 hash of all files in a directory and returns a list of objects containing path and hash.

**Solution**
```powershell
function Get-DirectoryHashes {
    param([string]$Path)
    Get-ChildItem -Path $Path -File |
        ForEach-Object {
            [PSCustomObject]@{
                Path = $_.FullName
                Hash = (Get-FileHash $_.FullName -Algorithm SHA256).Hash
            }
        }
}
```

---

## Exercise 3: Test-Port

**Question**  
Write a function `Test-Port` that checks whether a TCP port on a given host is open.

**Solution**
```powershell
function Test-Port {
    param(
        [string]$ComputerName = "localhost",
        [int]$Port
    )
    try {
        $tcp = New-Object System.Net.Sockets.TcpClient
        $tcp.Connect($ComputerName, $Port)
        $tcp.Close()
        return $true
    } catch {
        return $false
    }
}
```

---

## Exercise 4: Convert size units

**Question**  
Write a function that takes a number of bytes and returns a human-readable string (KB, MB, GB).

**Solution**
```powershell
function Convert-Size {
    param([long]$Bytes)
    switch ($Bytes) {
        {$_ -ge 1GB} { "{0:N2} GB" -f ($Bytes / 1GB); break }
        {$_ -ge 1MB} { "{0:N2} MB" -f ($Bytes / 1MB); break }
        {$_ -ge 1KB} { "{0:N2} KB" -f ($Bytes / 1KB); break }
        default { "$Bytes B" }
    }
}
```
