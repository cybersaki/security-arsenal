### Challenge 5 : Proguard reversing

In this challenge, the application requires a pin code to give you the key. However, no pin code is set so you will not be able to retrieve the key.

There are two ways to solve this exercise. The first way to solve this exercise is to use apktool like you did before. 
apktool will give extract the application's code as smali code. You will be able to browse the source code in the smali directory. 
However, it's not very intuitive but it's enough for this exercise.

The second way is to extract the application yourself using unzip. From there, you should see a file named classes.dex.
You can then use the tool dex2jar to convert the dex file to a jar file. Once this is done, you can either unzip the jar file to browse the code or use jd-gui.

This time the code has been minimised using ProGuard. This makes reversing the application more complex.

By inspecting the smali code or the java code, you should be able to find the way the key has been "encrypted". 
It's actually a XOR between the key and a string. You can easily retrieve the key by applying the same operation to the obfuscated key to get the actual key.

#### Solution : 

1) unpack the zip
2) go to Messageactivity.class
3) jad the file and open messageactivity.jad
4) Find the encryption key
5) apktool the app and find the strings.xml in res folder
6) Find the key name
7) Open irb and type the following command : 
```
"i]\rD\004\025\027\004_~\002\006`HZ@UBY\\Ku\002O2\003_MQB\020\007G~\004Q".split("").each_with_index.map{|x,i| x.ord^("Pentesterlab"[i%"Pentesterlab".size]).ord}.map{|x| x.chr}.join

"98c0afca-\x12cd0-4401-99\x19c-bf1941db5\x12e3"


Remove \x1 for the key.
```
