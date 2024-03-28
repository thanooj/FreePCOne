# FreePCOne
Free remote Windows Server PC using Github Actions


  ## Steps To Create Windows Server

- Sign Up a GitHub Account : https://github.com/
- Create a Private Repository, Go to repository settings > Secrets and variables > Actions > New Repository Secrets
- Sign Up a Ngrok Account : https://ngrok.com/
- Copy the 'Your Authtoken' key from ngrok and add to github repository secrets
- Setup New Workflow Manually the Put the following code in *main.yml* and commit changes
  
```
name: CI

on: [push, workflow_dispatch]

jobs:
  build:

    runs-on: windows-latest

    steps:
    - name: Download
      run: Invoke-WebRequest https://bin.equinox.io/c/bNyj1mQVY4c/ngrok-v3-stable-windows-amd64.zip -OutFile ngrok.zip
    - name: Extract
      run: Expand-Archive ngrok.zip
    - name: Auth
      run: .\ngrok\ngrok.exe authtoken $Env:NGROK_AUTH_TOKEN
      env:
        NGROK_AUTH_TOKEN: ${{ secrets.NGROK_AUTH_TOKEN }}
    - name: Enable TS
      run: Set-ItemProperty -Path 'HKLM:\System\CurrentControlSet\Control\Terminal Server'-name "fDenyTSConnections" -Value 0
    - run: Enable-NetFirewallRule -DisplayGroup "Remote Desktop"
    - run: Set-ItemProperty -Path 'HKLM:\System\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -name "UserAuthentication" -Value 1
    - run: Set-LocalUser -Name "runneradmin" -Password (ConvertTo-SecureString -AsPlainText "P@ssw0rd!" -Force)
    - name: Create Tunnel
      run: .\ngrok\ngrok.exe tcp 3389
```
- Run The WorkFlow and take note of credentials
  - User Name: runneradmin
  - Password: P@ssw0rd!
- Get the ngrok Endpoints URL and copy IP:HOST (e.g. 2.tcp.us-cal-1.ngrok.io:16478) and use it as computer address in Remote Desktop Connection with credentials
