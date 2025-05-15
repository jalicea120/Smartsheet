## ✅ Enable Sensitivity Label Support in PowerShell (US Gov)

To enable sensitivity label support for Microsoft 365 Groups and SharePoint sites using Microsoft Graph (US Government cloud), follow the steps below.

### 📦 Install Required PowerShell Modules

```powershell
Install-Module Microsoft.Graph -Scope CurrentUser
Install-Module Microsoft.Graph.Beta -Scope CurrentUser
```

### 🔐 Connect to Microsoft Graph (USGov)

```powershell
Connect-MgGraph -Environment USGov -Scopes "Directory.ReadWrite.All"
```

### ⚙️ Set PowerShell Execution Policy

```powershell
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned
# Press "Y" when prompted to confirm
```

### 🔍 Retrieve Current Directory Setting

```powershell
$grpUnifiedSetting = Get-MgBetaDirectorySetting | Where-Object { $_.Values.Name -eq "EnableMIPLabels" }
$grpUnifiedSetting.Values
```

### 🛠️ Enable Sensitivity Label Support

```powershell
$params = @{
     Values = @(
 	    @{
 		    Name = "EnableMIPLabels"
 		    Value = "True"
 	    }
     )
}
```

### 🔄 Update the Directory Setting

```powershell
Update-MgBetaDirectorySetting -DirectorySettingId $grpUnifiedSetting.Id -BodyParameter $params
```

### ✅ Confirm the Setting Was Applied

```powershell
$Setting = Get-MgBetaDirectorySetting -DirectorySettingId $grpUnifiedSetting.Id
$Setting.Values
```
