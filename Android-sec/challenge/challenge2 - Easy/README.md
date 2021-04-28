### Challenge2 : Database Challenge 
In this challenge, you can download the Android application using the following link: Android02.apk

The application requires a pin code to give you the key. However, no pin code is set so you will not be able to retrieve the key.

The way to solve this exercise is to extract the content of the apk (using apktool for example) to get the configuration files of the application. 
Once you manage to extract the apk, you will find multiple files. It's always important to check for file ending in .db or .sqlite to see what information is shipped with the 
application.  By reviewing one of these files, you should be able to find the key to solve this exercise.

#### Solution for database challenge :

1) read the tag "sqlite.db" from androidmanifest.xml

2) "find . -name sqlite.db"

3) cd /assets/

4) sqlite3 sqlite.db

5) .tables

6) select * from tables;
