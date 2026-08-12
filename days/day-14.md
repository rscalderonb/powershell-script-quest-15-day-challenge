# Day 14 – Custom objects and classes

## Exercise 1: Create custom objects

**Question**  
Build a list of custom objects representing servers with Name, IP, and Status properties.

**Solution**
```powershell
$servers = @(
    [PSCustomObject]@{Name="web01"; IP="10.0.0.10"; Status="Online"},
    [PSCustomObject]@{Name="db01"; IP="10.0.0.20"; Status="Offline"}
)
$servers | Format-Table -AutoSize
```

---

## Exercise 2: Simple PowerShell class

**Question**  
Define a simple class `Person` with properties and a method that returns a greeting.

**Solution**
```powershell
class Person {
    [string]$FirstName
    [string]$LastName

    Person([string]$first, [string]$last) {
        $this.FirstName = $first
        $this.LastName = $last
    }

    [string] Greet() {
        return "Hello, $($this.FirstName) $($this.LastName)"
    }
}

$p = [Person]::new("Ada", "Lovelace")
$p.Greet()
```

---

## Exercise 3: Add members to existing objects

**Question**  
Take process objects and add a custom property indicating whether they are using more than 50 MB.

**Solution**
```powershell
Get-Process |
    Select-Object -First 5 |
    ForEach-Object {
        $_ | Add-Member -NotePropertyName "HighMemory" -NotePropertyValue ($_.WorkingSet64 -gt 50MB) -PassThru
    } |
    Select-Object Name, HighMemory
```

---

## Exercise 4: Convert to custom type

**Question**  
Demonstrate creating a custom type name for objects so they can be extended later with types.ps1xml if desired.

**Solution**
```powershell
$obj = [PSCustomObject]@{Name="Test"; Value=42}
$obj.PSObject.TypeNames.Insert(0, "MyCustom.Type")
$obj
```
