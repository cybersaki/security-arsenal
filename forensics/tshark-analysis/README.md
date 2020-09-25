# TSHARK analysis 

## 1 ) Tshark unleashed
* Wireshark
	* Packet analyzer
	* Great for microscopic analysis
	* Difficult to get macroscopic summaries

* Tshark
	* Companion tool: same parsing enginer
	* Command line based
	* Great for automation: very underutilized in this regard.

#### Tshark basics :
* Automating activities with Tshark :
	* Parsing
	* Filtering
	* Decoding
	* Field extraction
	* Summarization
	* Exporting of results
	* Pipelining with other tools
	* Python automation

#### Lab setup :
OS + Wireshark + Tshark
```
apt-get update
sudo apt-cache madison wireshark //(wireshark version)
sudo apt-cache madison tshark //(tshark version)

sudo apt-add-repository ppa:wireshark-dev/stable

sudo apt install wireshark tshark
```

## 2 ) Capturing packets: Interfaces and promiscuous mode
#### List interfaces:
```
tshark -D

```

#### To run tshark without arguments :
```
tshark
```
Note : It will run the first active interface. It will skip loopback interface.

#### To ensure non-superusers be able to capture packets and select yes :
```
sudo dpkg-reconfigure wireshark-common 
```

#### Add current user to root group :
```
usermod -a -G wireshark $USER
```

#### Run tshark on any available user interface :
```
tshark -i any

ping google.com(just for example)
```
Tshark terminal will display the ICMP and DNS request and response packets.

#### Promiscuous mode:
* Default behaviour:
	* Only unicast,broadcast and chose multicast packets.

* Promiscuous mode:
	* All received packets
	* Network topology needs to make sense

* Monitor mode:
	* Applies to 802.11 interfaces only.

Note 1: To run promiscuous mode, we need a good network topology. 
Note 2: Monitor mode is promiscuous mode, but for wifi.

#### For example :
```
ifconfig ens33
```
It will display UP,BROADCAST,RUNNING,MULTICAST

#### To enable promiscuous mode:
```
sudo ifconfig ens33 promisc
```
Now it will display UP,BROADCAST,RUNNING,PROMISC,MULTICAST

#### Now run tshark to check the traffic of particular mode :
```
tshark -i ens33
```

## 3 ) Writing and exporting packet data :
Run ping in another terminal. Now we have to save these packets in a captured file.

#### Write an export :
```
tshark -i ens33 20 -w icmp.pcap
```
Note 1: 20 is the packet count. If no count is mentioned, then it will keep capturing.
Note 2: -w is for writing the export.

#### To read a captured file :
```
tshark -r icmp.pcap
```

#### To see the details of the packet(verbose) :
```
tshark -r icmp.pcap -c 1 -V
```

#### Much more details of the header(For each packet wise) :
```
tshark -T x //(Basic)

tshark -r icmp.pcap -T psml | less //(Actual one)

tshark -r icmp.pcap -T pdml //(Very delicate parsed actual data for each packet)

tshark -r icmp.pcap -T pdml > icmp.pdml //(Save the pdml file)

tshark -r icmp.pcap -T -c 1 json | less
```
Note : Code beautify can be used to read the pdml file to readable format.

#### For elasticsearch engineers:
```
tshark -r icmp.pcap -T -c 1 -T ek
```
Note : This dump can be fed into elasticsearch for analysis.

## 4 ) PDML-Packets to HTML :
First capture a few packets and output using PDML:
```
tshark -i ens33 -w icmp.pcap 

tshark -r icmp.pcap -T pdml > icmp.xml
```

Now we need to convert the pcap to html. 
Wireshark already has a pdml2html.xsl to convert.
For tshark we need xsltproc.

#### Installation and converting into html:
```
sudo apt install xsltproc

xsltproc /usr/share/wireshark/pdml2html.xsl icmp.xml > icmp.html
```

#### Open the file in firefox
```
firefox icmp.html &
```

## 5 ) Capture and display filter :
* Capture filter:
	* Low level filter applied by the packet capturing library.
	* This is enforced using BPF(Berkeley packet filter) during sniffing.
	* Ensures only packets we are interested in are captured.
	* Greatly reduces the load for Tshark/Wireshark/Analysis tool.

#### Capture only TCP port 80 traffic(example) :
```
tshark -i ens33 -c 10 -f "tcp port 80" -w tcp80.pcap
```

* Display filters:
	* Allows us to view a subsection of the captured packets based on a filter expression.
	* Helps in zeroing down on interesting packets
	* Display and capture filters have different syntax
	* Display filter expressions are more granular

Now in wireshark, check the requests passing. 
We find that http.request.method =="GET" is most traversed if you browse.

#### So validating the same filter :
```
tshark -r tcp80.pcap -Y 'http.request.method =="GET"' | less
// -Y is single pass display filter
```

## 6 ) Packet field extraction :
There are two files provided. First considering the traffic pcap.

#### First monitoring the file Wifi_traffic.pcap:
```
wireshark Wifi_traffic.pcap
```

The point is to extract the SSID="Security tube" from every beacon frame.

This tag : wlanc.fc.type_subtype == 0x0008 has common SSID field in all the packets :
```
tshark -r Wifi_traffic.pcap -Y ' wlanc.fc.type_subtype == 0x0008 '
```

#### Now to extract the SSID field from the tags :
```
tshark -r Wifi_traffic.pcap -Y ' wlanc.fc.type_subtype == 0x0008 ' -Tfields -e wlan.ssid | less
```
Note 1 : -Tfields is to export fields
Note 1 : -e mention the field

Some null values will be there. To consider non-null SSID's, Some SSIDs have less tag length. So we have to check them in wireshark for Tag length to neglect.
Apart from that, we can extract the bssid of the SSID also.
```
tshark -r Wifi_traffic.pcap -Y ' wlanc.fc.type_subtype == 0x0008 ' -Tfields -e wlan.ssid -e wlan.bssid | less
```

Now to monitor the HTTP traffic:
```
wireshark HTTP_traffic.pcap &
```

#### To extract the URI :
```
tshark -r HTTP_traffic.pcap -Y 'http.request.method == "GET"' -Tfields -e http.request.uri | less
```

#### To extract the URI along with host name :
```
tshark -r HTTP_traffic.pcap -Y 'http.request.method == "GET"' -Tfields -e http.host -e http.request.uri | less
```

To display headers for the extracted packets:
```
tshark -r HTTP_traffic.pcap -Y 'http.request.method == "GET"' -Tfields -e http.host -e http.request.uri -E header=y | less
```

## 7 ) Packet extraction + sort + uniq :

Select type/subtype beacon and copy the filter from wireshark as previous.

```
tshark -r WiFi_traffic.pcap -Y 'wlan.fc.type_subtype == 0x0008'-Tfields -e wlan.ssid -e wlan.bssid | less
```

Now to get the unique set of SSIDs, sort is used.
```
tshark -r WiFi_traffic.pcap -Y 'wlan.fc.type_subtype == 0x0008'-Tfields -e wlan.ssid -e wlan.bssid | sort | less
```

To get only the unique output :
```
tshark -r WiFi_traffic.pcap -Y 'wlan.fc.type_subtype == 0x0008'-Tfields -e wlan.ssid -e wlan.bssid | sort | uniq
```

Get the current channel of the packets:
```
tshark -r WiFi_traffic.pcap -Y 'wlan.fc.type_subtype == 0x0008'-Tfields -e wlan.ssid -e wlan.bssid -e wlan.ds.current_channel | sort | uniq
```

## 8 ) Tshark summaries - Protcols, summaries and read filters :

