# sensu-plugins-windows

This guide inject script replace identity server to use instance-id for windows image type qcow2.

Create "C:\Users\Administrator\Documents\label.ps1"

With content :

$label = (Invoke-WebRequest -uri "http://ipinfo.io/ip" -UseBasicParsing).Content
$instanceid = Get-Content 'C:\Program Files\Cloudbase Solutions\Cloudbase-Init\log\cloudbase-init.log' | Select-String -Pattern 'instance' | ForEach{ "{8}" -f ($_ -split ' ')}
((Get-Content -path C:\ProgramData\sensu\config\agent.yml -Raw) -replace 'ipaddr',$label) | out-file C:\ProgramData\sensu\config\agent.yml
((Get-Content -path C:\ProgramData\sensu\config\agent.yml -Raw) -replace 'hostname',$instanceid) | out-file C:\ProgramData\sensu\config\agent.yml
Start-Sleep -s 5
net start SensuAgent
Set-Service -Name SensuAgent -StartupType Automatic
Unregister-ScheduledTask -TaskName label -Confirm:$False
Remove-Item -Path C:\Users\Administrator\Documents\cloudbaseinit.msi -Force
Remove-Item -LiteralPath $MyInvocation.MyCommand.Path -Force



Save the file

dont forget to stop and disable first sensu agent first before do it.

thank you
