# Domain Enumeration

- PowerShell command to get information about the forest, domain controllers, domain name. 

```
$ADClass = [System.DirectoryServices.ActiveDirectory.Domain]

$ADClass::GetCurrentDomain()
```

## PowerView

- May get blocked by AMSI

https://github.com/PowerShellMafia/PowerSploit/blob/dev/Recon/PowerView.ps1



### Basic Domain Information 

- Basic domain enumeration:

```
Get-Domain

Get-Domain -Domain <Domain-Name>

Get-DomainSID
```

- Get domain policy:

```
Get-DomainPolicy

(Get-DomainPolicy)."system access"

(Get-DomainPolicy)."kerberos policy"

Get-DomainPolicy -domain <domain-name>
```

```
Get-DomainPolicyData | select -expand SystemAccess
```


- Get domain controllers:

```
Get-DomainController

Get-DomainController -Domain <domain-name>
```

### User Information

- List of all users

```
Get-DomainUser
```
 - List a specific user 

```
Get-DomainUser -Identity <username>
Get-DomainUser -Identity <username> -Properties <property1> <property2> <property..> 
```


- Find all machines where current user has local admin access
  - Performs Get-NetComputer and then Invoke-CheckLocalAdminAccess
  - Noisy

```
Find-LocalAdminAccess -Verbose
```



### Computer Information

- List of computers
  - ping uses ICMP

```
Get-DomainComputer
Get-DomainComputer -Properties <property> | sort -Property <property>

```



### Group Information

- List groups 

```
Get-DomainGroup
```

- List members of groups

```
Get-DomainGroupMember -Identity <group name> 
```

- List local groups on a machine (needs admin privs on non-dc machines)

```
Get-LocalGroup -ComputerName <name> -ListGroups
```

- Get members of local groups on a machine (needs admin privs on non-dc machines)

```
Get-LocalGroup -ComputerName <name> -Recurse
```

### Logon Information

- Get actively logged in users on a computer (local admin)

```
Get-Loggedon -ComputerName <name>
```

- Get locally logged users (needs remote registry)

```
Get-LoggedonLocal -ComputerName <name>
```

- Get last logged user (needs admin and remote registry)

```
Get-LastLoggedOn -ComputerName <name>
```

### Shares/Files Information

- Find shares on hosts

```
Invoke-ShareFinder -Verbose

Invoke-ShareFinder -verbose -ExcludeStandard -ExcludePrint -ExcludeIPC
```

- Find sensitive files on computers

```
Invoke-FileFinder -verbose
```

- Get all fileservers

```
Get-NetFileServer -verbose
```

### GPO Information

- List of GPO

```
Get-DomainGPO

Get-DomainGPO -Properties DisplayName
```

- List of GPOs that modify local group membership

```
Get-DomainGPOLocalGroup
```

- List machines where a specific user/group is a member of a specific group

```
Get-DomainGPOUserLocalGroupMapping -LocalGroup <group name> 
```





### Organisational Unit Information

- Get Organisational Units in a domain

```
Get-DomainOU -Properties <property>
```

- Find out which GPO is applied on an OU

```
Get-GPO -GPOname "{<GPOname from gplink attribute in Get-NetOU}"
```

### Domain ACL Information

- Get ACLS associated with object

```
Get-ObjectACL -SamAccountName <name> -ResolveGUIDs

Get-ObjectACL -ADSprefix 'Cn=XXXXX,CN=XXXX' -Verbose
```

- Find interesting ACEs

```
Invoke-ACLScanner -ResolveGUIDs
```

- Get ACL associated with a path

```
Get-PathAcl -Path "\\"
```

### Trusts 

- Get list of domain trusts

```
Get-DomainTrust
```

- Get list of forest trusts

```
Get-Forest

Get-Forest -Forest <name>

Get-ForestDomain

Get-ForestDomain -Forest <name>
```

- Get All global catalogs for the current forest

```
Get-ForestCatalog

Get-ForestCatalog -Forest <name>

Get-ForestTrust
Get-ForestTrust -Forest <name>
```
## SharpView

https://github.com/tevora-threat/SharpView

- .Net Port of SharpView

## ActiveDirectory Powershell Module 

- Microsoft Signed
- https://docs.microsoft.com/en-us/powershell/module/addsadministration/?view=win10-ps
- https://github.com/samratashok/ADModule

### Installing:

```
Import-Module .\Microsoft.ActiveDirectory.Management.dll

Import-Module .\ActiveDirectory.psd1
```

### Basic Domain Information

- Basic domain enumeration:

```
Get-AdDomain

Get-ADDomain -Identity <Domain-Name>
```

- Get domain controllers:

```
Get-ADDomainController

Get-AdDomainController -DomainName <domain-name> -Discover
```

### Users Information

- List of users

```
Get-ADUser -Filter * -Properties *

Get-ADUser -Identity <username> -Properties *
```

- Get List of all properties for users

```
Get-ADUser -Filter * -Properties * | select -First 1 | Get-Member -MemberType *property | select Name

Get-ADUser -Filter * -Properties * | select name, @{expression {[datetime]::fromFileTme{$_.pwdLastset}}}
```

- Search for sensitive information id Description field 

```
Get-ADUser -Filter 'Description -like "*built""' -Properties Descritpion | select name, Description 
```

### Computers Information

- List of computers

- test-connection uses ICMP

```
Get-ADComputer -Filter * | select Name

Get-ADComputer -Filter 'OperatingSystem -like "*searchterm"' -Properties OperatingSystem | select Name,OperatingSystem

Get-ADComputer -Filter * -Properties DNSHostName | %{Test-Connection -Count 1 -ComputerName $_.DNSHostName}

Get-ADComputer -Filter * -Properties * 
```

### Groups Information

- List Groups

```
Get-ADGroup -Filter * | select Name

Get-ADGroup -Filter * -Properties * 

Get-ADGroup -Filter 'Name -like "*admin*"' | select Name
```

- List members of a group

```
Get-ADGroupMember -Identity "search term" -Recursive

Get-ADPrincipalGroupMembership -Identity <username>
```

### GPO Information

- List GPOs

```
GET-GPO -All

Get-GPResultantSetOfPolicy -ReportType Html -Path C:\Users\Administrator\report.html
```

- Get OUs in a domain

```
Get-ADOrganizationalUnit -Filter * -Properties * 
```

- Find out which GPO is applied on an OU

```
Get-GPO -GUID <GPONAME from gplink attribute>
```

### ACL Information

- Enumerate ACLs

```
(Get-Acl 'AD:\CN=xxx\CN=xxx,DC=xxxx,DC=XXXXX').access
```

### Trusts

- Get domain trusts

```
Get-ADTrust *

Get-ADTrust -Identity <name>
```

- Get forest trusts

```
Get-ADForest *

Get-ADForest -Identity <name>

(Get-ADForest).Domains 
```

- Get forest mappings

```
Get-ADForest | select -ExpandProperty GlobalCatalogs

Get-ADTrust -Filter 'msDS-TrustForestTrustInfo -ne "$null" '
```

## ADSearch

https://github.com/tomcarver16/ADSearch

- Specify LDAP searches.

```
ADSearch.exe --search "<LDAP query>"
```

- Example to search for a domain group ending in admin

```
ADSearch.exe --search "(&(objectCategory=group)(cn=*Admins))"
```

- The output can be formatted in JSON with `--json`
