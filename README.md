# cTAKES/YTEX installation

cTAKES is an NLP tools for extracting clinical information from clinical notes in electronic medical records. YTEX is an extension of cTAKES to store extracted data from cTAKES in a relational database such as Oracle, MySQL or MsSQL. I had a couple of problems installing cTAKES/YTEX on Windows. This instruction helps me tacking what problems I ran into and how I made it work successfully. 

If installing this NLP tool first time, I would recommed you to read the original instruction on [cTAKES website](https://cwiki.apache.org/confluence/display/CTAKES/cTAKES+4.0+User+Install+Guide).



# Donwload cTAKES and UMLS reousrces

Make sure you have JAVA installed on your machine before reading this instruction.

Download [cTAKES webiste](http://ctakes.apache.org/downloads.cgi)
* User Installation: [Windows - apache-ctakes-4.0.0-bin.zip](http://apache.claz.org//ctakes/ctakes-4.0.0/apache-ctakes-4.0.0-bin.zip)
* UMLS Dictionary: [All Versions - ctakes-resources-4.0-bin.zip](http://sourceforge.net/projects/ctakesresources/files/ctakes-resources-4.0-bin.zip/download)
* Source code: [Windows - apache-ctaeks-4.0.0-src.zip](http://apache.claz.org//ctakes/ctakes-4.0.0/apache-ctakes-4.0.0-src.zip) (If you are a developer, download the source code and I will describe things I developed below)

Once downladed files, create a new folder say `D:\cTAKES` and decompress all zip files. Three folders are created - `D:\cTAKES\apache-ctakes-4.0.0`, `D:\cTAKES\apache-ctakes-4.0.0-src`, `D:\cTAKES\resources`.

You then move the "resources" folder to "apache-ctakes-4.0.0" folder. Becuase "resources" folder exists under the apache-ctakes-4.0.0, you see a popup saying that "Do you want to replace it?". Don't worry. You can overwrite files. The UMLS resource contains UMLS2011ab, rxnrom, orange_book, etc. that allow us to retreive more comprehensive results from cTAKES.


cTAKES extract clinical terminology based on UMLS dictionary. If you want to use the latest UMLS, check Update UMLS section below. I recommend to upgrade the latest UMLS.



# Set Home Enviornment

Let's set a cTAKES Home Directory on Windows environment. I typically use short cut `Windows button` + `Pause` to open System in Control pannel, or you can follow Control Panel > System and Security > System. Then you click "Advanced system settings" -> "Environment Variables". Add cTAKES Home Directory.

example)  
Variable Name: `CTAKES_HOME`  
Variable Value: `D:\cTAKES\apache-ctakes-4.0.0`  



# Quick Check
You are done installing the cTAKES. Here is a simple test you can do whether cTAKES is working or not.

Run `D:\cTAKES\apache-ctakes-4.0.0\bin\runctakesCVD.bat` as Administraotr 
* CAS Visual Debugger (CVD) will popup. Then click "Run" > "Load AE" in the menu. You need to load an analysis engine first. 
* Select "AggregatePlaintextProcessor.xml" under `D:\cTAKES\apache-ctakes-4.0.0\desc\ctakes-clinical-pipeline\desc\analysis_engine`. 
* Copy below a text blob, and paste it to the CVD Text field.

> Dr. Nutritious Medical Nutrition Therapy for Hyperlipidemia Referral from: Julie Tester, RD, LD, CNSD Phone contact: (555) 555-1212 Height: 144 cm Current Weight: 45 kg Date of current weight: 02-29-2001 Admit Weight: 53 kg BMI: 18 kg/m2 Diet: General Daily Calorie needs (kcals): 1500 calories, assessed as HB + 20% for activity. Daily Protein needs: 40 grams, assessed as 1.0 g/kg. Pt has been on a 3-day calorie count and has had an average intake of 1100 calories. She was instructed to drink 2-3 cans of liquid supplement to help promote weight gain. She agrees with the plan and has my number for further assessment. May want a Resting Metabolic Rate as well. She takes an aspirin a day for knee pain.

* Then click "Run" > "Run AggregatePlaintextProcessor" in the menu. This is disabled if you haven't loaded AE module.

You should be able to parse the text blob using cTAKES and see results on the Analysis Results. CVD is a good way to check whether cTAKES is working properly or not. You can also see tokenized outputs. 

<!-- TODO: How to use UMLS dataset and getting its permission.-->


# YTEX Installation

YTEX is now integrated into cTAKES 4.0.0. However, installing YTEX is not simple. I ran into many issues and errors while setting up YTEX in MySQL and MsSQL. Here I am keeping how I resolved the issues.

If you are new to YTEX, please check [YTEX intallation guide](https://cwiki.apache.org/confluence/display/CTAKES/YTEX+Installation) website. It is pretty good and gave me good insights. If you want to learn more, check YTEX documents [cTAKES 4.0 Component Use Guide](https://cwiki.apache.org/confluence/display/CTAKES/cTAKES+4.0+Component+Use+Guide). 


To setup cTAKES 4.0.0 + YTEX, I need to run a `build-setup.xml` file in `D:\cTAKES\apache-ctakes-4.0.0\bin\ctakes-ytex\scripts`. 

You can just run the script under the script folder, before setting any database connections.

```
cd %CTAKES_HOME%\bin\ctakes-ytex\scripts 
..\..\ant.bat -f build-setup.xml all > setup.out 
```

You now open the setup.out file to check error messages. My error messages show that `ytex.properties` file is not found. This file is where you configure database connection.

```
D:\cTAKES\apache-ctakes-4.0.0\bin\ctakes-ytex\scripts>java -cp D:\cTAKES\apache-ctakes-4.0.0\lib\ant-1.9.2.jar;D:\cTAKES\apache-ctakes-4.0.0\lib\ant-launcher-1.9.2.jar;D:\cTAKES\apache-ctakes-4.0.0\lib\ant-contrib-1.0b3.jar org.apache.tools.ant.Main -f build-setup.xml all       
Buildfile: D:\cTAKES\apache-ctakes-4.0.0\bin\ctakes-ytex\scripts\build-setup.xml

dbping:

dbping:
     [java] java.io.FileNotFoundException: D:\cTAKES\apache-ctakes-4.0.0\resources\org\apache\ctakes\ytex\ytex.properties (The system cannot find the path specified)DBPing: Connection to db failed - please check your settings and try again
     [java] 
     [java] 	at java.io.FileInputStream.open0(Native Method)
     [java] 	at java.io.FileInputStream.open(Unknown Source)
     [java] 	at java.io.FileInputStream.<init>(Unknown Source)
     [java] 	at java.io.FileInputStream.<init>(Unknown Source)
     [java] 	at org.apache.ctakes.ytex.tools.DBPing.main(DBPing.java:38)
```

You proabaly cannot find the ytex folder from the intallation package. You can, however, find it from the `source code` (third zipped file) or you can find it from `ctakes-ytex-res-4.0.0.jar` under the `lib` folder. 


Here is how you do if you installed the source code. 
* copy `D:\cTAKES\apache-ctakes-4.0.0-src\ctakes-ytex-res\src\main\resources\org\apache\ctakes\ytex` folder 
* paste it to `D:\cTAKES\apache-ctakes-4.0.0\resources\org\apache\ctakes`
* rename `ytex.properties.mysql.example` to `ytex.properties`

Here is how you can copy `ytex.properties` file from the ctakes resource jar. 
* cd `%CTAKES_HOME%\resources`
* mkdir `org\apache\ctakes\ytex`
* jar xf `..\lib\ctakes-ytex-res-*.jar` `org/apache/ctakes/ytex/ytex.properties.mysql.example`
* copy `org\apache\ctakes\ytex\ytex.properties.mysql.example` `org\apache\ctakes\ytex\ytex.properties`

If you are having a trouble of "jar" command, you can google to how to add jar to Windows enviornment. The executable jar is inside the jdk bin folder. If you were not able to extract the db configuration file, you can unzip ctakes-ytex-res-4.0.0.jar file in lib, and copy ytex folder from the unzipped lib to %CTAKES_HOME%\resources\org\apache\ctakes\ytex.

You can choose different config files such as Oracle, MsSQL or MySQL depending on your database. 


# MySQL

This is a summary of what I did to set up MySQL database and followed cTAKES/YTEX instruction to install YTEX database. I also included how I fixed issues as installing YTEX.

#### Create User Account

According to the YTEX installation guide, you need to create an YTEX account.
```
CREATE DATABASE ytex CHARACTER SET utf8;
CREATE USER 'ytex'@'localhost' IDENTIFIED BY 'ytex';
GRANT ALL PRIVILEGES ON ytex.* TO 'ytex'@'localhost';
FLUSH PRIVILEGES;
```

If you are uinsg later MySQL which requires caching_sha2_password, add WITH mysql_native_password when you create an account.
```
CREATE USER 'ytex'@'localhost' IDENTIFIED WITH mysql_native_password BY 'ytex';
```

#### Setup YTEX db properties 

If you use a different passowrd, you need to open the `ytex.properties` file and change db.passowrd. 

Again, make sure your ytex.properties file is stored in `%CTAKES_HOME%\resources\org\apache\ctakes\ytex\ytex.properties`. You also make sure db name, user id/pw, schema information are correct. 


# Intall YTEX Database 

Let's run the script again after setting db id/password.

```
cd %CTAKES_HOME%\bin\ctakes-ytex\scripts 
..\..\ant.bat -f build-setup.xml all > setup.out 
```

<!-- 
This `ant` build command is to install tables in the ytex database, and also add necessary data. If you don't have UMLS set up in your local database, this will takes a couple of minutes. If you have UMLS installed, a couple of hours to install necessary data.  

I ran this command this `ant` build script and as erroring out I noted down the issue and the way to solve it. You can skip some of issues if you don't see it. Just search a couple of key words in your error messages. You may able to find the solution. 
-->

In my case, I still won't be able to install YTEX because `dbping` error throws an error.

```
D:\cTAKES\apache-ctakes-4.0.0\bin\ctakes-ytex\scripts>java -cp D:\cTAKES\apache-ctakes-4.0.0\lib\ant-1.9.2.jar;D:\cTAKES\apache-ctakes-4.0.0\lib\ant-launcher-1.9.2.jar;D:\cTAKES\apache-ctakes-4.0.0\lib\ant-contrib-1.0b3.jar org.apache.tools.ant.Main -f build-setup.xml all       
Buildfile: D:\cTAKES\apache-ctakes-4.0.0\bin\ctakes-ytex\scripts\build-setup.xml

dbping:

dbping:
     [java] java.lang.ClassNotFoundException: com.mysql.jdbc.Driver
     [java] 	at java.net.URLClassLoader.findClass(Unknown Source)
     [java] DBPing: Connection to db failed - please check your settings and try again
     [java] 	at java.lang.ClassLoader.loadClass(Unknown Source)
     [java] 	at sun.misc.Launcher$AppClassLoader.loadClass(Unknown Source)
     [java] 	at java.lang.ClassLoader.loadClass(Unknown Source)
     [java] 	at java.lang.Class.forName0(Native Method)
     [java] 	at java.lang.Class.forName(Unknown Source)
     [java] 	at org.apache.ctakes.ytex.tools.DBPing.ping(DBPing.java:66)
     [java] 	at org.apache.ctakes.ytex.tools.DBPing.main(DBPing.java:41)
```


## Issue 1 - DBPing
It is throwing errors. First error I had was "DBPing Connection to db failed - please check your settings and try again". This is because of no MySQL DB connector lib. 

You need to download database connector library, and copy the file to the library folder. 

#### Solution
* Download database base connection library. 
*   If you are using MySQL, download [mysql-connector-java-5.1.45.jar](http://central.maven.org/maven2/mysql/mysql-connector-java/5.1.45/mysql-connector-java-5.1.45.jar)
*   If you are using MsSQL, download [sqljdbc](https://www.microsoft.com/en-us/download/details.aspx?id=54671), then unzipp and copy sqljdbc42.jar
* copy the library to `%CTAKES_HOME%\lib`

After adding this library in the `lib` folder, run the build-setup command again and monitor command messages. 


If you check `setup.out` file, it might be succecfully installed. You will the find `BUILD SUCCESSFUL` at the end of the file.

Here is an example after successfully installed.

<details><summary>Click Here to See Detail</summary>
<p>
	
```
D:\cTAKES\apache-ctakes-4.0.0\bin\ctakes-ytex\scripts>java -cp D:\cTAKES\apache-ctakes-4.0.0\lib\ant-1.9.2.jar;D:\cTAKES\apache-ctakes-4.0.0\lib\ant-launcher-1.9.2.jar;D:\cTAKES\apache-ctakes-4.0.0\lib\ant-contrib-1.0b3.jar org.apache.tools.ant.Main -f build-setup.xml all       
Buildfile: D:\cTAKES\apache-ctakes-4.0.0\bin\ctakes-ytex\scripts\build-setup.xml

dbping:

dbping:
     [java] Sat Apr 06 00:02:22 EDT 2019 WARN: Establishing SSL connection without server's identity verification is not recommended. According to MySQL 5.5.45+, 5.6.26+ and 5.7.6+ requirements SSL connection must be established by default if explicit option isn't set. For compliance with existing applications not using SSL the verifyServerCertificate property is set to 'false'. You need either to explicitly disable SSL by setting useSSL=false, or set useSSL=true and provide truststore for server certificate verification.
     [java] DBPing: connection succeeded

templateToConfig.init:

templateToConfig.extractTemplates:
     [echo] unpacking ytex templates from D:\cTAKES\apache-ctakes-4.0.0\lib\ctakes-ytex-res-4.0.0.jar to D:\cTAKES\apache-ctakes-4.0.0/resources
    [unzip] Expanding: D:\cTAKES\apache-ctakes-4.0.0\lib\ctakes-ytex-res-4.0.0.jar into D:\cTAKES\apache-ctakes-4.0.0\resources

templateToConfig.updateConfig:
     [echo] config.source.dir = D:\cTAKES\apache-ctakes-4.0.0/resources
     [echo] config.generated.dir = D:\cTAKES\apache-ctakes-4.0.0/resources
     [copy] Copying 6 files to D:\cTAKES\apache-ctakes-4.0.0\resources

templateToConfig:

setupDatabase:

init:

umls.check.mrconso:
     [echo] umls.schema ytex
     [echo] umls.catalog 
      [sql] Sat Apr 06 00:02:23 EDT 2019 WARN: Establishing SSL connection without server's identity verification is not recommended. According to MySQL 5.5.45+, 5.6.26+ and 5.7.6+ requirements SSL connection must be established by default if explicit option isn't set. For compliance with existing applications not using SSL the verifyServerCertificate property is set to 'false'. You need either to explicitly disable SSL by setting useSSL=false, or set useSSL=true and provide truststore for server certificate verification.
      [sql] Executing commands
      [sql] 1 of 1 SQL statements executed successfully
     [echo] mrconso.out = 
   [delete] Deleting: C:\Users\thejoo\AppData\Local\Temp\mrconso-check.txt
     [echo] mrconso.exists = ${mrconso.exists}

umls.all:
     [echo] mrconso.exists = ${mrconso.exists}

init:

umls.drop.view:
     [echo] drop v_snomed_fword_lookup table

jdbc.sqlcmd:
     [echo] db.schemaytex
     [echo] umls.schema ytex
     [echo] umls.catalog 
     [echo] execute ./mysql/./umls/drop_view.sql
     [copy] Copying 1 file to C:\Users\thejoo\AppData\Local\Temp
     [echo] executing C:\Users\thejoo\AppData\Local\Temp\ytex304437838.sql
      [sql] Sat Apr 06 00:02:23 EDT 2019 WARN: Establishing SSL connection without server's identity verification is not recommended. According to MySQL 5.5.45+, 5.6.26+ and 5.7.6+ requirements SSL connection must be established by default if explicit option isn't set. For compliance with existing applications not using SSL the verifyServerCertificate property is set to 'false'. You need either to explicitly disable SSL by setting useSSL=false, or set useSSL=true and provide truststore for server certificate verification.
      [sql] Executing resource: C:\Users\thejoo\AppData\Local\Temp\ytex304437838.sql
      [sql] 3 of 3 SQL statements executed successfully
   [delete] Deleting: C:\Users\thejoo\AppData\Local\Temp\ytex304437838.sql
   [delete] Deleting: C:\Users\thejoo\AppData\Local\Temp\ytex465780409use.sql

init.conn:
     [copy] Copying 1 file to C:\Users\thejoo\AppData\Local\Temp

umls.create.view:
     [echo] create the v_snomed_fword_lookup table

jdbc.sqlcmd:
     [echo] db.schemaytex
     [echo] umls.schema ytex
     [echo] umls.catalog 
     [echo] execute ./mysql/./umls/create_view.sql
     [copy] Copying 1 file to C:\Users\thejoo\AppData\Local\Temp
     [echo] executing C:\Users\thejoo\AppData\Local\Temp\ytex796634785.sql
      [sql] Sat Apr 06 00:02:23 EDT 2019 WARN: Establishing SSL connection without server's identity verification is not recommended. According to MySQL 5.5.45+, 5.6.26+ and 5.7.6+ requirements SSL connection must be established by default if explicit option isn't set. For compliance with existing applications not using SSL the verifyServerCertificate property is set to 'false'. You need either to explicitly disable SSL by setting useSSL=false, or set useSSL=true and provide truststore for server certificate verification.
      [sql] Executing resource: C:\Users\thejoo\AppData\Local\Temp\ytex796634785.sql
      [sql] 2 of 2 SQL statements executed successfully
   [delete] Deleting: C:\Users\thejoo\AppData\Local\Temp\ytex796634785.sql
   [delete] Deleting: C:\Users\thejoo\AppData\Local\Temp\ytex1041651537use.sql

umls.import.view:
     [echo] import v_snomed_fword_lookup from D:\cTAKES\apache-ctakes-4.0.0\bin\ctakes-ytex\scripts\data/umls/v_snomed_fword_lookup.txt.  Be patient, this can take a while.

jdl:
     [echo] jdl.format D:\cTAKES\apache-ctakes-4.0.0\bin\ctakes-ytex\scripts\data/umls/v_snomed_fword_lookup.xml
     [echo] jdl.data D:\cTAKES\apache-ctakes-4.0.0\bin\ctakes-ytex\scripts\data/umls/v_snomed_fword_lookup.txt
     [echo] umls.prefix ytex.
     [copy] Copying 1 file to C:\Users\thejoo\AppData\Local\Temp
     [java] log4j: reset attribute= "false".
     [java] log4j: Threshold ="null".
     [java] log4j: Retreiving an instance of org.apache.log4j.Logger.
     [java] log4j: Setting [ProgressAppender] additivity to [false].
     [java] log4j: Level value for ProgressAppender is  [INFO].
     [java] log4j: ProgressAppender level set to INFO
     [java] log4j: Class name: [org.apache.log4j.ConsoleAppender]
     [java] log4j: Parsing layout of class: "org.apache.log4j.PatternLayout"
     [java] log4j: Setting property [conversionPattern] to [%m].
     [java] log4j: Adding appender named [noEolAppender] to category [ProgressAppender].
     [java] log4j: Retreiving an instance of org.apache.log4j.Logger.
     [java] log4j: Setting [ProgressDone] additivity to [false].
     [java] log4j: Level value for ProgressDone is  [INFO].
     [java] log4j: ProgressDone level set to INFO
     [java] log4j: Class name: [org.apache.log4j.ConsoleAppender]
     [java] log4j: Parsing layout of class: "org.apache.log4j.PatternLayout"
     [java] log4j: Setting property [conversionPattern] to [%m%n].
     [java] log4j: Adding appender named [eolAppender] to category [ProgressDone].
     [java] log4j: Level value for root is  [INFO].
     [java] log4j: root level set to INFO
     [java] log4j: Class name: [org.apache.log4j.ConsoleAppender]
     [java] log4j: Parsing layout of class: "org.apache.log4j.PatternLayout"
     [java] log4j: Setting property [conversionPattern] to [%d{dd MMM yyyy HH:mm:ss} %5p %c{1} - %m%n].
     [java] log4j: Adding appender named [consoleAppender] to category [root].
     [java] 06 Apr 2019 00:02:24  INFO CsvLoader - delimiter 9 encapsulator 65534
     [java] 06 Apr 2019 00:02:24  INFO CsvLoader - insert into ytex.v_snomed_fword_lookup (cui,tui,fword,fstem,tok_str,stem_str) values (?,?,?,?,?,?)
     [java] Sat Apr 06 00:02:25 EDT 2019 WARN: Establishing SSL connection without server's identity verification is not recommended. According to MySQL 5.5.45+, 5.6.26+ and 5.7.6+ requirements SSL connection must be established by default if explicit option isn't set. For compliance with existing applications not using SSL the verifyServerCertificate property is set to 'false'. You need either to explicitly disable SSL by setting useSSL=false, or set useSSL=true and provide truststore for server certificate verification.
     [java] 06 Apr 2019 00:02:28  INFO CsvLoader - inserted 2733 rows total

umls.index.view:
     [echo] index the v_snomed_fword_lookup table

jdbc.sqlcmd:
     [echo] db.schemaytex
     [echo] umls.schema ytex
     [echo] umls.catalog 
     [echo] execute ./mysql/./umls/index_view.sql
     [copy] Copying 1 file to C:\Users\thejoo\AppData\Local\Temp
     [echo] executing C:\Users\thejoo\AppData\Local\Temp\ytex1093546108.sql
      [sql] Sat Apr 06 00:02:29 EDT 2019 WARN: Establishing SSL connection without server's identity verification is not recommended. According to MySQL 5.5.45+, 5.6.26+ and 5.7.6+ requirements SSL connection must be established by default if explicit option isn't set. For compliance with existing applications not using SSL the verifyServerCertificate property is set to 'false'. You need either to explicitly disable SSL by setting useSSL=false, or set useSSL=true and provide truststore for server certificate verification.
      [sql] Executing resource: C:\Users\thejoo\AppData\Local\Temp\ytex1093546108.sql
      [sql] 4 of 4 SQL statements executed successfully
   [delete] Deleting: C:\Users\thejoo\AppData\Local\Temp\ytex1093546108.sql
   [delete] Deleting: C:\Users\thejoo\AppData\Local\Temp\ytex833289655use.sql

umls.setup.dump:

uima.drop:

jdbc.sqlcmd:
     [echo] db.schemaytex
     [echo] umls.schema ytex
     [echo] umls.catalog 
     [echo] execute ./mysql/uima/drop_view.sql
     [copy] Copying 1 file to C:\Users\thejoo\AppData\Local\Temp
     [echo] executing C:\Users\thejoo\AppData\Local\Temp\ytex1036487328.sql
      [sql] Sat Apr 06 00:02:29 EDT 2019 WARN: Establishing SSL connection without server's identity verification is not recommended. According to MySQL 5.5.45+, 5.6.26+ and 5.7.6+ requirements SSL connection must be established by default if explicit option isn't set. For compliance with existing applications not using SSL the verifyServerCertificate property is set to 'false'. You need either to explicitly disable SSL by setting useSSL=false, or set useSSL=true and provide truststore for server certificate verification.
      [sql] Executing resource: C:\Users\thejoo\AppData\Local\Temp\ytex1036487328.sql
      [sql] 9 of 9 SQL statements executed successfully
   [delete] Deleting: C:\Users\thejoo\AppData\Local\Temp\ytex1036487328.sql
   [delete] Deleting: C:\Users\thejoo\AppData\Local\Temp\ytex330900497use.sql

jdbc.sqlcmd:
     [echo] db.schemaytex
     [echo] umls.schema ytex
     [echo] umls.catalog 
     [echo] execute ./mysql/uima/drop_document.sql
     [copy] Copying 1 file to C:\Users\thejoo\AppData\Local\Temp
     [echo] executing C:\Users\thejoo\AppData\Local\Temp\ytex1985071733.sql
      [sql] Sat Apr 06 00:02:29 EDT 2019 WARN: Establishing SSL connection without server's identity verification is not recommended. According to MySQL 5.5.45+, 5.6.26+ and 5.7.6+ requirements SSL connection must be established by default if explicit option isn't set. For compliance with existing applications not using SSL the verifyServerCertificate property is set to 'false'. You need either to explicitly disable SSL by setting useSSL=false, or set useSSL=true and provide truststore for server certificate verification.
      [sql] Executing resource: C:\Users\thejoo\AppData\Local\Temp\ytex1985071733.sql
      [sql] 29 of 29 SQL statements executed successfully
   [delete] Deleting: C:\Users\thejoo\AppData\Local\Temp\ytex1985071733.sql
   [delete] Deleting: C:\Users\thejoo\AppData\Local\Temp\ytex521907510use.sql

jdbc.sqlcmd:
     [echo] db.schemaytex
     [echo] umls.schema ytex
     [echo] umls.catalog 
     [echo] execute ./mysql/uima/drop_reference.sql
     [copy] Copying 1 file to C:\Users\thejoo\AppData\Local\Temp
     [echo] executing C:\Users\thejoo\AppData\Local\Temp\ytex1120773788.sql
      [sql] Sat Apr 06 00:02:29 EDT 2019 WARN: Establishing SSL connection without server's identity verification is not recommended. According to MySQL 5.5.45+, 5.6.26+ and 5.7.6+ requirements SSL connection must be established by default if explicit option isn't set. For compliance with existing applications not using SSL the verifyServerCertificate property is set to 'false'. You need either to explicitly disable SSL by setting useSSL=false, or set useSSL=true and provide truststore for server certificate verification.
      [sql] Executing resource: C:\Users\thejoo\AppData\Local\Temp\ytex1120773788.sql
      [sql] 7 of 7 SQL statements executed successfully
   [delete] Deleting: C:\Users\thejoo\AppData\Local\Temp\ytex1120773788.sql
   [delete] Deleting: C:\Users\thejoo\AppData\Local\Temp\ytex2072973262use.sql

uima.create:

jdbc.sqlcmd:
     [echo] db.schemaytex
     [echo] umls.schema ytex
     [echo] umls.catalog 
     [echo] execute ./mysql/uima/create_reference.sql
     [copy] Copying 1 file to C:\Users\thejoo\AppData\Local\Temp
     [echo] executing C:\Users\thejoo\AppData\Local\Temp\ytex616054423.sql
      [sql] Sat Apr 06 00:02:29 EDT 2019 WARN: Establishing SSL connection without server's identity verification is not recommended. According to MySQL 5.5.45+, 5.6.26+ and 5.7.6+ requirements SSL connection must be established by default if explicit option isn't set. For compliance with existing applications not using SSL the verifyServerCertificate property is set to 'false'. You need either to explicitly disable SSL by setting useSSL=false, or set useSSL=true and provide truststore for server certificate verification.
      [sql] Executing resource: C:\Users\thejoo\AppData\Local\Temp\ytex616054423.sql
      [sql] 10 of 10 SQL statements executed successfully
   [delete] Deleting: C:\Users\thejoo\AppData\Local\Temp\ytex616054423.sql
   [delete] Deleting: C:\Users\thejoo\AppData\Local\Temp\ytex194973019use.sql

jdbc.sqlcmd:
     [echo] db.schemaytex
     [echo] umls.schema ytex
     [echo] umls.catalog 
     [echo] execute ./mysql/uima/create_document.sql
     [copy] Copying 1 file to C:\Users\thejoo\AppData\Local\Temp
     [echo] executing C:\Users\thejoo\AppData\Local\Temp\ytex1943755009.sql
      [sql] Sat Apr 06 00:02:30 EDT 2019 WARN: Establishing SSL connection without server's identity verification is not recommended. According to MySQL 5.5.45+, 5.6.26+ and 5.7.6+ requirements SSL connection must be established by default if explicit option isn't set. For compliance with existing applications not using SSL the verifyServerCertificate property is set to 'false'. You need either to explicitly disable SSL by setting useSSL=false, or set useSSL=true and provide truststore for server certificate verification.
      [sql] Executing resource: C:\Users\thejoo\AppData\Local\Temp\ytex1943755009.sql
      [sql] 26 of 26 SQL statements executed successfully
   [delete] Deleting: C:\Users\thejoo\AppData\Local\Temp\ytex1943755009.sql
   [delete] Deleting: C:\Users\thejoo\AppData\Local\Temp\ytex1868004140use.sql

jdbc.sqlcmd:
     [echo] db.schemaytex
     [echo] umls.schema ytex
     [echo] umls.catalog 
     [echo] execute ./mysql/uima/create_view.sql
     [copy] Copying 1 file to C:\Users\thejoo\AppData\Local\Temp
     [echo] executing C:\Users\thejoo\AppData\Local\Temp\ytex601025773.sql
      [sql] Sat Apr 06 00:02:30 EDT 2019 WARN: Establishing SSL connection without server's identity verification is not recommended. According to MySQL 5.5.45+, 5.6.26+ and 5.7.6+ requirements SSL connection must be established by default if explicit option isn't set. For compliance with existing applications not using SSL the verifyServerCertificate property is set to 'false'. You need either to explicitly disable SSL by setting useSSL=false, or set useSSL=true and provide truststore for server certificate verification.
      [sql] Executing resource: C:\Users\thejoo\AppData\Local\Temp\ytex601025773.sql
      [sql] 5 of 5 SQL statements executed successfully
   [delete] Deleting: C:\Users\thejoo\AppData\Local\Temp\ytex601025773.sql
   [delete] Deleting: C:\Users\thejoo\AppData\Local\Temp\ytex1370884159use.sql

uima.create.trigger:

init:

init.conn:
     [copy] Copying 1 file to C:\Users\thejoo\AppData\Local\Temp

uima.ref.load:

jdl:
     [echo] jdl.format ref_uima_type.xml
     [echo] jdl.data ref_uima_type.txt
     [echo] umls.prefix ytex.
     [copy] Copying 1 file to C:\Users\thejoo\AppData\Local\Temp
     [java] log4j: reset attribute= "false".
     [java] log4j: Threshold ="null".
     [java] log4j: Retreiving an instance of org.apache.log4j.Logger.
     [java] log4j: Setting [ProgressAppender] additivity to [false].
     [java] log4j: Level value for ProgressAppender is  [INFO].
     [java] log4j: ProgressAppender level set to INFO
     [java] log4j: Class name: [org.apache.log4j.ConsoleAppender]
     [java] log4j: Parsing layout of class: "org.apache.log4j.PatternLayout"
     [java] log4j: Setting property [conversionPattern] to [%m].
     [java] log4j: Adding appender named [noEolAppender] to category [ProgressAppender].
     [java] log4j: Retreiving an instance of org.apache.log4j.Logger.
     [java] log4j: Setting [ProgressDone] additivity to [false].
     [java] log4j: Level value for ProgressDone is  [INFO].
     [java] log4j: ProgressDone level set to INFO
     [java] log4j: Class name: [org.apache.log4j.ConsoleAppender]
     [java] log4j: Parsing layout of class: "org.apache.log4j.PatternLayout"
     [java] log4j: Setting property [conversionPattern] to [%m%n].
     [java] log4j: Adding appender named [eolAppender] to category [ProgressDone].
     [java] log4j: Level value for root is  [INFO].
     [java] log4j: root level set to INFO
     [java] log4j: Class name: [org.apache.log4j.ConsoleAppender]
     [java] log4j: Parsing layout of class: "org.apache.log4j.PatternLayout"
     [java] log4j: Setting property [conversionPattern] to [%d{dd MMM yyyy HH:mm:ss} %5p %c{1} - %m%n].
     [java] log4j: Adding appender named [consoleAppender] to category [root].
     [java] 06 Apr 2019 00:02:31  INFO CsvLoader - delimiter 9 encapsulator 65534
     [java] 06 Apr 2019 00:02:31  INFO CsvLoader - insert into ytex.ref_uima_type (uima_type_id,uima_type_name,table_name) values (?,?,?)
     [java] Sat Apr 06 00:02:32 EDT 2019 WARN: Establishing SSL connection without server's identity verification is not recommended. According to MySQL 5.5.45+, 5.6.26+ and 5.7.6+ requirements SSL connection must be established by default if explicit option isn't set. For compliance with existing applications not using SSL the verifyServerCertificate property is set to 'false'. You need either to explicitly disable SSL by setting useSSL=false, or set useSSL=true and provide truststore for server certificate verification.
     [java] 06 Apr 2019 00:02:32  INFO CsvLoader - inserted 38 rows total

init:

init.conn:
     [copy] Copying 1 file to C:\Users\thejoo\AppData\Local\Temp

uima.examples.load:

jdl:
     [echo] jdl.format examples/fracture_demo.xml
     [echo] jdl.data examples/fracture_demo.txt
     [echo] umls.prefix ytex.
     [copy] Copying 1 file to C:\Users\thejoo\AppData\Local\Temp
     [java] log4j: reset attribute= "false".
     [java] log4j: Threshold ="null".
     [java] log4j: Retreiving an instance of org.apache.log4j.Logger.
     [java] log4j: Setting [ProgressAppender] additivity to [false].
     [java] log4j: Level value for ProgressAppender is  [INFO].
     [java] log4j: ProgressAppender level set to INFO
     [java] log4j: Class name: [org.apache.log4j.ConsoleAppender]
     [java] log4j: Parsing layout of class: "org.apache.log4j.PatternLayout"
     [java] log4j: Setting property [conversionPattern] to [%m].
     [java] log4j: Adding appender named [noEolAppender] to category [ProgressAppender].
     [java] log4j: Retreiving an instance of org.apache.log4j.Logger.
     [java] log4j: Setting [ProgressDone] additivity to [false].
     [java] log4j: Level value for ProgressDone is  [INFO].
     [java] log4j: ProgressDone level set to INFO
     [java] log4j: Class name: [org.apache.log4j.ConsoleAppender]
     [java] log4j: Parsing layout of class: "org.apache.log4j.PatternLayout"
     [java] log4j: Setting property [conversionPattern] to [%m%n].
     [java] log4j: Adding appender named [eolAppender] to category [ProgressDone].
     [java] log4j: Level value for root is  [INFO].
     [java] log4j: root level set to INFO
     [java] log4j: Class name: [org.apache.log4j.ConsoleAppender]
     [java] log4j: Parsing layout of class: "org.apache.log4j.PatternLayout"
     [java] log4j: Setting property [conversionPattern] to [%d{dd MMM yyyy HH:mm:ss} %5p %c{1} - %m%n].
     [java] log4j: Adding appender named [consoleAppender] to category [root].
     [java] 06 Apr 2019 00:02:33  INFO CsvLoader - delimiter 9 encapsulator 65534
     [java] 06 Apr 2019 00:02:33  INFO CsvLoader - insert into ytex.fracture_demo (site_id,note_text,fracture,note_set) values (?,?,?,?)
     [java] Sat Apr 06 00:02:34 EDT 2019 WARN: Establishing SSL connection without server's identity verification is not recommended. According to MySQL 5.5.45+, 5.6.26+ and 5.7.6+ requirements SSL connection must be established by default if explicit option isn't set. For compliance with existing applications not using SSL the verifyServerCertificate property is set to 'false'. You need either to explicitly disable SSL by setting useSSL=false, or set useSSL=true and provide truststore for server certificate verification.
     [java] 06 Apr 2019 00:02:34  INFO CsvLoader - inserted 2 rows
     [java] 06 Apr 2019 00:02:34  INFO CsvLoader - inserted 2 rows
     [java] 06 Apr 2019 00:02:34  INFO CsvLoader - inserted 2 rows
     [java] 06 Apr 2019 00:02:34  INFO CsvLoader - inserted 2 rows
     [java] 06 Apr 2019 00:02:34  INFO CsvLoader - inserted 2 rows
     [java] 06 Apr 2019 00:02:34  INFO CsvLoader - inserted 2 rows
     [java] 06 Apr 2019 00:02:34  INFO CsvLoader - inserted 2 rows
     [java] 06 Apr 2019 00:02:34  INFO CsvLoader - inserted 2 rows
     [java] 06 Apr 2019 00:02:34  INFO CsvLoader - inserted 2 rows
     [java] 06 Apr 2019 00:02:34  INFO CsvLoader - inserted 2 rows
     [java] 06 Apr 2019 00:02:34  INFO CsvLoader - inserted 2 rows
     [java] 06 Apr 2019 00:02:34  INFO CsvLoader - inserted 2 rows
     [java] 06 Apr 2019 00:02:34  INFO CsvLoader - inserted 2 rows
     [java] 06 Apr 2019 00:02:34  INFO CsvLoader - inserted 2 rows
     [java] 06 Apr 2019 00:02:34  INFO CsvLoader - inserted 2 rows
     [java] 06 Apr 2019 00:02:34  INFO CsvLoader - inserted 31 rows total

jdbc.sqlcmd:
     [echo] db.schemaytex
     [echo] umls.schema ytex
     [echo] umls.catalog 
     [echo] execute ./mysql/uima/fracture_demo.sql
     [copy] Copying 1 file to C:\Users\thejoo\AppData\Local\Temp
     [echo] executing C:\Users\thejoo\AppData\Local\Temp\ytex1390835486.sql
      [sql] Sat Apr 06 00:02:35 EDT 2019 WARN: Establishing SSL connection without server's identity verification is not recommended. According to MySQL 5.5.45+, 5.6.26+ and 5.7.6+ requirements SSL connection must be established by default if explicit option isn't set. For compliance with existing applications not using SSL the verifyServerCertificate property is set to 'false'. You need either to explicitly disable SSL by setting useSSL=false, or set useSSL=true and provide truststore for server certificate verification.
      [sql] Executing resource: C:\Users\thejoo\AppData\Local\Temp\ytex1390835486.sql
      [sql] 2 of 2 SQL statements executed successfully
   [delete] Deleting: C:\Users\thejoo\AppData\Local\Temp\ytex1390835486.sql
   [delete] Deleting: C:\Users\thejoo\AppData\Local\Temp\ytex1836334136use.sql

init:

init.conn:
     [copy] Copying 1 file to C:\Users\thejoo\AppData\Local\Temp

uima.stopword.load:

jdl:
     [echo] jdl.format stopword.xml
     [echo] jdl.data stopword.txt
     [echo] umls.prefix ytex.
     [copy] Copying 1 file to C:\Users\thejoo\AppData\Local\Temp
     [java] log4j: reset attribute= "false".
     [java] log4j: Threshold ="null".
     [java] log4j: Retreiving an instance of org.apache.log4j.Logger.
     [java] log4j: Setting [ProgressAppender] additivity to [false].
     [java] log4j: Level value for ProgressAppender is  [INFO].
     [java] log4j: ProgressAppender level set to INFO
     [java] log4j: Class name: [org.apache.log4j.ConsoleAppender]
     [java] log4j: Parsing layout of class: "org.apache.log4j.PatternLayout"
     [java] log4j: Setting property [conversionPattern] to [%m].
     [java] log4j: Adding appender named [noEolAppender] to category [ProgressAppender].
     [java] log4j: Retreiving an instance of org.apache.log4j.Logger.
     [java] log4j: Setting [ProgressDone] additivity to [false].
     [java] log4j: Level value for ProgressDone is  [INFO].
     [java] log4j: ProgressDone level set to INFO
     [java] log4j: Class name: [org.apache.log4j.ConsoleAppender]
     [java] log4j: Parsing layout of class: "org.apache.log4j.PatternLayout"
     [java] log4j: Setting property [conversionPattern] to [%m%n].
     [java] log4j: Adding appender named [eolAppender] to category [ProgressDone].
     [java] log4j: Level value for root is  [INFO].
     [java] log4j: root level set to INFO
     [java] log4j: Class name: [org.apache.log4j.ConsoleAppender]
     [java] log4j: Parsing layout of class: "org.apache.log4j.PatternLayout"
     [java] log4j: Setting property [conversionPattern] to [%d{dd MMM yyyy HH:mm:ss} %5p %c{1} - %m%n].
     [java] log4j: Adding appender named [consoleAppender] to category [root].
     [java] 06 Apr 2019 00:02:36  INFO CsvLoader - delimiter 44 encapsulator 65534
     [java] 06 Apr 2019 00:02:36  INFO CsvLoader - insert into ytex.ref_stopword (stopword) values (?)
     [java] Sat Apr 06 00:02:36 EDT 2019 WARN: Establishing SSL connection without server's identity verification is not recommended. According to MySQL 5.5.45+, 5.6.26+ and 5.7.6+ requirements SSL connection must be established by default if explicit option isn't set. For compliance with existing applications not using SSL the verifyServerCertificate property is set to 'false'. You need either to explicitly disable SSL by setting useSSL=false, or set useSSL=true and provide truststore for server certificate verification.
     [java] 06 Apr 2019 00:02:36  INFO CsvLoader - inserted 360 rows total

uima.all:

kernel.drop:

jdbc.sqlcmd:
     [echo] db.schemaytex
     [echo] umls.schema ytex
     [echo] umls.catalog 
     [echo] execute ./mysql/kernel/drop_tables.sql
     [copy] Copying 1 file to C:\Users\thejoo\AppData\Local\Temp
     [echo] executing C:\Users\thejoo\AppData\Local\Temp\ytex1897747047.sql
      [sql] Sat Apr 06 00:02:37 EDT 2019 WARN: Establishing SSL connection without server's identity verification is not recommended. According to MySQL 5.5.45+, 5.6.26+ and 5.7.6+ requirements SSL connection must be established by default if explicit option isn't set. For compliance with existing applications not using SSL the verifyServerCertificate property is set to 'false'. You need either to explicitly disable SSL by setting useSSL=false, or set useSSL=true and provide truststore for server certificate verification.
      [sql] Executing resource: C:\Users\thejoo\AppData\Local\Temp\ytex1897747047.sql
      [sql] 22 of 22 SQL statements executed successfully
   [delete] Deleting: C:\Users\thejoo\AppData\Local\Temp\ytex1897747047.sql
   [delete] Deleting: C:\Users\thejoo\AppData\Local\Temp\ytex894257681use.sql

kernel.create:

jdbc.sqlcmd:
     [echo] db.schemaytex
     [echo] umls.schema ytex
     [echo] umls.catalog 
     [echo] execute ./mysql/kernel/create_tables.sql
     [copy] Copying 1 file to C:\Users\thejoo\AppData\Local\Temp
     [echo] executing C:\Users\thejoo\AppData\Local\Temp\ytex701768143.sql
      [sql] Sat Apr 06 00:02:37 EDT 2019 WARN: Establishing SSL connection without server's identity verification is not recommended. According to MySQL 5.5.45+, 5.6.26+ and 5.7.6+ requirements SSL connection must be established by default if explicit option isn't set. For compliance with existing applications not using SSL the verifyServerCertificate property is set to 'false'. You need either to explicitly disable SSL by setting useSSL=false, or set useSSL=true and provide truststore for server certificate verification.
      [sql] Executing resource: C:\Users\thejoo\AppData\Local\Temp\ytex701768143.sql
      [sql] 23 of 23 SQL statements executed successfully
   [delete] Deleting: C:\Users\thejoo\AppData\Local\Temp\ytex701768143.sql
   [delete] Deleting: C:\Users\thejoo\AppData\Local\Temp\ytex588177037use.sql

kernel.all:

all:

all:

BUILD SUCCESSFUL
Total time: 16 seconds
```

</p>
</details>


I was able to install YTEX successfully. However, YTEX was not using UMLS schema. It was disabled by default. It only uses very limited clinical words (2733 in `v_snomed_fword_lookup` table).  I recommend to install the latest UMLS and configure UMLS schema in `ytex.properties`. 


## Upgrade UMLS to the latest version

You first need to download and install UMLS in the database. You can easily find tutorials how to install UMLS on your local machine. The UMLS is available [here](https://uts.nlm.nih.gov/home.html). 

I created a `UMLS2018` database in MySQL, then grant `ytex` access to UMLS2018 database.

```
GRANT ALL PRIVILEGES ON UMLS2018.* TO 'ytex'@'localhost';
FLUSH PRIVILEGES;
```

Then I uncommented `umls.schema` and change the value to `UMLS2018` in the `ytex.properties` file. You then run the `build-setup.xml` file again.


If you run into other error messages, you then read futher below. 


## Issue 2 - Rank in MySQL version 8 

The MySQL version 8 or later doesn't like to have a column name using a reserved word. YTEX db scripts use rank in the column name and generated following error message.
```
Failed to execute:   create table feature_rank ( feature_rank_id int auto_increment not 
```

You can simply follow this step and replace the table.
1. open a file - `bin\ctakes-ytex\scripts\data\mysql\kernel\create_tables.sql` 
2. find sql scripts that use rank as a column name (feature_rank and hotspot_sentence tables)
3. replace column name rank to \`rank\`  (rank seems a reserved name in MySQL)

Here is the table that I changed.
<pre>
create table feature_rank (
  feature_rank_id int auto_increment not null primary key,
  feature_eval_id int not null comment 'fk feature_eval',
  feature_name varchar(50) not null comment 'name of feature',
  evaluation double not null default 0 comment 'measurement of feature worth',
  `rank` int not null default 0 comment 'rank among all features',
  unique index nk_feature_name(feature_eval_id, feature_name),
  index ix_feature_rank(feature_eval_id, `rank`),
  index ix_feature_evaluation(feature_eval_id, evaluation),
  index fk_feature_eval(feature_eval_id)
) engine=myisam comment 'evaluation of a feature in a corpus'

...

create table hotspot_sentence (
    hotspot_sentence_id int auto_increment not null primary key,
    hotspot_instance_id int not null comment 'fk hotspot_instance',
    anno_base_id int not null comment 'fk anno_sentence',
    evaluation double not null default 0 comment 'max eval from hotspot',
    `rank` int not null default 0 comment 'min rank from hotspot',
    unique index NK_hotspot_sentence (hotspot_instance_id, anno_base_id),
    index FK_hotspot_instance_id (hotspot_instance_id),
	index FK_anno_base_id (anno_base_id),
    INDEX IX_evaluation (hotspot_instance_id, evaluation),
    INDEX IX_rank (hotspot_instance_id, `rank`)
) engine = myisam comment 'sentences that contain hotspots at specified threshold';
</pre>


## Issue 3 - HibernateException

After re-run the ant script, I passed the DBPing error, but build was still failed due to NoClassDefFoundError of HibernateException.

```
'txAdvice' defined in class path resource [org/apache/ctakes/ytex/beans-datasource.xml]: Cannot resolve reference to bean 'transactionManager' while setting bean property 'transactionManager'; nested exception is org.springframework.beans.factory.BeanCreationException: Error creating bean with name 'transactionManager' defined in class path resource [org/apache/ctakes/ytex/beans-datasource.xml]: Instantiation of bean failed; nested exception is java.lang.NoClassDefFoundError: org/hibernate/HibernateException

...

[java] Caused by: java.lang.ClassNotFoundException: org.hibernate.HibernateException
[java] 	at java.net.URLClassLoader.findClass(Unknown Source)
[java] 	at java.lang.ClassLoader.loadClass(Unknown Source)
[java] 	at sun.misc.Launcher$AppClassLoader.loadClass(Unknown Source)
[java] 	at java.lang.ClassLoader.loadClass(Unknown Source)
```

As it says, there is no class available in the package. You can download the hiberante libraray and copy it to library folder.

#### Solution
* download [hibernate-core-4.2.6.Final](https://mvnrepository.com/artifact/org.hibernate/hibernate-core/4.2.6.Final)
* copy the file to `%CTAKES_HOME$/lib`


## Issue 4 - MetadataProvider

Another NoClassDefFoundError issue. Keyword is MetadataProvider in hiberante annotation lib.

After re-run the ant script, I passed HibernateException, but build was still failed and errors show like below.

```
   [java] Caused by: java.lang.NoClassDefFoundError: org/hibernate/annotations/common/reflection/MetadataProvider
     [java] 	at org.springframework.orm.hibernate4.LocalSessionFactoryBean.afterPropertiesSet(LocalSessionFactoryBean.java:277)
     [java] 	at 
```     

To resovle this issue, you need to download a hibernate-commons-annotations-4.0.2.Final lib. Link:


#### Solution
* download [hibernate-commons-annotations-4.0.2.Final](https://mvnrepository.com/artifact/org.hibernate.common/hibernate-commons-annotations/4.0.2.Final)
* copy the file to `%CTAKES_HOME$/lib`


## Issue 5 - Jboss Logging
ClassNotFoundException issue due to Jboss Logging. Error messages would look like this.

```
...
[java] 	... 54 more
[java] Caused by: java.lang.ClassNotFoundException: org.jboss.logging.BasicLogger
[java] 	at java.net.URLClassLoader.findClass(Unknown Source)
[java] 	at java.lang.ClassLoader.loadClass(Unknown Source)
[java] 	at sun.misc.Launcher$AppClassLoader.loadClass(Unknown Source)
[java] 	at java.lang.ClassLoader.loadClass(Unknown Source)
[java] 	... 70 more
```

#### Solution
* download [jboss-logging-3.1.0.GA.jar](https://mvnrepository.com/artifact/org.jboss.logging/jboss-logging/3.1.0.GA)
* copy the file to `%CTAKES_HOME$/lib`



## Issue 6 - Transaction
ClassNotFoundException issue due to transaction-api library. Error messages would look like this.

```
...
[java] 	... 54 more
[java] Caused by: java.lang.ClassNotFoundException: javax.transaction.SystemException
[java] 	at java.net.URLClassLoader.findClass(Unknown Source)
[java] 	at java.lang.ClassLoader.loadClass(Unknown Source)
[java] 	at sun.misc.Launcher$AppClassLoader.loadClass(Unknown Source)
[java] 	at java.lang.ClassLoader.loadClass(Unknown Source)
[java] 	... 62 more
```

#### Solution
* download [javax.transaction-api-1.2.jar](https://mvnrepository.com/artifact/javax.transaction/javax.transaction-api/1.2)
* copy the file to `%CTAKES_HOME$/lib`



## Issue 7 - Persistence
ClassNotFoundException issue due to persistence api library. Error messages would look like this.
```
...
[java] 	... 54 more
[java] Caused by: java.lang.ClassNotFoundException: javax.persistence.Entity
[java] 	at java.net.URLClassLoader.findClass(Unknown Source)
[java] 	at java.lang.ClassLoader.loadClass(Unknown Source)
[java] 	at sun.misc.Launcher$AppClassLoader.loadClass(Unknown Source)
[java] 	at java.lang.ClassLoader.loadClass(Unknown Source)
[java] 	... 58 more
```

#### Solution
* download [javax.persistence-api-2.2.jar](https://mvnrepository.com/artifact/javax.persistence/javax.persistence-api/2.2)
* copy the file to `%CTAKES_HOME$/lib`


## Issue 8 - EhCacheRegionFactory
ClassNotFoundException issue due to EhCacheRegionFactory library. Error messages would look like this.
```
...
[java] Caused by: org.hibernate.service.classloading.spi.ClassLoadingException: Unable to load class [org.hibernate.cache.ehcache.EhCacheRegionFactory]
[java] 	at org.hibernate.service.classloading.internal.ClassLoaderServiceImpl.classForName(ClassLoaderServiceImpl.java:152)
[java] 	at org.hibernate.cfg.SettingsFactory.createRegionFactory(SettingsFactory.java:484)
[java] 	... 64 more
[java] Caused by: java.lang.ClassNotFoundException: Could not load requested class : org.hibernate.cache.ehcache.EhCacheRegionFactory
[java] 	at org.hibernate.service.classloading.internal.ClassLoaderServiceImpl$AggregatedClassLoader.findClass(ClassLoaderServiceImpl.java:319)
[java] 	at java.lang.ClassLoader.loadClass(Unknown Source)
[java] 	at java.lang.ClassLoader.loadClass(Unknown Source)
[java] 	at java.lang.Class.forName0(Native Method)
[java] 	at java.lang.Class.forName(Unknown Source)
[java] 	at org.hibernate.service.classloading.internal.ClassLoaderServiceImpl.classForName(ClassLoaderServiceImpl.java:149)
[java] 	... 65 more
```

#### Solution
* download [hibernate-ehcache-4.2.6.Final.jar](https://mvnrepository.com/artifact/org.hibernate/hibernate-ehcache/4.2.6.Final)
* copy the file to `%CTAKES_HOME$/lib`


## Issue 9 - Weka
ClassNotFoundException issue due to weka library. Error messages would look like this.
```
[java] Caused by: java.lang.ClassNotFoundException: weka.core.Instance
[java] 	at java.net.URLClassLoader.findClass(Unknown Source)
[java] 	at java.lang.ClassLoader.loadClass(Unknown Source)
[java] 	at sun.misc.Launcher$AppClassLoader.loadClass(Unknown Source)
[java] 	at java.lang.ClassLoader.loadClass(Unknown Source)
[java] 	... 41 more
```

#### Solution
* download [weka-stable-3.6.10.jar](https://mvnrepository.com/artifact/nz.ac.waikato.cms.weka/weka-stable/3.6.10)
* copy the file to `%CTAKES_HOME$/lib`



## Issue 10 - gramMAtrixExporter

The class is depricated that is no longer available. The error messages looks like below.

```
...
[java] Caused by: org.springframework.beans.factory.BeanCreationException: Error creating bean with name 'gramMatrixExporter' defined in class path resource [org/apache/ctakes/ytex/beans-kernel.xml]: Initialization of bean failed; nested exception is org.springframework.beans.FatalBeanException: Failed to obtain BeanInfo for class [org.apache.ctakes.ytex.weka.GramMatrixExporterImpl]; nested exception is java.beans.IntrospectionException: type mismatch between read and write methods
     [java] 	at org.springframework.beans.factory.support.AbstractAutowireCapableBeanFactory.doCreateBean(AbstractAutowireCapableBeanFactory.java:527)
...
```

Spring bean is not able to load depricated function. Go to folder where you copy ytex resources. In my example, `%CTAKES_HOME%\resources\org\apache\ctakes\ytex`. 

#### Solution
* find `beans-kernel.xml` under the ytex resource folder
* Open it and search fo gramMatrixExporter
* Comment this bean so it won't load when you run your script. 

Here is my example.
```
<!-- <bean id="gramMatrixExporter" class="org.apache.ctakes.ytex.weka.GramMatrixExporterImpl">
		<property name="dataSource" ref="dataSource" />
		<property name="kernelEvaluationDao" ref="kernelEvaluationDao" />	
		<property name="transactionManager" ref="transactionManager" />
	</bean> -->
```

After following this steps, you run the `build-setup.xml` file. It will take some time to install UMLS data in YTEX database. The number of words in `v_snomed_fword_lookup` is 1.5 million (without UMLS, default was 2733). This way, cTAKES will be more useful to extract clinical terms.

I successfully installed cTAKES/YTEX using UMLS. 



