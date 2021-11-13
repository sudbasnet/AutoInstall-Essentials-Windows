# Things I want to auto-install on Windows
I play around different computers and OS, I own more computers (PC, laptops, PIs) than I need, most of them low-end and just used for learning and experimental purposes. So far, Windows has been my OS of choice for personal use because its pretty stable, supports almost all development environments that I care about, and is easy for the occasional gaming. I install/re-install Windows for one reason or another, and it's a pain to install different softwares that are essential to me. So, I am attempting to create some scripts to auto-install as many of these softwares as I can. 

Currently, I'm just researching resources availabe on the web.

### Chrome Browser
https://www.snel.com/support/install-chrome-in-windows-server/
```shell
$LocalTempDir = $env:TEMP; $ChromeInstaller = "ChromeInstaller.exe"; 
(new-object    System.Net.WebClient).DownloadFile('http://dl.google.com/chrome/install/375.126/chrome_installer.exe', "$LocalTempDir\$ChromeInstaller"); 
& "$LocalTempDir\$ChromeInstaller" /silent /install; 
$Process2Monitor =  "ChromeInstaller";

Do { 
	$ProcessesFound = Get-Process | ?{$Process2Monitor -contains $_.Name} | Select-Object -ExpandProperty Name; If ($ProcessesFound) { 
		"Still running: $($ProcessesFound -join ', ')" | Write-Host; 
		Start-Sleep -Seconds 2 
	} else { 
		rm "$LocalTempDir\$ChromeInstaller" -ErrorAction SilentlyContinue -Verbose 
	} 
} 
Until (!$ProcessesFound)
```
### Firefox Browser
https://www.snel.com/support/install-firefox-in-windows-server/
```shell
$workdir = "c:\installer\"

If (Test-Path -Path $workdir -PathType Container)
{ Write-Host "$workdir already exists" -ForegroundColor Red}
ELSE
{ New-Item -Path $workdir  -ItemType directory }

$source = "https://download.mozilla.org/?product=firefox-latest&os=win64&lang=en-US"
$destination = "$workdir\firefox.exe"

if (Get-Command 'Invoke-Webrequest')
{
     Invoke-WebRequest $source -OutFile $destination
}
else
{
    $WebClient = New-Object System.Net.WebClient
    $webclient.DownloadFile($source, $destination)
}

Start-Process -FilePath "$workdir\firefox.exe" -ArgumentList "/S"

Start-Sleep -s 35

rm -Force $workdir/firefox*
```
### Visual Studio Code
Nothing specific found. Might have to go step by step and create a new script.

### git
Official git website suggests auto-install through [Git Chocolatey package](https://chocolatey.org/packages/git).
So I might need to auto-install chocolatey package manager. On the official [Setup/Install page](https://docs.chocolatey.org/en-us/choco/setup) of [Chocolatey](https://docs.chocolatey.org/en-us/), there are instructions to install it through cmd or powershell, with or without administrative access. Since I'm looking to run a single script as an adminstrator, I will only look at the one with said access. After installing Chocolatey, we should be able to install git with `choco install git`.
#### [Install Chocolatey with cmd.exe](https://docs.chocolatey.org/en-us/choco/setup#install-with-cmd.exe)
```shell
@"%SystemRoot%\System32\WindowsPowerShell\v1.0\powershell.exe" -NoProfile -InputFormat None -ExecutionPolicy Bypass -Command "[System.Net.ServicePointManager]::SecurityProtocol = 3072; iex ((New-Object System.Net.WebClient).DownloadString('https://community.chocolatey.org/install.ps1'))" && SET "PATH=%PATH%;%ALLUSERSPROFILE%\chocolatey\bin"
```
#### [Install Chocolatey with powershell](https://docs.chocolatey.org/en-us/choco/setup#install-with-powershell.exe)
With PowerShell, there is an additional step. You must ensure `Get-ExecutionPolicy` is not Restricted. We suggest using `Bypass` to bypass the policy to get things installed or `AllSigned` for quite a bit more security.
*	Run `Get-ExecutionPolicy`. If it returns `Restricted`, then run `Set-ExecutionPolicy AllSigned` or `Set-ExecutionPolicy Bypass -Scope Process`.
*	Now run the following command:
```shell
Set-ExecutionPolicy Bypass -Scope Process -Force; [System.Net.ServicePointManager]::SecurityProtocol = [System.Net.ServicePointManager]::SecurityProtocol -bor 3072; iex ((New-Object System.Net.WebClient).DownloadString('https://community.chocolatey.org/install.ps1'))
```

### Sublime Text

### Notepad++

### Windows Terminal

### VLC

### 7Zip

### Java

### NodeJS

### TypeScript

### Dart

### Python, pip

### Steam
