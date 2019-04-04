# cTAKES/YTEX installation on Windows & MySQL/MsSQL

cTAKES is an NLP tools for extracting clinical information from clinical notes in electronic medical records. YTEX is an extension of cTAKES to store extracted data from cTAKES in a relational database such as Oracle, MySQL or MsSQL. I had a couple of problems installing cTAKES/YTEX on Windows. This instruction helps me tacking what problems I ran into and how I made it work successfully. 

If installing this NLP tool first time, I would recommed you to read the original instruction on [cTAKES website](https://cwiki.apache.org/confluence/display/CTAKES/cTAKES+4.0+User+Install+Guide).


## 1. Donwload cTAKES and UMLS reousrces

Download [cTAKES webiste](http://ctakes.apache.org/downloads.cgi)
* User Installation: Windows - apache-ctakes-4.0.0-bin.zip
* UMLS Dictionary: All Versions - ctakes-resources-4.0-bin.zip
* Source code: Windows - apache-ctaeks-4.0.0-src.zip (if you are interested in looking into its source code)

Once downladed two files, decompress the two zip files then you will see two folders 1) apache-ctakes-4.0.0 and 2) resources. 

Now, just copy the resource files to apache-ctakes-4.0.0 folder. You will also see the resources folder under the apache-ctakes-4.0.0, but don't worry. You can overwrite files. The UMLS resource contains umls2011ab, rxnrom, orange_book, etc. that allow us to retreive more comprehensive results from cTAKES.

### Quick Check
You are done installing the cTAKES. Here is a simple test you can do whether cTAKES is working or not.

* Click bin\runctakesCVD.bat
* CAS Visual Debugger (CVD) will popup. Then click Run > Load AE in the menu. You need to load an analysis engine first. 
* Select AggregatePlaintextProcessor.xml under desc\ctakes-clinical-pipeline\desc\anaysis_engine. 
* Then copy below example from cTAKES User Guide website, and paste it to the CVD Text field.

> Dr. Nutritious Medical Nutrition Therapy for Hyperlipidemia Referral from: Julie Tester, RD, LD, CNSD Phone contact: (555) 555-1212 Height: 144 cm Current Weight: 45 kg Date of current weight: 02-29-2001 Admit Weight: 53 kg BMI: 18 kg/m2 Diet: General Daily Calorie needs (kcals): 1500 calories, assessed as HB + 20% for activity. Daily Protein needs: 40 grams, assessed as 1.0 g/kg. Pt has been on a 3-day calorie count and has had an average intake of 1100 calories. She was instructed to drink 2-3 cans of liquid supplement to help promote weight gain. She agrees with the plan and has my number for further assessment. May want a Resting Metabolic Rate as well. She takes an aspirin a day for knee pain.

* Then click Run > Run AggregatePlaintextProcessor in the menu. This is disabled if you haven't load AE module.

You shoudl be able to process the document, and able see results on the Analysis Results (attach a pic). If you have a problem of running this example, you need to check if Java is installed. 

If you need YTEX installed, continue to read below. Otherwise, you can just stop here.


## 2. Set cTAKES Home Directory:

You need to set cTAKES home directory. You can just use the path where you unzipped the file or have a new path where you can easily reference like D:\cTAKES. 

Let's set a cTAKES Home Directory on Windows environment. I typically use short cut "window button" + "Pause" to open System in Control pannel, or you can follow Control Panel > System and Security > System. Then you click "Advanced system settings" -> "Environment Variables" -> Add "CTAKES_HOME" in User variable, and your home directory ex.) D:\cTAKES in Value column. 

TODO: How to use UMLS dataset and getting its permission.



## 3. Install YTEX

Now, installing YTEX. YTEX is now integrated into cTAKES 4.0.0. However, setting YTEX is not simple to install. I ran into many troubles to install YTEX and this is the main reason I started to make some notes. 

I first read [YTEX intallation guide](https://cwiki.apache.org/confluence/display/CTAKES/YTEX+Installation) on their website to understand what it does. This website is the old version of cTAKES. Although it gave me some insights how to install YTEX on MsSQL and MySQL databases. You can also find addintional YTEX documents in [cTAKES 4.0 Component Use Guide](https://cwiki.apache.org/confluence/display/CTAKES/cTAKES+4.0+Component+Use+Guide). 

To setup cTAKES 4.0.0 + YTEX, I need to know how to execute the setup script in bin\ctakes-ytex\scripts. In the cTAKES document, I need to navigate to CTAKES_HOME, and execute this command. However, it gave me errors. 

```
cd /d %CTAKES_HOME%\bin\ctakes-ytex\scripts ..\..\ant.bat -f build-setup.xml all > setup.out 2>&1
```

You need to open the setup.out file to check error messages. Before checking this make sure you have installed a database as well.


MySQL
------------

This is a summary of what I read from the cTAKES/YTEX documents. I tried to follow what was suggested and fixed issues as it appears.

#### Create Account

According to the YTEX installation guide, you need to create an YTEX account.
```
CREATE DATABASE ytex CHARACTER SET utf8;
CREATE USER 'ytex'@'localhost' IDENTIFIED BY 'ytex';
GRANT ALL PRIVILEGES ON ytex.* TO 'ytex'@'localhost';
```

If you are uinsg later MySQL which requires caching_sha2_password, add WITH mysql_native_password when you create an account.
```
CREATE USER 'ytex'@'localhost' IDENTIFIED WITH mysql_native_password BY 'ytex';
```

#### Setup DB Account in YTEX

Copying a MySQL config file from the ytex resource jar file to the cTAKES resources folder. You run following four commands.

```
cd %CTAKES_HOME%\resources
mkdir org\apache\ctakes\ytex
jar xf ..\lib\ctakes-ytex-res-*.jar org/apache/ctakes/ytex/ytex.properties.mysql.example 
copy org\apache\ctakes\ytex\ytex.properties.mysql.example org\apache\ctakes\ytex\ytex.properties
```

If you are having a trouble of "jar" command, you can google to how to add jar to Windows enviornment. The executable jar is inside the jdk bin folder. 

If you use a different passowrd, you need to open the "ytex.properties" file and change db.passowrd. 

Again, make sure your ytex.properties file is stored in "%CTAKES_HOME%\resources\org\apache\ctakes\ytex\ytex.properties". You also make sure db name, user id/pw, schema information are correct. 


#### Altenative way 

If you don't want to use jar to extract the db configuration file, you can unzip ctakes-ytex-res-4.0.0.jar file in lib, and copy ytex folder from the unzipped lib to CTAKES_HOME\resources\org\apache\ctakes\ytex.

There are a couple of examples of ytex.properties which you can select one that match your database, and rename the example file   to ytex.properties. In my case, I used ytex.properties.mssql.example and renamed it to ytex.properties.


Setup UMLS DB
------------

Recommend to install UMLS.. (link)



Install YTEX Database
------------

This is where you will expect to see errors.. and failed to install YTEX. 

I first changed my current directory to \bin\ctakes-ytex\scripts, and execute this command. 

```
D:\cTAKES\bin\ctakes-ytex\scripts>..\..\ant.bat -f build-setup.xml all
```

This `ant` build command is to install tables in the ytex database, and also add necessary data. If you don't have UMLS set up in your local database, this will takes a couple of minutes. If you have UMLS installed, a couple of hours to install necessary data.  

I ran this command this `ant` build script and as erroring out I noted down the issue and the way to solve it. You can skip some of issues if you don't see it. Just search a couple of key words in your error messages. You may able to find the solution. 


#### Issue 1 - DBPing
It is throwing errors. First error I had was "DBPing Connection to db failed - please check your settings and try again". This is because of no MySQL DB connector lib. 

You need to download database connector library, and copy the file to %CTAKES_HOME%\lib. 
- MySQL: mysql-conntector-java (link)
- MsSQL: sqljdbc (link) 

After adding this library, run the command again and monitor command messages. You will see new error messages.


#### Issue 2 - rank 

The MySQL version 8 or later doesn't like to have a column name using a reserved word. YTEX db scripts use rank in the column name and generated following error message.
```
Failed to execute:   create table feature_rank ( feature_rank_id int auto_increment not 
```

You can simply follow this step and replace the table.
1. open a file - bin\ctakes-ytex\scripts\data\mysql\kernel\create_tables.sql 
2. find sql scripts that use rank as a column name (feature_rank and hotspot_sentence tables)
3. replace column name rank to \`rank\`

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


#### Issue 3 - HibernateException

After re-run the ant script, I passed the DBPing error, but build was still failed due to NoClassDefFoundError of HibernateException..

Detail:
'txAdvice' defined in class path resource [org/apache/ctakes/ytex/beans-datasource.xml]: Cannot resolve reference to bean 'transactionManager' while setting bean property 'transactionManager'; nested exception is org.springframework.beans.factory.BeanCreationException: Error creating bean with name 'transactionManager' defined in class path resource [org/apache/ctakes/ytex/beans-datasource.xml]: Instantiation of bean failed; nested exception is java.lang.NoClassDefFoundError: org/hibernate/HibernateException

As it says, there is no class and you need to download a jar called hibernate-core-4.2.6.Final, and copy it to the CTAKES_HOME/lib folder.  download link https://mvnrepository.com/artifact/org.hibernate/hibernate-core/4.2.6.Final

3.
After re-run the ant script, I passed HibernateException, but build was still failed due to 
"Invocation of init method failed; nested exception is java.lang.NoClassDefFoundError: org/hibernate/annotations/common/reflection/MetadataProvider"

Again, no class found in the program. To resovle this issue, you need to download a hibernate-commons-annotations-4.0.2.Final lib. Link:

4.
Let's run this script again.. and another no class issue.  "Invocation of init method failed; nested exception is java.lang.NoClassDefFoundError: org/jboss/logging/BasicLogger"

jBoss logger library is missing. Donwload jboss-logging-3.1.0.GA.jar file from Maven. 

5. 
After re-run the script, I got another no class found error.

"Invocation of init method failed; nested exception is java.lang.NoClassDefFoundError: javax/transaction/SystemException"

Download javax.transaction-api-1.2.jar and store it to CTAKES_HOME/lib. 


6. 

"Invocation of init method failed; nested exception is java.lang.NoClassDefFoundError: javax/persistence/Entity"

Download: javax.persistence-api-2.2.jar 

7. 

"java.lang.ClassNotFoundException: Could not load requested class : org.hibernate.cache.ehcache.EhCacheRegionFactory"

Download: hibernate-ehcache-4.2.6.Final.jar


8. 

"Caused by: java.lang.ClassNotFoundException: weka.core.Instance"

Download: weka-stable-3.6.10.jar 


9. depricated gramMAtrixExporter

" Caused by: org.springframework.beans.factory.BeanCreationException: Error creating bean with name 'gramMatrixExporter' defined in class path resource [org/apache/ctakes/ytex/beans-kernel.xml]: Initialization of bean failed; nested exception is org.springframework.beans.FatalBeanException: Failed to obtain BeanInfo for class [org.apache.ctakes.ytex.weka.GramMatrixExporterImpl]; nested exception is java.beans.IntrospectionException: type mismatch between read and write methods" 

Spring bean is not able to load depricated function. Go to a folder where you copy ytex resources. In my excample, cTAKES\resources\org\apache\ctakes\ytex. You can find beans-kernel.xml file. Open it and serach fo gramMatrixExporter. I just commented this bean so it won't load when you run your script. 

Finally, it is running and build is successfully completed!!

