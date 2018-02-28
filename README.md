# cTAKES/YTEX installation instruction on Windows

First step before reading this instruction I would recommed to read instruction on official cTAKES website  (https://cwiki.apache.org/confluence/display/CTAKES/cTAKES+4.0+User+Install+Guide). I had a problem installing cTAKES/YTEX so decided to write brief tips how I installed successfully. 


1. Donwload cTAKES and UMLS reousrces

Download cTAKES from http://ctakes.apache.org/downloads.cgi
User Installation: Windows - apache-ctakes-4.0.0-bin.zip
UMLS Dictionary: All Versions - ctakes-resources-4.0-bin.zip

Once downladed two files, decompress the two zip files then you will see two folders 1) apache-ctakes-4.0.0 and 2) resources. 

Copy all files under apache-ctakes-4.0.0 (no need to have apache-ctakes-4.0.0 folder itself) and also copy the resources folder to your ctakes hoome directory ex.) C:\cTAKES. You will notice that there are the same file names so while copying files you just need to overwrite all files. ctakes-resources-4.0 has more rich UMLS data than the basic cTAKES tool. 

2. Set cTAKES Home Directory:

Let's set a cTAKES Home Directory on Windows environment. I typically use short cut "window button" + "Pause" to System in Control pannel, or you can open control panel then click System. Then you click "Advanced system settings" -> "Environment Variables" -> Add "CTAKE_HOME" in User variable, and your home directory ex.) C:\cTAKES in Value column. 

TODO: How to use UMLS dataset and getting its permission.


If Step 1 and Step 2 is completed, you can run cTAKES debugger Processing Engine. Now we want to install YTEX. 


3. Install YTEX

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

