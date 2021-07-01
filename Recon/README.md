Subdomain enumeration:
Sublist3r
python sublist3r.py -d yahoo.com

subbrute:
python subbrute.py google.com

subfinder:
./subfinder -d target.com
./subfinder -d target.com -b -w all.txt

Amass:
amass -d target.com

Censys

Shodan : Reference materials : https://leanpub.com/shodan

Google dorks : 
site:google.com
site:google.com -www -cloud
inurl
intitle
intext

Github dorks :
API_key and AWS_Secret
"password" "dev"
api endpoints

Amazon web services :
site:s3.amazonaws.com + abc.com

Aquatone :
~/aquatone/tesla.com
❯ grep -c -E ',(CNAME|A|AAAA),' *.csv

~/aquatone/tesla.com
❯ wc -l hosts.txt 
217 hosts.txt

subdomaintakeovers : ~/aquatone/tesla.com aquatone-takeover --list-detectors | grep -Fc Service.

Dirsearch:
dirsearch.py -u target.com -e html,json,php -x 403,500 -t 50

assetfinder:
assetfinder -subs-only ppf.sony.net | httprobe

Look in smali and dex code for invoke-static and iget-object for leaking phone numbers and sms.
verify_phone.json

Conduct reconnaissance using opendata7 and aws :
aws s3 ls s3://rapid7-opendata/ --no-sign-request
https://www.rapid7.com/blog/post/2018/10/16/how-to-conduct-dns-reconnaissance-for-02-using-rapid7-open-data-and-aws/

Bucketfinder :
https://digi.ninja/projects/bucket_finder.php
./bucket_finder.rb url

Awscli :
aws s3 ls s3://hackerone-attachment

aws s3 mv test.txt s3://hackerone-attachments


Google dorks :
-site:target.com filetype:php
- site:target.com filetype:aspx
- site:target.com filetype:swf (Shockwave Flash)
- site:target.com filetype:wsdl

- site: target.com inurl:.php?id=
- site: target.com inurl:.php?user=
- site: target.com inurl:.php?book=

- site: target.com inurl:login.php
- site: target.com intext: “login”
- site: target.com inurl:portal.php
- site: target.com inurl:register.php

-site: target.com intext: “index of /”

site: target.com filetype:txt
- site: target.com inurl:.php.txt
-site: target.com ext:txt

Check for ftp and smtp credentials.
