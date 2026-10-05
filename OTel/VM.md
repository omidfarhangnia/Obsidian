winver دستوری برای دریافت ورژن فعلی ویندوز

```
Get-ComputerInfo | Select-Object `
WindowsProductName,
WindowsVersion,
OsBuildNumber,
OsArchitecture,
CsName
```