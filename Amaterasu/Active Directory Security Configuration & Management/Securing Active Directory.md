# AD Best Practices
---
- Be mindful of common misconfigurations and attacks
- Standard server hardening recommendations
	- Change default passwords
	- Least privilege
		- Physical and remote access
- Standard security best practices
	- Proper logging and alerting
	- Documentation and auditing
- Proper backup of AD (with testing)
	- Both for small restores and full DR
- Use administrative workstations for managing AD
- DCs are ONLY DCs
## Least Privilege
- Local administrator accounts
	- Users should not be local admins
	- Limits capabilities for attackers
- Domain administrators
	- Should never be used for general tasks - only admin when needed
		- Separate accounts for admins
	- JIT access
		- Accounts do not need perpetual admin permissions
- Segmented admin accounts
	- Workstations administrators
	- Server administrators
	- Domain-level administrators
## Password and Accounts Security
- Recommended to follow NIST guidelines
	- SP 800-63B Section 5.1.1 (Memorized Secrets)
	- Minimum 8 chars, up to 64 chars, spaces allowed
	- Check against dictionary, compromised lists, etc
	- No periodic password changes
		- Only force change on suspected compromise
- Use LAPS for local admin accounts
- Use automated account lockout features
- Monitor password resets
- Proper IAM workflows
- Use Managed Service Accounts (MSAs) when possible
## Security (and other) Protocols
- Disable "legacy" protocols
	- LLMNR
		- No needed with proper DNS
	- NTLM
		- Replaced by Kerberos
		- Be careful! - Some applications may still need
- IPv6
	- Work with network team
	- If not used internally, consider disabling on servers/workstations
# Group Policy 
---
- Used to apply settings across AD
	- Security configurations
	- Logging settings
	- Permissions
	- OS configurations
- Settings configured in "objects" (GPOs)
- GPOs applied (linked) to site, domain, OU
- Most frequently used management method
	- Can apply settings to various "portions" of AD
	- Specific OUs, domains, etc
## GPO Functionality
- Policies created, but without being applied anywhere
	- Settings configured within policies
	- Typically, groups of similar settings
- Policies linked to desired location (OU)
- GPOs are stored to Central Store
	- Each identified by unique GUID
	- Group Policy container (GPC)
		- AD container that contains GPO properties
	- Group Policy template (GPT)
		- File folder that includes additional data
		- SYSVOL folder on DC
## Group Policy Templates
- Administrative templates
	- .ADMX (.ADM) files
- Expand capabilities of Group Policy
	- Browser configuration
	- Third-party application settings
- Files copied to Central Store
	- .ADML - language files
- Central Store
	- Folder in SYSVOL folder on DC
	- `\\dc.com\SYSVOL\domain\policies\...``
## GPO Best Practices & Recommendations
- Group settings together/small GPOs
	- Application use or purpose
	- Security purpose 
- Keep detailed documentation about settings & links
- Minimize use of blocking and enforcement
- Well-designed, organized OU structure
- Limit accounts with modify permissions
- Default Domain Policy/Default DC Policy
- Use `gpresult` for testing & troubleshooting
# Group Policy Demo
---
Group Policy Management:
- Group Policy Objects
	- New GPO
		- Edit...
			- GP Management Editor
				- Computer Configuration
					- Policies
						- Windows Settings
							- Security Settings
								- Audit Policy
- Link somewhere
	- Click and drag on Domain Controllers
# Securing Group Policy
---
## Group Policy Permissions
- GPO Permissions
	- Verify no non-admin have owner, write, or modify
- GPT Permissions
	- Similar check on ability to edit/modify GPT XML files
- SYSVOL & NETLOGON
	- By default, all AD objects have read-only permissions
- OU Permissions
	- Non-admin should not have ability to modify or create
# Fixing Common AD Misconfigurations
---
## Misconfigurations
- Account & password policies
- NTLM
	- May be disabled by default, but still verify
- DC connectivity (RDP only)
	- Restrict through network firewall rules, or local firewall rules
	- If using local firewall rules, use GPO to enforce
- Secure sensitive accounts & functions
	- All admin accounts, including local
	- Service accounts
	- Domain join functionality
- Use tools to scan and test AD
# Active Directory Monitoring & Logging
---
## Windows Event Logs
- Logs of everything that happens in Windows
	- Application
	- Security
- Not all enabled by default
- Central management through GPO
	- Audit policies
- Log Forwarding
	- Ability to send logs to other Windows servers
	- Using native Windows features only
	- Third-party solution required for anything else (syslog, etc)
## Active Directory Logs
- Can be viewed in Event Viewer
	- Not ideal method, but can be used
- Complete coverage requires additional configuration
	- Group Policy
	- Account/logon auditing settings
- Recommended to offload logs to other system
	- Easy of viewing
	- Log retention
	- Automated analysis and alerting
## Audit Policies
![[Screenshot 2023-11-05 at 21.53.54.png]]
## Offloading Logs from DCs
- Third-party solution recommended
	- Splunk
	- Security Onion
	- Multiple other vendors
- AD logs significant amount of data
	- Especially with larger organizations
	- All user logins/logouts
	- Kerberos ticket creation/use
	- Account modifications
- Without offloading, retention can be measured in hours
## AD-Specific Event IDs
- Microsoft recommended IDs to monitor
	- 380+ total
	- 12 high priority
	- ~80 medium
	- the rest is low priority or eventual
- 1102 - Audit log cleared
- 4765 - SID History was added to an account
- 4739 - Domain policy changed
- 4660 - Object deleted
- 4625 - Account failed to log on
- 4719 - System audit policy was changed
- etc
## Best Practices
- Identify auditing and logging goals
	- Requires thorough understanding of AD environment
- Enable audit policies as needed to meet goals
	- Domain-wide GPO, including DCs
- Offload logs to dedicated log/monitoring server
	- Backup this server and logs
- Monitor appropriate event IDs based on goals & needs
	- Logon/Logoff
	- Account modification/management/access
	- Policy changes
	- Details on privileged accounts
- Continuous review process of all logs and processes