---
title: "Scripts varios de PowerShell"
date: 2016-01-26T11:57:40-03:00
---

### Exchange

Get-MailboxFolderStatistics USUARIO \| Select-Object Name

Get-MailboxFolderPermission USUARIO:\Carpeta

#### Listar tamaños de los Mailbox de Exchange

```powershell>
=== Listar Usuario/Primary SMTP Address/Alias ===
<code powershell>
Get-Recipient -ResultSize Unlimited | 
? {$_.RecipientType -eq "MailContact" -or $_.RecipientType -eq "UserMailbox" -and $_.PrimarySmtpAddress } |
Select DisplayName,PrimarySmtpAddress,Alias |
Export-Csv report.csv -NTI
```

### Varios

#### Sesiones de escritorio remoto

Ver sesiones activas

```powershell>
Cancelar sesion activa
<code powershell>
rwisnta numero_sesion /server:servidor.cayu.com.ar
```

#### Export en CSV de Datos de Active Directory

```powershell>
=== Ver WWN De las placas de fibra ===
**Find HBA and WWN Information on Windows using WMI (Updated Feb 2015)**
<code powershell>
function Get-HBAWin { 
param( 
[String[]]$ComputerName = $ENV:ComputerName,
[Switch]$LogOffline 
) 
 
$ComputerName | ForEach-Object { 
try {
	$Computer = $_
	
	$Params = @{
		Namespace    = 'root\WMI'
		class        = 'MSFC_FCAdapterHBAAttributes'
		ComputerName = $Computer 
		ErrorAction  = 'Stop'
		}
	
	Get-WmiObject @Params  | ForEach-Object { 
			$hash=@{ 
				ComputerName     = $_.__SERVER 
				NodeWWN          = (($_.NodeWWN) | ForEach-Object {"{0:X2}" -f $_}) -join ":" 
				Active           = $_.Active 
				DriverName       = $_.DriverName 
				DriverVersion    = $_.DriverVersion 
				FirmwareVersion  = $_.FirmwareVersion 
				Model            = $_.Model 
				ModelDescription = $_.ModelDescription 
				} 
			New-Object psobject -Property $hash 
		}#Foreach-Object(Adapter) 
}#try
catch {
	Write-Warning -Message "$Computer is offline or not supported"
	if ($LogOffline)
	{
		"$Computer is offline or not supported" >> "$home\desktop\Offline.txt"
	}
}

}#Foreach-Object(Computer) 
 
}#Get-HBAWin
```
