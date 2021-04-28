### Challenge6 - Extreme Proguard reversing with logical code

In this challenge, the application requires a pin code to give you the key. 
However, no pin code is set so you will not be able to retrieve the key.

There are two ways to solve this exercise. The first way to solve this exercise is to use apktool like you did before. 
apktool will give extract the application's code as smali code. You will be able to browse the source code in the smali directory. 
However, it's not very intuitive but it's enough for this exercise.

The second way is to extract the application yourself using unzip. From there, you should see a file named classes.dex. 
You can then use the tool dex2jar to convert the dex file to a jar file. Once this is done, you can either unzip the jar file to browse the code or use jd-gui.

This time the code has been minimised using ProGuard. This makes reversing the application more complex.

By inspecting the smali code or the java code, you should be able to find the way the key has been encrypted. 
This time, proper encryption is used with AES in CBC mode. You can easily retrieve the key by writing a small program to decrypt the encrypted string using the key.

#### Solution : 

1) unzip the package
2) dex2jar classes.dex
3) unzip classes-dex.jar
4) check com/android06/pentesterlab/messageactivity.class
5) jad the class file.
6) the file points to a.class for decryption key
7) jad the a.class file.
8) the decryption pattern is written in a.jad
9) combine the pattern+key combo from messageactivity.jad and a.jad into a new file called decrypt.rb and run the ruby code

```
str = "ygiG2VpgnW6z2ocCPEVaYhDwBs3UxZENbgh1iQJ6NhpBqHsczQsDh1rD3WjejQ7JH1o+lvBdtxhG64qyLQyHSg"

key = "__pentesterlab__"

require 'base64'
require 'openssl'
abyte = Base64.decode64(str)
iv=abyte[0..15]
enc = abyte[16..-1]

cipher = OpenSSL::Cipher::AES.new(128,'CBC')
cipher.decrypt
cipher.iv = iv
cipher.key = key

clear = cipher.update(enc)
clear << cipher.final
puts clear
```
