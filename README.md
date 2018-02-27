# cTAKES
cTAKES/YTEX installation instruction on Windows

First step before reading this instruction I would recommed to read instruction on official cTAKES website  (https://cwiki.apache.org/confluence/display/CTAKES/cTAKES+4.0+User+Install+Guide). I had a problem installing cTAKES/YTEX so decided to write brief tips how I installed successfully. 


1. Donwload cTAKES and UMLS reousrces

Download cTAKES from http://ctakes.apache.org/downloads.cgi
User Installation: Windows - apache-ctakes-4.0.0-bin.zip
UMLS Dictionary: All Versions - ctakes-resources-4.0-bin.zip

Once downladed two files, decompress the two zip files then you will see two folders 1) apache-ctakes-4.0.0 and 2) resources. 

Copy all files under apache-ctakes-4.0.0 (no need to have apache-ctakes-4.0.0 folder itself) and also copy the resources folder to your ctakes hoome directory ex.) C:\cTAKES. You will notice that there are the same file names so while copying files you just need to overwrite all files. ctakes-resources-4.0 has more rich UMLS data than the basic cTAKES tool. 

2. Set cTAKES Home Directory:

Let's set a cTAKES Home Directory on Windows environment. I typically use short cut "window button" + "Pause" to System in Control pannel, or you can open control panel then click System. Then you click "Advanced system settings" -> "Environment Variables" -> Add "CTAKE_HOME" in User variable, and your home directory ex.) C:\cTAKES in Value column. 









