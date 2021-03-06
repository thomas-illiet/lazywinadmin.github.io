---
layout: single
title: "[UPDATE] PowerShell - Monitor and Report Active Directory Group Membership Change"
excerpt: 
permalink: /2013/11/update-powershell-monitor-and-report.html
tags: 
- active directory
- group membership
- monitoring
- organizational unit
- powershell
- report
published: true
comments: true
---
<a href="http://2.bp.blogspot.com/-85afUuHt0y0/UpLqk-EUUrI/AAAAAAABe7o/K1uCfaZAhto/s1600/2013-10-13+10-11-52+PM.png" imageanchor="1" style="clear: left; float: left; margin-bottom: 1em; margin-right: 1em;"><img border="0" src="https://2.bp.blogspot.com/-85afUuHt0y0/UpLqk-EUUrI/AAAAAAABe7o/K1uCfaZAhto/s1600/2013-10-13+10-11-52+PM.png" /></a>
<span style="background-color: yellow;"><b><u>UPDATE:</u></b> The most recent update is available <a href="https://github.com/lazywinadmin/AD-GROUP-Monitor_MemberShip" target="_blank">on Github</a>

I found some time to update the script "<b>Monitor Active Directory Membership changes</b>". This is the version 1.6.

To summarize, this script allow you to monitor Active Directory groups membership changes. The script will send your a report via email only when a change occur. I explained in details<a href="{{ site.url }}/2013/10/powershell-monitor-and-report-active.html" target="_blank"> in my last post how the script work</a>.

<u>
</u><u>So what are the main changes in this version ?</u>


* <b>SearchRoot</b>you can now specify the Organization Unit path(s) where all your groups are located, the script will take care of the rest and watch them all. You also have the option to filter using the parameters <b>SearchScope</b>, <b>GroupType</b>, <b>GroupScope</b>.

* <b>File</b> you can now specify one or multiple files where the list of groups is saved. Distinguished Names, SID, GUID, GroupName, Domain\GroupName are accepted.

<u>Previous post related to this script:</u>
<b>[2013/10]</b> <a href="{{ site.url }}/2013/10/powershell-monitor-and-report-active.html" target="_blank">PowerShell - Monitor and Report Active Directory Group Membership Change</a>
<b>[2012/03]</b> <a href="{{ site.url }}/2012/03/powershell-monitor-membership-change-to.html" target="_blank">Powershell - Monitor Active Directory Groups membership change</a>

<b><u>Thank you:</u></b> I want to thank those who sent me suggestions via email or posts comments, I'm very happy to see that this script is helping a lot of my fellow sysadmins.




# Overview


* Version 1.6 Changes

* Requirements

* Report Example

* Using the Script

* Running the script the first time

* Running the script a second time

* Running the script after a change

* Using the SearchRoot parameter (Organizational Unit path)

* Using the File parameter

* Syntax

* Help

* Download



# Version 1.6 Changes


* <b>Support for Organization Unit path(s)</b>

* SearchRoot parameter You can know specify a DN or a CanonicalName where all the groups you want to monitor are located.

* SearchScope parameter

* Base : Limits the search to the base (SearchRoot) object.The result contains a maximum of one object.

* OneLevel : Searches the immediate child objects of the base (SearchRoot) object, excluding the base object.

* Subtree : Searches the whole sub-tree, including the base (SearchRoot) object and all its child objects.

* GroupScope parameter Specify the group scope of groups you want to find. Acceptable values are :

* Global

* Universal

* DomainLocal

* GroupType parameter Specify the group type of groups you want to find. Acceptable values are:

* Security

* Distribution

* <b>Support for File(s)</b>

* Input Specify the File where the Group are listed. DN, SID, GUID, or Domain\Name of the group are accepted

* <b>Update of the ParameterSetNames</b>

* Group

* Organization Unit

* List

* <b>Change History files</b>

* In the previous version, a file was generate for each change of each group, I decided to use only one file per group for the change history. The script will just append the new information to it.

* <b>Membership and Changes History files changes</b>

* ADD Properties <b>DisplayName</b> and the <b>DateTime</b>

* <b>Report changes</b>

* ADD Additional information on the Group

* ADD Title include the DOMAIN\GroupName

* <b>[MailAddress]</b> type which is available in PowerShell v3.0 has been <u>removed</u> on the parameter <b>$Emailto</b> and <b>$EmailFrom</b> to allow support on PowerShell 2.0. This is actually replaced by a<b>Regular Expression validation</b>

* ... and some more minors other changes...

<b><u>Note:</u></b> Unfortunately, if you were already using the script, this update won't work with the old ChangeHistory.csv files (So you will need to archives the old ones in another directory). I added some properties and decided to keep only one file per group to track the change history, these changes are not compatible with the old files (previous to version 1.6)


# Requirements



* PowerShell v3.0

* PowerShell Snapin from <a href="http://software.dell.com/products/activeroles-server/powershell.aspx" target="_blank">Quest : ActiveRoles Management Shell for Active Directory</a>

* A Scheduled Task to run every X minutes

* Permission to Read Group(s) Membership in AD

* Permission to write CSV files locally

* EmailTo and EmailFrom must have an Email Format (Regex validation). FakeEmail@Servername won't work



# Report Example

Here is a quick example of report generated by the script.

<u>Changes:</u>

* 
* Email Subject contains the DOMAIN\GROUPNAME

* Add some Information about the group

* Columns in Membership Change and Change History now matche

* New Columns DisplayName and DateTime



<a href="http://4.bp.blogspot.com/-jpDDI8m24vc/UpLrk_XYiDI/AAAAAAABe7w/pdP9x1TFqTI/s1600/2013-11-23+8-44-52+PM.png" imageanchor="1" style="margin-left: 1em; margin-right: 1em;"><img border="0" src="https://4.bp.blogspot.com/-jpDDI8m24vc/UpLrk_XYiDI/AAAAAAABe7w/pdP9x1TFqTI/s1600/2013-11-23+8-44-52+PM.png" /></a>

# Using the script

In the following example I'm playing with two test groups: <b>FXGROUP01</b> and <b>FXGROUP02</b>
<a href="http://4.bp.blogspot.com/-uBtW77kjZm0/UpLoW45foSI/AAAAAAABe7Q/xXdQ6UuoeX4/s1600/2013-11-25+1-03-33+AM.png" imageanchor="1" style="margin-left: 1em; margin-right: 1em;"><img border="0" src="https://4.bp.blogspot.com/-uBtW77kjZm0/UpLoW45foSI/AAAAAAABe7Q/xXdQ6UuoeX4/s1600/2013-11-25+1-03-33+AM.png" /></a>
For my test, I placed the script in the directory <i>C:\LazyWinAdmin\Tool-Monitor-AD_Groups</i>

<a href="http://2.bp.blogspot.com/-1OLcLb_-gso/UpLpNLfbGcI/AAAAAAABe7Y/hRR-iuPEOHU/s1600/2013-11-25+1-07-07+AM.png" imageanchor="1" style="margin-left: 1em; margin-right: 1em;"><img border="0" src="https://2.bp.blogspot.com/-1OLcLb_-gso/UpLpNLfbGcI/AAAAAAABe7Y/hRR-iuPEOHU/s1600/2013-11-25+1-07-07+AM.png" /></a>




### Running the script the first time

You will notice that the script is creating folders and files.
At this point you won't get any email report.

<a href="http://4.bp.blogspot.com/-IMuYP2JdB88/UpLpglICjyI/AAAAAAABe7g/2FKgX8bo6c8/s1600/2013-11-25+1-08-50+AM.png" imageanchor="1" style="margin-left: 1em; margin-right: 1em;"><img border="0" height="308" src="https://4.bp.blogspot.com/-IMuYP2JdB88/UpLpglICjyI/AAAAAAABe7g/2FKgX8bo6c8/s1600/2013-11-25+1-08-50+AM.png" width="640" /></a>

```
PS C:\LazyWinAdmin\TOOL-MONITOR-AD_Group> .\TOOL-MONITOR-AD_Group.ps1 -group "FXGroup01","FXGroup02" -Emailfrom Reporting@fx.lab -Emailto "Catfx@fx.lab" -EmailServer 192.168.1.10 -Verbose
```

```
VERBOSE: Creating the Output Folder : C:\LazyWinAdmin\TOOL-MONITOR-AD_Group\Output
VERBOSE: Creating the ChangeHistory Folder : C:\LazyWinAdmin\TOOL-MONITOR-AD_Group\ChangeHistory
VERBOSE: GROUP: FXGroup01
VERBOSE: FXGroup01 - The following file did not exist: FX_FXGROUP01-membership.csv
VERBOSE: FXGroup01 - Exporting the current membership information into the file:
FX_FXGROUP01-membership.csv
VERBOSE: FXGroup01 - Comparing Current and Before
VERBOSE: FXGroup01 - Compare Block Done !
VERBOSE: FXGroup01 - No Change
VERBOSE: GROUP: FXGroup02
VERBOSE: FXGroup02 - The following file did not exist: FX_FXGROUP02-membership.csv
VERBOSE: FXGroup02 - Exporting the current membership information into the file:
FX_FXGROUP02-membership.csv
VERBOSE: FXGroup02 - Comparing Current and Before
VERBOSE: FXGroup02 - Compare Block Done !
VERBOSE: FXGroup02 - No Change
VERBOSE: Script Completed

```


<u>Two directories and two files are created:</u>

* <b>2 Files</b>For each of the groupwe just queried <u>FXGROUP01</u> and <u>FXGROUP02</u>. Since these groups are currently empty, the script will add the value "<span style="font-family: &quot;courier new&quot; , &quot;courier&quot; , monospace;">No User or Group" in both files.

* <b>OUTPUT Directory </b>Each time the script run, It query the group membership in the Active Directory and save the current membership in the files (It won't touch the file if it's the same membership at each check).

* <b>CHANGEHISTORY Directory</b> contains the list of changes observed by the script. One file per Group per domain, if multiple changes occur, the script will append the change in the same file.

<b>Output Directory</b>contains the 2 files for each monitored groups

<a href="http://4.bp.blogspot.com/-s-QsLilOm4o/UpLtPZCF9XI/AAAAAAABe8E/gdN02xJhCeE/s1600/2013-11-25+1-22-00+AM.png" imageanchor="1" style="margin-left: 1em; margin-right: 1em;"><img border="0" src="https://4.bp.blogspot.com/-s-QsLilOm4o/UpLtPZCF9XI/AAAAAAABe8E/gdN02xJhCeE/s1600/2013-11-25+1-22-00+AM.png" /></a>
<div class="separator" style="clear: both; text-align: left;">
<div class="separator" style="clear: both; text-align: left;">Each file contains the current membership of each groups. Since these are empty the script just create the following file with two properties<b><span style="font-family: &quot;courier new&quot; , &quot;courier&quot; , monospace;"> SamAccountName</b> and <b><span style="font-family: &quot;courier new&quot; , &quot;courier&quot; , monospace;">Name</b> with the value "<span style="font-family: &quot;courier new&quot; , &quot;courier&quot; , monospace;">No User or Group"
<a href="http://4.bp.blogspot.com/-6FwUbLyvv68/UpLtPSQcWtI/AAAAAAABe8M/nuFNLw7jxjk/s1600/2013-11-25+1-24-11+AM.png" imageanchor="1" style="margin-left: 1em; margin-right: 1em;"><img border="0" src="https://4.bp.blogspot.com/-6FwUbLyvv68/UpLtPSQcWtI/AAAAAAABe8M/nuFNLw7jxjk/s1600/2013-11-25+1-24-11+AM.png" /></a>

<div class="separator" style="clear: both; text-align: left;">The <b>ChangeHistory Directory</b> is empty at this point since no change was observed by the script.
<a href="http://1.bp.blogspot.com/-rfed01MW2w4/UpLtPUkUuEI/AAAAAAABe8A/GKK997dy638/s1600/2013-11-25+1-22-22+AM.png" imageanchor="1" style="margin-left: 1em; margin-right: 1em;"><img border="0" src="https://1.bp.blogspot.com/-rfed01MW2w4/UpLtPUkUuEI/AAAAAAABe8A/GKK997dy638/s1600/2013-11-25+1-22-22+AM.png" /></a>




### Running the script a second time (without change on the groups)

If I re-run the script we will get the following output.
The script does not see any change in the membership by comparing the content of the file <b><span style="font-family: &quot;courier new&quot; , &quot;courier&quot; , monospace;">FX_FXGROUP01-membership.csv</b> and the current membership in Active Directory for this group.

<a href="http://4.bp.blogspot.com/-Q4VsgS8y7-M/UpLuBSwFy7I/AAAAAAABe8U/uFfsEkSaR-0/s1600/2013-11-25+1-27-29+AM.png" imageanchor="1" style="margin-left: 1em; margin-right: 1em;"><img border="0" height="461" src="https://4.bp.blogspot.com/-Q4VsgS8y7-M/UpLuBSwFy7I/AAAAAAABe8U/uFfsEkSaR-0/s1600/2013-11-25+1-27-29+AM.png" width="640" /></a>



### Running the script after a change

Ok now let's make one change and add one account in FXGROUP01 and run the script again.



```
PS C:\LazyWinAdmin\TOOL-MONITOR-AD_Group> .\TOOL-MONITOR-AD_Group.ps1 -group "FXGroup01","FXGroup02" -Emailfrom Reporting@fx.lab -Emailto "Catfx@fx.lab" -EmailServer 192.168.1.10 -Verbose
```

```
VERBOSE: GROUP: FXGroup01
VERBOSE: FXGroup01 - The following file Exists: FX_FXGROUP01-membership.csv
VERBOSE: FXGroup01 - Comparing Current and Before
VERBOSE: FXGroup01 - Compare Block Done !
VERBOSE: FXGroup01 - Some changes found

```
DateTime       : 20131118-08:51:10
State          : Removed
DisplayName    :
SamAccountName : No User or Group
DN             :

DateTime       : 20131118-08:51:10
State          : Added
DisplayName    :
SamAccountName : fxtest
DN             : CN=fxtest,CN=Users,DC=FX,DC=LAB

```
VERBOSE: FXGroup01 - Get the change history for this group
VERBOSE: FXGroup01 - Change history files: 0
VERBOSE: FXGroup01 - Save changes to a ChangesHistory file
VERBOSE: FXGroup01 - Preparing the notification email...
VERBOSE: FXGroup01 - Email Sent.
VERBOSE: FXGroup01 - Exporting the current membership to FX_FXGROUP01-membership.csv
VERBOSE: GROUP: FXGroup02
VERBOSE: FXGroup02 - The following file Exists: FX_FXGROUP02-membership.csv
VERBOSE: FXGroup02 - Comparing Current and Before
VERBOSE: FXGroup02 - Compare Block Done !
VERBOSE: FXGroup02 - No Change
VERBOSE: Script Completed
```

```

```

As you can see One account was added "<b>fxtest</b>" and the default "<b>No User or Group</b>" was removed by the script

<a href="http://2.bp.blogspot.com/-p_COZH-E2mA/UpLyJtYGqNI/AAAAAAABe9E/jq3Gyuun9YQ/s1600/2013-11-25+1-45-19+AM.png" imageanchor="1" style="margin-left: 1em; margin-right: 1em;"><img border="0" src="https://2.bp.blogspot.com/-p_COZH-E2mA/UpLyJtYGqNI/AAAAAAABe9E/jq3Gyuun9YQ/s1600/2013-11-25+1-45-19+AM.png" /></a>


Let's make another change to this group, add another user.


```
PS C:\LazyWinAdmin\TOOL-MONITOR-AD_Group> .\TOOL-MONITOR-AD_Group.ps1 -group "FXGroup01","FXGroup02" -Emailfrom Reporting@fx.lab -Emailto "Catfx@fx.lab" -EmailServer 192.168.1.10 -Verbose
```

```
VERBOSE: GROUP: FXGroup01
VERBOSE: FXGroup01 - The following file Exists: FX_FXGROUP01-membership.csv
VERBOSE: FXGroup01 - Comparing Current and Before
VERBOSE: FXGroup01 - Compare Block Done !
VERBOSE: FXGroup01 - Some changes found

```
DateTime       : 20131118-09:02:49
State          : Added
DisplayName    :
SamAccountName : AnneD
DN             : CN=AnneD,CN=Users,DC=FX,DC=LAB

```
VERBOSE: FXGroup01 - Get the change history for this group
VERBOSE: FXGroup01 - Change history files: 1
VERBOSE: FXGroup01 - Change history files - Loading
C:\LazyWinAdmin\TOOL-MONITOR-AD_Group\ChangeHistory\FX_FXGROUP01-ChangeHistory.csv
VERBOSE: FXGroup01 - Change history process completed
VERBOSE: FXGroup01 - Save changes to a ChangesHistory file
VERBOSE: FXGroup01 - Preparing the notification email...
VERBOSE: FXGroup01 - Email Sent.
VERBOSE: FXGroup01 - Exporting the current membership to FX_FXGROUP01-membership.csv
VERBOSE: GROUP: FXGroup02
VERBOSE: FXGroup02 - The following file Exists: FX_FXGROUP02-membership.csv
VERBOSE: FXGroup02 - Comparing Current and Before
VERBOSE: FXGroup02 - Compare Block Done !
VERBOSE: FXGroup02 - No Change
VERBOSE: Script Completed
```

```

```


<u>Here is the report generated</u>

<a href="http://4.bp.blogspot.com/-SRc7VpBPh5Y/UpLyGWLnsMI/AAAAAAABe88/wRx9fVryM8M/s1600/2013-11-25+1-45-27+AM.png" imageanchor="1" style="margin-left: 1em; margin-right: 1em;"><img border="0" src="https://4.bp.blogspot.com/-SRc7VpBPh5Y/UpLyGWLnsMI/AAAAAAABe88/wRx9fVryM8M/s1600/2013-11-25+1-45-27+AM.png" /></a>


Notice this time, the report contains a section called "Change History" so you'll be able to know which previous changes were made.




# Using the SearchRoot parameter (Organization Unit path)




```
PS C:\LazyWinAdmin\TOOL-MONITOR-AD_Group> .\TOOL-MONITOR-AD_Group.ps1 -SearchRoot 'FX.LAB/TEST/Groups' -Emailfrom Reporting@fx.lab -Emailto "Catfx@fx.lab" -EmailServer 192.168.1.10 -Verbose
```

```
VERBOSE: OU: FX.LAB/TEST/Groups
VERBOSE: GROUP: CN=FXGROUP01,OU=Groups,OU=TEST,DC=FX,DC=LAB
VERBOSE: CN=FXGROUP01,OU=Groups,OU=TEST,DC=FX,DC=LAB - The following file Exists:
FX_FXGROUP01-membership.csv
VERBOSE: CN=FXGROUP01,OU=Groups,OU=TEST,DC=FX,DC=LAB - Comparing Current and Before
VERBOSE: CN=FXGROUP01,OU=Groups,OU=TEST,DC=FX,DC=LAB - Compare Block Done !
VERBOSE: CN=FXGROUP01,OU=Groups,OU=TEST,DC=FX,DC=LAB - No Change
VERBOSE: GROUP: CN=FXGROUP02,OU=Groups,OU=TEST,DC=FX,DC=LAB
VERBOSE: CN=FXGROUP02,OU=Groups,OU=TEST,DC=FX,DC=LAB - The following file Exists:
FX_FXGROUP02-membership.csv
VERBOSE: CN=FXGROUP02,OU=Groups,OU=TEST,DC=FX,DC=LAB - Comparing Current and Before
VERBOSE: CN=FXGROUP02,OU=Groups,OU=TEST,DC=FX,DC=LAB - Compare Block Done !
VERBOSE: CN=FXGROUP02,OU=Groups,OU=TEST,DC=FX,DC=LAB - No Change
VERBOSE: Script Completed
```



# Using the File parameter


<a href="http://3.bp.blogspot.com/-Kn8J5En65bQ/UpLz15VDwhI/AAAAAAABe9c/ZIhcTX9sqMo/s1600/2013-11-25+1-50-39+AM.png" imageanchor="1" style="margin-left: 1em; margin-right: 1em;"><img border="0" src="https://3.bp.blogspot.com/-Kn8J5En65bQ/UpLz15VDwhI/AAAAAAABe9c/ZIhcTX9sqMo/s1600/2013-11-25+1-50-39+AM.png" /></a>




```
PS C:\LazyWinAdmin\TOOL-MONITOR-AD_Group> .\TOOL-MONITOR-AD_Group.ps1 -file .\groupslist.txt -Emailfrom Reporting@fx.lab -Emailto "Catfx@fx.lab" -EmailServer 192.168.1.10 -Verbose
```

```
VERBOSE: Loading File: .\groupslist.txt
VERBOSE: GROUP: CN=FXGROUP01,OU=Groups,OU=TEST,DC=FX,DC=LAB
VERBOSE: CN=FXGROUP01,OU=Groups,OU=TEST,DC=FX,DC=LAB - The following file Exists:
FX_FXGROUP01-membership.csv
VERBOSE: CN=FXGROUP01,OU=Groups,OU=TEST,DC=FX,DC=LAB - Comparing Current and Before
VERBOSE: CN=FXGROUP01,OU=Groups,OU=TEST,DC=FX,DC=LAB - Compare Block Done !
VERBOSE: CN=FXGROUP01,OU=Groups,OU=TEST,DC=FX,DC=LAB - No Change
VERBOSE: GROUP: FXGROUP02
VERBOSE: FXGROUP02 - The following file Exists: FX_FXGROUP02-membership.csv
VERBOSE: FXGROUP02 - Comparing Current and Before
VERBOSE: FXGROUP02 - Compare Block Done !
VERBOSE: FXGROUP02 - No Change
VERBOSE: Script Completed

```




# Syntax

There are three different ParameterSetNames, so you will see three different SYNTAX.
* Group

* SearchRoot (Organization Unit path)

* File


```
SYNTAX
    C:\LazyWinAdmin\TOOL-MONITOR-AD_Group\TOOL-MONITOR-AD_Group.ps1 -Group <String[]> -Emailfrom
    <String> -Emailto <String[]> -EmailServer <String> [<CommonParameters>]

    C:\LazyWinAdmin\TOOL-MONITOR-AD_Group\TOOL-MONITOR-AD_Group.ps1 -SearchRoot <String[]>
    [-SearchScope <String>] [-GroupScope <String>] [-GroupType <String>] -Emailfrom <String>
    -Emailto <String[]> -EmailServer <String> [<CommonParameters>]

    C:\LazyWinAdmin\TOOL-MONITOR-AD_Group\TOOL-MONITOR-AD_Group.ps1 -File <String[]> -Emailfrom
    <String> -Emailto <String[]> -EmailServer <String> [<CommonParameters>]
```





# Help



```
<#
.SYNOPSIS
    This script is monitoring group(s) in Active Directory and send an email when someone is changing the membership.

.DESCRIPTION
    This script is monitoring group(s) in Active Directory and send an email when someone is changing the membership.
    It will also report the Change History made for this/those group(s).

.PARAMETER Group
    Specify the group(s) to query in Active Directory.
    You can also specify the 'DN','GUID','SID' or the 'Name' of your group(s).
    Using 'Domain\Name' will also work.

.PARAMETER Group
    Specify the group(s) to query in Active Directory.
    You can also specify the 'DN','GUID','SID' or the 'Name' of your group(s).
    Using 'Domain\Name' will also work.

.PARAMETER SearchRoot
    Specify the DN, GUID or canonical name of the domain or container to search. By default, the script searches the entire sub-tree of which SearchRoot is the topmost object (sub-tree search). This default behavior can be altered by using the SearchScope parameter.
    
.PARAMETER SearchScope
    Specify one of these parameter values
        'Base'      Limits the search to the base (SearchRoot) object.
                    The result contains a maximum of one object.
        'OneLevel'  Searches the immediate child objects of the base (SearchRoot)
                    object, excluding the base object.
        'Subtree'   Searches the whole sub-tree, including the base (SearchRoot)
                    object and all its child objects.

.PARAMETER GroupScope
    Specify the group scope of groups you want to find. Acceptable values are: 
        'Global'; 
        'Universal'; 
        'DomainLocal'.

.PARAMETER GroupType
    Specify the group type of groups you want to find. Acceptable values are: 
        'Security';
        'Distribution'.

.PARAMETER File
    Specify the File where the Group are listed. DN, SID, GUID, or Domain\Name of the group are accepted.

.PARAMETER EmailServer
    Specify the Email Server IPAddress/FQDN.

.PARAMETER EmailTo
    Specify the Email Address(es) of the Destination. Example: fxcat@fx.lab

.PARAMETER EmailFrom
    Specify the Email Address of the Sender. Example: Reporting@fx.lab

.EXAMPLE
    .\TOOL-Monitor-AD_Group.ps1 -Group "FXGroup" -EmailFrom "From@Company.com" -EmailTo "To@Company.com" -EmailServer "mail.company.com"

    This will run the script against the group FXGROUP and send an email to To@Company.com using the address From@Company.com and the server mail.company.com.

.EXAMPLE
    .\TOOL-Monitor-AD_Group.ps1 -Group "FXGroup","FXGroup2","FXGroup3" -EmailFrom "From@Company.com" -Emailto "To@Company.com" -EmailServer "mail.company.com"

    This will run the script against the groups FXGROUP,FXGROUP2 and FXGROUP3  and send an email to To@Company.com using the address From@Company.com and the Server mail.company.com.

.EXAMPLE
    .\TOOL-Monitor-AD_Group.ps1 -Group "FXGroup" -EmailFrom "From@Company.com" -Emailto "To@Company.com" -EmailServer "mail.company.com" -Verbose

    This will run the script against the group FXGROUP and send an email to To@Company.com using the address From@Company.com and the server mail.company.com. Additionally the switch Verbose is activated to show the activities of the script.

.EXAMPLE
    .\TOOL-Monitor-AD_Group.ps1 -Group "FXGroup" -EmailFrom "From@Company.com" -Emailto "Auditor@Company.com","Auditor2@Company.com" -EmailServer "mail.company.com" -Verbose

    This will run the script against the group FXGROUP and send an email to Auditor@Company.com and Auditor2@Company.com using the address From@Company.com and the server mail.company.com. Additionally the switch Verbose is activated to show the activities of the script.

.EXAMPLE
    .\TOOL-Monitor-AD_Group.ps1 -SearchRoot 'FX.LAB/TEST/Groups' -Emailfrom Reporting@fx.lab -Emailto "Catfx@fx.lab" -EmailServer 192.168.1.10 -Verbose

    This will run the script against all the groups present in the CanonicalName 'FX.LAB/TEST/Groups' and send an email to catfx@fx.lab using the address Reporting@fx.lab and the server 192.168.1.10. Additionally the switch Verbose is activated to show the activities of the script.

.EXAMPLE
    .\TOOL-Monitor-AD_Group.ps1 -file .\groupslist.txt -Emailfrom Reporting@fx.lab -Emailto "Catfx@fx.lab" -EmailServer 192.168.1.10 -Verbose

    This will run the script against all the groups present in the file groupslists.txt and send an email to catfx@fx.lab using the address Reporting@fx.lab and the server 192.168.1.10. Additionally the switch Verbose is activated to show the activities of the script.

.INPUTS
    System.String

.OUTPUTS
    Email Report
.NOTES
    NAME:    TOOL-Monitor-AD_Group.ps1
    AUTHOR:    Francois-Xavier CAT 
    DATE:    2012/02/01
    EMAIL:    info@lazywinadmin.com

    REQUIREMENTS:
        -Read Permission in Active Directory on the monitored groups
        -Quest Active Directory PowerShell Snapin
        -A Scheduled Task (in order to check every X seconds/minutes/hours)

    VERSION HISTORY:
    1.0 2012.02.01
        Initial Version

    1.1 2012.03.13
        CHANGE to monitor both Domain Admins and Enterprise Admins

    1.2 2013.09.23
        FIX issue when specifying group with domain 'DOMAIN\Group'
        CHANGE Script Format (BEGIN, PROCESS, END)
        ADD Minimal Error handling. (TRY CATCH)

    1.3 2013.10.05
        CHANGE in the PROCESS BLOCK, the TRY CATCH blocks and placed
         them inside the FOREACH instead of inside the TRY block
        ADD support for Verbose
        CHANGE the output file name "DOMAIN_GROUPNAME-membership.csv"
        ADD a Change History File for each group(s)
         example: "GROUPNAME-ChangesHistory-yyyyMMdd-hhmmss.csv"
        ADD more Error Handling
        ADD a HTML Report instead of plain text
        ADD HTML header
        ADD HTML header for change history

    1.4 2013.10.11
        CHANGE the 'Change History' filename to
         "DOMAIN_GROUPNAME-ChangesHistory-yyyyMMdd-hhmmss.csv"
        UPDATE Comments Based Help
        ADD Some Variable Parameters
    1.5 2013.10.13
        ADD the full Parameter Names for each Cmdlets used in this script
        ADD Alias to the Group ParameterName
    1.6 2013.11.21
        ADD Support for Organizational Unit (SearchRoot parameter)
        ADD Support for file input (File Parameter)
        ADD ParamaterSetNames and parameters GroupType/GroupScope/SearchScope
        REMOVE [mailaddress] type on $Emailfrom and $EmailTo to make the script available to PowerShell 2.0
        ADD Regular expression validation on $Emailfrom and $EmailTo
    
        2013.11.23
        ADD ValidateScript on File Parameter
        ADD Additional information about the Group in the Report
        CHANGE the format of the $changes output, it will now include the DateTime Property
        UPDATE Help
        ADD DisplayName Property in the report
        
        2013.11.27
        Minor syntax changes
        UPDATE Help
#>
```



# Download

<a href="https://github.com/lazywinadmin/PowerShell/tree/master/AD-GROUP-Monitor_MemberShip" target="_blank">Download on Github (most updated)</a>
<a href="http://gallery.technet.microsoft.com/Monitor-Active-Directory-4c4e04c7" target="_blank">Download on Technet Gallery</a>



Thanks for Reading! If you have any questions, leave a comment or send me an email at fxcat@lazywinadmin.com. I invite you to follow me on [Twitter @lazywinadmin](https://twitter.com/lazywinadmin) / [Google+](https://plus.google.com/u/0/118118278125759171027/posts) / <a href="http://ca.linkedin.com/in/fxcat" target="_blank">LinkedIn</a> You can also follow the <b>LazyWinAdmin Blog</b> on [Facebook Page](https://www.facebook.com/pages/LazyWinAdmin/191148464265117) and [Google+ Page](https://plus.google.com/u/0/b/111250084602492302873/111250084602492302873/posts).
<div class="zemanta-pixie" style="height: 15px; margin-top: 10px;"><img alt="" class="zemanta-pixie-img" src="http://img.zemanta.com/pixy.gif?x-id=681ebada-9c16-43f5-9b98-ca32094dbeac" style="border: currentColor; float: right;" />
