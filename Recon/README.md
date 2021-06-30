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
