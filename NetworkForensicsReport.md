# Network Forensic Analysis Report


## Time Thieves 
You must inspect your traffic capture to answer the following questions:

## Wireshark Filters Used:

`ip.addr == 10.6.12.0/24`

`ip.addr == 10.6.12.0/24 && http.request.method == GET`

`ip.src == 10.6.12.203`

## Questions 

### 1. What is the domain name of the users' custom site?

Domain Name: `Frank-n-Ted-DC. frank-n-ted.com`
Filter used: `ip.addr == 10.6.12.0/24`

![01_findDomainName](https://raw.githubusercontent.com/yffenim/final_project/main/NetworkForensicsImages/01_findDomainName.png)

###2. What is the IP address of the Domain Controller (DC) of the AD network?

IP Address: `10.6.12.12` for `Frank-n-Ted-DC. frank-n-ted.com`
Filter used: `ip.addr == 10.6.12.0/24`

![02_findDC_IPaddress](https://raw.githubusercontent.com/yffenim/final_project/main/NetworkForensicsImages/02_findDC_IPaddress.png)

### 3. What is the name of the malware downloaded to the 10.6.12.203 machine?

Malware file name: `june11.dll`
Filter used: `ip.addr == 10.6.12.0/24 && http.request.method == GET`

![04_findDLL1](https://raw.githubusercontent.com/yffenim/final_project/main/NetworkForensicsImages/04_findDLL1.png)
![05_exportDLL]()

### 4. Upload the file to [VirusTotal.com](https://www.virustotal.com/gui/). 

![06_VT1](https://raw.githubusercontent.com/yffenim/final_project/main/NetworkForensicsImages/05_exportDLL.png)
![07_VT2](https://raw.githubusercontent.com/yffenim/final_project/main/NetworkForensicsImages/06_VT1.png)
![08_VT3](https://github.com/yffenim/final_project/blob/main/NetworkForensicsImages/08_VT3.png)

### 5. What kind of malware is this classified as?

This is a Trojan malware, often used for the staging aspect of the Cyberkill chain during an attack.

---

## Vulnerable Windows Machine

### 1. Find the following information about the infected Windows machine:

HOST NAME: `ROTTERDAM-PC`
IP ADDRESS: `172.16.4.205`
MAC ADDRESS: `00:59:07:b0:63:a4`
Filter used: `ip.addr == 172.16.4.0/24`

Finding the Domain name by filtering by network: 

![01_DomainName](https://raw.githubusercontent.com/yffenim/final_project/main/NetworkForensicsImages/01_DomainName.png)
    
### 2. What is the username of the Windows user whose computer is infected?

Checking for clues on security vulnerabilities automated detected by Wireshark so we can narrow down our search:

![02_WhyHTTP](https://raw.githubusercontent.com/yffenim/final_project/main/NetworkForensicsImages/02_WhyHTTP.png)

Filtering for `HTTP` `POST` traffic with: `ip.addr == 172.16.4.0/24 && http.request.method == POST`

![03_HTTP_Post_By_Filter](https://raw.githubusercontent.com/yffenim/final_project/main/NetworkForensicsImages/03_HTTP_Post_By_Filter.png)

This shows that the IP address `172.16.4.205` has made repeated `POST` requests to a suspicious URL ending with `/fakeurl.htm`

Searching the internet confirms that this is likely malware  https://www.malware-traffic-analysis.net/2018/07/05/index.html

In order to find the username connected to this ip, we add `CNameString` as a column or as a filter. Here shown added as a column for efficient visualization:

![04_CName](https://raw.githubusercontent.com/yffenim/final_project/main/NetworkForensicsImages/04_CName.png)

**The name in question is: matthijs.devries**

### 3. What are the IP addresses used in the actual infection traffic?

To find this, click on the Statistics Tab > Select Conversation > Select IPv4 > Sort Packets based on high to low:

The infected IP address can be seen predominantly connecting to `185.243.115.84`. Also of interest is: `166.62.111.64`

![05_InfectedTraffic](https://raw.githubusercontent.com/yffenim/final_project/main/NetworkForensicsImages/05_InfectedTraffic.png)

![06_InfectedTraffic](https://raw.githubusercontent.com/yffenim/final_project/main/NetworkForensicsImages/06_InfectedTraffic.png)

Following-up on the the network traffic between the infected machine and `185.243.115.84` by right clicking on the conversation we're interested in > Apply as Filter > Selected > A <-> B or by using the filter: `ip.addr == 172.16.4.205 && ip.addr == 166.62.111.64`

![07_InfectedTrafficConversation](https://raw.githubusercontent.com/yffenim/final_project/main/NetworkForensicsImages/07_InfectedTrafficConversation.png)

We can check the destination of these suspect `POST` requests (`http://green.mattingsolutions.co/`) with VirusTotal, we can see that it is flagged as malware:

![11_VTforPT2]()

### 4. As a bonus, retrieve the desktop background of the Windows host.

We can see that there's a suspicious POST request with `empty.gif` filenamme. We can export Wireshark `HTTP` objects by clicking File > Export Objects > HTTP and then saving the object with the `.gif` extension. 

We choose which object to export based on largest size as the smaller ones did not work and we assumed the largest ones contained the full file data. 

![08_FindDesktopImage](https://raw.githubusercontent.com/yffenim/final_project/main/NetworkForensicsImages/08_FindDesktopImage.png)

We determined that the image object is common seagull:

![09_DesktopImage](https://raw.githubusercontent.com/yffenim/final_project/main/NetworkForensicsImages/09_DesktopImage.gif)

![10_Seagull](https://raw.githubusercontent.com/yffenim/final_project/main/NetworkForensicsImages/10_Seagull.png)

