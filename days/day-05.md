# Day 05 – Conditional logic and loops

## Exercise 1: Simple menu

**Question**  
Write a script that presents a text menu with three options and performs a different action based on the user’s choice. Loop until the user chooses to exit.

**Solution**
```powershell
do {
    Write-Host "1. Show date"
    Write-Host "2. Show processes"
    Write-Host "3. Exit"
    $choice = Read-Host "Select option"

    switch ($choice) {
        "1" { Get-Date }
        "2" { Get-Process | Select-Object -First 5 }
        "3" { Write-Host "Goodbye"; break }
        default { Write-Host "Invalid choice" }
    }
} while ($choice -ne "3")
```

---

## Exercise 2: Retry logic

**Question**  
Write a function that attempts to perform an action (for example, testing a path) up to N times with a short delay between attempts.

**Solution**
```powershell
function Invoke-WithRetry {
    param(
        [scriptblock]$ScriptBlock,
        [int]$MaxAttempts = 3,
        [int]$DelaySeconds = 2
    )

    for ($i = 1; $i -le $MaxAttempts; $i++) {
        try {
            return & $ScriptBlock
        } catch {
            if ($i -eq $MaxAttempts) { throw }
            Start-Sleep -Seconds $DelaySeconds
        }
    }
}
```

---

## Exercise 3: Validate input

**Question**  
Write a script that repeatedly asks the user for a positive integer until a valid value is entered.

**Solution**
```powershell
do {
    $input = Read-Host "Enter a positive integer"
    $valid = $input -match '^\d+$' -and [int]$input -gt 0
    if (-not $valid) {
        Write-Host "Invalid input, try again."
    }
} while (-not $valid)

Write-Host "You entered: $input"
```

---

## Exercise 4: Process list with progress

**Question**  
Write a script that iterates over a list of items and shows a simple progress message for each item.

**Solution**
```powershell
$items = 1..10
$total = $items.Count
$i = 0

foreach ($item in $items) {
    $i++
    Write-Progress -Activity "Processing" -Status "Item $item" -PercentComplete (($i / $total) * 100)
    Start-Sleep -Milliseconds 200
}
Write-Progress -Activity "Processing" -Completed
```
