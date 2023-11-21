An AD environment has a critical dependency on the _Domain Name System_ (DNS) service. As such, a typical domain controller will also host a DNS server that is authoritative for a given domain.

>Members of _Domain Admins_[3](https://portal.offsec.com/courses/pen-200/books-and-videos/modal/modules/active-directory-introduction-and-enumeration/active-directory-introduction/active-directory-introduction#fn3) are among the most privileged objects in the domain

>members of the _Enterprise Admins_ group are granted full control over all the domains in the forest and have Administrator privilege on all DCs.

`net user /domain` - query user accounts for a domain
`net user <username> /domain` - query info about specific user
`net group /domain` - query groups in the domain
`net group <groupname> /domain` - query info about specific group, its members

>We should look for the DC that holds the most updated information. This is known as the _Primary Domain Controller_ (PDC). There can be only one PDC in a domain. To find the PDC, we need to find the DC holding the _PdcRoleOwner_ property.

# .Net classes
Reveal the PdcRoleOwner property with a name of PDC.
```powershell
[System.DirectoryServices.ActiveDirectory.Domain]::GetCurrentDomain()
```

```powershell
# Store the domain object in the $domainObj variable
$domainObj = [System.DirectoryServices.ActiveDirectory.Domain]::GetCurrentDomain()

# Store the PdcRoleOwner name to the $PDC variable
$PDC = $domainObj.PdcRoleOwner.Name

# Print the $PDC variable
$PDC
```
To get DN:
```powershell
([adsi]'').distinguishedName
```
Script to construct LDAP path:
```powershell
$PDC = [System.DirectoryServices.ActiveDirectory.Domain]::GetCurrentDomain().PdcRoleOwner.Name
$DN = ([adsi]'').distinguishedName 
$LDAP = "LDAP://$PDC/$DN"
$LDAP
```
Script to get domain info by LDAP request:
```powershell
$PDC = [System.DirectoryServices.ActiveDirectory.Domain]::GetCurrentDomain().PdcRoleOwner.Name
$DN = ([adsi]'').distinguishedName 
$LDAP = "LDAP://$PDC/$DN"

$direntry = New-Object System.DirectoryServices.DirectoryEntry($LDAP)

$dirsearcher = New-Object System.DirectoryServices.DirectorySearcher($direntry)
$dirsearcher.FindAll()
```
Script to filter domain info by samAccountType (which is search for the users in the following example):
```powershell
$PDC = [System.DirectoryServices.ActiveDirectory.Domain]::GetCurrentDomain().PdcRoleOwner.Name
$DN = ([adsi]'').distinguishedName 
$LDAP = "LDAP://$PDC/$DN"

$direntry = New-Object System.DirectoryServices.DirectoryEntry($LDAP)

$dirsearcher = New-Object System.DirectoryServices.DirectorySearcher($direntry)
$dirsearcher.filter="samAccountType=805306368"
$dirsearcher.FindAll()
```
Script to iterate through each object and print each property of this object:
```powershell
$domainObj = [System.DirectoryServices.ActiveDirectory.Domain]::GetCurrentDomain()
$PDC = $domainObj.PdcRoleOwner.Name
$DN = ([adsi]'').distinguishedName 
$LDAP = "LDAP://$PDC/$DN"

$direntry = New-Object System.DirectoryServices.DirectoryEntry($LDAP)

$dirsearcher = New-Object System.DirectoryServices.DirectorySearcher($direntry)
$dirsearcher.filter="samAccountType=805306368"
$result = $dirsearcher.FindAll()

Foreach($obj in $result)
{
    Foreach($prop in $obj.Properties)
    {
        $prop
    }

    Write-Host "-------------------------------"
}
```
Filter by any property:
```powershell
$dirsearcher = New-Object System.DirectoryServices.DirectorySearcher($direntry)
$dirsearcher.filter="name=jeffadmin"
$result = $dirsearcher.FindAll()

Foreach($obj in $result)
{
    Foreach($prop in $obj.Properties)
    {
        $prop.memberof
    }

    Write-Host "-------------------------------"
}
```
Encapsulate the current functionality into an actual function:
```powershell
function LDAPSearch {
    param (
        [string]$LDAPQuery
    )

    $PDC = [System.DirectoryServices.ActiveDirectory.Domain]::GetCurrentDomain().PdcRoleOwner.Name
    $DistinguishedName = ([adsi]'').distinguishedName

    $DirectoryEntry = New-Object System.DirectoryServices.DirectoryEntry("LDAP://$PDC/$DistinguishedName")

    $DirectorySearcher = New-Object System.DirectoryServices.DirectorySearcher($DirectoryEntry, $LDAPQuery)

    return $DirectorySearcher.FindAll()

}
```
To enumerate every group:
```powershell
foreach ($group in $(LDAPSearch -LDAPQuery "(objectCategory=group)")) {
>> $group.properties | select {$_.cn}, {$_.member}
>> }
```

>Nested groups are relatively common in AD and scales well, allowing flexibility and dynamic membership customization of even the largest AD implementations.

# PowerView
The same with powerview:
`Get-NetUser` - query users or specific user
`Get-NetDomain` - query domain
`Get-NetGroup` - query groups or specific group
`Get-NetComputer` - query computers or specific computer
>When an attacker or penetration tester improves access through multiple higher-level accounts to reach a goal, it is known as a _chained compromise_.

# Permissions and Logged on Users
`Find-LocalAdminAccess` - The command relies on the _OpenServiceW function_, which will connect to the _Service Control Manager_ (SCM) on the target machines. The SCM essentially maintains a database of installed services and drivers on Windows computers. PowerView will attempt to open this database with the _SC_MANAGER_ALL_ACCESS_ access right, which require administrative privileges, and if the connection is successful, PowerView will deem that our current user has administrative privileges on the target machine.
`Get-NetSession` - uses the _NetWkstaUserEnum_ and _NetSessionEnum_ APIs under the hood. 
>The permissions required to enumerate sessions with _NetSessionEnum_ are defined in the **SrvsvcSessionInfo** registry key, which is located in the **HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\LanmanServer\DefaultSecurity** hive.

`Get-Acl -Path ` - get permissions for the object. Returns SID - A capability SID is an _unforgeable_ token of authority that grants a Windows component or a Universal Windows Application access to various resources.

Alternative to Get-NetSession is PsLoggedOn app from SysInternals Suite.

>PsLoggedOn relies on the _Remote Registry_ service in order to scan the associated key. The Remote Registry service has not been enabled by default on Windows workstations since Windows 8, but system administrators may enable it for various administrative tasks, for backwards compatibility, or for installing monitoring/deployment tools, scripts, agents, etc.

# Enumeration through Service Principal Names
> Applications must be executed in the context of an operating system user. If a user launches an application, that user account defines the context. However, services launched by the system itself run in the context of a _Service Account_.
> In other words, isolated applications can use a set of predefined service accounts, such as _LocalSystem_, LocalService, and _NetworkService_.

>When applications like _Exchange_, MS SQL, or _Internet Information Services_ (IIS) are integrated into AD, a unique service instance identifier known as _Service Principal Name_ (SPN) associates a service to a specific service account in Active Directory.

We can enumerate services vie SPNs and obtain its IP address and port number. Using setspn.exe for that:
```
setspn -L iis_service
Registered ServicePrincipalNames for CN=iis_service,CN=Users,DC=corp,DC=com:
        HTTP/web04.corp.com
        HTTP/web04
        HTTP/web04.corp.com:80
```
Using PowerView:
```powershell
Get-NetUser -SPN | select samaccountname,serviceprincipalname

samaccountname serviceprincipalname
-------------- --------------------
krbtgt         kadmin/changepw
iis_service    {HTTP/web04.corp.com, HTTP/web04, HTTP/web04.corp.com:80}
```
Resolve found hostnames with nslookup.
Since these types of accounts are used to run services, we can assume that they have more privileges than regular domain user account.
# Enumerating Object Permissions
>In short, an object in AD may have a set of permissions applied to it with multiple _Access Control Entries_ (ACE).These ACEs make up the _Access Control List_ (ACL). Each ACE defines whether access to the specific object is allowed or denied.

>As a very basic example, let's say a domain user attempts to access a domain share (which is also an object). The targeted object, in this case the share, will then go through a validation check based on the ACL to determine if the user has permissions to the share. This ACL validation involves two main steps. In an attempt to access the share, the user will send an _access token_, which consists of the user identity and permissions. The target object will then validate the token against the list of permissions (the ACL). If the ACL allows the user to access the share, access is granted. Otherwise the request is denied.

A list of most interesting permission types:
```
GenericAll: Full permissions on object
GenericWrite: Edit certain attributes on the object
WriteOwner: Change ownership of the object
WriteDACL: Edit ACE's applied to object
AllExtendedRights: Change password, reset password, etc.
ForceChangePassword: Password change for object
Self (Self-Membership): Add ourselves to for example a group
```
Enumerate it with PowerView:
`Get-ObjectAcl -Identity <user-for-example>`
To convert SIDs to Name basically:
`Convert-SidToName <sid>`
In Get-ObjectAcl's output we interested in objectSID, ActiveDirectoryRights and SID. objectSID is an sid to whom ACE belongs to, and SID is objects for which access is granted.
Use -eq flag in Powershell to filter the ActiveDirectoryRights:
```
Get-ObjectAcl -Identity "Management Department" | ? {$_.ActiveDirectoryRights -eq "GenericAll"} | select SecurityIdentifier,ActiveDirectoryRights
```
# Enumerating Domain Shares
`Find-DomainShare` and also it has -CheckShareAccess flag to display shares only available for us.
# Automated Enumeration
>SharpHound is written in C# and uses Windows API functions and LDAP namespace functions similar to those we used manually in the previous sections. For example, SharpHound will attempt to use NetWkstaUserEnum and NetSessionEnum to enumerate logged-on sessions, just as we did earlier. It will also run queries against the Remote Registry service, which we also leveraged earlier.