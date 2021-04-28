Getting the Android application
In this challenge, you can download the Android application using the following link: Android01.apk

The application requires a pin code to give you the key. However, no pin code is set so you will not be able to retrieve the key.

The way to solve this exercise is to extract the content of the apk (using apktool for example) to get the configuration files of the application. Once you manage to extract the apk, you should check the content of the following files:

AndroidManifest.xml: this is the main configuration file used by all Android applications. It used by Android to get information about the application as well as what functionalities are exposed to other applications (amongst other things).
res/values/strings.xml
apktool allows you to unzip the apk file but also to decode files like AndroidManifest.xml to make it easier to review them.

By inspecting these files, you should be able to get the key to score this exercise.


#### Solution : 

1) apktool d "Android01.apk"
2) cat /res/values/strings.xml
