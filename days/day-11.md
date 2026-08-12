# Day 11 – Registry and configuration

## Exercise 1: Read a registry value

**Question**  
Write a script that reads a specific registry value and displays it.

**Solution**
```powershell
$path = "HKLM:\SOFTWARE\Microsoft\Windows NT\CurrentVersion"
Get-ItemProperty -Path $path | Select-Object ProductName, CurrentVersion, CurrentBuild
```

---

## Exercise 2: List installed programs (basic)

**Question**  
List installed programs from the common uninstall registry keys.

**Solution**
```powershell
$paths = @(
    "HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Uninstall\*",
    "HKLM:\SOFTWARE\Wow6432Node\Microsoft\Windows\CurrentVersion\Uninstall\*"
)
Get-ItemProperty $paths -ErrorAction SilentlyContinue |
    Where-Object { $_.DisplayName } |
    Select-Object DisplayName, DisplayVersion, Publisher |
    Sort-Object DisplayName
```

---

## Exercise 3: Create a registry key (safe demo)

**Question**  
Demonstrate creating a temporary registry key under HKCU and then removing it.

**Solution**
```powershell
$testPath = "HKCU:\Software\TempQuestDemo"
New-Item -Path $testPath -Force | Out-Null
Set-ItemProperty -Path $testPath -Name "Demo" -Value "Hello"
Get-ItemProperty -Path $testPath
Remove-Item -Path $testPath -Recurse -Force
```

---

## Exercise 4: Environment variables

**Question**  
List all user-level environment variables and highlight any that contain "PATH".

**Solution**
```powershell
Get-ChildItem Env: |
    Where-Object { $_.Name -match "PATH" -or $_.Value -match "PATH" } |
    Format-Table Name, Value -AutoSize
```
