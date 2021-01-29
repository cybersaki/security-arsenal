# Active Directory Red teaming Security cheatsheet :
#### (commands only, for detailed, look at basic notes)

-------------
## 01 - Intro

#### Powershell help system :
```
Get-Help 

Get-Help *
```
(Lists everything about the help topics.)

```
Get-Help process
```
(Lists everything which contains the word process.)

```
Update-Help
```
(Update the help system(v3+) )

```
Get-help Get-Item -Full
```
(Lists full help about a topic)

```
Get-help Get-Item -Examples
```
(Lists exampels of how to run a cmdlet)

#### Powershell Scripts : Execution policy
Execution policy
Note : It is NOT a security measure, it is present to prevent user from accidently executing script. Several ways to bypass 
```
powershell -ExecutionPolicy bypass
powershell -c <cmd>
powershell -encodedcommand
$enc:PSExecutionPolicyPreference="bypass"
```

####  Powershell modules :
Powershell also support modules. A module can be imported with :
```
Import-Module <modulepath>
```

#### All the commands in a module can be listed with:
```
Get-Command -Module <Module name>
```

-------------------------------
## 02 - Domain Enumeration - 01

Lets start with Domain Enumeration and map various entities, trusts, relationships and privileges for the target domain.
The enumeration can be done by using Native executables and .NET classes:
```
$ADClass = 
[System.DirectoryServices.ActiveDirectory.Domain]
$ADClass::GetCurrentDomain()
```

### Domain Enumeration
#### Get current domain
```
Get-NetDomain (PowerView)
Get-ADDomain (ActiveDirectoryModule)
```

#### Get object of another domain
```
Get-NetDomain -Domain moneycorp.local (Powerview)
Get-ADDomain -Identity moneycorp.local (ActiveDirectoryModule)
```

#### Get domain SID for the current domain
```
Get-DomainSID
(Get-ADDomain).DomainSID
```

To load powerview :
Go to c:\AD\Tools>
Open .\PowerView.ps1

Get-NetDomain
Note : PowerView uses a lot of .NET classes

Open a new powershell session :
cd C:\AD\Tools\ADModule-master\
Import-Module .\Microsoft.ActiveDirectory.Management.dll
Import-Module .\ActiveDirectory\ActiveDirectory.psd1

Now to get information about the current domain :
Get-ADDomain

In the powershell 1 :
Get-NetDomain -Domain moneycorp.local
note : Less information

Now in the powershell 2:
Get-ADDomain -Identity moneycorp.local
Note : More information about parent and child domains.

Now in powershell 1 :
SID of the Domain set:
Get-DomainSID

#### Domain policy :
```
Get-DomainPolicy
```

#### For password policy:
```
(Get-DomainPolicy).*system access*
```

#### For Kerberos policy:
```
(Get-DomainPolicy).*Kerberos Policy*
```

#### Get Domain controllers for the current domain
```
Get-NetDomainController (PowerView)
Get-ADDomainController (ActiveDirectoryModule)
```

#### Get domain controllers for another domain
```
Get-NetDomainController -Domain moneycorp.local
Get-ADDomainController -DomainName moneycorp.local -Discover
```
Now in powershell 1:

Get-NetDomainController
Note : Ipaddress, OSVersion and Name of the domain.

Now in powershell 2:

Get-ADDomainController


#### Get a list of users in the current domain
```
Get-NetUser (PowerView)
Get-NetUser -Username student1 (PowerView)
Get-ADUser -Filter * -Properties * (ActiveDirectoryModule)
Get-ADUser -Identity student1 -Properties * (ActiveDirectoryModule)
```
#### Get list of all properties for users in the current domain
```
Get-UserProperty (Powerview)
Get-UserProperty -Properties pwdlastset (Powerview)
Get-ADUser -Filter * -Properties * | select -First 1 | Get-Member - MemberType *Property | select Name
Get-ADUser -Filter * -Properties * | select name,@{expression-{[datetime]::fromFileTime($_.pwdlastset)}}
```
Note : Use Get-UserProperty -Properties logoncout to find decoy users with no logon count.

#### Search for a particular string in a user's attributes:
```
Find-UserField -SearchField Description -SearchTerm "built" (Powerview)
Get-ADUser -Filter 'Description -like "* built*"' -Properties Description | select name,Description
```

-------------------------------
## 03 - Domain Enumeration - 02

#### Get a list of computers in the current domain
```
Get-NetComputer (PowerView)
Get-NetComputer -OperatingSystem "*Server 2016*" (PowerView)
Get-NetComputer -Ping (PowerView)
Get-NetComputer -FullData (PowerView)

Get-ADComputer -Filter * | select Name (ActiveDirectoryModule)
Get-ADComputer -Filter 'OperatingSystem -like "*Server 2016*"' -Properties OperatingSystem | select Name,OperatingSystem (ActiveDirectoryModule)
Get-ADComputer -Filter * -Properties DNSHostName | %{Test-Connection -Count 1 -ComputerName $_.DNSHOSTName} (ActiveDirectoryModule)
Get-ADComputer -Filter * -Properties * (ActiveDirectoryModule)
```

#### Get all the groups in the current domain
```
Get-NetGroup (PowerView)
Get-NetGroup -Domain <targetdomain> (PowerView)
Get-NetGroup -FullData (PowerView)
Get-ADGroup -Filter * | select Name (ActiveDirectoryModule)
Get-ADGroup -Filter * -Properties * (ActiveDirectoryModule)
```

#### Get all groups containing the word "admin" in group name
```
Get-NetGroup *admin* (PowerView)
Get-ADGroup -Filter 'Name -like "*admin*"' | select Name
```

#### Get all the members of the Domain Admins group
```
Get-NetGroupMember -GroupName "Domain Admins" - Recurse (Powerview)
Get-ADGroupMember -Identity "Domain Admins" -Recursive (ActiveDirectoryModule)
```

#### Get the group membership for a user:
```
Get-NetGroup -UserName "student1" (Powerview)
Get-ADPrincipalGroupMembership -Identity student1 (ActiveDirectoryModule)
```

Note : In membersid, in the last =500 is the unique part of each member

#### List all the local groups on a machine(needs administrator privs on non-dc machines):
```
Get-NetLocalGroup -Computer dcorp-dc.dollarcorp.moneycorp.local -ListGrops (Powerview)
```

#### Get members of all the local groups on a machine (needs administrator privs on non-dc machines)
```
Get-NetLocalGroup -ComputerName dcorp-dc.dollarcorp.moneycorp.local -Recurse
```

#### Get actively logged users on a computer (needs local admin rights on the target)
```
Get-NetLoggedon -ComputerName <servername> (Powerview)
```

#### Get locally logged users on a compter (needs remote registry on the target - started by-default on server OS)
```
Get-LoggedonLocal -ComputerName dcorp-dc.dollarcorp.moneycorp.local (Powerview)
```

#### Get the last logged user on a computer(needs administrative rights and remote registry on the target)
```
Get-LastLoggedOn -ComputerName <servername> (PowerView)
```

#### Find shares on hosts in current domain
```
Invoke-ShareFinder -Verbose (PowerView)
```

#### Find sensitive files on computers in the domain
```
Invoke-FileFinder -Verbose (PowerView)
```

#### Get all fileservers of the domain
```
Get-NetFileServer (PowerView)
```
Note : This will look for a high value target -File server, exchange server or sharepoint server etc.

-------------------------------
## 04 - Domain Enumeration - 03

#### Get list of GPO in current domain.
```
Get-NetGPO (Powerview)
Get-NetGPO -ComputerName dcorp-student1.dollarcorp.moneycorp.local (Powerview)
Get-GPO -All (GroupPolicy module) (ActiveDirectorymodule)
Get-GPResultantSetOfPolicy -ReportType Html -Path C:\Users\Administrator\report.html (Provides RSoP) (ActiveDirectoryModule)
```

#### Get GPO(s) which use Restricted Groups or groups.xml for interesting users
```
Get-NetGPOGroup
```

Now in powershell 1 :
Get-NetGPO | select displayname

To display a particular machine(our machine here) :
Get-NetGPO -ComputerName dcorp-stdadmin.dollarcorp.moneycorp.local

gpresult /R

#### Get users which are in a local group of a machine using GPO
```
Find-GPOComputerAdmin -Computername dcorp-student1.dollarcorp.moneycorp.local (Powerview)
```

#### Get machines where the given user is member of a specific group
```
Find-GPOLocation -UserName student1 -Verbose (Powerview)
```

#### Get OUs in a domain
```
Get-NetOU -FullData (Powerview)
Get-ADOrganizationalUnit -Filter * -Properties * (ActiveDirectoryModule)
```

#### Get GPO applied on an OU. Read GPOname from gplink attribute from Get-NetOU
```
Get-NetGPO -GPOname "{AB306569-220D-43FF-B03B-83E8F4EF8081}" (Powerview)
Get-GPO -Guid AB306569-220D-43FF-B03B-83E8F4EF8081 (GroupPolicyModule) (ActiveDirectoryModule)
```

#### Get the ACLs associated with the specified object
```
Get-ObjectAcl -samAccountName student1 -ResolveGUIDs (Powerview)
```

#### Get the ACLs associated with the specifiedd prefix to be used for search
```
Get-ObjectAcl -ADSprefix ÇN=Administrator,CN=Users' = Verbose (Powerview)
```

#### We can also enumerate ACLs using ActiveDirectory module but without resolving GUIDs
```
(Get-Acl'AD:\CN=Administrator,CN=Users,DC=dollarcorp,DC=moneycorp,DC=local').Access (ActiveDirectoryModule)
```

Note :
On student1 we have buildin permissions in 'Identity reference' with 'ActiveDirectoryRights'
Another interesting option is 'InheritedObjectType' and ObjectSID.

#### Get the ACLs associated with the specified LDAP path to be used for search
```
Get-ObjectAcl -ADSpath "LDAP://CN=Domain Admins,CN=Users,DC=dollarcorp,DC=moneycorp,DC=local" -ResolveGUIDs -Verbose
```

#### Search for interesting ACEs
```
Invoke-ACLScanner -ResolveGUIDs
```

#### Get the ACLs associated with the specified path
```
Get-PathAcl -Path "\\dcorp-dc.dollarcorp.moneycorp.local\sysvol"
```

-------------------------------
## 05 - Domain Enumeration - 04

### Domain trust mapping :
#### Get a list of all domain trusts for all the current domain
```
Get-NetDomainTrust (PowerView)
Get-NetDomainTrust -Domain us.dollarcorp.moneycorp.local (PowerView)

Get-ADTrust(ActiveDirectoryModule)
Get-ADTrust -Identity us.dollarcorp.moneycorp.local(ActiveDirectoryModule)
```

### Forest mapping:
#### Get details about the current forest
```
Get-NetForest (PowerView)
Get-NetForest -Forest eurocorp.local (PowerView)
Get-ADForest (ActiveDirectoryModule)
Get-ADForest -Identity eurocorp.local (ActiveDirectoryModule)
```

#### Get all the domains in the current forest
```
Get-NetForestDomain (PowerView)
Get-NetForestDomain -Forest eurocorp.local (Powerview)
(Get-ADForest).Domains (ActiveDirectoryModule)
```

#### Get all global catalogs for the current forest
```
Get-NetForestCatalog (PowerView)
Get-NetForestCatalog -Forest eurocorp.local (PowerView)
Get-ADForest | select -ExpandProperty GlobalCatalogs (ActiveDirectoryModule)
```

#### Map trusts of a forest
```
Get-NetForestTrust (PowerView)
Get-NetForestTrust -Forest eurocorp.local (PowerView)
Get-ADTrust -Filter 'msDS-TrustForestTrustInfo -ne "$null"' (ActiveDirectoryModule)
```

### Domain enumeration - User hunting
####  Find all machines on the current domain where the current user has local admin access
```
Find-LocalAdminAccess -Verbose (PowerView)
```

#### This function queries the DC of the current or provided domain for a list of computers(Get-NetComputer) and then use multi-threaded
```
Invoke-CheckLocalAdminAccess on each machine.
```

Note : This can be done with the help of remote administration tools like WMI and powershell remoting.Pretty useful in cases ports(RPC and SMB) usd by Find-LocalAdminAccess are blocked.

#### See :
```
Find-WMILocalAdminAccess.ps1 (Powerview)
```

#### Find local admins on all machines of the domain (needs administrator privs on non-dc machines).
```
Invoke-EnumerateLocalAdmin -Verbose (Powerview)
```

Note: This function queries the DC of the current or provided domain for a list of computers(Get-NetComputer) and then use multi-threaded Get-NetLocalGroup on each machine.
(Powerview)

#### Find computers where a domain admin(or specified user/group) has sessions:
```
Invoke-UserHunter (Powerview)
Invoke-UserHunter -GroupName "RDPUsers" (Powerview)
```

Note : This function queries the DC of the current or provided domain for members of the given group (Domain Admins by default) using Get-NetGroupMember, gets a list of computers(Get-NetComputer) and list sessions and logged on users(Get-NetSession/Get-NetLoggedon) from each machine (Powerview)

#### To confirm admin access
```
Invoke-UserHunter -CheckAccess (Powerview)
```

Now in powershell 1:

PS C:\AD\Tools> . .\Find-WMILocalAdminAccess.ps1

Find-WMILocalAdminAccess -ComputerFle .\computers.txt -Verbose


#### Find computers where a domain admin is logged-in
```
Invoke-UserHunter -Stealth (PowerView)
```

Note : This option queries the DC of the current or provided domain for member sof the given group(Domain Admins by default) using Get-NetGroupMember, gets a list_only_of high traffic servers (DC,File Servers and Distributed File servers) for less traffic generation and list sessions and logged on users(Get-NetSession/Get-NetLoggedon) from each machine.
(PowerView)

Note : 
-Netcease is a script which changes permissions on the NetSessionEnum method by removing permission for Authenticated Users group.
-This fails many of the attacker's session enumeration and hence user hunting capabilities.
	.\NetCease.ps1
-Another interesting script from the same author is SAMRi10 which hardens Windows 10 and Server 2016 against enumeration which uses SAMR protocol(like net.exe)

-------------------------------
## 06 - Local Privilege Escalation - 01

#### We can use below tools for complete coverage
	- PowerUP:
	- BeRoot:
	- Privesc:

### Services issues using PowerUp:
####  Get Services with unquoted paths and a space in their name.
```
Get-ServiceUnquoted -Verbose (Powerview)
```

####  Get services where the current user can write to its binary path or change arguments to the binary.
```
Get-ModifiableServiceFile -Verbose (Powerview)
```

####  Get the services whose configuration current user can modify. (Powerview)
```
Get-ModifiableService-Verbose (Powerview)
```

Practice. In powershell 1:

PS C:\AD\Tools> C:\FTPServer\FTP Server\filezilla\filezilla.exe

Note : Notice the space in FTP Server.
Also filezilla is not vulnerable to this, just for example.

```
GetWmiObject -class win32_service | fl *
GetWmiObject -class win32_service | select pathname
```

#### Run all checks from:
	-Powerup
	 Inboke-AllChecks (Powerview)

	-BeRoot is an executable:
	.\beRoot.exe (Powerview)

	-Privesc
	 Invoke-PrivEsc (Powerview)
 
Now in powershell 1:

C:\AD\Tools> . .\PowerUp.ps1

```
Invoke-AllChecks

GetWmiObject -class win32_service | select pathname
```
Note : Abusefunction will be display the path which can be hijacked.

Next check service permissions, and now in this path.
Here we can make it into the new executable itself. And CanRestart means could restart the service.

Now for beRoot :
cd .\beRoot\
PS C:\AD\Tools\beRoot> .\beRoot.exe

Look only for service issues right now. It will be in the first.

Now checking privesc :
cd ..\Privesc-master\Privesc-master\
PS C:\AD\Tools\Privesc-master\Privesc-master> . .\privesc.ps1

```
Invoke-privesc
```
Check for "services with space in path" and check for "service permissions".

-------------------------------
## 07 - Local Privilege Escalation - 02













