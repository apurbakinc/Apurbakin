name: RDP

Jobs

secure-rdp

workflow dispatch:

runs-on: windows-latest timeout-minutes: 3600

stops:

name: Configure Core RDP Settings

Enable Remote Desktop and disable Network Level Authentication (if needed)

Set-ItemProperty Path "HKLM:\System\CurrentControlSet\Control\Terminal Server Name "fDenyT5Connections Value 0 Force

Set-ItemProperty -Path "HKLM\Systen\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp

-Force Name "UserAuthentication" Value

Set-ItemProperty Path "HKLM\Systen\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp -Name "Securitylayer Value 0-Force

#Remove any existing rule with the same nane to avoid duplication netsh advfirewall firewall delete rule name "RDP-Tailscale"

#For testing, allow any incontr allow any incoming connection on port 3389

netsh advfirewall firewall add rule name "RDP-Tailscale"

dir-in action-allow protocol TCP localport-3389

#(Optional) Restart the Remote Desktop service to ensure changes take effect

Restart-Service Name TeraService -Force

Create RDP User with Secure Password

Add-Type-AssemblyNane Systen. Security ScharSet

Upper [char[]](65..90) A-Z

Lower [char[]] (97..122)

Nunber [char[]] (48..57)

Special #0-9 [char[1](58..64)+

([char[1](33..47)

[char[1](91.96) (char[1](123..126)) # Special characters

$ramPassword()

SrawPassword ScharSet. Upper Get-Randon Count

Get-Randon -Count 4 Get-Randon -Count 4 SramPassword ScharSet. Lower SrawPassword ScharSet. Number

SrawPassword+ $charSet. Special Get-Random -Count: 4

Spassword-join (SrasPassword Sort-Object Get-Random)

SsecurePass ConvertTo-SecureString Spassword -AsPlainText -Force

New-LocalUser Name "RDP" -Password $securePass -Account Never Expires

Add-LocalGroupMember Group "Administrators Member "RDP"

Add-Local GroupMember -Group "Remote Desktop Users Member "RDP

echo "RDP CREDS-User: RDP | Password: Spassword" Senv: GITHUB ENV

if (not (Get-LocalUser Name "RDP"))

Write-Error "User creation failed" exit 1

run

Install Tailscale

$tsUrl "https://pkgs.tailscale.com/stable/tailscale-setup-1.82.0-and64.msi

SinstallerPath "Senv: TEMP\tailscale.msi"

Invoke-WebRequest -Uri Stoüel OutFile SinstallerPath

Renove-Item SinstallerPath -Force

Start-Process msiexec.exe -Argumentlist "li", ""SinstallerPath""", "/quiet", "/norestart" -Wait

Establish Tailscale Connection

Bring up Tailscale with the provided auth key with the provided auth key and set a unique et a unique hostnane "Senv: ProgramFiles\Tailscale\tailscale.exe" up authkey ${{ secrets, TAILSCALE_AUTH_KEY)-hostname-gh

Senv GITHUB RUN ID

#Wait for Taliscale to assign an IP

StsIP Snull $retries 0

while (-not StsIP-and Sretries -1t 10) (

StsIP & "Senv: ProgramFiles\Tailscale\tailscale.exe"

Start-Sleep-Seconds 5 $retries++

if (not StsIP) (

Write-Error "Tailscale IP not assigned. Exiting." exit 1

echo "TAILSCALE IP-SESIP" Senv: GITHUB ENV

name: Verify RDP Accessibility

run: Write-Host "Tailscale ID: Sony: TAILSCALE IP

port 3389 #Test connectivity using Test-NetConnection against the Tailscale IP testResult Test-NetConnection -ComputerName Senv: TAILSCALE IP-Port 3389

if (not $testResult.TopTest Succeeded) ( Write-Error "TCP connection to RDP port 3389 failed" exit 1

Write-Host "TCP connectivity successful!"

name: Maintain Connection

run

Write-Host RDP ACCESS

Write-Host "Address: Sony: TAILSCALE IP Write-Host "Username: RDP"

Write-Host "Password: $(echo Senv: RDP CREDS)

Write-Host

Keep runner active indefinitely (or until manually cancelled) while ($true) (

Write-Host "[$(Got-Date)] RDP Active Use Ctrl+C workflow terminate

Start-Sleep -Seconds 300
