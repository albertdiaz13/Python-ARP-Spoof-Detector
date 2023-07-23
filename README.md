# Python-ARP-Spoof-Detector
### A company suspects an On-Path attack is under way.

Create a Python program that verifies active ARP Spoofing attacks on host machines.

## [ARP Tables & Spoofing](https://www.cisco.com/assets/sol/sb/Switches_Emulators_v2_2_015/help/nk_configuring_ip_information19.html)

Address Resolution Protocol (ARP): A network protocol used to determine the MAC address of the next router or device on the path. The ARP table is used to maintain a correlation between each MAC address and its corresponding IP address.

What is Arp Spoofing?

- Mac address impersonation
- Manipulating data in an ARP table
- Commonly used in On-Path attacks
- Identified by MAC address duplication

Use the command `arp -a` on Windows to diplay the ARP table. 

![Clean ARP Table](https://github.com/albertdiaz13/Python-ARP-Spoof-Detector/assets/18507384/074220fe-b59d-483e-8a6b-1afb35811648)

Screenshot of a typical ARP table with no duplicate physical addresses.

## Lab Environment

For this demo, configure a Kali Linux 2019 virtual machine on VirtualBox or your preferred VM software. We also need a target VM such as another Kali instance or Windows VM. Make sure the virtual machines do not share the same IP address, and use the `ping` command to ensure connection between both systems. Check network information on Kali with the `$ ip a` command, or `ipconfig` on Windows.

On the vulnerable machine, install python, pycharm, and [NPCAP](https://npcap.com/), a packet capture library for Windows.

## Steps

+ ARP Table Extraction > The ARP table can be accessed via the OS library. Its lines should be extracted and saved.

+ Address Dictionary Creation > Filter the extracted data and save it to a dictionary for easy use.

+ Duplication Check > Check if a MAC address exists more than once in the system. If so, print a notification. 

+ Logging > The program must log ARP Spoofing activity by creating a log file that includes time-related data.

+ Ensure Correct Functionality > Make sure the program is divided into functions, the variables have logical names, and the program has an overall logical flow.

### Scapy 
Scapy is a tool written in Python that can be used to forge, send, receive, decode, and manipulate packets over the network, as well as create custom packets. It can be used in library form in the Linux OS.

The Scapy library can manage tasks such as network scanning, network discovery, and attack execution.
Let's start by importing the os, datetime, and scapy modules
```python
from scapy.all import *
from datetime import datetime
import os 
```
### Extract the ARP table
```python
# The function accesses the ARP table and extracts its entries.
def arp_table_extraction():
    # Read the ARP table and save it in a list form.
    arp_table = os.popen("arp -a").read()
    arp_table_lines = arp_table.splitlines()
    # Create a dictionary to hold IP addresses with corresponding MAC addresses.
    addresses = {}
    # Iterating over the ARP entries list, filtering unnecessary data and saving IP & MAC addresses
    for line in arp_table_lines:
        if "ff-ff-ff-ff-ff-ff" in line or "ff-ff-ff-ff-ff-ff" in line:
            break
        if arp_table_lines.index(line) > 3:
            ip, mac, _type = line.split()
            addresses[ip] = mac

    indentify_duplication(addresses)
```

### Perform ARP table entry filtration & Locate MAC address duplications
```python

# The function examines the IP & MAC addresses dictionary and checks it for MAC duplication.
def indentify_duplication(addresses):
    # Temporary list to save iterated MAC addresses for later comparison.
    tmp_mac_lst = []
    print("Scanning...")
    time.sleep(3)
    for mac in addresses.values():
        # Terminates the iterations if a MAC duplication was found.
        if mac in tmp_mac_lst:
            print("Finished scanning")
            create_log("Arp Spoofed!\nThe address is:" + mac)
            break
        tmp_mac_lst.append(mac)
```

### Generate logs based on attacks
```python

# This function creates a file and append into it a log of the ARP spoofing event.
def create_log(message):
    print("Generating logs...")
    time.sleep(3)
    date = datetime.now()
    with open("log.txt", "a") as log:
        log.write(message + "\nDate: {}\n\n".format(date))
    print("The event is logged in log.txt")

# A condition to verify that this file is directly executed.
if __name__ == "__main__":
    arp_table_extraction()

# input("\nPress 'Enter' to exit the program")# prevents program from closing upon execution

```
## Testing

To perform ARP table spoofing, some versions of kali can run the `arpspoof` command:

`$ arpspoof -i eth0 -t <target IP> <spoofed IP>`

Alternatively, we can use [ettercap-graphical](https://www.kali.org/tools/ettercap/) a pentesting GUI that comes pre-installed with Kali.

![EttercapGUI](https://github.com/albertdiaz13/Python-ARP-Spoof-Detector/assets/18507384/f7ac2e90-ecc0-4761-96ae-a80042546b8d)


Click on the magnifying glass icon to scan available hosts, then click on the globe icon to begin ARP poisoning..

![ettercapMITM](https://github.com/albertdiaz13/Python-ARP-Spoof-Detector/assets/18507384/4a1e3aaa-0eb0-4c6a-89ab-a18cf517285a)

To determine if the MITM attack was successful, run 'arp -a' on the target machine. If the ARP table displays duplicate Physical Addresses, the ARP spoof was successful.

![ARPSpoofed](https://github.com/albertdiaz13/Python-ARP-Spoof-Detector/assets/18507384/c6c5f5e4-8c55-4cbe-bba1-07f47446c0f4)

## Program Output

Once we confirm the host is compromised, run the Python script and open the output log file. 

`Scanning...`

`Finished scanning`

`Generating logs...`

`The event is logged in log.txt`

`Process finished with exit code 0`

### Log file:

![Arp Spoofed!](https://github.com/albertdiaz13/Python-ARP-Spoof-Detector/assets/18507384/9989ecd2-b5bd-4520-a73c-58aa7c5b18e0)



