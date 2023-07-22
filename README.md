# Python-ARP-Spoof-Detector
Create a program that can detect active ARP Spoofing attacks on host machines.
### A company suspects an On-Path attack is under way.
- Verify if a station is being ARP Spoofed
- Use Python to perform the verification

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

## Steps

ARP Table Extraction > The ARP table can be accessed via the OS library. Its lines should be extracted and saved.

Address Dictionary Creation > Filter the extracted data and save it to a dictionary for easy use.

Duplication Check > Check if a MAC address exists more than once in the system. If so, print a notification. 

Logging > The program must log ARP Spoofing activity by creating a log file that includes time-related data (when the activity occurred, for how long, etc.)

Ensure Correct Functionality > Make sure the program is divided into functions, the variables have logical names, and the program has an overall logical flow.
