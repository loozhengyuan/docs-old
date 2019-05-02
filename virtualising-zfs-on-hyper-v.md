# Virtualising ZFS on Hyper-V

## Prerequisites
1. Microsoft Hyper-V Server 2016
2. Compatible Host Bus Adapter (Like LSI, etc)

## Step 1: Preparing the VM

Prerequisites
```shell
Set-VM -Name 'VM NAME' -AutomaticStopAction TurnOff
```

# Optional Parameters
```shell
Set-VM -GuestControlledCacheTypes $true -VMName 'VM NAME'
Set-VM -LowMemoryMappedIoSpace 3Gb -VMName 'VM NAME'
Set-VM -HighMemoryMappedIoSpace 33280Mb -VMName 'VM NAME'
```

## Step 2: Preparing the Host

List all devices
```shell
Get-PnpDevice -PresentOnly | Sort-Object -Property FriendlyName
```

Identify device; Change *Gigabit* to narrow down your search as much as possible
```shell
$pnpquery = (Get-PnpDevice -PresentOnly).Where{ $_.FriendlyName -like '*Gigabit*' }; $pnpquery
```

Set the variable $instanceId
```shell
$instanceId = $pnpquery[0] | Get-PnpDeviceProperty DEVPKEY_Device_InstanceId | ForEach { $_.Data }; $instanceId
```

Disabling the device
```shell
Disable-PnpDevice -InstanceId $instanceId
```

Set the variable
```shell
$locationPath = $pnpquery[0] | Get-PnpDeviceProperty DEVPKEY_Device_LocationPaths | ForEach { $_.Data }; $locationPath
```

Dismount the device from the host
```shell
Dismount-VMHostAssignableDevice -LocationPath $locationPath
```

(Optional) If your ACPI location path is also supplied, you can use this instead
```shell
Dismount-VMHostAssignableDevice -LocationPath $locationPath[0]
```

## Step 3: Assigning it to VM

Add device to VM
```shell
Add-VmAssignableDevice -LocationPath $locationPath -VmName 'VM NAME'
```

Verify that the device has been passed through
```shell
Get-VmAssignableDevice -VmName 'VM NAME'
```

## Optional: Removing and remounting back to host

Identify the device to be removed
```shell
Get-VmAssignableDevice -VmName 'VM NAME'
```

Remove device from VM
```shell
Remove-VmAssignableDevice -LocationPath $locationPath -VmName 'VM NAME'
```

Remount it back to host
```shell
Mount-VMHostAssignableDevice -LocationPath $locationPath
```
