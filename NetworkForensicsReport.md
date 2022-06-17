# Network Forensic Analysis Report


## Time Thieves 
You must inspect your traffic capture to answer the following questions:

## Wireshark Filters Used:

`ip.addr == 10.6.12.0/24`

`ip.addr == 10.6.12.0/24 && http.request.method == GET`

`ip.src == 10.6.12.203`

## Questions 

1. What is the domain name of the users' custom site?

Domain Name: `Frank-n-Ted-DC. frank-n-ted.com`
Filter used: `ip.addr == 10.6.12.0/24`

![01_findDomainName]()

2. What is the IP address of the Domain Controller (DC) of the AD network?

IP Address: `10.6.12.12` for `Frank-n-Ted-DC. frank-n-ted.com`
Filter used: `ip.addr == 10.6.12.0/24`

![02_findDC_IPaddress]()

3. What is the name of the malware downloaded to the 10.6.12.203 machine?

Malware file name: `june11.dll`
Filter used: `ip.addr == 10.6.12.0/24 && http.request.method == GET`

![03_findDLL]()
![04_findDLL1]()
![05_exportDLL]()

4. Upload the file to [VirusTotal.com](https://www.virustotal.com/gui/). 

![06_VT1]()
![07_VT2]()
![08_VT3]()

5. What kind of malware is this classified as?

This is a Trojan malware, often used for the staging aspect of the Cyberkill chain during an attack.


---

## Vulnerable Windows Machine

1. Find the following information about the infected Windows machine:
    - Host name
    - IP address
    - MAC address
    
2. What is the username of the Windows user whose computer is infected?
3. What are the IP addresses used in the actual infection traffic?
4. As a bonus, retrieve the desktop background of the Windows host.

