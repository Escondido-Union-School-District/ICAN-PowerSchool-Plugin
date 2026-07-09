# ICAN-PowerSchool-Plugin
This plugin allows for  aggregated attendance and absences report generation for SDCOE's ICAN system
Send Data to SDCOE with PowerSchool’s Data Export Manager

## Overview
This documentation explains how to send data daily (M-F) to SDCOE using PowerSchool’s Data Export Manager.  Applies to PowerSchool SIS versions 19 and newer and assumes the new user interface is enabled.
This process ensures that updates made in PowerSchool are automatically reﬂected in SDCOE Network Dashboards and Applications by the next business day.

## Data Types --- Description
*Student* --- Used to initially add students to the network and to continually update their records with Socio-Economical status (if using), Plan 504 participation, English Language Acquisition (ELA) status and Special Education (IEP) status.
*Attendance* --- Used to identify attendance summaries for students.

⚠ PowerSchool will serve as the source of truth and continue to be the authoritative source for your district data. All ﬁelds in SDCOE applications that are automated from PowerSchool will be updated automatically using data in PowerSchool on a nightly basis. If you need to make any changes to these ﬁelds, please update them in PowerSchool. SDCOE applications do not synchronize data back into PowerSchool.

### Step #1: Create the connection through Remote Connections Manager
1.	In PowerSchool, navigate to the Remote Connections Manager plugin: Start Page → System Management → Server → Plugin Configuration.  Click on the “Remote Connections Manager” plugin name to view its settings.  Under “Functions”, click on the “Remote Connection Manager” again while on the setup page.  Note: this plugin comes preinstalled with PowerSchool SIS.
2.	Click the Create Connection button. The Create Connection window will open. Populate the ﬁelds as indicated below:
2.a	Name: SDCOE (any name of your choosing that clearly indicates the purpose)
2.b	Description: leave a descriptive comment what the connection is for
2.c	Protocol: SFTP
2.d	Host: bssftp.sdcoe.net 
2.e	Port: 22
2.f	Authentication Type: Password
2.g	Username and Password: Please contact the SDCOE ICAN team when you are ready to receive SFTP credentials for your district.
2.h	Remote Path: /cohort6
2.i	Allow upload: check the box
3.	Click the “Test Connection” button. If the connection is successful, a green “Connection Successful” bar will appear.
3.a Potential errors
3.a.1 If “Connection failed. Unknown host.” error appears, verify the SFTP connection settings and ensure your organization is allowing SFTP connections from the PowerSchool SIS environment.
3.a.2 The "Connection failed. Authentication failed." message means the credentials you’re using are incorrect - verify username and password.
3.a.3 "Connection failed. The server's host key does not match with any known hosts in the system. The fingerprint of the received key is #######. Do you want to accept this host key?" This message might occur if this is your ﬁrst time connecting to SDCOE’s SFTP Server or the host key has changed. Click “Yes, Accept Host Key”.  You may need to save the setup so that the fingerprint is recorded before proceeding with another connection test (click on the connection name from the plugin setup screen to return to edit mode).
4.	Click the “Submit” button. This will save the connection so it may be used later in the Data Export Manager.

### Step #2: Create Templates in the Data Export Manager
1.	Navigate to the Data Export Manager: Start Page → Data and Reporting → Exports → Data Export Manager
2.	Start by creating templates on the Export tab
3.	Depending on your district’s choice, you will need to either create two templates through the Data Export Manager (refer to Step #2.1 Student and Step #2.2 Attendance for details) or a single template using a method similar to Step #2.2 Attendance — Step #2.3 Student with Attendance
3.a	Student
3.b	Attendance
OR
3.c	Student with Attendance
4.	Select export format and output options
4.a	Export Field Name: Example names can be district.students.csv, district.demographics.csv, or district.attendance.csv (name should reﬂect the template data type and your district’s name)
4.b	Line Delimiter: CR/LF
4.c	Field Delimiter: Comma
4.d	Character Set: Windows Ansi
4.e	Include Column Headers: Checked
4.f	Surround “ﬁeld values” in Quotes: Checked

#### Step #2.1 Student
1.	Select Category “Tables”
2.	Select Export From “Students” — this will list the Students table fields along with all one-to-one extended tables related to the Students table.  While most student current enrollment, contacts, and demographics information resides in the Students table, your district may have chosen to keep other relevant custom fields in extended tables.  Additionally, referential fields like “elastatus” contained in extended tables like S_CA_STU_X, are either automatically updated when an ELA program record is manually updated or have to be manually updated directly — for them to reflect the current state of student programs and achievements.
3.	Select fields from their respective tables/extensions.  You can rearrange fields from the same table/extension as they appear on the right-hand side but the grouping of fields by table/extension cannot be changed.  Fields are listed in the Table.field format:
3.a Students.state_studentnumber
3.b Students.last_name
3.c Students.first_name
3.d Students.schoolid
3.d.Note: You will need to send a separate CDS and school name mapping to SDCOE
3.e Students.grade_level
3.f Students.entrydate
3.g Students.exitdate
3.g.Note: PowerSchool SIS tracks most exit dates as the first day of non-enrollment.  For example, if 6/12/2026 date is provided, the actual last day of membership for the student is 6/11/2026
3.h Students.enroll_status
3.h.Note: “0” means “Active”, otherwise “Inactive”; while this field is not needed for ICAN, it’s necessary to include it in order to be able to filter by it
3.i Students.gender
3.j Students.fedethnicity
3.j.Note: 1 means “Yes” for “Hispanic”, other values would mean “No”
3.k Students.ethnicity
3.k.Note: This field is deprecated but may still be used and contains 3-digit race codes; you will need to let SDCOE know how you want to map these race codes to ICAN. Alternatively, if you have a custom field where you track race categories (rolled up ethnicity categories like White, Multiple, Asian, etc). select that field from its corresponding extended table instead
3.l Students.lunchstatus
3.l.Note: This field may not be used at all in your district but normally indicates a Free/Reduced/Paid lunch eligibility for a student and can be updated both through the Lunch Program screen under Student Profile and Lunch Program Eligibility under Compliance.  As there is no standard way of indicating Socio-Economically Disadvantaged (SED) status within PowerSchool (it frequently implies multiple criteria, not just lunch eligibility), districts may decide to use lunch status as the SED indicator.  In that case, “P” is “No” and “F” or “R” are “Yes”.  If using a specific custom field for SED, select that field instead from its corresponding extended table
3.m Students.S_CA_Stu_X.ieptype
3.m.Note: This field is located under Compliance → Students With Disabilities (SDW).  Students with code 10 have an IEP plan, students with 20 have ISP, otherwise they’re not in IEP or ISP.  If using a custom field for tracking IEP or Special Education service types, select that field from the corresponding extension instead and let the ICAN team know what the values translate to.
3.n Plan 504 is tracked in the S_CA_Stu_CALPADSPROGRAMS_C extension as program code 101.  As it is not possible to pull it as a field from the program table, use your district’s custom fields (create if missing) instead
3.o Students.S_CA_Stu_X.elastatus
3.o.Note: This field is dependent on ELA Status/Program History entries under Compliance (the program has to be manually updated to trigger the sync).  It can also be maintained manually via bulk uploads or placement on custom screens/pages.  “EL” means “Yes” for English Learner status.  “TBD” is also sometimes treated as “Yes” by districts
4.	Filter for the desired grade level range and for enroll_status = “0” to only pull active students
5.	Save as template, giving it a fitting name.

#### Step #2.2 Attendance
1.	Download and install this PowerQuery plugin for aggregated attendance and membership data export:
1.a	System Management → Server → Plugin Configuration; Install
1.b	Browse to the ZIP file and confirm installation
1.c	Back on the plugin listing page, check the enablement box next to “ICAN Attendance and Membership Data Sets”, confirm the action
2.	In the Data Export Manager, select Category “Additional Datasets”
3.	In the Export From, select “ICAN Attendance: Year to Date”.  Note: additional datasets are sorted by categories and then by name, so you may need to scroll down the list to locate ICAN datasets
4.	Select all fields appearing in dataset (the order does not matter):
4.a	ICAN.CDS
4.b	ICAN.CumulativeDaysAbsent
4.c	ICAN.CumulativeDaysEnrolled
4.d	ICAN.DaysAbsentAllSites
4.e	ICAN.DaysEnrolledAllSites
4.f	ICAN.Grade
4.g	ICAN.ReportingDate
4.h	ICAN.School
4.i	ICAN.SSID
4.Note: You can relabel data set fields as desired (text boxes on the right in the screenshot above).  Please refrain from using special and blank characters
5.	On the next step, define filters for your export.  Select lowest and highest grade levels you’d like to export.  To exclude some school sites from the data pull, you can utilize both the built-in filter for school IDs and the data filter for School field (school name).  School IDs need to be listed as a comma-separated string with no spaces.  School IDs can be found in district setup for schools as well as in the schools drop-down, and in the Schools table are referred to as school_number while in Students table they’re schoolid
6.	You can include additional filtering to the extracted data but it shouldn’t be necessary
7.	Leave the selection at “Export Filtered Rows” and click “Next”
8.	Name your extract, define the file type and delimiters, and click the “Save Template” button.
9.	Give your template an easy to understand ame that fits the purpose, and save it as new.

#### Step #2.3 Student with Attendance
1.	Download and install this PowerQuery plugin for aggregated attendance and membership data export:
1.a	System Management → Server → Plugin Configuration; Install
1.b	Browse to the ZIP file and confirm installation
1.c	Back on the plugin listing page, check the enablement box next to “ICAN Attendance and Membership Data Sets”, confirm the action
2.	In the Data Export Manager, select Category “Additional Datasets”
3.	In the Export From, select “ICAN Demographics and Attendance: Year to Date”.  Note: additional datasets are sorted by categories and then by name, so you may need to scroll down the list to locate ICAN datasets
4.	Follow steps 4-9 from Step #2.2.  The only difference is that all demographic fields will be included in this data set instead of exporting them separately.

### Step #3: Schedule the Export to Send Data to SDCOE Nightly
1.	After you create a template, it will show up under the My Templates tab. Navigate to the My Templates tab and click on the calendar icon.
2.	Schedule the export by ﬁlling in the ﬁelds:
2.a	Schedule Active: Checked
2.b	Days to Execute: M-F
2.c	When to Execute: Choose a time between 10 PM and 5 AM.
2.d	Send output to: SDCOE (or what you named the connection when you created a remote connection)
2.e	Path: Blank
3.	Verify that the export now shows up under the Scheduled System Templates tab.
4.	Repeat for each template.

Note: If you need to make changes to the export scheduler, edit it from the Scheduled System Templates tab.

## Historical Attendance and Demographics
The provided ICAN plugin contains two data sets for historical information: attendance only and attendance with demographics.  During onboarding, you will be asked to pull some historical attendance and membership data for the participating school sites.  While you could export and compile such reports via other means, the plugin makes it a simpler process.  You will need to run exports for the end of each month requested by the ICAN team.  Then save them as ANSI CSV files with a clearly stated month and academic year name in the file name.
It matters not how far back you go as long as you keep these caveats in mind.

## ICAN Plugin Caveats and Notes
●	ExcludeSites should always contain 0 to exclude District Office as there is no attendance being taken at the district office level.
●	Grade level filter accepts numeric values only to match Students.grade_level.
●	Historical data sets include an additional column for academic year for ease of viewing and sorting when exporting multiple files.
●	SED indicator is based on CALPADS programs 181 and 182 — your district must maintain these programs in order for this indicator to be reliable.  It is also a recommended and best practice to maintain 181/182 records in SIS even for Directly Certified or Categorical (Homeless/Foster/Migrant) students.
●	EL status is derived from the ELA student programs and only EL status is considered as a positive indicator for EL.  TBD does not count.
●	SpEd indicator is based on the IEP type.  IEP type is not a commonly reported field but is used as the most fitting standard field.  If you are using this plugin for all student data, it is recommended that your district begins maintenance of the IEP type field.  As this field is static and has no longitudinal tracking associated with it (in contrast to ELA and 181/182 programs), the value produced onto the report is the most recent effective value from the student record.  If Special Education indication needs to align with the historical report date, you will need to get that information from SEIS and match it to the extract for ICAN by SSID.
●	Static data rules apply to fields like student name,  gender, race, and ethnicity: if there were changes to this student information between “now” and the historical extract date, and if such changes are important, you will possibly need to export student information data from CALPADS and match it back to the ICAN data by SSID.
●	Absence counts are based on the absent/present tag in attendance code setup, and on the Daily attendance period.  Meeting attendance codes do not count.  If your district is using some attendance codes/periods for program participation tracking, ensure that these periods are not set up to act as Daily Attendance (ADA).
●	Membership counts are based on in-session days defined in each school’s calendar.  If your district has different calendar setups for different sites, do expect to see variation in cumulative and total membership values.
●	The current and historical academic year information is taken from the Years and Terms setup at the District Office level. It is a standard practice to always set up years and terms at the District Office container in PowerSchool, matching those of school sites.  In most cases all schools within the district will start and end terms on the same days.  However, if your district has some schools that start and end the year sooner or later than the others, ensure that years and terms at the district level cover ALL of their date ranges.  Membership totals will not be affected by a wider date range: they’re based solely on the specific school’s calendar.  Remote enrollments and summer school sessions do not count.

## If automating outside of PowerSchool SIS

EUSD PowerShell Automation Script
#Path to the encrypted read-only database user password for the Oracle connection
$enpath = 'C:\Users\username\configs\PowerSchoolRO.txt'
#Run the line below uncommented to create an encrypted password file if doing it for the first time.  Can only be decrypted by the same user from the same machine.
#Read-Host -AsSecureString | ConvertFrom-SecureString | Out-File $enpath
$EncryptedPass = Get-Content -Path $enpath

Add-Type -AssemblyName System.Data.OracleClient
$username = "PSNavigator"
$password = [Runtime.InteropServices.Marshal]::PtrToStringAuto([Runtime.InteropServices.Marshal]::SecureStringToBSTR((ConvertTo-SecureString $EncryptedPass)))
$datasource = "yourDBhost:1521/PSPRODDB"
$ConnectionString = "User Id=$username;Password=$password;Data Source=$datasource"
$OracleConnection = New-Object System.Data.OracleClient.OracleConnection($ConnectionString)
$OracleConnection.Open()

#All Students
$CommandText = Get-Content -Path "C:\Users\username\configs\ican_students.sql" -Raw
$command = $OracleConnection.CreateCommand()
$command.CommandText = $CommandText

#For faster processing, fill the dataset object with output from the command-array
$dataSet = New-Object System.Data.DataSet
$dataArr = New-Object System.Data.OracleClient.OracleDataAdapter($command)
[void]$dataArr.fill($dataSet)

$ican = [System.Collections.ArrayList]@($dataSet.Tables[0])
$dts = Get-Date -Format 'yyyyMMdd'
$filepath = "C:\Users\username\data\ICAN\districtName_students_$dts.csv"
$ican | Export-Csv -Path "$filepath" -NoTypeInformation -Force -Confirm:$false

#SFTP (create an encrypted password same as for Oracle; variables for SFTP connection can be changed to align with purpose - they don’t have to be reused from Oracle)
$enpath = 'C:\Users\username\configs\icanSFTP.txt'
#Read-Host -AsSecureString | ConvertFrom-SecureString | Out-File $enpath
$EncryptedPass = Get-Content -Path $enpath
$password = [Runtime.InteropServices.Marshal]::PtrToStringAuto([Runtime.InteropServices.Marshal]::SecureStringToBSTR((ConvertTo-SecureString $EncryptedPass)))
$username = 'username'
$hostname = 'bssftp.sdcoe.net'
$portnum = '22'

#The code below leverages WinSCP version 5.1.0; extensive documentation exists on WinSCP .NET libraries online
cd C:\Users\username\WS510
Add-Type -Path "C:\Users\umraaadmin\WS510\WinSCPnet.dll"
$sessionOptions = New-Object WinSCP.SessionOptions -Property @{
    Protocol = [WinSCP.Protocol]::Sftp
    HostName = $hostname
    UserName = $username
    Password = $password
    SshHostKeyFingerPrint = "ssh-rsa 2048 tryThisOnceToGetExactFingerprintInPowerShellErrorMessageThenReplace="
    }
$session = New-Object WinSCP.Session
$session.ExecutablePath = 'C:\Users\username\WS510\WinSCP.exe'
$session.Open($sessionOptions)

$transferOptions = New-Object WinSCP.TransferOptions
$transferOptions.FilePermissions = $null
$transferOptions.PreserveTimestamp = $false

Send-WinSCPItem -WinSCPSession $session -LocalPath "$filepath" -RemotePath '/cohort6/' -TransferOptions $transferOptions

$session.Close()

## Security Notes
1.	To protect your automation server and/or SIS database server, work with your Network Engineering team to block all incoming and outgoing connections except for those you are going to use for ICAN and other vendors.
2.	Follow PowerSchool LLC’s cybersecurity best practices guide and always change default service account passwords to strong password strings.  Use a password manager to store these passwords, and grant access to view them on the need-to-know basis.
3.	Set up the strictest cybersecurity possible on the server in question to only communicate on necessary ports with necessary hosts/IP.  Rule of least necessary permissions.
4.	Limit the number of non-native tools on the server.  For example, Powershell is native to Windows, however, WinSCP is a 3rd party application.  Install applications and distribution libraries only from verified sources.
5.	Do not grant local machine full admin access to anything and anyone who doesn’t need it to perform assigned tasks.  The automation server should have a dedicated account protected with MFA, used only by designated staff.
