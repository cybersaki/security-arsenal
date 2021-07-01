# Recon cheatsheet for pentesting and bugbounty

## 1) Subdomain enumeration:
### 1.1 )Sublist3r
```
python sublist3r.py -d yahoo.com
```

### 1.3) subbrute:
```
python subbrute.py google.com
```

### 1.4) subfinder:
```
./subfinder -d target.com
./subfinder -d target.com -b -w all.txt
```

### 1.5) Amass:
```
amass -d target.com
```

### 1.6) Shodan : 
#### 1.6.1) Reference materials : 
https://leanpub.com/shodan

#### 1.6.2) Shodan dorks:
```
net:64.233.160.0/19
org:"Google"
```

### 1.7) Censys dorks:
```
ip:64:233.160.0/19
autonomous_system.asn:15169
autonomous_system.organization:"Google Inc."
```

### 1.8) Amazon web services :
```
site:s3.amazonaws.com + abc.com
```

### 1.9) Aquatone :
```
~/aquatone/tesla.com
❯ grep -c -E ',(CNAME|A|AAAA),' *.csv

~/aquatone/tesla.com
❯ wc -l hosts.txt 
217 hosts.txt

subdomaintakeovers : ~/aquatone/tesla.com aquatone-takeover --list-detectors | grep -Fc Service.
```

### 1.10) assetfinder:
```
assetfinder -subs-only ppf.sony.net | httprobe
```

### 1.11) Whois and massdns:
#### 1.11.1) Whois :
whois search for an emal,
Now reverse whois search for websites registered in the email.
```
whois -h whois.cymru.com 69.50.232.53
whois domain.com

massdns -r lists/resolvers.txt -t -A -q -O -S domains.txt
```

#### 1.11.2) Massdns :
```
./subbrute.py /root/work/bin/all.txt $TARGET.com | ./bin/massdns -r resolvers.txt -t A -a -o -w massdns_output.txt

./scripts/ct.py example.com | ./bin/massdns -r lists/resolvers.txt -t A -o S -w results.txt

All.txt = https://gist.githubusercontent.com/jhaddix/86a06c5dc309d08580a018c66354a056/raw/f58e82c9abfa46a932eb92edbe6b18214141439b/all.txt
```

### 1.13) domlink:
https://github.com/vysec/DomLink

### 1.14) masscan :
```
masscan -p1-65535 -iL $TARGET_LIST --max-rate 100000 -oG $TARGET_OUTPUT
```

### 1.15) IP Address:
```
dig a eff.org +short
>69.50.232.53
```

### 1.16) Knockpy:
```
Knockpy <url>
```

### 1.17) Subresolve:
```
ruby resolve.rb domainlist

ruby subdomain.rb gist
((https://gist.github.com/ehsahil/0b618104319a97b21bea88fbb5ea49c2))

ruby recon.rb gist
((https://gist.github.com/ehsahil/f0f6e731a22a111399aa8503e34b6919))
```

### 1.18) Lazyrecon:
```
lazyrecon <url>
```

### 1.19) Lazyshot:
```
python lazyshot.py wordlist
```

### 1.20) IP range crawl:
```
bgp.he.net/dns/
```

### 1.21) Altdns:
```
./altdns.py -i subdomains.txt -o data_output -w words.txt -r -s output.txt
```
### 1.22) Domains from csp :
https://github.com/yamakira/domains-from-csp

### 1.23) Analyse your HTTP response headers :
https://securityheaders.com/

### 1.24) More targets with burpsuite by Jason Haddix :
https://medium.com/u/1dfc5adea2d4?source=post_page-----82b7e5f62e21--------------------------------

### 1.25) Domain Analyzer :
https://github.com/eldraco/domain_analyzer

### 1.26) Domain profiler :
https://github.com/jpf/domain-profiler

### 1.27) VHost Scan :
https://github.com/codingo/VHostScan

### 1.28) Threat crowd :
https://www.threatcrowd.org/

### 1.29) Visual site mapper :
http://www.visualsitemapper.com/

### 1.30) Google transparency report :
https://transparencyreport.google.com/https/certificates

### 1.31) Certspotter:
https://certspotter.com/api/v0/certs?domain=domain.com

### 1.32) Certdb:
https://certdb.com/

### 1.33) crtsh:
https://crt.sh/?q=%25domain.com

### 1.34) Facebook certificate transparency monitoring systems :
https://developers.facebook.com/tools/ct

### 1.35) Bucket finder - Digininja :
https://digi.ninja/projects/bucket_finder.php

### 1.36) Lazys3 :
https://github.com/nahamsec/lazys3

### 1.37) Virustotal:
```
python virustotal_subdomain_enum.py <host> 20
```

------------------------------------------------------------------------------------------------------------------------
## 2) Directory and file recon :
### 2.1) Dirsearch:
```
dirsearch.py -u target.com -e html,json,php -x 403,500 -t 50
```

### 2.2) Linkfinder for JS :
https://github.com/GerbenJavado/LinkFinder

### 2.3) jsparser :
https://github.com/nahamsec/JSParser

### 2.4) JS tricks :
https://medium.com/bugbountywriteup/bug-bounty-tips-tricks-js-javascript-files-bdde412ea49d

### 2.5) waybackurl :
https://gist.github.com/mhmdiaa/adf6bff70142e5091792841d4b372050

### 2.6) Gobuster:
```
gobuster -m dns -u $TARGET.com -t 100 -w all.txt
```

------------------------------------------------------------------------------------------------------------------------
## 3) Endpoint recon : 
### 3.1) waffinder:
```
wafwoof (url)
```

To bypass waf, we need original IP:
```
https://censys.io/
https://dnsdumpster.com/
https://securitytrails.com/dns-trails
http://viewdns.info/iphistory/?domain=tesla.com
```
Once you got several ips, you can test with simple curl command :
```
curl --silent --fail -H "Host: www.test.com" http://$IP_YOU_HAVE_FOUND
```

### 3.2) Spaces finder:
```
https://github.com/appsecco/spaces-finder
python3 spaces_finder.py -l sample_spaces.txt -g interesting_keywords.txt -D -m 500000 -t 2
```

### 3.3) Blog for api keys :
```
https://danielmiessler.com/blog/apis-2fas-achilles-heel/
```

### 3.4) API recon using kiterunner : 
```
kr scan url -w /home/kali/folder/routes-large.kite -o json (or -o text)

kr brute url -A=apiroutes-210228
```
#### 3.4.1) For many urls :
```
kr scan source.txt -w /home/kali/folder/routes-large.kite
```

#### 3.4.2) Combining :
```
kr scan url -w /home/kali/folder/routes-large.kite -A=apiroutes-210228
```

#### 3.4.3) Replay a request:
```
kr kb replay -w /home/kali/folder/routes-large.kite "GET 404 [6282,282,282] url 0029ab68cd009ffee"
```

#### 3.4.4) Sending to burp :
--proxy=http://127.0.0.1:8080

#### 3.4.5) Filter out status code requests:
```
kr scan url -w /home/kali/folder/routes-large.kite -A=apiroutes-210228 --fail-status-codes 400,401,404,403,501,502,426,411
```

### 3.5) meg:
```
meg <endpoint> <host>
meg / https://edoverflow.com

sed 's#^#http://#g' list-of-hosts > output

meg / https://edoverflow.com out-edoverflow/
```

### 3.6) Discover interesting files on web :
```
meg -s 200 \
  lists/php targets-all \
  out-php/ 2> /dev/null
```

### 3.7) Nmap - Sensitive ports opened
```
nmap -sV -sC -sS -Pn -p 1-65535 -vvv -O -A <host> 
```

### 3.8) Nikto - Banner and version disclosure 
```
Nikto -h <url>
```

### 3.9) Certificate checks :
P.S : Check Shodan of the site first to confirm the certificate transparency 

#### 3.10) Openssl :
```
openssl s_client -connect example.com:443
```

#### 3.11) sslscan :
```
sslscan (url)
```

#### 3.12) Advanced REST client:
Run from GUI to receive the additional headers and certificate transparency associated with the endpoint.

------------------------------------------------------------------------------------------------------------------------

## 4) Recon using Static code analysis :
### 4.1) Brakeman for Ruby :
https://brakemanscanner.org/

### 4.2) Bandit for python:
https://github.com/openstack/bandit

------------------------------------------------------------------------------------------------------------------------
## 5) Android apps recon :
Get the file from https://apkpure.com/ or  /data/app/[PACKAGE NAME]/base.apk

Check if packet capture shows any weird data
If you see empty data to port 443, then SSL pinning is possible.

If the app is not making any connection to server or burp isnt catching any response,
either its ssl pinning or non-http traffic.

### 5.1) SSL Trustkiller : 
https://github.com/iSECPartners/Android-SSL-TrustKiller

### 5.2) SSL unpinning :
https://github.com/ac-pm/SSLUnpinning_Xposed

### 5.3) Bypassing ssl pinning :
https://blog.it-securityguard.com/the-stony-path-of-android-%F0%9F%A4%96-bug-bounty-bypassing-certificate-pinning/

Decompile app using apktool and dex2jar

Use smali code for tool analysis and java code to read the stuff found in smali code.
```
\.json or http(s)?;//

grep -hnrE "\.json" /Users/kali/smali/
```
Use linkfinder to find all urls in the smali code.

### 5.4) oneliner bash to decompile and linkfinder :
```
apktool d app.apk; cd app;mkdir collection; find . -name \*.smali -exec sh -c 'cp "$1" collection/$(head /dev/urandom | md5 | cut -d" " -f1).smali' _ {} \;; linkfinder.py -i 'collection/*.smali' -o cli
```

### 5.5) To narrow this down, use regex option(-r) :
```
linkfinder.py -i 'collection/*.smali' -o cli -r '\.json'
```
Check the /assets and /res/raw

### 5.6) Check for recon tricks :
https://www.youtube.com/watch?v=OLgmPxTHLuY

Look in smali and dex code for invoke-static and iget-object for leaking phone numbers and sms.
verify_phone.json

------------------------------------------------------------------------------------------------------------------------
## 6) AWS recon :
### 6.1) Conduct reconnaissance using opendata7 and aws :
```
aws s3 ls s3://rapid7-opendata/ --no-sign-request
```
https://www.rapid7.com/blog/post/2018/10/16/how-to-conduct-dns-reconnaissance-for-02-using-rapid7-open-data-and-aws/

### 6.2) Bucketfinder :
```
https://digi.ninja/projects/bucket_finder.php
./bucket_finder.rb url
```

### 6.3) Awscli :
```
aws s3 ls s3://hackerone-attachment

aws s3 mv test.txt s3://hackerone-attachments
```

### 6.4) Slurp for aws :
https://github.com/bbb31/slurp

### 6.5) s3 bucket finder :
https://github.com/gwen001/s3-buckets-finder

------------------------------------------------------------------------------------------------------------------------
## 7) Google dorking :
### 7.1) Google dorks for sensitive files and urls
```
site:google.com
site:google.com -www -cloud
inurl
intitle
intext

- site:target.com filetype:php
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

- site: target.com intext: “index of /”

- site: target.com filetype:txt
- site: target.com inurl:.php.txt
- site: target.com ext:txt
```
Check for ftp and smtp credentials.

### 7.2) Google dorks for s3 buckets:
```
site:s3.amazonaws.com file:pdf
site:s3.amazonaws.com password
```
------------------------------------------------------------------------------------------------------------------------
## 8) Github dorking :
### 8.1) Find secrets in code : 
- API and key. (Get some more endpoints and find API keys.)
- token
- secret
- vulnerable
- http://

### 8.2) Find juicy information in source code :
https://github.com/dxa4481/truffleHog : 
```
truffleHog --regex --entropy=False https://github.com/dxa4481/truffleHog.git
```
https://github.com/anshumanbh/git-all-secrets

### 8.3) Collection of github dorks :
https://github.com/techgaun/github-dorks/blob/master/github-dorks.txt

### 8.4) Tool to run github dorks against a repo : 
https://github.com/techgaun/github-dorks

### 8.5) Check for special key words :
```
API_key and AWS_Secret
"password" "dev"
api endpoints
```
https://edoverflow.com//2017/github-for-bugbountyhunters
https://github.com/techgaun/github-dorks

### 8.6) Code repos for recon in github:
```
".delloite.com"+"password"

"delete the private ssh"

"SQL injection vuln in data.views"

"Multiple XSS vulnerabilities"
```

### 8.7) Mass cloning on github : 
```
https://github.com/mazen160/GithubCloner
```

------------------------------------------------------------------------------------------------------------------------
## 9) Nuclei project for all rounder :
```
nuclei -l test.txt -t ../nuclei-templates/ -o output.txt
```
Note : Buy a VPS and run Nuclei.

------------------------------------------------------------------------------------------------------------------------
## 10) Webscreenshot for easy check on the URLS :
From the previous methods, collect all urls, directories and files into a spreadsheet separately and delete duplicates.

Export the spreadsheets into text files and use it for webscreenshot to display tons of websites easily in a single index page.
```
python webscreenshot.py -i ~/recondata/yahoo.com/alive.txt -o home/yahoo.com/screenshots -w 20 -m -a "X-FORWARDED-FOR: 127.0.0.1"
```
