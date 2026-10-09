
### Get Windows Version

```powershell
Get-ComputerInfo | Select-Object `
WindowsProductName,
WindowsVersion,
OsBuildNumber,
OsArchitecture,
CsName
```

### Get Installed Hotfixes

```powershell
Get-HotFix | 
    Sort-Object InstalledOn -Descending |
    Select-Object -First 10 ` HotFixID, InstalledOn, Description
```

A hotfix in Windows is a small, targeted software update released outside of the regular update cycle to fix a specific bug, error, or critical security flaw.

### Get Processor Information

```powershell
Get-CimInstance Win32_Processor |
    Select-Object Name,
                  Manufacturer,
                  NumberOfCores,
                  NumberOfLogicalProcessors,
                  VirtualizationFirmwareEnabled,
                  SecondLevelAddressTranslationExtensions
```

### Get RAM Information

```powershell
Get-CimInstance Win32_ComputerSystem | Select-Object TotalPhysicalMemory
```

### VirtualizationFirmwareEnabled : True

VirtualizationFirmwareEnabled یک ویژگی در سیستم عامل ویندوز است که نشان می‌دهد آیا قابلیت میانجی‌سازی (Virtualization) در سطح فریمور/BIOS فعال شده است یا خیر.

### SecondLevelAddressTranslationExtensions : True

SecondLevel Address Translation Extensions (SLAT) یک قابلیت سخت‌افزاری است که به ویندوز کمک می‌کند تا عملکرد مجازی‌سازی را بهتر انجام دهد.

