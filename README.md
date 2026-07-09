# ICAN-PowerSchool-Plugin
This plugin allows for  aggregated attendance and absences report generation for SDCOE's ICAN system

Send Data to SDCOE
with PowerSchool’s Data Export Manager
Overview

This documentation explains how to send data daily (M-F) to SDCOE using PowerSchool’s Data Export Manager.  Applies to PowerSchool SIS versions 19 and newer and assumes the new user interface is enabled.

This process ensures that updates made in PowerSchool are automatically reﬂected in SDCOE Network Dashboards and Applications by the next business day.

Data Types	Description
Student 	Used to initially add students to the network and to continually update their records with Socio-Economical status (if using), Plan 504 participation, English Language Acquisition (ELA) status and Special Education (IEP) status.
Attendance	Used to identify attendance summaries for students.

⚠ PowerSchool will serve as the source of truth and continue to be the authoritative source for your district data. All ﬁelds in SDCOE applications that are automated from PowerSchool will be updated automatically using data in PowerSchool on a nightly basis. If you need to make any changes to these ﬁelds, please update them in PowerSchool. SDCOE applications do not synchronize data back into PowerSchool.

Step #1: Create the connection through Remote Connections Manager
1.	In PowerSchool, navigate to the Remote Connections Manager plugin: Start Page → System Management → Server → Plugin Configuration.  Click on the “Remote Connections Manager” plugin name to view its settings.  Under “Functions”, click on the “Remote Connection Manager” again while on the setup page.  Note: this plugin comes preinstalled with PowerSchool SIS.
2.	Click the Create Connection button. The Create Connection window will open. Populate the ﬁelds as indicated below:
○	Name: SDCOE (any name of your choosing that clearly indicates the purpose)
○	Description: leave a descriptive comment what the connection is for
○	Protocol: SFTP
○	Host: bssftp.sdcoe.net 
○	Port: 22
○	Authentication Type: Password
○	Username and Password: Please contact jtorres@sdcoe.net when you are ready to receive SFTP credentials for your district.
○	Remote Path: /cohort6
○	Allow upload: check the box
3.	Click the “Test Connection” button. If the connection is successful, a green “Connection Successful” bar will appear.
 
If “Connection failed. Unknown host.” error appears instead, verify the SFTP connection settings and ensure your organization is allowing SFTP connections from the PowerSchool SIS environment:
 
The message below means the credentials you’re using are incorrect - verify username and password:
 
Note: The message below might occur if this is your ﬁrst time connecting to SDCOE’s SFTP Server. Click “Yes, Accept Host Key”.  You may need to save the setup so that the fingerprint is recorded before proceeding with another connection test (click on the connection name from the plugin setup screen to return to edit mode):
 

4.	Click the “Submit” button. This will save the connection so it may be used later in the Data Export Manager.

Step #2: Create Templates in the Data Export Manager
1.	Navigate to the Data Export Manager: Start Page → Data and Reporting → Exports → Data Export Manager
2.	Start by creating templates on the Export tab
3.	Depending on your district’s choice, you will need to either create two templates through the Data Export Manager (refer to Step #2.1 Student and Step #2.2 Attendance for details) or a single template using a method similar to Step #2.2 Attendance — Step #2.3 Student with Attendance
○	Student
○	Attendance
OR
○	Student with Attendance
4.	Select export format and output options
○	Export Field Name: Example names can be district.students.csv, district.demographics.csv, or district.attendance.csv (name should reﬂect the template data type and your district’s name)
○	Line Delimiter: CR/LF
○	Field Delimiter: Comma
○	Character Set: Windows Ansi
○	Include Column Headers: Checked
○	Surround “ﬁeld values” in Quotes: Checked

Step #2.1 Student
1.	Select Category “Tables”
2.	Select Export From “Students” — this will list the Students table fields along with all one-to-one extended tables related to the Students table.  While most student current enrollment, contacts, and demographics information resides in the Students table, your district may have chosen to keep other relevant custom fields in extended tables.  Additionally, referential fields like “elastatus” contained in extended tables like S_CA_STU_X, are either automatically updated when an ELA program record is manually updated or have to be manually updated directly — for them to reflect the current state of student programs and achievements.
3.	Select fields from their respective tables/extensions.  You can rearrange fields from the same table/extension as they appear on the right-hand side but the grouping of fields by table/extension cannot be changed.  Fields are listed in the Table.field format:
○	Students.state_studentnumber
○	Students.last_name
○	Students.first_name
○	Students.schoolid
	You will need to send a separate CDS and school name mapping to SDCOE
○	Students.grade_level
○	Students.entrydate
○	Students.exitdate
	PowerSchool SIS tracks most exit dates as the first day of non-enrollment.  For example, if 6/12/2026 date is provided, the actual last day of membership for the student is 6/11/2026
○	Students.enroll_status
	“0” means “Active”, otherwise “Inactive”; while this field is not needed for ICAN, it’s necessary to include it in order to be able to filter by it
○	Students.gender
○	Students.fedethnicity
	1 means “Yes” for “Hispanic”, other values would mean “No”
○	Students.ethnicity
	This field is deprecated but may still be used and contains 3-digit race codes; you will need to let SDCOE know how you want to map these race codes to ICAN. Alternatively, if you have a custom field where you track race categories (rolled up ethnicity categories like White, Multiple, Asian, etc). select that field from its corresponding extended table instead
○	Students.lunchstatus
	This field may not be used at all in your district but normally indicates a Free/Reduced/Paid lunch eligibility for a student and can be updated both through the Lunch Program screen under Student Profile and Lunch Program Eligibility under Compliance.  As there is no standard way of indicating Socio-Economically Disadvantaged (SED) status within PowerSchool (it frequently implies multiple criteria, not just lunch eligibility), districts may decide to use lunch status as the SED indicator.  In that case, “P” is “No” and “F” or “R” are “Yes”.  If using a specific custom field for SED, select that field instead from its corresponding extended table
○	Students.S_CA_Stu_X.ieptype
	This field is located under Compliance → Students With Disabilities (SDW).  Students with code 10 have an IEP plan, students with 20 have ISP, otherwise they’re not in IEP or ISP.  If using a custom field for tracking IEP or Special Education service types, select that field from the corresponding extension instead and let the ICAN team know what the values translate to.
○	Plan 504 is tracked in the S_CA_Stu_CALPADSPROGRAMS_C extension as program code 101.  As it is not possible to pull it as a field from the program table, use your district’s custom fields (create if missing) instead
○	Students.S_CA_Stu_X.elastatus
	This field is dependent on ELA Status/Program History entries under Compliance (the program has to be manually updated to trigger the sync).  It can also be maintained manually via bulk uploads or placement on custom screens/pages.  “EL” means “Yes” for English Learner status.  “TBD” is also sometimes treated as “Yes” by districts
4.	Filter for the desired grade level range and for enroll_status = “0” to only pull active students
5.	Save as template, giving it a fitting name.

Step #2.2 Attendance
1.	Download and install this PowerQuery plugin for aggregated attendance and membership data export:
○	System Management → Server → Plugin Configuration; Install
○	Browse to the ZIP file and confirm installation
○	Back on the plugin listing page, check the enablement box next to “ICAN Attendance and Membership Data Sets”, confirm the action
2.	In the Data Export Manager, select Category “Additional Datasets”
3.	In the Export From, select “ICAN Attendance: Year to Date”.  Note: additional datasets are sorted by categories and then by name, so you may need to scroll down the list to locate ICAN datasets
4.	Select all fields appearing in dataset (the order does not matter):
○	ICAN.CDS
○	ICAN.CumulativeDaysAbsent
○	ICAN.CumulativeDaysEnrolled
○	ICAN.DaysAbsentAllSites
○	ICAN.DaysEnrolledAllSites
○	ICAN.Grade
○	ICAN.ReportingDate
○	ICAN.School
○	ICAN.SSID
 
You can relabel data set fields as desired (text boxes on the right in the screenshot above).  Please refrain from using special and blank characters
5.	On the next step, define filters for your export.  Select lowest and highest grade levels you’d like to export.  To exclude some school sites from the data pull, you can utilize both the built-in filter for school IDs and the data filter for School field (school name).  School IDs need to be listed as a comma-separated string with no spaces.  School IDs can be found in district setup for schools as well as in the schools drop-down, and in the Schools table are referred to as school_number while in Students table they’re schoolid
 
6.	You can include additional filtering to the extracted data but it shouldn’t be necessary
7.	Leave the selection at “Export Filtered Rows” and click “Next”
8.	Name your extract, define the file type and delimiters, and click the “Save Template” button.  Sample file name and expected parameters are displayed below:
 
9.	Give your template an easy to understand ame that fits the purpose, and save it as new.

Step #2.3 Student with Attendance
1.	Download and install this PowerQuery plugin for aggregated attendance and membership data export:
○	System Management → Server → Plugin Configuration; Install
○	Browse to the ZIP file and confirm installation
○	Back on the plugin listing page, check the enablement box next to “ICAN Attendance and Membership Data Sets”, confirm the action
2.	In the Data Export Manager, select Category “Additional Datasets”
3.	In the Export From, select “ICAN Demographics and Attendance: Year to Date”.  Note: additional datasets are sorted by categories and then by name, so you may need to scroll down the list to locate ICAN datasets
4.	Follow steps 4-9 from Step #2.2.  The only difference is that all demographic fields will be included in this data set instead of exporting them separately.

 Step #3: Schedule the Export to Send Data to SDCOE Nightly
1.	After you create a template, it will show up under the My Templates tab. Navigate to the My Templates tab and click on the calendar icon.


2.	Schedule the export by ﬁlling in the ﬁelds:
●	Schedule Active: Checked
●	Days to Execute: M-F
●	When to Execute: Choose a time between 10 PM and 5 AM.
●	Send output to: SDCOE (or what you named the connection when you created a remote connection)
●	Path: Blank
3.	Verify that the export now shows up under the Scheduled System Templates tab.
4.	Repeat for each template.

Note: If you need to make changes to the export scheduler, edit it from the Scheduled System Templates tab.

Historical Attendance and Demographics
The provided ICAN plugin contains two data sets for historical information: attendance only and attendance with demographics.  During onboarding, you will be asked to pull some historical attendance and membership data for the participating school sites.  While you could export and compile such reports via other means, the plugin makes it a simpler process.  You will need to run exports for the end of each month requested by the ICAN team.  Then save them as ANSI CSV files with a clearly stated month and academic year name in the file name.  The example below displays how to run and export a Month 10 report for 2022 for attendance only:
 
 
It matters not how far back you go as long as you keep these caveats in mind.

ICAN Plugin Caveats and Notes
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

# All Students
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


 SQL Queries
There are four queries below; each highlighted in yellow.
1. ican.att_ytd pulls just year-to-date attendance
2. ican.att_historical pulls just historical attendance
3. ican.allData_ytd pulls demographics and attendance year-to-date
4. ican.allData_historical pulls demographics and attendance historically.

Year-to-Date Attendance

<query name="ican.att_ytd" coreTable="Students" flattened="true" tags="dice,ican">
	<summary>ICAN Attendance: Year to Date</summary>
	<description>Pulls absences counting towards ADA</description>
    <args>
      <arg name="LowerGrade" description="Specify Lower Grade" column="students.grade_level" type="primitive" default="0" required="true" />
      <arg name="UpperGrade" description="Specify Upper Grade" column="students.grade_level" type="primitive" default="12" required="true" />
	  <arg name="ExcludeSites" description="Exclude Sites: list schoolids separated by commas" column="students.lastfirst" type="primitive" default="0" required="false" />
    </args>
    <columns>
      <column column="students.id">ICAN.SSID</column>
      <column column="S_CA_SCH_X.stateschoolnumber">ICAN.CDS</column>
      <column column="schools.name">ICAN.School</column>
      <column column="students.grade_level">ICAN.Grade</column>
      <column column="students.entrydate">ICAN.ReportingDate</column>
      <column column="students.id">ICAN.CumulativeDaysEnrolled</column>
      <column column="students.id">ICAN.CumulativeDaysAbsent</column>
      <column column="students.id">ICAN.DaysEnrolledAllSites</column>
      <column column="students.id">ICAN.DaysAbsentAllSites</column>
    </columns>
    <sql><![CDATA[
	WITH CurrentTerm AS (
	SELECT t.firstday AS firstday, t.lastday+1 AS lastday
	FROM Terms t
	WHERE sysdate BETWEEN t.firstday AND t.lastday+1 AND t.isyearrec = '1' AND t.schoolid = '0'
	AND ROWNUM = 1
	)
	SELECT s.state_studentnumber AS SSID,
	  lpad(scx.stateschoolnumber, 7, '0') AS CDS,
	  sc.name AS School,
	  s.grade_level AS Grade,
	  to_char(sysdate, 'YYYYMMDD') AS ReportingDate,
	  NVL(cmem.totalMem, 0) AS CumulativeDaysEnrolled,
	  NVL(cabs.totalAbs, 0) AS CumulativeDaysAbsent,
	  NVL(amem.totalMem, 0) AS DaysEnrolledAllSites,
	  NVL(aabs.totalAbs, 0) AS DaysAbsentAllSites
	 
	FROM Students s
    	LEFT JOIN (
	SELECT att.studentid, COUNT(ac.att_code) AS totalAbs
	FROM Attendance att
	JOIN CurrentTerm ct ON att.att_date BETWEEN ct.firstday AND sysdate
	JOIN Attendance_Code ac ON  att.yearid = ac.yearid AND att.schoolid = ac.schoolid AND att.attendance_codeid = ac.id
	AND ac.CALCULATE_ADA_YN = 1  AND ac.Presence_Status_CD = 'Absent'
	AND (:ExcludeSites IS NULL OR att.schoolid NOT IN (
	SELECT TRIM(REGEXP_SUBSTR(:ExcludeSites, '[^,]+', 1, LEVEL))
	FROM dual
	CONNECT BY REGEXP_SUBSTR(:ExcludeSites, '[^,]+', 1, LEVEL) IS NOT NULL
	))
	JOIN Students s ON att.studentid = s.id AND att.schoolid = s.schoolid AND att.att_date BETWEEN s.entrydate AND s.exitdate
	AND s.enroll_status = '0' AND s.grade_level BETWEEN :LowerGrade AND :UpperGrade
	WHERE  att.att_mode_code = 'ATT_ModeDaily'
	GROUP BY att.studentid    	
    	) cabs ON s.id = cabs.studentid
    	LEFT JOIN (
	SELECT COUNT(cd.date_value) AS totalMem, s.id AS stid
	FROM PS.Calendar_Day cd
	JOIN CurrentTerm ct ON cd.date_value BETWEEN ct.firstday AND sysdate
	AND cd.insession = 1  AND cd.MemberShipValue = 1 AND cd.schoolid NOT IN ('0')
	JOIN Students s ON cd.schoolid = s.schoolid AND cd.date_value BETWEEN s.entrydate AND s.exitdate-1
	AND s.enroll_status = '0' AND s.grade_level BETWEEN :LowerGrade AND :UpperGrade
	AND (:ExcludeSites IS NULL OR s.schoolid NOT IN (
	SELECT TRIM(REGEXP_SUBSTR(:ExcludeSites, '[^,]+', 1, LEVEL))
	FROM dual
	CONNECT BY REGEXP_SUBSTR(:ExcludeSites, '[^,]+', 1, LEVEL) IS NOT NULL
	))
	GROUP BY s.id   	
    	) cmem ON s.id = cmem.stid
    	LEFT JOIN (
	SELECT att.studentid, COUNT(ac.att_code) AS totalAbs
	FROM Attendance att
	JOIN CurrentTerm ct ON att.att_date BETWEEN ct.firstday AND sysdate
	JOIN Attendance_Code ac ON  att.yearid = ac.yearid AND att.schoolid = ac.schoolid AND att.attendance_codeid = ac.id
	AND ac.CALCULATE_ADA_YN = 1  AND ac.Presence_Status_CD = 'Absent'
	AND (:ExcludeSites IS NULL OR att.schoolid NOT IN (
	SELECT TRIM(REGEXP_SUBSTR(:ExcludeSites, '[^,]+', 1, LEVEL))
	FROM dual
	CONNECT BY REGEXP_SUBSTR(:ExcludeSites, '[^,]+', 1, LEVEL) IS NOT NULL
	))
	JOIN Students s ON att.studentid = s.id AND s.enroll_status = '0' AND s.grade_level BETWEEN :LowerGrade AND :UpperGrade
	WHERE  att.att_mode_code = 'ATT_ModeDaily'
	GROUP BY att.studentid    	
    	) aabs ON s.id = aabs.studentid
    	LEFT JOIN (
            SELECT COUNT(cd.date_value) AS totalMem, eh.stid AS stid
	FROM PS.Calendar_Day cd
	JOIN CurrentTerm ct ON cd.date_value BETWEEN ct.firstday AND sysdate
	AND cd.insession = 1  AND cd.MemberShipValue = 1 AND cd.schoolid NOT IN ('0')
	JOIN (
                SELECT s.id AS stid, s.entrydate, s.exitdate, s.schoolid, s.grade_level
                FROM Students s
                JOIN CurrentTerm ct ON s.entrydate BETWEEN ct.firstday AND ct.lastday AND s.exitdate > s.entrydate
                AND s.grade_level BETWEEN :LowerGrade AND :UpperGrade
	AND (:ExcludeSites IS NULL OR s.schoolid NOT IN (
	SELECT TRIM(REGEXP_SUBSTR(:ExcludeSites, '[^,]+', 1, LEVEL))
	FROM dual
	CONNECT BY REGEXP_SUBSTR(:ExcludeSites, '[^,]+', 1, LEVEL) IS NOT NULL
	))
                UNION
                SELECT re.studentid AS stid, re.entrydate, re.exitdate, re.schoolid, re.grade_level
                FROM ReEnrollments re
                JOIN Students s ON re.studentid = s.id
                JOIN CurrentTerm ct ON re.entrydate BETWEEN ct.firstday AND ct.lastday AND re.exitdate > re.entrydate
                AND re.grade_level BETWEEN :LowerGrade AND :UpperGrade
	AND (:ExcludeSites IS NULL OR re.schoolid NOT IN (
	SELECT TRIM(REGEXP_SUBSTR(:ExcludeSites, '[^,]+', 1, LEVEL))
	FROM dual
	CONNECT BY REGEXP_SUBSTR(:ExcludeSites, '[^,]+', 1, LEVEL) IS NOT NULL
	))        	
            ) eh ON cd.schoolid = eh.schoolid AND cd.date_value BETWEEN eh.entrydate AND eh.exitdate-1
	GROUP BY eh.stid    	
    	) amem ON s.id = amem.stid
	JOIN Schools sc ON s.schoolid = sc.school_number
    	JOIN S_CA_SCH_X scx ON sc.dcid = scx.schoolsdcid
	WHERE s.enroll_status = '0' AND s.grade_level BETWEEN :LowerGrade AND :UpperGrade
	AND (:ExcludeSites IS NULL OR s.schoolid NOT IN (
	SELECT TRIM(REGEXP_SUBSTR(:ExcludeSites, '[^,]+', 1, LEVEL))
	FROM dual
	CONNECT BY REGEXP_SUBSTR(:ExcludeSites, '[^,]+', 1, LEVEL) IS NOT NULL
	))
    ]]></sql>
  </query>

Historical Attendance
	<query name="ican.att_historical" coreTable="Students" flattened="true" tags="dice,ican">
	<summary>ICAN Attendance: Historical</summary>
	<description>Pulls absences counting towards ADA</description>
    <args>
	  <arg name="AsOfDate" description="Specify Report Date" column="students.entrydate" type="primitive" required="true" />
      <arg name="LowerGrade" description="Specify Lower Grade" column="students.grade_level" type="primitive" default="0" required="true" />
      <arg name="UpperGrade" description="Specify Upper Grade" column="students.grade_level" type="primitive" default="12" required="true" />
	  <arg name="ExcludeSites" description="Exclude Sites: list schoolids separated by commas" column="students.lastfirst" type="primitive" default="0" required="false" />
    </args>
    <columns>
      <column column="students.id">ICAN.SSID</column>
      <column column="S_CA_SCH_X.stateschoolnumber">ICAN.CDS</column>
      <column column="schools.name">ICAN.School</column>
      <column column="students.grade_level">ICAN.Grade</column>
      <column column="students.entrydate">ICAN.ReportingDate</column>
      <column column="students.id">ICAN.CumulativeDaysEnrolled</column>
      <column column="students.id">ICAN.CumulativeDaysAbsent</column>
      <column column="students.id">ICAN.DaysEnrolledAllSites</column>
      <column column="students.id">ICAN.DaysAbsentAllSites</column>
      <column column="students.last_name">ICAN.AcademicYear</column>
    </columns>
    <sql><![CDATA[
    	WITH RequestedTerm AS (
	SELECT t.firstday AS firstday, t.lastday+1 AS lastday, t.yearid AS yearid, t.abbreviation AS acadyear
	FROM Terms t
	WHERE :AsOfDate BETWEEN t.firstday AND t.lastday+1 AND t.isyearrec = '1' AND t.schoolid = '0'
	AND ROWNUM = 1
    	)
	SELECT s.state_studentnumber AS SSID,
	  lpad(scx.stateschoolnumber, 7, '0') AS CDS,
	  sc.name AS School,
	  cmem.grade_level AS Grade,
	  to_char(:AsOfDate, 'YYYYMMDD') AS ReportingDate,
	  NVL(cmem.totalMem, 0) AS CumulativeDaysEnrolled,
	  NVL(cabs.totalAbs, 0) AS CumulativeDaysAbsent,
	  NVL(amem.totalMem, 0) AS DaysEnrolledAllSites,
	  NVL(aabs.totalAbs, 0) AS DaysAbsentAllSites,
          rt.acadyear AS AcademicYear
	FROM Students s
        CROSS JOIN RequestedTerm rt
    	JOIN (
            SELECT COUNT(cd.date_value) AS totalMem, eh.stid AS stid, eh.schoolid, eh.grade_level
	FROM PS.Calendar_Day cd
            JOIN RequestedTerm rt ON cd.date_value BETWEEN rt.firstday AND :AsOfDate
	JOIN (
                SELECT s.id AS stid, s.entrydate, s.exitdate, s.schoolid, s.grade_level
                FROM Students s
                CROSS JOIN RequestedTerm rt
                WHERE s.entrydate BETWEEN rt.firstday AND :AsOfDate AND s.exitdate > :AsOfDate AND s.exitdate > s.entrydate
                AND s.grade_level BETWEEN :LowerGrade AND :UpperGrade
	AND (:ExcludeSites IS NULL OR s.schoolid NOT IN (
	SELECT TRIM(REGEXP_SUBSTR(:ExcludeSites, '[^,]+', 1, LEVEL))
	FROM dual
	CONNECT BY REGEXP_SUBSTR(:ExcludeSites, '[^,]+', 1, LEVEL) IS NOT NULL
	))
                UNION
                SELECT re.studentid AS stid, re.entrydate, re.exitdate, re.schoolid, re.grade_level
                FROM ReEnrollments re
                CROSS JOIN RequestedTerm rt
                WHERE re.entrydate BETWEEN rt.firstday AND :AsOfDate AND re.exitdate > :AsOfDate AND re.exitdate > re.entrydate
                AND re.grade_level BETWEEN :LowerGrade AND :UpperGrade
	AND (:ExcludeSites IS NULL OR re.schoolid NOT IN (
	SELECT TRIM(REGEXP_SUBSTR(:ExcludeSites, '[^,]+', 1, LEVEL))
	FROM dual
	CONNECT BY REGEXP_SUBSTR(:ExcludeSites, '[^,]+', 1, LEVEL) IS NOT NULL
	))        	
            ) eh ON cd.schoolid = eh.schoolid AND cd.date_value BETWEEN eh.entrydate AND eh.exitdate-1
            WHERE cd.insession = 1  AND cd.MemberShipValue = 1 AND cd.schoolid NOT IN ('0')
	GROUP BY eh.stid, eh.schoolid, eh.grade_level 	
    	) cmem ON s.id = cmem.stid
    	LEFT JOIN (
	SELECT att.studentid, att.schoolid, COUNT(ac.att_code) AS totalAbs
	FROM Attendance att
            JOIN RequestedTerm rt ON att.yearid = rt.yearid
            JOIN (
                SELECT s.id AS stid, s.entrydate, s.exitdate, s.schoolid, s.grade_level
                FROM Students s
                CROSS JOIN RequestedTerm rt
                WHERE s.entrydate BETWEEN rt.firstday AND :AsOfDate AND s.exitdate > :AsOfDate AND s.exitdate > s.entrydate
                AND s.grade_level BETWEEN :LowerGrade AND :UpperGrade
	AND (:ExcludeSites IS NULL OR s.schoolid NOT IN (
	SELECT TRIM(REGEXP_SUBSTR(:ExcludeSites, '[^,]+', 1, LEVEL))
	FROM dual
	CONNECT BY REGEXP_SUBSTR(:ExcludeSites, '[^,]+', 1, LEVEL) IS NOT NULL
	))
                UNION
                SELECT re.studentid AS stid, re.entrydate, re.exitdate, re.schoolid, re.grade_level
                FROM ReEnrollments re
                CROSS JOIN RequestedTerm rt
                WHERE re.entrydate BETWEEN rt.firstday AND :AsOfDate AND re.exitdate > :AsOfDate AND re.exitdate > re.entrydate
                AND (:ExcludeSites IS NULL OR re.schoolid NOT IN (
	SELECT TRIM(REGEXP_SUBSTR(:ExcludeSites, '[^,]+', 1, LEVEL))
	FROM dual
	CONNECT BY REGEXP_SUBSTR(:ExcludeSites, '[^,]+', 1, LEVEL) IS NOT NULL
	))
	) eh ON att.studentid = eh.stid AND att.schoolid = eh.schoolid AND att.att_date BETWEEN eh.entrydate AND eh.exitdate-1
	JOIN Attendance_Code ac ON  att.yearid = ac.yearid AND att.schoolid = ac.schoolid AND att.attendance_codeid = ac.id
	AND ac.CALCULATE_ADA_YN = 1  AND ac.Presence_Status_CD = 'Absent'
	AND (:ExcludeSites IS NULL OR att.schoolid NOT IN (
	SELECT TRIM(REGEXP_SUBSTR(:ExcludeSites, '[^,]+', 1, LEVEL))
	FROM dual
	CONNECT BY REGEXP_SUBSTR(:ExcludeSites, '[^,]+', 1, LEVEL) IS NOT NULL
	))
	WHERE  att.att_mode_code = 'ATT_ModeDaily' AND att.att_date BETWEEN eh.entrydate AND :AsOfDate
	GROUP BY att.studentid, att.schoolid    	
    	) cabs ON cmem.stid = cabs.studentid AND cmem.schoolid = cabs.schoolid
    	JOIN (
            SELECT COUNT(cd.date_value) AS totalMem, eh.stid AS stid
	FROM PS.Calendar_Day cd
            JOIN RequestedTerm rt ON cd.date_value BETWEEN rt.firstday AND :AsOfDate
	JOIN (
                SELECT DISTINCT s.id AS stid, s.entrydate, s.exitdate, s.schoolid, s.grade_level
                FROM Students s
                JOIN RequestedTerm rt ON s.entrydate BETWEEN rt.firstday AND :AsOfDate AND s.exitdate > s.entrydate
                WHERE s.grade_level BETWEEN :LowerGrade AND :UpperGrade
	AND (:ExcludeSites IS NULL OR s.schoolid NOT IN (
	SELECT TRIM(REGEXP_SUBSTR(:ExcludeSites, '[^,]+', 1, LEVEL))
	FROM dual
	CONNECT BY REGEXP_SUBSTR(:ExcludeSites, '[^,]+', 1, LEVEL) IS NOT NULL
	))
                UNION
                SELECT DISTINCT re.studentid AS stid, re.entrydate, re.exitdate, re.schoolid, re.grade_level
                FROM ReEnrollments re
                JOIN RequestedTerm rt ON re.entrydate BETWEEN rt.firstday AND :AsOfDate AND re.exitdate > re.entrydate
                WHERE re.grade_level BETWEEN :LowerGrade AND :UpperGrade
	AND (:ExcludeSites IS NULL OR re.schoolid NOT IN (
	SELECT TRIM(REGEXP_SUBSTR(:ExcludeSites, '[^,]+', 1, LEVEL))
	FROM dual
	CONNECT BY REGEXP_SUBSTR(:ExcludeSites, '[^,]+', 1, LEVEL) IS NOT NULL
	))        	
            ) eh ON cd.date_value BETWEEN eh.entrydate AND eh.exitdate-1 AND cd.schoolid = eh.schoolid
            WHERE cd.insession = 1  AND cd.MemberShipValue = 1 AND cd.schoolid NOT IN ('0')
	GROUP BY eh.stid 	
    	) amem ON s.id = amem.stid
	LEFT JOIN (
            SELECT att.studentid, COUNT(ac.att_code) AS totalAbs
	FROM Attendance att
            JOIN RequestedTerm rt ON att.yearid = rt.yearid
            JOIN (
                SELECT DISTINCT s.id AS stid, s.entrydate, s.exitdate, s.schoolid, s.grade_level
                FROM Students s
                JOIN RequestedTerm rt ON s.entrydate BETWEEN rt.firstday AND :AsOfDate AND s.exitdate > s.entrydate
                WHERE s.grade_level BETWEEN :LowerGrade AND :UpperGrade
	AND (:ExcludeSites IS NULL OR s.schoolid NOT IN (
	SELECT TRIM(REGEXP_SUBSTR(:ExcludeSites, '[^,]+', 1, LEVEL))
	FROM dual
	CONNECT BY REGEXP_SUBSTR(:ExcludeSites, '[^,]+', 1, LEVEL) IS NOT NULL
	))
                UNION
                SELECT DISTINCT re.studentid AS stid, re.entrydate, re.exitdate, re.schoolid, re.grade_level
                FROM ReEnrollments re
                JOIN RequestedTerm rt ON re.entrydate BETWEEN rt.firstday AND :AsOfDate AND re.exitdate > re.entrydate
                WHERE re.grade_level BETWEEN :LowerGrade AND :UpperGrade
	AND (:ExcludeSites IS NULL OR re.schoolid NOT IN (
	SELECT TRIM(REGEXP_SUBSTR(:ExcludeSites, '[^,]+', 1, LEVEL))
	FROM dual
	CONNECT BY REGEXP_SUBSTR(:ExcludeSites, '[^,]+', 1, LEVEL) IS NOT NULL
	))
	) eh ON att.studentid = eh.stid AND att.schoolid = eh.schoolid AND att.att_date BETWEEN eh.entrydate AND eh.exitdate-1
	JOIN Attendance_Code ac ON  att.yearid = ac.yearid AND att.schoolid = ac.schoolid AND att.attendance_codeid = ac.id
	AND ac.CALCULATE_ADA_YN = 1  AND ac.Presence_Status_CD = 'Absent'
	AND (:ExcludeSites IS NULL OR att.schoolid NOT IN (
	SELECT TRIM(REGEXP_SUBSTR(:ExcludeSites, '[^,]+', 1, LEVEL))
	FROM dual
	CONNECT BY REGEXP_SUBSTR(:ExcludeSites, '[^,]+', 1, LEVEL) IS NOT NULL
	))
	WHERE  att.att_mode_code = 'ATT_ModeDaily' AND att.att_date BETWEEN eh.entrydate AND :AsOfDate
	GROUP BY att.studentid  	
    	) aabs ON s.id = aabs.studentid
    	
	JOIN Schools sc ON cmem.schoolid = sc.school_number
    	JOIN S_CA_SCH_X scx ON sc.dcid = scx.schoolsdcid
	WHERE (:ExcludeSites IS NULL OR s.schoolid NOT IN (
	SELECT TRIM(REGEXP_SUBSTR(:ExcludeSites, '[^,]+', 1, LEVEL))
	FROM dual
	CONNECT BY REGEXP_SUBSTR(:ExcludeSites, '[^,]+', 1, LEVEL) IS NOT NULL
	))
    ]]></sql>
  </query>

Demographics and Year-to-Date Attendance
  <query name="ican.allData_ytd" coreTable="Students" flattened="true" tags="dice,ican">
	<summary>ICAN Demographics and Attendance: Year to Date</summary>
	<description>Student demographics and absences counting towards ADA</description>
    <args>
      <arg name="LowerGrade" description="Specify Lower Grade" column="students.grade_level" type="primitive" default="0" required="true" />
      <arg name="UpperGrade" description="Specify Upper Grade" column="students.grade_level" type="primitive" default="12" required="true" />
	  <arg name="ExcludeSites" description="Exclude Sites: list schoolids separated by commas" column="students.lastfirst" type="primitive" default="0" required="false" />
    </args>
    <columns>
      <column column="students.id">ICAN.SSID</column>
      <column column="S_CA_SCH_X.stateschoolnumber">ICAN.CDS</column>
      <column column="schools.name">ICAN.School</column>
      <column column="students.grade_level">ICAN.Grade</column>
	  <column column="students.last_name">ICAN.LastName</column>
	  <column column="students.first_name">ICAN.FirstName</column>
	  <column column="students.gender">ICAN.Gender</column>
	  <column column="students.fedethnicity">ICAN.Hispanic</column>
	  <column column="students.last_name">ICAN.RaceEthnicity</column>
	  <column column="students.last_name">ICAN.SED</column>
	  <column column="students.last_name">ICAN.EL</column>
	  <column column="students.last_name">ICAN.SpEd</column>
      <column column="students.entrydate">ICAN.ReportingDate</column>
      <column column="students.id">ICAN.CumulativeDaysEnrolled</column>
      <column column="students.id">ICAN.CumulativeDaysAbsent</column>
      <column column="students.id">ICAN.DaysEnrolledAllSites</column>
      <column column="students.id">ICAN.DaysAbsentAllSites</column>
    </columns>
    <sql><![CDATA[
	WITH CurrentTerm AS (
	SELECT t.firstday AS firstday, t.lastday+1 AS lastday
	FROM Terms t
	WHERE sysdate BETWEEN t.firstday AND t.lastday+1 AND t.isyearrec = '1' AND t.schoolid = '0'
	AND ROWNUM = 1
	),
        RefCodes AS (
            SELECT 'N' AS feedin, '100' AS feedout FROM dual
            UNION SELECT 'A', '200' FROM dual
            UNION SELECT 'I', '300' FROM dual
            UNION SELECT 'F', '400' FROM dual
            UNION SELECT 'B', '600' FROM dual
            UNION SELECT 'W', '700' FROM dual
    	)
	SELECT s.state_studentnumber AS SSID,
	  lpad(scx.stateschoolnumber, 7, '0') AS CDS,
	  sc.name AS School,
	  s.grade_level AS Grade,
          s.last_name AS "LastName",
          s.first_name AS "FirstName",
          s.gender AS "Gender",
          CASE to_char(s.fedethnicity) WHEN '1' THEN 'Yes' ELSE 'No' END AS "Hispanic",
          CASE WHEN LENGTH(r.racecat) = 1 THEN rc.feedout
            WHEN REPLACE(r.racecat, 'A', '') = '' THEN '200'
            WHEN REPLACE(r.racecat, 'A', '') = 'F' THEN '400'
            WHEN REPLACE(r.racecat, 'I', '') = '' THEN '300'
            WHEN LENGTH(r.racecat) > 1 THEN '800'
            ELSE '' END AS "RaceEthnicity",
          NVL(frpm.sed, 'No') AS "SED",
          CASE NVL(el.elastatus, 'No') WHEN 'EL' THEN 'Yes' ELSE 'No' END AS "EL",
          CASE WHEN to_char(NVL(x.ieptype, '90')) IN ('10', '20') THEN 'Yes' ELSE 'No' END AS "SpEd",
	  to_char(sysdate, 'YYYYMMDD') AS ReportingDate,
	  NVL(cmem.totalMem, 0) AS CumulativeDaysEnrolled,
	  NVL(cabs.totalAbs, 0) AS CumulativeDaysAbsent,
	  NVL(amem.totalMem, 0) AS DaysEnrolledAllSites,
	  NVL(aabs.totalAbs, 0) AS DaysAbsentAllSites
	 
	FROM Students s
    	LEFT JOIN (
	SELECT att.studentid, COUNT(ac.att_code) AS totalAbs
	FROM Attendance att
	JOIN CurrentTerm ct ON att.att_date BETWEEN ct.firstday AND sysdate
	JOIN Attendance_Code ac ON  att.yearid = ac.yearid AND att.schoolid = ac.schoolid AND att.attendance_codeid = ac.id
	AND ac.CALCULATE_ADA_YN = 1  AND ac.Presence_Status_CD = 'Absent'
	AND (:ExcludeSites IS NULL OR att.schoolid NOT IN (
	SELECT TRIM(REGEXP_SUBSTR(:ExcludeSites, '[^,]+', 1, LEVEL))
	FROM dual
	CONNECT BY REGEXP_SUBSTR(:ExcludeSites, '[^,]+', 1, LEVEL) IS NOT NULL
	))
	JOIN Students s ON att.studentid = s.id AND att.schoolid = s.schoolid AND att.att_date BETWEEN s.entrydate AND s.exitdate
	AND s.enroll_status = '0' AND s.grade_level BETWEEN :LowerGrade AND :UpperGrade
	WHERE  att.att_mode_code = 'ATT_ModeDaily'
	GROUP BY att.studentid    	
    	) cabs ON s.id = cabs.studentid
    	LEFT JOIN (
	SELECT COUNT(cd.date_value) AS totalMem, s.id AS stid
	FROM PS.Calendar_Day cd
	JOIN CurrentTerm ct ON cd.date_value BETWEEN ct.firstday AND sysdate
	AND cd.insession = 1  AND cd.MemberShipValue = 1 AND cd.schoolid NOT IN ('0')
	JOIN Students s ON cd.schoolid = s.schoolid AND cd.date_value BETWEEN s.entrydate AND s.exitdate-1
	AND s.enroll_status = '0' AND s.grade_level BETWEEN :LowerGrade AND :UpperGrade
	AND (:ExcludeSites IS NULL OR s.schoolid NOT IN (
	SELECT TRIM(REGEXP_SUBSTR(:ExcludeSites, '[^,]+', 1, LEVEL))
	FROM dual
	CONNECT BY REGEXP_SUBSTR(:ExcludeSites, '[^,]+', 1, LEVEL) IS NOT NULL
	))
	GROUP BY s.id   	
    	) cmem ON s.id = cmem.stid
    	LEFT JOIN (
	SELECT att.studentid, COUNT(ac.att_code) AS totalAbs
	FROM Attendance att
	JOIN CurrentTerm ct ON att.att_date BETWEEN ct.firstday AND sysdate
	JOIN Attendance_Code ac ON  att.yearid = ac.yearid AND att.schoolid = ac.schoolid AND att.attendance_codeid = ac.id
	AND ac.CALCULATE_ADA_YN = 1  AND ac.Presence_Status_CD = 'Absent'
	AND (:ExcludeSites IS NULL OR att.schoolid NOT IN (
	SELECT TRIM(REGEXP_SUBSTR(:ExcludeSites, '[^,]+', 1, LEVEL))
	FROM dual
	CONNECT BY REGEXP_SUBSTR(:ExcludeSites, '[^,]+', 1, LEVEL) IS NOT NULL
	))
	JOIN Students s ON att.studentid = s.id AND s.enroll_status = '0' AND s.grade_level BETWEEN :LowerGrade AND :UpperGrade
	WHERE  att.att_mode_code = 'ATT_ModeDaily'
	GROUP BY att.studentid    	
    	) aabs ON s.id = aabs.studentid
    	LEFT JOIN (
            SELECT COUNT(cd.date_value) AS totalMem, eh.stid AS stid
	FROM PS.Calendar_Day cd
	JOIN CurrentTerm ct ON cd.date_value BETWEEN ct.firstday AND sysdate
	AND cd.insession = 1  AND cd.MemberShipValue = 1 AND cd.schoolid NOT IN ('0')
	JOIN (
                SELECT s.id AS stid, s.entrydate, s.exitdate, s.schoolid, s.grade_level
                FROM Students s
                JOIN CurrentTerm ct ON s.entrydate BETWEEN ct.firstday AND ct.lastday AND s.exitdate > s.entrydate
                AND s.grade_level BETWEEN :LowerGrade AND :UpperGrade
	AND (:ExcludeSites IS NULL OR s.schoolid NOT IN (
	SELECT TRIM(REGEXP_SUBSTR(:ExcludeSites, '[^,]+', 1, LEVEL))
	FROM dual
	CONNECT BY REGEXP_SUBSTR(:ExcludeSites, '[^,]+', 1, LEVEL) IS NOT NULL
	))
                UNION
                SELECT re.studentid AS stid, re.entrydate, re.exitdate, re.schoolid, re.grade_level
                FROM ReEnrollments re
                JOIN Students s ON re.studentid = s.id
                JOIN CurrentTerm ct ON re.entrydate BETWEEN ct.firstday AND ct.lastday AND re.exitdate > re.entrydate
                AND re.grade_level BETWEEN :LowerGrade AND :UpperGrade
	AND (:ExcludeSites IS NULL OR re.schoolid NOT IN (
	SELECT TRIM(REGEXP_SUBSTR(:ExcludeSites, '[^,]+', 1, LEVEL))
	FROM dual
	CONNECT BY REGEXP_SUBSTR(:ExcludeSites, '[^,]+', 1, LEVEL) IS NOT NULL
	))        	
            ) eh ON cd.schoolid = eh.schoolid AND cd.date_value BETWEEN eh.entrydate AND eh.exitdate-1
	GROUP BY eh.stid    	
    	) amem ON s.id = amem.stid
	JOIN Schools sc ON s.schoolid = sc.school_number
    	JOIN S_CA_SCH_X scx ON sc.dcid = scx.schoolsdcid
    	LEFT JOIN S_CA_Stu_X x ON s.dcid = x.studentsdcid
    	LEFT JOIN (
            SELECT sr.studentid,
            LISTAGG ((CASE WHEN sr.racecd = '100' THEN 'N' WHEN sr.racecd = '400' THEN 'F' WHEN sr.racecd = '600' THEN 'B' WHEN sr.racecd = '700' THEN 'W'
                WHEN sr.racecd LIKE '2__' THEN 'A' WHEN sr.racecd LIKE '3__' THEN 'I' ELSE '' END), '') WITHIN GROUP (ORDER BY sr.studentid, sr.racecd) AS racecat
            FROM StudentRace sr JOIN Students s ON sr.studentid = s.id
            AND s.entrydate <= sysdate AND s.exitdate > sysdate
            GROUP BY sr.studentid
            ORDER BY sr.studentid
    	) r ON s.id = r.studentid
    	LEFT JOIN RefCodes rc ON r.racecat = rc.feedin
    	LEFT JOIN S_CA_Stu_ELA_C el ON s.dcid = el.studentsdcid AND el.elastatus = 'EL'
            AND el.elastatusstartdate <= sysdate AND (el.elastatusenddate > sysdate OR el.elastatusenddate IS NULL)
    	LEFT JOIN (
            SELECT DISTINCT 'Yes' AS sed, studentsdcid, schoolid
            FROM S_CA_Stu_CALPADSPrograms_C
            WHERE programcode IN ('181','182') AND startdate <= sysdate AND (enddate > sysdate OR enddate IS NULL)
    	) frpm ON s.dcid = frpm.studentsdcid AND s.schoolid = frpm.schoolid
	WHERE s.enroll_status = '0' AND s.grade_level BETWEEN :LowerGrade AND :UpperGrade
	AND (:ExcludeSites IS NULL OR s.schoolid NOT IN (
	SELECT TRIM(REGEXP_SUBSTR(:ExcludeSites, '[^,]+', 1, LEVEL))
	FROM dual
	CONNECT BY REGEXP_SUBSTR(:ExcludeSites, '[^,]+', 1, LEVEL) IS NOT NULL
	))
    ]]></sql>
  </query>
	

Demographics and Historical Attendance 
<query name="ican.allData_historical" coreTable="Students" flattened="true" tags="dice,ican">
	<summary>ICAN Demographics and Attendance: Historical</summary>
	<description>Student demographics and absences counting towards ADA</description>
    <args>
	  <arg name="AsOfDate" description="Specify Report Date" column="students.entrydate" type="primitive" required="true" />
      <arg name="LowerGrade" description="Specify Lower Grade" column="students.grade_level" type="primitive" default="0" required="true" />
      <arg name="UpperGrade" description="Specify Upper Grade" column="students.grade_level" type="primitive" default="12" required="true" />
	  <arg name="ExcludeSites" description="Exclude Sites: list schoolids separated by commas" column="students.lastfirst" type="primitive" default="0" required="false" />
    </args>
    <columns>
      <column column="students.id">ICAN.SSID</column>
      <column column="S_CA_SCH_X.stateschoolnumber">ICAN.CDS</column>
      <column column="schools.name">ICAN.School</column>
      <column column="students.grade_level">ICAN.Grade</column>
	  <column column="students.last_name">ICAN.LastName</column>
	  <column column="students.first_name">ICAN.FirstName</column>
	  <column column="students.gender">ICAN.Gender</column>
	  <column column="students.fedethnicity">ICAN.Hispanic</column>
	  <column column="students.last_name">ICAN.RaceEthnicity</column>
	  <column column="students.last_name">ICAN.SED</column>
	  <column column="students.last_name">ICAN.EL</column>
	  <column column="students.last_name">ICAN.SpEd</column>
      <column column="students.entrydate">ICAN.ReportingDate</column>
      <column column="students.id">ICAN.CumulativeDaysEnrolled</column>
      <column column="students.id">ICAN.CumulativeDaysAbsent</column>
      <column column="students.id">ICAN.DaysEnrolledAllSites</column>
      <column column="students.id">ICAN.DaysAbsentAllSites</column>
      <column column="students.last_name">ICAN.AcademicYear</column>
    </columns>
    <sql><![CDATA[
    	WITH RequestedTerm AS (
	SELECT t.firstday AS firstday, t.lastday+1 AS lastday, t.yearid AS yearid, t.abbreviation AS acadyear
	FROM Terms t
	WHERE :AsOfDate BETWEEN t.firstday AND t.lastday+1 AND t.isyearrec = '1' AND t.schoolid = '0'
	AND ROWNUM = 1
    	),
        RefCodes AS (
            SELECT 'N' AS feedin, '100' AS feedout FROM dual
            UNION SELECT 'A', '200' FROM dual
            UNION SELECT 'I', '300' FROM dual
            UNION SELECT 'F', '400' FROM dual
            UNION SELECT 'B', '600' FROM dual
            UNION SELECT 'W', '700' FROM dual
    	)
	SELECT s.state_studentnumber AS SSID,
	  lpad(scx.stateschoolnumber, 7, '0') AS CDS,
	  sc.name AS School,
	  cmem.grade_level AS Grade,
          s.last_name AS "LastName",
          s.first_name AS "FirstName",
          s.gender AS "Gender",
          CASE to_char(s.fedethnicity) WHEN '1' THEN 'Yes' ELSE 'No' END AS "Hispanic",
          CASE WHEN LENGTH(r.racecat) = 1 THEN rc.feedout
            WHEN REPLACE(r.racecat, 'A', '') = '' THEN '200'
            WHEN REPLACE(r.racecat, 'A', '') = 'F' THEN '400'
            WHEN REPLACE(r.racecat, 'I', '') = '' THEN '300'
            WHEN LENGTH(r.racecat) > 1 THEN '800'
            ELSE '' END AS "RaceEthnicity",
          NVL(frpm.sed, 'No') AS "SED",
          CASE NVL(el.elastatus, 'No') WHEN 'EL' THEN 'Yes' ELSE 'No' END AS "EL",
          CASE WHEN to_char(NVL(x.ieptype, '90')) IN ('10', '20') THEN 'Yes' ELSE 'No' END AS "SpEd",
	  to_char(:AsOfDate, 'YYYYMMDD') AS ReportingDate,
	  NVL(cmem.totalMem, 0) AS CumulativeDaysEnrolled,
	  NVL(cabs.totalAbs, 0) AS CumulativeDaysAbsent,
	  NVL(amem.totalMem, 0) AS DaysEnrolledAllSites,
	  NVL(aabs.totalAbs, 0) AS DaysAbsentAllSites,
          rt.acadyear AS AcademicYearID
	FROM Students s
        CROSS JOIN RequestedTerm rt
    	JOIN (
            SELECT COUNT(cd.date_value) AS totalMem, eh.stid AS stid, eh.schoolid, eh.grade_level
	FROM PS.Calendar_Day cd
            JOIN RequestedTerm rt ON cd.date_value BETWEEN rt.firstday AND :AsOfDate
	JOIN (
                SELECT s.id AS stid, s.entrydate, s.exitdate, s.schoolid, s.grade_level
                FROM Students s
                CROSS JOIN RequestedTerm rt
                WHERE s.entrydate BETWEEN rt.firstday AND :AsOfDate AND s.exitdate > :AsOfDate AND s.exitdate > s.entrydate
                AND s.grade_level BETWEEN :LowerGrade AND :UpperGrade
	AND (:ExcludeSites IS NULL OR s.schoolid NOT IN (
	SELECT TRIM(REGEXP_SUBSTR(:ExcludeSites, '[^,]+', 1, LEVEL))
	FROM dual
	CONNECT BY REGEXP_SUBSTR(:ExcludeSites, '[^,]+', 1, LEVEL) IS NOT NULL
	))
                UNION
                SELECT re.studentid AS stid, re.entrydate, re.exitdate, re.schoolid, re.grade_level
                FROM ReEnrollments re
                CROSS JOIN RequestedTerm rt
                WHERE re.entrydate BETWEEN rt.firstday AND :AsOfDate AND re.exitdate > :AsOfDate AND re.exitdate > re.entrydate
                AND re.grade_level BETWEEN :LowerGrade AND :UpperGrade
	AND (:ExcludeSites IS NULL OR re.schoolid NOT IN (
	SELECT TRIM(REGEXP_SUBSTR(:ExcludeSites, '[^,]+', 1, LEVEL))
	FROM dual
	CONNECT BY REGEXP_SUBSTR(:ExcludeSites, '[^,]+', 1, LEVEL) IS NOT NULL
	))        	
            ) eh ON cd.schoolid = eh.schoolid AND cd.date_value BETWEEN eh.entrydate AND eh.exitdate-1
            WHERE cd.insession = 1  AND cd.MemberShipValue = 1 AND cd.schoolid NOT IN ('0')
	GROUP BY eh.stid, eh.schoolid, eh.grade_level 	
    	) cmem ON s.id = cmem.stid
    	LEFT JOIN (
	SELECT att.studentid, att.schoolid, COUNT(ac.att_code) AS totalAbs
	FROM Attendance att
            JOIN RequestedTerm rt ON att.yearid = rt.yearid
            JOIN (
                SELECT s.id AS stid, s.entrydate, s.exitdate, s.schoolid, s.grade_level
                FROM Students s
                CROSS JOIN RequestedTerm rt
                WHERE s.entrydate BETWEEN rt.firstday AND :AsOfDate AND s.exitdate > :AsOfDate AND s.exitdate > s.entrydate
                AND s.grade_level BETWEEN :LowerGrade AND :UpperGrade
	AND (:ExcludeSites IS NULL OR s.schoolid NOT IN (
	SELECT TRIM(REGEXP_SUBSTR(:ExcludeSites, '[^,]+', 1, LEVEL))
	FROM dual
	CONNECT BY REGEXP_SUBSTR(:ExcludeSites, '[^,]+', 1, LEVEL) IS NOT NULL
	))
                UNION
                SELECT re.studentid AS stid, re.entrydate, re.exitdate, re.schoolid, re.grade_level
                FROM ReEnrollments re
                CROSS JOIN RequestedTerm rt
                WHERE re.entrydate BETWEEN rt.firstday AND :AsOfDate AND re.exitdate > :AsOfDate AND re.exitdate > re.entrydate
                AND (:ExcludeSites IS NULL OR re.schoolid NOT IN (
	SELECT TRIM(REGEXP_SUBSTR(:ExcludeSites, '[^,]+', 1, LEVEL))
	FROM dual
	CONNECT BY REGEXP_SUBSTR(:ExcludeSites, '[^,]+', 1, LEVEL) IS NOT NULL
	))
	) eh ON att.studentid = eh.stid AND att.schoolid = eh.schoolid AND att.att_date BETWEEN eh.entrydate AND eh.exitdate-1
	JOIN Attendance_Code ac ON  att.yearid = ac.yearid AND att.schoolid = ac.schoolid AND att.attendance_codeid = ac.id
	AND ac.CALCULATE_ADA_YN = 1  AND ac.Presence_Status_CD = 'Absent'
	AND (:ExcludeSites IS NULL OR att.schoolid NOT IN (
	SELECT TRIM(REGEXP_SUBSTR(:ExcludeSites, '[^,]+', 1, LEVEL))
	FROM dual
	CONNECT BY REGEXP_SUBSTR(:ExcludeSites, '[^,]+', 1, LEVEL) IS NOT NULL
	))
	WHERE  att.att_mode_code = 'ATT_ModeDaily' AND att.att_date BETWEEN eh.entrydate AND :AsOfDate
	GROUP BY att.studentid, att.schoolid    	
    	) cabs ON cmem.stid = cabs.studentid AND cmem.schoolid = cabs.schoolid
    	JOIN (
            SELECT COUNT(cd.date_value) AS totalMem, eh.stid AS stid
	FROM PS.Calendar_Day cd
            JOIN RequestedTerm rt ON cd.date_value BETWEEN rt.firstday AND :AsOfDate
	JOIN (
                SELECT DISTINCT s.id AS stid, s.entrydate, s.exitdate, s.schoolid, s.grade_level
                FROM Students s
                JOIN RequestedTerm rt ON s.entrydate BETWEEN rt.firstday AND :AsOfDate AND s.exitdate > s.entrydate
                WHERE s.grade_level BETWEEN :LowerGrade AND :UpperGrade
	AND (:ExcludeSites IS NULL OR s.schoolid NOT IN (
	SELECT TRIM(REGEXP_SUBSTR(:ExcludeSites, '[^,]+', 1, LEVEL))
	FROM dual
	CONNECT BY REGEXP_SUBSTR(:ExcludeSites, '[^,]+', 1, LEVEL) IS NOT NULL
	))
                UNION
                SELECT DISTINCT re.studentid AS stid, re.entrydate, re.exitdate, re.schoolid, re.grade_level
                FROM ReEnrollments re
                JOIN RequestedTerm rt ON re.entrydate BETWEEN rt.firstday AND :AsOfDate AND re.exitdate > re.entrydate
                WHERE re.grade_level BETWEEN :LowerGrade AND :UpperGrade
	AND (:ExcludeSites IS NULL OR re.schoolid NOT IN (
	SELECT TRIM(REGEXP_SUBSTR(:ExcludeSites, '[^,]+', 1, LEVEL))
	FROM dual
	CONNECT BY REGEXP_SUBSTR(:ExcludeSites, '[^,]+', 1, LEVEL) IS NOT NULL
	))        	
            ) eh ON cd.date_value BETWEEN eh.entrydate AND eh.exitdate-1 AND cd.schoolid = eh.schoolid
            WHERE cd.insession = 1  AND cd.MemberShipValue = 1 AND cd.schoolid NOT IN ('0')
	GROUP BY eh.stid 	
    	) amem ON s.id = amem.stid
	LEFT JOIN (
            SELECT att.studentid, COUNT(ac.att_code) AS totalAbs
	FROM Attendance att
            JOIN RequestedTerm rt ON att.yearid = rt.yearid
            JOIN (
                SELECT DISTINCT s.id AS stid, s.entrydate, s.exitdate, s.schoolid, s.grade_level
                FROM Students s
                JOIN RequestedTerm rt ON s.entrydate BETWEEN rt.firstday AND :AsOfDate AND s.exitdate > s.entrydate
                WHERE s.grade_level BETWEEN :LowerGrade AND :UpperGrade
	AND (:ExcludeSites IS NULL OR s.schoolid NOT IN (
	SELECT TRIM(REGEXP_SUBSTR(:ExcludeSites, '[^,]+', 1, LEVEL))
	FROM dual
	CONNECT BY REGEXP_SUBSTR(:ExcludeSites, '[^,]+', 1, LEVEL) IS NOT NULL
	))
                UNION
                SELECT DISTINCT re.studentid AS stid, re.entrydate, re.exitdate, re.schoolid, re.grade_level
                FROM ReEnrollments re
                JOIN RequestedTerm rt ON re.entrydate BETWEEN rt.firstday AND :AsOfDate AND re.exitdate > re.entrydate
                WHERE re.grade_level BETWEEN :LowerGrade AND :UpperGrade
	AND (:ExcludeSites IS NULL OR re.schoolid NOT IN (
	SELECT TRIM(REGEXP_SUBSTR(:ExcludeSites, '[^,]+', 1, LEVEL))
	FROM dual
	CONNECT BY REGEXP_SUBSTR(:ExcludeSites, '[^,]+', 1, LEVEL) IS NOT NULL
	))
	) eh ON att.studentid = eh.stid AND att.schoolid = eh.schoolid AND att.att_date BETWEEN eh.entrydate AND eh.exitdate-1
	JOIN Attendance_Code ac ON  att.yearid = ac.yearid AND att.schoolid = ac.schoolid AND att.attendance_codeid = ac.id
	AND ac.CALCULATE_ADA_YN = 1  AND ac.Presence_Status_CD = 'Absent'
	AND (:ExcludeSites IS NULL OR att.schoolid NOT IN (
	SELECT TRIM(REGEXP_SUBSTR(:ExcludeSites, '[^,]+', 1, LEVEL))
	FROM dual
	CONNECT BY REGEXP_SUBSTR(:ExcludeSites, '[^,]+', 1, LEVEL) IS NOT NULL
	))
	WHERE  att.att_mode_code = 'ATT_ModeDaily' AND att.att_date BETWEEN eh.entrydate AND :AsOfDate
	GROUP BY att.studentid  	
    	) aabs ON s.id = aabs.studentid
    	
	JOIN Schools sc ON cmem.schoolid = sc.school_number
    	JOIN S_CA_SCH_X scx ON sc.dcid = scx.schoolsdcid
    	LEFT JOIN S_CA_Stu_X x ON s.dcid = x.studentsdcid
    	LEFT JOIN (
            SELECT sr.studentid,
            LISTAGG ((CASE WHEN sr.racecd = '100' THEN 'N' WHEN sr.racecd = '400' THEN 'F' WHEN sr.racecd = '600' THEN 'B' WHEN sr.racecd = '700' THEN 'W'
                WHEN sr.racecd LIKE '2__' THEN 'A' WHEN sr.racecd LIKE '3__' THEN 'I' ELSE '' END), '') WITHIN GROUP (ORDER BY sr.studentid, sr.racecd) AS racecat
            FROM StudentRace sr JOIN Students s ON sr.studentid = s.id
            AND s.id IN (
                SELECT id FROM Students WHERE entrydate <= :AsOfDate AND exitdate > :AsOfDate UNION ALL
                SELECT studentid FROM ReEnrollments WHERE entrydate <= :AsOfDate AND exitdate > :AsOfDate
                )
            GROUP BY sr.studentid
            ORDER BY sr.studentid
    	) r ON s.id = r.studentid
    	LEFT JOIN RefCodes rc ON r.racecat = rc.feedin
    	LEFT JOIN S_CA_Stu_ELA_C el ON s.dcid = el.studentsdcid AND el.elastatus = 'EL'
            AND el.elastatusstartdate <= :AsOfDate AND (el.elastatusenddate > :AsOfDate OR el.elastatusenddate IS NULL)
    	LEFT JOIN (
            SELECT DISTINCT 'Yes' AS sed, studentsdcid, schoolid
            FROM S_CA_Stu_CALPADSPrograms_C
            WHERE programcode IN ('181','182') AND startdate <= :AsOfDate AND (enddate > :AsOfDate OR enddate IS NULL)
    	) frpm ON s.dcid = frpm.studentsdcid AND s.schoolid = frpm.schoolid
	WHERE (:ExcludeSites IS NULL OR s.schoolid NOT IN (
	SELECT TRIM(REGEXP_SUBSTR(:ExcludeSites, '[^,]+', 1, LEVEL))
	FROM dual
	CONNECT BY REGEXP_SUBSTR(:ExcludeSites, '[^,]+', 1, LEVEL) IS NOT NULL
	))
    ]]></sql>


Security Notes
1.	To protect your automation server and/or SIS database server, work with your Network Engineering team to block all incoming and outgoing connections except for those you are going to use for ICAN and other vendors.
2.	Follow PowerSchool LLC’s cybersecurity best practices guide and always change default service account passwords to strong password strings.  Use a password manager to store these passwords, and grant access to view them on the need-to-know basis.
3.	Set up the strictest cybersecurity possible on the server in question to only communicate on necessary ports with necessary hosts/IP.  Rule of least necessary permissions.
4.	Limit the number of non-native tools on the server.  For example, Powershell is native to Windows, however, WinSCP is a 3rd party application.  Install applications and distribution libraries only from verified sources.
5.	Do not grant local machine full admin access to anything and anyone who doesn’t need it to perform assigned tasks.  The automation server should have a dedicated account protected with MFA, used only by designated staff.
