## ✅ Force all redirect downloads to OneDrive Downloads Folder

Enable all downloads from Microsoft Edge to save at the OneDrive\Downloads folder. To accomplish this, we need to implement some PowerShell scripts, as OneDrive\Downloads is not part of the default directories created.

To start, we need to create two separate PowerShell files (.ps1) using the code below for Detection and Remediation:

### ⚙️ PowerShell Script Needed to Detect if Downloads Folder Exists

```powershell
$oneDrive = $env:OneDriveCommercial
$target = Join-Path $oneDrive 'Downloads'
$regKey = 'HKCU:\Software\Microsoft\Windows\CurrentVersion\Explorer\Shell Folders'
if ((Test-Path $target) -and (Get-ItemPropertyValue -Path $regKey -Name 'Downloads') -eq $target) {
  exit 0 } else { exit 1 }
```

### ⚙️ PowerShell Script Needed to Remediate if Downloads Folder Does Not Exist

```powershell
$oneDrive = $env:OneDriveCommercial
$target = Join-Path $oneDrive 'Downloads'
$regKey = 'HKCU:\Software\Microsoft\Windows\CurrentVersion\Explorer\Shell Folders'
if (-not (Test-Path $target)) { New-Item -Path $target -ItemType Directory -Force | Out-Null }
Set-ItemProperty -Path $regKey -Name 'Downloads' -Value $target
exit 0
```

### 🖥️ Microsoft Intune

#### Step #1: Set Detection and Remediation

- Go to the Microsoft Intune Admin Portal
- Under **Devices -> Scripts and remediations**
- Click on **+Create** under Remediations
- Enter name: *Remediate OneDrive - Downloads*
- Under the **Settings tab**, select the PowerShell file for detection under ***"Detection script file"***
- For Remediation, select the PowerShell file for remediation under ***"Remediation script"***
- Enable ***"Run this script using the logged-on credentials" AND "Run script in 64-bit PowerShell"***
- Select the assignment as necessary.
- Review and create.

#### Step #2: Create Configuration Policy for Download Redirects

- In Microsoft Intune, go to **Devices -> Configuration**
- Create a new profile: select ***"Windows 10 and later"*** as Platform and **"Settings catalog"** as Profile type
- Enter name: *Redirect Browser Downloads to OneDrive*
- Under the Configuration Settings tab, search for ***"Microsoft Edge"*** and click on it. Now add the following settings:
  
```
Ask where to save downloaded files - Disabled
Ask where to save downloaded files (User) - Disabled
Set download directory (User) - Enabled
---- Set download directory (User): ${profile}\OneDrive - [ONLY_REPLACE_THIS_WITH_TENANT_NAME]\Downloads
```
***Replace [ONLY_REPLACE_THIS_WITH_TENANT_NAME] with actual tenant name.***

- Select the assignment as necessary.
- Review and create.
