# Under construction

## Subdomain enumeration:
### Sublist3r
```
python sublist3r.py -d yahoo.com
```

### subbrute:
```
python subbrute.py google.com
```

### subfinder:
```
./subfinder -d target.com
./subfinder -d target.com -b -w all.txt
```

### Amass:
```
amass -d target.com
```
Censys

Shodan : Reference materials : https://leanpub.com/shodan

### Github dorks :
```
API_key and AWS_Secret
"password" "dev"
api endpoints
```

### Amazon web services :
```
site:s3.amazonaws.com + abc.com
```

### Aquatone :
```
~/aquatone/tesla.com
❯ grep -c -E ',(CNAME|A|AAAA),' *.csv

~/aquatone/tesla.com
❯ wc -l hosts.txt 
217 hosts.txt

subdomaintakeovers : ~/aquatone/tesla.com aquatone-takeover --list-detectors | grep -Fc Service.
```

### Dirsearch:
```
dirsearch.py -u target.com -e html,json,php -x 403,500 -t 50
```

### assetfinder:
```
assetfinder -subs-only ppf.sony.net | httprobe
```

### Whois and massdns:
whois search for an emal,
Now reverse whois search for websites registered in the email.
```
whois -h whois.cymru.com 69.50.232.53
whois domain.com

massdns -r lists/resolvers.txt -t -A -q -O -S domains.txt
```

### IP Address:
```
dig a eff.org +short
>69.50.232.53
```

### Shodan dorks:
```
net:64.233.160.0/19
org:"Google"
```

### Censys dorks:
```
ip:64:233.160.0/19
autonomous_system.asn:15169
autonomous_system.organization:"Google Inc."
```

### Knockpy:
```
Knockpy <url>
```

### Subresolve:
```
ruby resolve.rb domainlist

ruby subdomain.rb gist
((https://gist.github.com/ehsahil/0b618104319a97b21bea88fbb5ea49c2))

ruby recon.rb gist
((https://gist.github.com/ehsahil/f0f6e731a22a111399aa8503e34b6919))
```

### Lazyrecon:
```
lazyrecon <url>
```

### Lazyshot:
```
python lazyshot.py wordlist
```

### IP range crawl:
```
bgp.he.net/dns/
```

### Altdns:
```
./altdns.py -i subdomains.txt -o data_output -w words.txt -r -s output.txt
```

### Domains from csp :
https://github.com/yamakira/domains-from-csp

### Analyse your HTTP response headers :
https://securityheaders.com/

### More targets with burpsuite by Jason Haddix :
https://medium.com/u/1dfc5adea2d4?source=post_page-----82b7e5f62e21--------------------------------

### Domain Analyzer :
https://github.com/eldraco/domain_analyzer

### Domain profiler :
https://github.com/jpf/domain-profiler

### VHost Scan :
https://github.com/codingo/VHostScan

### Threat crowd :
https://www.threatcrowd.org/

### Visual site mapper :
http://www.visualsitemapper.com/

### Google transparency report :
https://transparencyreport.google.com/https/certificates

### Certspotter:
https://certspotter.com/api/v0/certs?domain=domain.com

### Certdb:
https://certdb.com/

### crtsh:
https://crt.sh/?q=%25domain.com

### Facebook certificate transparency monitoring systems :
https://developers.facebook.com/tools/ct

### Bucket finder - Digininja :
https://digi.ninja/projects/bucket_finder.php

### Lazys3 :
https://github.com/nahamsec/lazys3

### Slurp for aws :
https://github.com/bbb31/slurp

### s3 bucket finder :
https://github.com/gwen001/s3-buckets-finder

### Github dorking :
https://edoverflow.com//2017/github-for-bugbountyhunters
https://github.com/techgaun/github-dorks

### Linkfinder for JS :
https://github.com/GerbenJavado/LinkFinder

### jsparser :
https://github.com/nahamsec/JSParser

### JS tricks :
https://medium.com/bugbountywriteup/bug-bounty-tips-tricks-js-javascript-files-bdde412ea49d

### waybackurl :
https://gist.github.com/mhmdiaa/adf6bff70142e5091792841d4b372050

### Gobuster:
```
gobuster -m dns -u $TARGET.com -t 100 -w all.txt
```

### Massdns :
```
./subbrute.py /root/work/bin/all.txt $TARGET.com | ./bin/massdns -r resolvers.txt -t A -a -o -w massdns_output.txt

./scripts/ct.py example.com | ./bin/massdns -r lists/resolvers.txt -t A -o S -w results.txt

All.txt = https://gist.githubusercontent.com/jhaddix/86a06c5dc309d08580a018c66354a056/raw/f58e82c9abfa46a932eb92edbe6b18214141439b/all.txt
```

### domlink:
https://github.com/vysec/DomLink

### masscan :
```
masscan -p1-65535 -iL $TARGET_LIST --max-rate 100000 -oG $TARGET_OUTPUT
```

### waffinder:
wafwoof (url>

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
### Virustotal:
```
python virustotal_subdomain_enum.py <host> 20
```

### Spaces finder:
```
https://github.com/appsecco/spaces-finder
python3 spaces_finder.py -l sample_spaces.txt -g interesting_keywords.txt -D -m 500000 -t 2
```

### Blog for api keys :
```
https://danielmiessler.com/blog/apis-2fas-achilles-heel/
```

### Code repos for recon in github:
```
".delloite.com"+"password"

"delete the private ssh"

"SQL injection vuln in data.views"

"Multiple XSS vulnerabilities"
```

### Mass cloning on github : 
```
https://github.com/mazen160/GithubCloner
```

### Static code analysis :
#### Brakeman for Ruby :
https://brakemanscanner.org/

#### Bandit for python:
https://github.com/openstack/bandit

#### Find secrets in code : 
- API and key. (Get some more endpoints and find API keys.)
- token
- secret
- vulnerable
- http://

#### Find juicy information in source code :
https://github.com/dxa4481/truffleHog : 
```
truffleHog --regex --entropy=False https://github.com/dxa4481/truffleHog.git
```
https://github.com/anshumanbh/git-all-secrets

### Collection of github dorks :
https://github.com/techgaun/github-dorks/blob/master/github-dorks.txt

### Tool to run github dorks against a repo : 
https://github.com/techgaun/github-dorks

### meg:
```
meg <endpoint> <host>
meg / https://edoverflow.com

sed 's#^#http://#g' list-of-hosts > output

meg / https://edoverflow.com out-edoverflow/
```

### Discover interesting files on web :
```
meg -s 200 \
  lists/php targets-all \
  out-php/ 2> /dev/null
```

### Android apps recon :
Get the file from https://apkpure.com/ or  /data/app/[PACKAGE NAME]/base.apk

Check if packet capture shows any weird data
If you see empty data to port 443, then SSL pinning is possible.

If the app is not making any connection to server or burp isnt catching any response,
either its ssl pinning or non-http traffic.

#### SSL Trustkiller : 
https://github.com/iSECPartners/Android-SSL-TrustKiller

#### SSL unpinning :
https://github.com/ac-pm/SSLUnpinning_Xposed

#### Bypassing ssl pinning :
https://blog.it-securityguard.com/the-stony-path-of-android-%F0%9F%A4%96-bug-bounty-bypassing-certificate-pinning/

Decompile app using apktool and dex2jar

Use smali code for tool analysis and java code to read the stuff found in smali code.
```
\.json or http(s)?;//

grep -hnrE "\.json" /Users/kali/smali/
```
Use linkfinder to find all urls in the smali code.

#### oneliner bash to decompile and linkfinder :
```
apktool d app.apk; cd app;mkdir collection; find . -name \*.smali -exec sh -c 'cp "$1" collection/$(head /dev/urandom | md5 | cut -d" " -f1).smali' _ {} \;; linkfinder.py -i 'collection/*.smali' -o cli
```

To narrow this down, use regex option(-r) :
```
linkfinder.py -i 'collection/*.smali' -o cli -r '\.json'
```
Check the /assets and /res/raw

#### Check for recon tricks :
https://www.youtube.com/watch?v=OLgmPxTHLuY

Look in smali and dex code for invoke-static and iget-object for leaking phone numbers and sms.
verify_phone.json

### Hunting subdomain in FDNS dataset : 
```
curl --silent -L https://opendata.rapid7.com/sonar.fdns_v2/2018-04-21-1524297601-fdns_any.json.gz | pigz -dc | head -n 10 | jq .
cat 2018-04-21-1524297601-fdns_any.json.gz | pigz -dc | grep "\.example\.com" | jq .name
```
https://opendata.rapid7.com/about/

### Conduct reconnaissance using opendata7 and aws :
```
aws s3 ls s3://rapid7-opendata/ --no-sign-request
```
https://www.rapid7.com/blog/post/2018/10/16/how-to-conduct-dns-reconnaissance-for-02-using-rapid7-open-data-and-aws/

### Bucketfinder :
```
https://digi.ninja/projects/bucket_finder.php
./bucket_finder.rb url
```

#### Awscli :
```
aws s3 ls s3://hackerone-attachment

aws s3 mv test.txt s3://hackerone-attachments
```

### Google dorks :
#### Google dorks for sensitive files and urls
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

#### Google dorks for s3 buckets:
```
site:s3.amazonaws.com file:pdf
site:s3.amazonaws.com password
```

### Nuclei project
```
nuclei -l test.txt -t ../nuclei-templates/ -o output.txt
```
