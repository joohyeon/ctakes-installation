# cTAKES/YTEX installation instruction on Windows and MsSQL

I had a couple of problems installing cTAKES and YTEX on Windows. This helps tacking what problems I had to face and to tweak them to successfully run the cTAKES and YTEX application. First goal is to install an cTAKES excutable file and run a couple of samples and check outputs. Next goal is to run cTAKES from the source code. My goal is to make some improvement of cTAKES algorithms and to develop a wrapper to run cTAKES the way I need to.

If you are installing this NLP tool first time, than I would recommed to read the original instruction on official cTAKES website  (https://cwiki.apache.org/confluence/display/CTAKES/cTAKES+4.0+User+Install+Guide). 


## 1. Donwload cTAKES and UMLS reousrces

Download cTAKES from http://ctakes.apache.org/downloads.cgi
User Installation: Windows - apache-ctakes-4.0.0-bin.zip
UMLS Dictionary: All Versions - ctakes-resources-4.0-bin.zip

If you are interested in looking into its source code, you can also download the source code - apache-ctaeks-4.0.0-src.zip.

Once downladed two files, decompress the two zip files then you will see two folders 1) apache-ctakes-4.0.0 and 2) resources. 

Now, just copy the resource files to apache-ctakes-4.0.0 folder. You will also see the resources folder under the apache-ctakes-4.0.0, but don't worry. You can overwrite files. The UMLS resource contains umls2011ab, rxnrom, orange_book, etc. that allow us to retreive more comprehensive results from cTAKES.

### Quick check
You are done installing the cTAKES. Here is a aimple test you can do whether cTAKES is working or not.

* Click bin\runctakesCVD.bat
* CAS Visual Debugger (CVD) will popup. Then click Run > Load AE in the menu. You need to load an analysis engine first. 
* Select AggregatePlaintextProcessor.xml under desc\ctakes-clinical-pipeline\desc\anaysis_engine. 
* Then copy below and paste it to the CVD Text field.
```
Dr. Nutritious Medical Nutrition Therapy for Hyperlipidemia Referral from: Julie Tester, RD, LD, CNSD Phone contact: (555) 555-1212 Height: 144 cm Current Weight: 45 kg Date of current weight: 02-29-2001 Admit Weight: 53 kg BMI: 18 kg/m2 Diet: General Daily Calorie needs (kcals): 1500 calories, assessed as HB + 20% for activity. Daily Protein needs: 40 grams, assessed as 1.0 g/kg. Pt has been on a 3-day calorie count and has had an average intake of 1100 calories. She was instructed to drink 2-3 cans of liquid supplement to help promote weight gain. She agrees with the plan and has my number for further assessment. May want a Resting Metabolic Rate as well. She takes an aspirin a day for knee pain.
```
* Then click Run > Run AggregatePlaintextProcessor in the menu. This is disabled if you haven't load AE module.

You shoudl be able to process the document, and able see results on the Analysis Results (attach a pic). If you have a problem of running this example, you need to check if Java is installed. 


## 2. Set cTAKES Home Directory:

You need to set cTAKES home directory. You can just use the path where unzipped the file or have a new path where you can easily reference like D:\cTAKES. 

Let's set a cTAKES Home Directory on Windows environment. I typically use short cut "window button" + "Pause" to System in Control pannel, or you can open control panel then click System. Then you click "Advanced system settings" -> "Environment Variables" -> Add "CTAKE_HOME" in User variable, and your home directory ex.) C:\cTAKES in Value column. 

TODO: How to use UMLS dataset and getting its permission.


If Step 1 and Step 2 is completed, you can run cTAKES debugger Processing Engine. Now we want to install YTEX. 


## 3. Install YTEX

Now, installing YTEX. cTAKES version 4.0.0 is integrated with YTEX. However, intalling YTEX is not simple that many trouble running the program. So I had to patch ytex myself to avoid problems. 

First, I would recommend to read YTEX intallation guide - https://cwiki.apache.org/confluence/display/CTAKES/YTEX+Installation

At the end of YTEX user guide, you need to run a setup script to install YTEX. However, it is throwing errors. First error I had was "DBPing Connection to db failed - please check your settings and try again". This is because of no DB connector lib. 

You need to download database connector library, and copy the file to CTAKE_HOME\lib. 
- MySQL: mysql-conntector-java (link)
- MsSQL: sqljdbc (link) 

You also see that "cTAKES\resources\org\apache\ctakes\ytex\ytex.properties (The system cannot find the path specified)" in the error. You need a ytex.properties file which contains db connection, user id/pw, schema, etc. 

1.
If you check your CTAKES_HOME\resources\org\apache\ctakes\, there is no ytex fold created. Unfortunately, this is not included in the 4.0 installation packages. Altenatively, you can unzip ctakes-ytex-res-4.0.0.jar file in lib, and copy ytex folder from the unzipped lib to CTAKES_HOME\resources\org\apache\ctakes\ytex.

There are a couple of examples of ytex.properties which you can select one that match your database, and rename the example file   to ytex.properties. In my case, I used ytex.properties.mssql.example and renamed it to ytex.properties.

2.
After re-run the ant script, I passed the DBPing error, but build was still failed due to NoClassDefFoundError of HibernateException..

Detail:
'txAdvice' defined in class path resource [org/apache/ctakes/ytex/beans-datasource.xml]: Cannot resolve reference to bean 'transactionManager' while setting bean property 'transactionManager'; nested exception is org.springframework.beans.factory.BeanCreationException: Error creating bean with name 'transactionManager' defined in class path resource [org/apache/ctakes/ytex/beans-datasource.xml]: Instantiation of bean failed; nested exception is java.lang.NoClassDefFoundError: org/hibernate/HibernateException

As it says, there is no class and you need to download a jar called hibernate-core-4.2.6.Final, and copy it to the CTAKES_HOME/lib folder.  download link https://mvnrepository.com/artifact/org.hibernate/hibernate-core/4.2.6.Final

3.
After re-run the ant script, I passed HibernateException, but build was still failed due to XXXXX.

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

