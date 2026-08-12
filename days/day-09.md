# Day 09 – Network related tasks

## Exercise 1: Test multiple hosts

**Question**  
Write a script that pings a list of hosts and reports which ones respond.

**Solution**
```powershell
param([string[]]$Hosts)

$Hosts | ForEach-Object {
    $result = Test-Connection -ComputerName $_ -Count 1 -Quiet -ErrorAction SilentlyContinue
    [PSCustomObject]@{
        Host = $_
        Reachable = $result
    }
}
```

---

## Exercise 2: Resolve DNS

**Question**  
Write a script that resolves a list of hostnames to IP addresses.

**Solution**
```powershell
param([string[]]$Names)

$Names | ForEach-Object {
    try {
        $addrs = [System.Net.Dns]::GetHostAddresses($_)
        [PSCustomObject]@{
            Name = $_
            Addresses = ($addrs | ForEach-Object { $_.IPAddressToString }) -join ", "
        }
    } catch {
        [PSCustomObject]@{ Name = $_; Addresses = "Resolution failed" }
    }
}
```

---

## Exercise 3: List listening ports

**Question**  
List all TCP ports currently in the Listen state along with the owning process name.

**Solution**
```powershell
Get-NetTCPConnection -State Listen |
    Select-Object LocalAddress, LocalPort, OwningProcess,
        @{Name="Process"; Expression={(Get-Process -Id $_.OwningProcess -ErrorAction SilentlyContinue).Name}}
```

---

## Exercise 4: Download a file with progress

**Question**  
Write a script that downloads a file from a URL and shows basic progress.

**Solution**
```powershell
param(
    [string]$Url,
    [string]$OutFile
)

Invoke-WebRequest -Uri $Url -OutFile $OutFile -UseBasicParsing
Write-Host "Downloaded to $OutFile"
```
