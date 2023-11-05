# Active Directory Basics
---
## Directory Services
- Means of mapping and providing access to network resources
- Used in almost any organization
- Most are built in hierarchical database
	- Lightweight Directory Access Protocol (LDAP)
- Allows interaction between subjects, objects, resources
- Central platform for configuring and managing
	- Identities (user and computer for accounts, objects)
	- Authentication (credentials for accounts, objects)
	- Authorization
	- Access control (Rights and permissions across systems)
## Active Directory
- Core of AD env is Domain Controller
- DC uses an LDAP directory database
- Configure resources in AD to grant access to resources for users
- Distinguished Name (DN)
	- Unique name that represents collection of attributes for object
	- Built from CN (common name) and DC (domain component)
	- DC = .com
	- DC = .lab
	- OU = Sales
	- CN = John Smith
	- DN = CN=John Smith,OU=Sales,DC=lab,DC=com
## AD Structure
- Organized into Organizational Units (OUs)
	- Allows for hierarchical structure
	- Can be organized many ways  (department, location, etc)
	- Policies & security settings applied to OUs (GPOs)
	- Containers - 'default' OUs
		- Cannot have GPOs applied
- Objects
	- User accounts, groups, service accounts, computer accounts, etc - anything that's joined to domain
- Service Accounts
	- Authentication for applications, services, etc - for each application there is need for having service account to talk to the domain. Usually they have a small amount of network resources to which service accounts have an access.
	- Managed service accounts - password managed by AD
## Kerberos
- Authentication & Authorization system used in many technologies
	- Windows, Linux, MacOS, etc
- Symmetric key cryptography
- Key Distribution Center (KDC) - main component of Kerberos
	- Holds user and service keys
		- Principals - users, applications, services, etc
- Ticket Granting Service (TGS)
	- Lives on KDC
	- Responsible for assign tickets to properly authenticated principals
	- Used to properly authenticate against other principals
	- Eliminates need for constant password authentication
### Kerberos Authentication Process
1. User enters credentials - sent to KDC, receive TGT (Ticket Granting Ticket) - ticket to create other tickets.
	1. if password correct, TGT is properly decrypted
2. User needs access to another system
	1. TGT is sent to TGS requesting access
	2. TGT acts as proof of prior authentication
3. TGS creates second ticket
	1. Used to authenticate against new system
	2. Contains two keys - one of user, one of destination server
4. Destination server receives ticket
	1. Attempts to decrypt key
	2. if successful, proof that KDC issued ticket and is authentic
## Domains and Forests
- All domains are part of a forest
	- Forest - top level of domain hierarchy (even with single domain)
- Each branch of domain/forest - tree
- lab.com (forest)
	- ine.lab.com (Domain)
		- Lab Users (OU)
			- John Smith (user object)
- Domain and Forest name do not need to be different
	- Multiple domains in one forest - cleaner to have different names
	- Single domain - forest and domain are commonly named same
- Forest and Domain functional level
# Active Directory Management
---
Most common methods to manage active directory
## Management Methods & Tools
- Manage directly on DC or remotely
- Active Directory Users & Computers (ADUC)
- Active Directory Administrative Center (ADAC)
- Active Directory Domains and Trusts - managing trust and federation between domains or forests.
- Multiple command-line based tools:
	- NTDSUtil.exe
	- DSQuery.exe
- Powershell
	- ActiveDirectory module
- Group Policy Objects (GPOs)
## Active Directory Users and Computers
- Older tool, since windows 2000
- Capable of majority of management tasks
- More advanced management features require ADAC
	- Newer AD features introduced
- MMC snap-in
## Active Directory Administrative Center
- Newer tool
- Standalone management application
- Capable of advanced management tasks (and new features)
- Shows PowerShell commands for executed tasks
## PowerShell
- Command-line management tool
- Introduces scripting/automation into AD management
- Requires ActiveDirectory module to be installed/loaded
## Group Policy
- Used to apply settings across AD
	- Security configurations
	- Logging settings
	- Permissions
- Settings configured in "objects" (GPOs)
- GPOs applied (linked) to site, domain, OU
- Most frequently used management method
	- Can apply settings to various "parts" of AD
	- Specific OUs, domains, etc
# Read-Only Domain Controllers (RODCs)
---
## Domain Controller functions
- Store AD DS database
	- NTDS.dit
	- Includes user/computer objects (and others), AD structure, etc
- Endpoint for AD management
	- Locally or remotely
- At least 1 is required for AD forest/domain 
	- Previously referred to as PDC
	- More recommended for redundancy & multiple branch locations
- AD DS database is writable 
- Full DCs sometimes shown as RWDC (read-write DC)
	- Usually only when referencing RODC
## Read-Only Domain Controller (RODC)
- Very similar to standard DC
- AD DS database (NTDS.dit) NOT writable
- Synchronizes with full DC on regular basis
	- Changes made on full DCs pushed to RODCs
	- Time synchronization between sites critically important
- Frequently used to have on-premise DC for smaller locations
	- Administrator may not be present
	- No need for local administration of domain
- Users can still authenticate against RODC
	- Computers authenticate and apply GPOs from RODC
## Importance of RODCs
- Minimize attack surface
- Ideal for small branch offices
	- On-premise DC provides faster, more reliable access
	- If network connection to primary DCs lost, office can still function
	- RODC provides all functions, except writing
- RODC holds all AD objects & attributes except passwords
	- Some caching is performed to properly authenticate users
	- RODC contacts RWDC to validate
- Replication is one-way - only from RWDC to RODC
	- Malicious actors that compromise RODC cannot replicate any changes