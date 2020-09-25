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

