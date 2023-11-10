https://learn.microsoft.com/en-us/windows-server/identity/ad-ds/manage/understand-security-principals
# Security Principal
Security principals - an entity that can be authenticated by OS. For instance, user account, computer account, thread or process that runs in the security context of a user or computer account, or the security groups for these accounts. 
# Security Identifier
Each security principal is represented by unique security identifier - SID.

Each security principal is automatically assigned a SID when it's created. The SID is stored in a security database.

Each time a user sign in, the system creates an access token for that user. The access token contains the following:
- user's SID,
- user rights,
- SIDs for groups that the user belongs to,
The access token provides the security context for whatever actions the user performs on that computer.
## Security Identifier architecture
![[Pasted image 20231110202759.png]]
`S-R-X-Y1-Y2-Yn-1-Yn`
S - string is SID
R - revision level
X - identifier authority value
Y - a series of subauthority values
`S-1-5-32-544`
- A revision level (1)
- An identifier authority value (5, NT Authority)
- A domain identifier (32, Builtin)
- A relative identifier (544, Administrators)
`S-1-5-21-1004336348-1177238915-682003330-512`
- A revision level (1)
- An identifier authority (5, NT Authority)
- A domain identifier (21-1004336348-1177238915-682003330, Contoso)
- A relative identifier (512, Domain Admins)
# Access Token
If user's authentication is successful, the process returns a SID for the user and a list of SIDs for the user's security groups. 
The Local Security Authority (LSA) uses this information to create an access token. 
After that, the copy of the access token is attached to every thread and process that executes on the user's behalf. Whenever a thread or process interacts with a securable object or tries to perform a system task that requires user rights, the OS checks the access token that's associated with the thread to determine the level of authorization.
Two types of access tokens:
- Primary - assigned to a process to represent the default security information for that process.
- Impersonation - enable a thread to run in a security context that differs from the security context of the process that owns the thread.
# Security Descriptors
Security descriptor is a data structure that's associated with each securable object. Security descriptor includes information:
- who owns an object.
- who can access it and in what way.
- what types of access are audited.
- access control list of an object which includes all the security permissions that apply to that object.
# Access Control
![[Pasted image 20231110201736.png]]
Security descriptor can contain two types of ACLs:
- a discretionary access control list (DACL) - identifies the users and groups who are allowed or denied access.
- a system access control list (SACL) - controls how access is audited.
We can use this access control model to individually secure objects and attributes such as:
- files
- folders
- Active Directory objects
- registry keys
- printers
- devices
- services
- processes
- threads
# Permissions
Permissions enable the owner of each securable object to control who can perform an operation or a set of operations on the object or object property.

Permissions are expressed in the security architecture as ACEs.
Permissions are different from user rights. Permissions are attached to objects, and user rights apply to user accounts.

Admins can assign user rights to groups or users. These rights authorize users to perform specific actions, such as signing in to a system interactively or backing up files and directories.

On computers, user rights enable admins to control who has the authority to perform operations that affect an entire computer. 

Although user rights can be managed centrally through Group Policy, they are applied locally. Users can have different user rights on different computers.
# Security context in authentication
A user accounts enables a user to sign in to computers, networks, and domains with an identity that can be authenticated by the computer, network, or domain.

Security principals have accounts, which can be local to a computer or domain-based. For example, domain-joined Windows client computers can participate in a network domain by communicating with a domain controller, even when no user is signed in.

To initiate communications, the computer must have an active account in the domain. Before accepting communications from the computer, LSA on the domain controller authenticates the computer's identity and then defines the computer's security context.

The security context defines the identity and capabilities of a user or a service on a particular computer, or of a user, service, group or computer on a network. For example, it defines the resources that can be accessed and the actions that can be performed by a user, service or computer on that resource.

The security context can vary from one computer to another such as when a user authenticates to a server or a workstation other than the user's primary workstation.
# Globally unique identifiers
When a new domain user or group account is created, AD stores the account's SID in the ObjectSID property of a user or group object. 
It also assigns the new object a globally unique identifier (GUID) which is 128-bit value that's unique not only in the enterprise, but also across the world. Each object's GUID is stored in its ObjectGUID property.
AD uses GUIDs internally to identify objects. GUID is one of an object's properties that's published in the global catalog. 

When a user object moves from one domain to another, a new SID must be generated for the user account and stored in ObjectSID property. Before the new value is written to the property, the privous value is copied to another property of a User object, SIDHistory.

SIDHistory can hold multiple values. Each time a user objects moves to another domain, a new SID is generated and stored in the ObjectSID property, and another value is added to the list of old SIDs in SIDHistory.

When a user signs in and is successfully authenticated, the domain auth service queries AD for all the SIDs associated with the user, the new, the old and the SIDs for the user's groups. All this SIDs are returned to the authentication client and they're included in the user's access token.

When a user changes domains, there's no need to change the access control list (ACL) on any resource. If an ACL has the user's old SID, but not the new one, the old SID is still in the user's access token.