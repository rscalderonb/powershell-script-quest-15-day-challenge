# Day 07 – Error handling

## Exercise 1: Try/Catch with specific exceptions

**Question**  
Write a script that attempts to read a file and handles the case where the file does not exist gracefully.

**Solution**
```powershell
param([string]$Path)

try {
    $content = Get-Content -Path $Path -ErrorAction Stop
    $content
} catch [System.Management.Automation.ItemNotFoundException] {
    Write-Error "File not found: $Path"
} catch {
    Write-Error "Unexpected error: $_"
}
```

---

## Exercise 2: ErrorVariable usage

**Question**  
Demonstrate collecting non-terminating errors using `-ErrorVariable` while continuing processing.

**Solution**
```powershell
Get-ChildItem -Path C:\Windows\System32 -Filter *.dll -ErrorAction SilentlyContinue -ErrorVariable errs |
    Select-Object -First 5 Name

if ($errs) {
    Write-Host "Encountered $($errs.Count) errors"
}
```

---

## Exercise 3: Custom error record

**Question**  
Write a function that validates a parameter and throws a proper error record if validation fails.

**Solution**
```powershell
function Set-PositiveNumber {
    param([int]$Value)
    if ($Value -le 0) {
        $ex = New-Object System.ArgumentException "Value must be positive"
        $err = New-Object System.Management.Automation.ErrorRecord (
            $ex, "InvalidValue", "InvalidArgument", $Value
        )
        throw $err
    }
    $Value
}
```

---

## Exercise 4: Cleanup with finally

**Question**  
Write a script that opens a file stream, processes it, and always closes the stream even if an error occurs.

**Solution**
```powershell
$path = "example.txt"
$stream = $null
try {
    $stream = [System.IO.File]::OpenRead($path)
    # process stream
} catch {
    Write-Error $_
} finally {
    if ($stream) { $stream.Close() }
}
```
