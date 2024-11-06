# Penetration Testing Report

**Date:** 10/19/2024  
**Prepared by:** Babatunde Dallas  
**Client:** Robocat Industries  
**Suspect:** Alyssa P. Hacker  
**Incident:** Possible data exfiltration from Robocat Industries  

## Tools Used
- **WinRAR:** For ZIP file extraction and analysis.
- **Wireshark:** For packet capture analysis.
- **CyberChef:** For decoding and extracting data from encoded network payloads.

## Overview
This report documents the detailed process of analyzing a network trace to uncover unauthorized data access involving HTTP communication, authentication protocols, and potential data exfiltration.

## Goals and Findings

1. **Can you discover what she was doing?**  
   The suspect logged in on Tue, 05 Apr 2022 06:57:46 GMT from the IP host `10.0.1.99:8989` using the Mozilla Firefox browser. She accessed multiple pages, including `/canonical.html` and `/success.txt?ipv4`.

2. **Any chance that you can find evidence of it and what she might have taken from our servers?**  
   The suspect requested and downloaded a file named `CATastrophic-initial-design-ideas.zip` using HTTP 1.1.  
   - **catzilla.jpeg** (550 KB) - Created on 04/05/2022 at 2:13 AM  
   - **mech.jpeg** (60.0 KB) - Created on 04/05/2022 at 2:10 AM  

3. **How did she even do it?**  
   The suspect logged in using the following credentials:  
   - **Username:** alyssa1337  
   - **Password:** correcthorsebatterystaple  

   She downloaded a file named `CATastrophic-initial-design-ideas.zip` (624 KB) from the server (IP: 172.16.204.133) via a VMware machine (IP: 10.0.1.99). The file was successfully transferred to a destination IP `85.221.77.19`.

4. **Can you discover if she sent the data somewhere to a third party?**  
   Yes, evidence shows the suspect sent the file to a contact named Bob with the email address “totallynotaspy@mailinator.com.”

## Report Details

The investigation began by extracting a `.pcap` file and analyzing it in Wireshark. I searched for GET/POST requests using `http.request.method == POST` and `http.request.method == GET` to locate any file transfers. The network trace revealed an HTTP request from IP `10.0.1.99:8989` that retrieved the file `CATastrophic-initial-design-ideas.zip`.

Upon confirming the GET request, I exported HTTP objects from Wireshark and sorted them by size. The file `CATastrophic-initial-design-ideas.zip` was the largest (624 KB). After downloading the file, I extracted it and found images (Appendix 1).

By following the TCP Stream, I discovered the suspect used Mozilla Firefox to download the file. The authorization used for the download was encoded, but I decoded it with CyberChef, revealing the suspect’s login credentials. The timestamps from the TCP Stream confirmed the activity took place on Tue, 05 Apr 2022 06:57:46 GMT (Appendix 2 and 3).

I also analyzed possible data exfiltration using `http.response.code == 200`, confirming that the file was successfully transferred. I filtered the TCP stream for `tcp.stream eq 247` to trace the file request (Appendix 5).

While reviewing the network trace, I found references to a GitHub repository and `protomail.com`, an encrypted email service (Appendix 6 & 7). I used the suspect’s credentials to log in to the ProtonMail account and found a sent message in the "Sent" folder. The suspect had sent the design ZIP file to Bob at `totallynotaspy@mailinator.com` on Tuesday, April 5th, 2022, at 2:00 AM (Appendix 8).

## Appendix

### Appendix 1: Extracted images from `CATastrophic-initial-design-ideas.zip`
<img width="638" alt="image" src="https://github.com/user-attachments/assets/17a9bfb3-e351-46ea-9897-7b2a2b65113c">


### Appendix 2: TCP Stream showing file access timestamp
<img width="635" alt="image" src="https://github.com/user-attachments/assets/5511ab83-25da-4186-91f3-5cbe9bbb748a">


### Appendix 3: Decoded basic authorization for suspect login
<img width="715" alt="image" src="https://github.com/user-attachments/assets/77b63a41-c103-428a-acbd-67d90d9d7e45">


### Appendix 4: HTTP 200 OK response for file transfer
<img width="696" alt="image" src="https://github.com/user-attachments/assets/907b78ee-df6f-4ee6-9887-15f8336ca8fd">


### Appendix 5: Detailed TCP Stream analysis of the file request
<img width="686" alt="image" src="https://github.com/user-attachments/assets/7cd84573-2fb8-4033-815a-5c9ced32f3ab">

### Appendix 6: Reference to GitHub repository
<img width="675" alt="image" src="https://github.com/user-attachments/assets/46daf74b-1459-42f2-8821-590565922def">


### Appendix 7: Reference to protomail.com
<img width="684" alt="image" src="https://github.com/user-attachments/assets/4be090f2-cdc1-463c-93d9-3ccedbfa2f59">


### Appendix 8: Email sent to Bob
<img width="723" alt="image" src="https://github.com/user-attachments/assets/b1cffa72-29a4-4f57-b554-35349f1a05b7">


