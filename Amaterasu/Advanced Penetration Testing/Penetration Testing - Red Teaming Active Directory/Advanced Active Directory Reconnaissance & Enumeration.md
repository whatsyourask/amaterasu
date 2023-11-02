# Learning Objectives
- How to thoroughly (and stealthily) perform reconnaissance and enumeration activities against an AD environment
- How to hunt for privileged AD users and identify (stealthier) attack paths
# Introduction
A red team member will usually identify misconfigurations or exploit trust relationships which will take him all the way to domain admin. To achieve this, stealthy and extensive reconnaissance and enumeration are required, prior to any exploitation activities.
# The Traditional Approach
Cover the following scenarios:
- Using a sniffer or a network scanning tool
- Through a non-domain joined Linux machine, without a windows shell
- Through a domain joined Windows machine
## Reconnaissance & enumeration using a sniffer or a network scanning tool
Good start - fire up a sniffer and passively sniffing traffic. We may stumble upon:
- SNMP community strings
- hostnames
- domain names
- ARP traffic broadcast
- etc
Tools:
- Wireshark
- tcpdump
## Reconnaissance & enumeration through a non-domain joined Linux system, without a windows shell
Firing up *nbtscan*:
`nbtscan -r <ip-range>`

---
Perform reverse DNS queries to identify hostnames using nmap:
`nmap -sL <target or range>`

---
metasploit smb_version module to scan networks for windows systems.
`use auxiliary/scanner/smb/smb_version`
![[Screenshot 2023-11-02 at 22.52.19.png]]

---
Don't forget about common SNMP misconfigurations
metasploit SNMP scanner attempts to guess the community string, if not acquired already via sniffing:
`use auxiliary/scanner/snmp/snmp_login`

Community string can be acquired through sniffing if SNMPv1 or SNMPv2c are in use.

Ettercap can capture the community string by executing a MITM attack. In order to identify the address of the NMS interacting with the SNMP agent, you will have to add the `-p [pcapfile]` argument.

Once we acquire the community string, we can enumerate systems running SNMP. Under the hood a Management information Base (MIB) walk is performed for the enumeration, snmpcheck can assist us in that:
`snmpcheck.pl -c <community_string> -t <ip>`

---
dig can also assist us in our enumeration efforts. We can try to look up the windows global catalog (GC) record and the authoritative domain server record to determine DC address
`dig -t NS domain_name`
or
`dig _gc.domain_name`
But we have to know already the domain name or guess it.
![[Screenshot 2023-11-02 at 22.59.17.png]]

---
We can perform enumeration activities against the targeted domain with a valid set of credentials or over a NULL session over SMB sessions - doesn't require a Windows shell.

SMB null sessions are becoming extinct they can still be met and leveraged to acquire a great amount of information.

If this is not working, then use any valid set of domain creds to start enumeration without Windows shell.

To do so, use rpcclient:
`rpcclient -U <username> <ip-address>`

For null sessions:
`rpcclient -U "" <ip-address>`

Suppose we phished an employee domain creds. To identify the accessible machines in a range and then perform enum activities over an SMB authenticated session, we should try the following using rpcclient:
```bash
cat ips.txt | while read line
do
echo $line && rpcclient -U "DOMAIN\SamanthaRivers%P@ssw0rd123" -c "enumdomusers;quit" $line
done
```
![[Screenshot 2023-11-02 at 23.05.37.png]]

To get info of the remote server:
`rpcclient $> srvinfo`
To enumerate domain users:
`rpcclient $> enumdomusers`
To enumerate domain and built-in groups:
`rpcclient $> enumalsgroups domain`
`rpcclient $> enumalsgroups builtin`
To identify a SID:
`rpcclient $> lookupnames <username or groupname>`
We can get details for a user having specific RIDs. To identify the original admin user on a Windows machine, execute the following:
`rpcclient $> queryuser 500`

---
Don't neglect to perform share enumeration with:
- enum4linux
- smbmap 
- nmap 'smb-enum-shares'
For example, with valid creds:
`smbclient -U "DOMAIN\username%password" -L hostname`
![[Screenshot 2023-11-02 at 23.09.58.png]]
## Defeating anonymous user restrictions
We will have to overcome obstacles about anonymous users. The **RestrictAnonymous** registry key is one of it.
It may pay dividends on envs containing  legacy systems.

To bypass RestrictAnonymous - Anonymous SID to username translation. It allows us to perform username enumeration through a SID walk in background.
A tool for that - `dumpusers`

---
SNMP is another route we can follow in our attempts to bypass anonymous restrictions and continue our enumeration activiites - have to acquire community string for this task.

---
At the end of the task, we would like to put ourselves inside the 'Authenticated Users' group - any valid creds will do.
## Recon & enumeration through a domain joined Windows machine
Run a DNS query as follows to get the SRV records for DCs:
`nslookup -querytype=SRV _LDAP._TCP.DC._MSDCS.domain_name`
![[Screenshot 2023-11-02 at 23.17.04.png]]

---
We can also use ADSI (powershell) which is highly recommended: `[System.DirectoryServices.ActiveDirectory.Domain]::GetCurrentDomain().DomainControllers`
![[Screenshot 2023-11-02 at 23.18.26.png]]

---
We can also use nltest:
`nltest /server:ip_of_any_member /dclist:domain_name`
![[Screenshot 2023-11-02 at 23.19.33.png]]

---
net view will return workgroups and domains on the network:
`net view /domain`
Returns a list of member systems of domains and workgroups:
`net view /domain:domain_name`

---
We can identify hostnames via DNS. We should also check for any allowed zone transfers. 
>Any interaction with DNS systems can be easily spotted.

The loop below will perform nslookup commands against the specified DNS server of the domain:
```cmd
for /L %i in (1,1,255) do @nslookup 10.10.10.%i [server to resolve from] 2>nul | find "Name" && echo 10.10.10.%i
```
![[Screenshot 2023-11-02 at 23.24.34.png]]

---
nbtstat will remote a remote machine's MAC address, hostname and domain membership, codes that represent roles it performs in the env (DC, IIS, database, etc), through NetBIOS over TCP/IP statistics, NetBIOS name tables  and NetBIOS name cache.
`nbtstat -A remote_machine_ip`
With loop:
```cmd
for /L %i in (1,1,255) do %nbtstat -A 10.10.10.%i 2>nul && echo 10.10.10.%i
```
![[Screenshot 2023-11-02 at 23.27.05.png]]

---
Once we are inside the 'Authenticated Users' group we can continue our enumeration activities. 
Tools to help:
- DumpSec
- shareenum (sysinternals)
- enum.exe

---
Look around for any shares with insufficiently secure permissions configured.
`net use e: \\ip\ipc$ password /user:domain\username`
`net view \\ip`

# Red team-oriented reconnaissance & enumeration
In this part - stealthy reconnaissance and enumeration techniques against AD, leveraging native Windows/AD funcs and components.

>Red team members don't follow attack path that involve throwing exploits aroung recklessly. A red team member will usualyy identify misconfigs or exploit trust relationships which will take him all the way to domain admin, **with minimum noise**.

Cover the following:
- hunting for users
- Local admins enumeration
- GPO enumeration and abuse
- AD ACLs
- Domain Trusts and more
>The majority of those from an unprivileged user's point of view

>System admins do not seem to realize the amount of info we can pull from AD as a basic domain user. 
>What we are actually doing as a red team inside AD is (unauthorized) domain administration.

>We constantly try to find misconfigurations and chain access/trust relationships to move from our initial foothold to compromising the entire domain or forest.

The 2 main tools we are going to use:
- PowerView
- AD Powershell module

> Large portion of enumeration activities leverages PowerShell - heavily monitored and logged nowadays. Attackers and red teamers are now leveraging C# and .NET to perform their operations.

PowerShell can still be used to covert operations (AMSI bypass, Constrained Language Mode bypass, AppLocker bypass, Logging bypass etc)
The latest in C#,.NET tradecraft will be covered.

AD Powershell module should be installed after initial compromise from an elevated shell.
`import-Module ServerManager`
`Add-WindowsFeature RSAT-AD-PowerShell`
## Fundamentals & User Hunting
### DNS using LDAP
We can do DNS lookups using LDAP. We don't have to ask DNS, which has detailed looging and information about what users are querying for are stored. We can just look at AD. For example we can ask for a list of specific computers or all the DCs and their associated IP addresses through just an LDAP call.

We can also do reverse lookups like "what's the site" or "what's this computer" - all are through AD

---
To identify machines inside the domain or do reverse lookups via LDAP:
```powershell
get-adcomputer -filter * -Properties ipv4address | where {$_.IPv4address} | select name,ipv4address

get-adcomputer -filter {ipv4address -eq 'IP'} -Properties Lastlogondate,passwordlastset,ipv4address
```
![[Screenshot 2023-11-03 at 00.08.18.png]]
### SPN Scanning / Service Discovery
We don't have to perform port scanning - nowadays we can use SPN scanning.

SPN scanning leverages standard LDAP queries using and looking for Service Principal Names. 

A service that supports Kerberos Authentication must register an SPN.

There is a number of SPN types like MSSQLSvc, TERMSERV, WSMan, exchangeMDB that we can search for. 
The SPN format will have the SPN type, the server name and SQL port number for instance.

Most common SPNs - https://adsecurity.org/?page_id=183

SPN scanning is a way better way of scanning for service accounts rather than searching for service or SVC in the name during service discovery activities.

We can also request all the user accounts that have SPN associated with them.

An example of how to perform SPN scanning is Sean Metcalf's `Find-PSServiceAccounts`.![[Screenshot 2023-11-03 at 00.13.22.png]]
Manually perform SPN scanning - AD Powershell module:
```powershell
Get-ADComputer -filter {ServicePrincipalName -Like "*SPN*" } -Properties OperatingSystem,OperatingSystemVersion,OperatingSystemServicePack,PasswordLastSet,LastLogonDate,ServicePrincipalName,TrustedForDelegation,TrustedtoAuthForDelegation
```
For more info about SPN scanning - https://adsecurity.org/?p=230
### Group Policies
We can also discover all group policies in an organization. By default, all authenticated users have read access over them.

By analyzing group policies we can see if there's:
- Domain Powershell logging policy
- Full auditing policy
- Configuration like 'prevent local account at logon', 'add server admin to local administrator group'
- EMET configuration
- AppLocker configuration
Use PowerView:
```powershell
Get-NetGPO | select displayname,name,whenchanged
```
![[Screenshot 2023-11-03 at 00.17.49.png]]
### Fundamentals of user hunting
In our engagements it's crucial to gain an understanding of where specific users are logged in. User hunting activities can be performed with pre-elevated access and post-elevated access.

PowerView leverages a couple of native API calls:
- NetWkstaUserEnum
- NetSessionEnum
Most people tend to use Add-Type, but there is a reason we don't want to use this. It is not fileless.

Pinvoke and that embedded C# code will actually call some compilation artifacts, whenever run from the script. To minimize on-disk footprint PowerView utilities concepts like straight reflection for API interaction through PowerShell.

A great way to understand how this approach works is studying the specifics of PSReflect - https://github.com/mattifestation/PSReflect

When we type `net session` on our computers - NetSessionEnum happens.

With native net.exe commands we are unable to investigae a remote system, but the API call allows us to do this. As an unprivileged user we can ask for all the sessions on a remote system like a DC or a file server.
The result will be who is logged in and from where they are logged in.
>We should run this call against a high value and high traffic server. This way we can map the whereabouts of a large number of logged in users, *without being spotted*

---
When we request the members of a particular group, the results of these different nested groups are also grouped themselves. So, we want to unroll everything and figure out what the effective members of these types of groups are.

For this, we can use the -Recurse option of PowerView, that will unroll all the nested group memberships and rerun an effective set of all the groups of users having access rights for this particular group.

> Under the hood it's essentially LDAP queries and ADSI accelerators. The LDAP queries are optimized in PowerView to suit the red team approach.

Execute the PowerView command below:
```powershell
Get-NetGroupMember 'Domain Admins' -Recurse
```
![[Screenshot 2023-11-03 at 00.27.49.png]]

We can also perform more complex queries during user hunting. For example, request for all the members of Domain Admins and then tokenize every display name in order to re-query for all users:
```powershell
Get-NetGroupMember -GroupName 'Domain Admins' -FullData | %{ $a=$_.displayname.split('')[0..1] -join ' '; Get-NetUser -Filter "(displayname=*$a*)" } | Select-Object -Property displayname,samaccountname
```

When we dump an AD schema, we try to figure out a linkable pattern for admins who have multiple accounts. It's not uncommon that someone has an elevated account and a non-elevated account.

>If we compromise the identified machine, sit there and wait until the target logs in with his elevated account, we can compromise this elevated account.

---
Invoke-UserHunter - PowerView command that queries the domain for all the computer objects and then for each computer it utilizes the native API calls to enumerate logged users.
>Invoke-UserHunter has interesting option - stealth

`Invoke-UserHunter -Stealth -ShowAll`
![[Screenshot 2023-11-03 at 00.33.42.png]]
Invoke-UserHunter -Stealth enumerates all the distributed file systems and DCs and pulls all user objects, script path(s), home directories, etc. It actually pull certain type of fields that tend to map where file servers are and a user AD schema.
> The idea behind stealth is that it gets as many computers as it can that are heavily trafficked (there may be a dozen of machines inside a network where a lot of people connect to)

Then, it performs a `Get-NetSession` against those systems. The sessions of those systems can provide us with an almost complete map of the network due to their heavy traffic.
![[Screenshot 2023-11-03 at 00.37.07.png]]

---
Don't forget that you can get all the users of an AD forest by simply querying a single domain controller's Global Catalog, even a child domain's one! Admin privileges are not required for this operation.
![[Screenshot 2023-11-03 at 00.39.16.png]]
#### Local Administrator Enumeration
Windows OS allows* any basic domain user (authenticated) to enumerate the members of a local group on a remote machine.
>windows 10 anniversary edition & wiindows server 16 lock get-localgroup down by default

Two ways to accomplish this:
- WinNT service provider - a service provider we can use with ADSI accelerators that allows enumeration of local groups and users across the network.
- NetLocalGroupGetMembers Win 32 API call, which doesn't result in the same amount of information, but it tends to be much faster, since it leverages native Windows funcs.
Using WinNT to query local admins on a remote machine:
```powershell
([ADSI]'WinNT://computer_name/Administrators').psbase.Invoke('Members') | %{$_.GetType().InvokeMember('Name', 'GetProperty', $null, $_, $null)}
```
Using Get-NetLocalGroup:
```powershell
Get-NetLocalGroup -ComputerName computer_name
```
Using Get-NetLocalGroup with NetLocalGroupGetMembers API call:
```powershell
Get-NetLocalGroup -ComputerName computer_name -API
```
Get the list of effective users who can access a target system:
```powershell
Get-NetLocalGrouop -ComputerName computer_name -Recurse
```
![[Screenshot 2023-11-03 at 00.45.48.png]]
#### Derivative Local Admin
>It's not uncommon to come across a system of heavily delegated local administrator roles. This system increases the difficulty of tracking down users to gain access to a target system, but greatly increases the chances of gaining that access.

>The answer to "How do we utilize a domain account to work forward and gain access to target machines in these complicated scenarios?" is a concept called "Derivative Local Admin"

Refer to Justin Warner's article for tech details.
#### Identifying Administrator Accounts: Group Enumeration
The old school way for group enumeration is `-GroupName "Domain Admins"`.
PowerView command:
```powershell
Get-NetGroupMember -GroupName "Domain Admins"
```