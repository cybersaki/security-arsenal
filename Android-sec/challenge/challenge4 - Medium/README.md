### Challenge4 : JAD the Obfuscated code

In this challenge, the application requires a pin code to give you the key. However, no pin code is set so you will not be able to retrieve the key.

There are two ways to solve this exercise. The first way to solve this exercise is to use apktool like you did before. 
apktool will give extract the application's code as smali code. You will be able to browse the source code in the smali directory. 
However, it's not very intuitive but it's enough for this exercise.

The second way is to extract the application yourself using unzip. From there, you should see a file named classes.dex. 
You can then use the tool dex2jar to convert the dex file to a jar file. Once this is done, you can either unzip the jar file to browse the code or use jd-gui.

By inspecting the smali code or the java code, you should be able to find the way the key has been "encrypted". 
It's actually a XOR between the key and a single character. You can easily retrieve the clear text by applying the same operation to the obfuscated text.

#### Solution :

1) unzip app
2) dex2jar the classes.dex
3) unzip the classes.jar
4) download jad and jad the messageactivity.class
5) open messageactivity.jad and find the string
6) in ruby irb, paste the following command to get the answer : 

"\005\006\006\003U\001UU\031WQ\f\006\031\000PU\f\031\rVU\002\031W\000\rQ\f\004\rVU\003\006\004".split("").map{|x| (x.ord^52).chr}.join
