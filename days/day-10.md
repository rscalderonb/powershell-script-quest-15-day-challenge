# Day 10 – Jobs and background work

## Exercise 1: Start a background job

**Question**  
Start a simple background job that calculates something and then retrieve its result.

**Solution**
```powershell
$job = Start-Job -ScriptBlock {
    1..10 | ForEach-Object { $_ * $_; Start-Sleep -Milliseconds 100 }
}
Wait-Job $job | Out-Null
Receive-Job $job
Remove-Job $job
```

---

## Exercise 2: Multiple jobs and wait

**Question**  
Start several jobs, wait for all of them, and collect the results.

**Solution**
```powershell
$jobs = 1..3 | ForEach-Object {
    Start-Job -ScriptBlock { param($n) Start-Sleep -Seconds 1; "Job $n done" } -ArgumentList $_
}
Wait-Job $jobs | Out-Null
$jobs | Receive-Job
$jobs | Remove-Job
```

---

## Exercise 3: Job with timeout

**Question**  
Start a job and wait for it with a timeout. If it exceeds the timeout, stop it.

**Solution**
```powershell
$job = Start-Job -ScriptBlock { Start-Sleep -Seconds 30; "Finished" }
$result = Wait-Job $job -Timeout 5
if (-not $result) {
    Stop-Job $job
    Write-Host "Job timed out and was stopped"
}
Remove-Job $job -Force
```

---

## Exercise 4: Parallel foreach (conceptual)

**Question**  
Demonstrate a simple parallel approach using jobs for a list of items.

**Solution**
```powershell
$items = 1..5
$jobs = $items | ForEach-Object {
    Start-Job -ScriptBlock { param($i) "Processed $i" } -ArgumentList $_
}
$jobs | Wait-Job | Receive-Job
$jobs | Remove-Job
```
