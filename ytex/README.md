# cTAKES/YTEX UIMA Setting 

Run cTAKES with Maven
Check this website. https://cwiki.apache.org/confluence/display/CTAKES/cTAKES+4.0+Developer+Install+Guide  
1. Follow "Import Project from Maven" - compiling cTAKES root maven project. *important* to do this step. otherwise you will run into errors. 
2. Follow "Compilte with Maven"
3. Follow "Running UIMA CVD with Maven" (optional)


trying to read data from MIMIC3 database and cTAKES results are stored in YTEX database.
- change D:\cTAKES\apache-ctakes-4.0.0-src\ctakes-ytex-uima\desc\cpe\test_plaintext.xml
- change D:\cTAKES\apache-ctakes-4.0.0-src\ctakes-ytex-uima\desc\collection_reader\DBCollectionReader_mysql.xml
- change D:\cTAKES\apache-ctakes-4.0.0-src\ctakes-ytex-uima\desc\analysis_engine\AggregatePlaintextUMLSProcess.xml
- change D:\cTAKES\apache-ctakes-4.0.0-src\ctakes-ytex-uima\desc\analysis_engine\DBConsumer.xml
D:\dev\IntelliJ\myspace\ctakes\ctakes-ytex-uima\desc\analysis_engine\DictionaryLookupAnnotator.xml
- add D:\cTAKES\apache-ctakes-4.0.0-src\ctakes-ytex-uima\desc\analysis_engine\ExtractionPrepAnnotator.xml
- add D:\cTAKES\apache-ctakes-4.0.0-src\ctakes-ytex-uima\desc\analysis_engine\LookupWindowAnnotator.xml
- add D:\cTAKES\apache-ctakes-4.0.0-src\ctakes-ytex-uima\desc\analysis_engine\MaxLookupWindows.xml
- add D:\cTAKES\apache-ctakes-4.0.0-src\ctakes-ytex-uima\desc\analysis_engine\NP2LookupWindow.xml
- add D:\cTAKES\apache-ctakes-4.0.0-src\ctakes-ytex-uima\desc\analysis_engine\SimpleSegmentAnnotator.xml
- add D:\cTAKES\apache-ctakes-4.0.0-src\ctakes-ytex-uima\desc\analysis_engine\SimpleSegmentAnnotator.xml

- copy ctaeks-ytex-res to the ctakes-ytex source folder (D:\cTAKES\apache-ctakes-4.0.0-src\ctakes-ytex\src\main\resources\org\apache\ctakes\ytex)
- then copy ytex.properties.mysql.example to ytex.properties
- then change user id/pwd and database connection  

- add D:\cTAKES\apache-ctakes-4.0.0-src\ctakes-ytex\src\main\resources\org\apache\ctakes\ytex\dictionary\lookup\LookupDesc_SNOMED.xml
- compile 'Apache cTAKES YTEX', 'Apache cTAKES YTEX UIMA' Maven Project 



- add D:\cTAKES\apache-ctakes-4.0.0-src\ctakes-ytex\src\main\resources\org\apache\ctakes\ytex\umls\model\UMLS.hbm..xml
FileNotFoundException: class path resource [org/apache/ctakes/ytex/umls/model/UMLS.hbm.xml

- change line 28-29 in the UMLS.hbm.xml file. change `@filter.umls.catalog@` to `catalog="@filter.umls.catalog@"` 
<hibernate-mapping package="org.apache.ctakes.ytex.umls.model"
         	schema="@umls.schema@" catalog="@filter.umls.catalog@"> 


- 'gramMatrixExporter'


To get started, do the following:
* Create ytex.properties: create src\main\resources\org\apache\ctakes\ytex\ytex.properties (see examples in ctakes-ytex-res/src/main/resources/org/apache/ctakes/ytex)
* Add jdbc drivers to maven repo: if you are using ms sql server / or oracle, add the jdbc driver(s) to your local maven repo (see comments in dependencies section of pom.xml)
* Add ms sql auth dlls to path: if you are using ms sql server with integrated auth, make sure the ms sql jdbc auth directory is in your system PATH (sqljdbc_4.0\enu\auth\x64)
* Unzip ctakes-ytex-resources.zip: extract to  ctakes-ytex-res/src/main
* run the maven build from the command line for ctakes-ytex-res, ctakes-ytex, ctakes-ytex-uima projects.  From the ctakes root directory:

