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

