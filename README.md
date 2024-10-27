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
   There is no evidence that the suspect sent the file to someone else over the internet.

## Report Details

The investigation began by extracting a `.pcap` file and analyzing it in Wireshark. I searched for GET/POST requests using `http.request.method == POST` and `http.request.method == GET` to locate any file transfers. The suspect used the host IP `10.0.1.99:8989` to get the file `CATastrophic-initial-design-ideas.zip`.

Upon confirming the GET request, I exported HTTP objects from Wireshark and sorted them by size. The file `CATastrophic-initial-design-ideas.zip` was the largest (624 KB). After downloading the file, I extracted it and found images (Appendix 1).

By following the TCP Stream, I discovered the suspect used Mozilla Firefox to download the file. The authorization used for the download was encoded, but I decoded it with CyberChef, revealing the suspect’s login credentials. The timestamps from the TCP Stream confirmed the activity took place on Tue, 05 Apr 2022 06:57:46 GMT (Appendix 2 and 3).

I also analyzed possible data exfiltration using `http.response.code == 200`, confirming that the file was successfully transferred. I filtered the TCP stream for `tcp.stream eq 247` to trace the file request (Appendix 5).

Finally, I filtered POST requests to check for any file uploads but found no evidence of further exfiltration attempts (Appendix 6 & 7).
 
## Appendix

### Appendix 1: Extracted images from `CATastrophic-initial-design-ideas.zip`
![image](https://github.com/user-attachments/assets/26099cac-84d7-49a0-9b27-ab50da275e2d)


### Appendix 2: TCP Stream showing file access timestamp
![image](https://github.com/user-attachments/assets/7222d4a7-967f-440c-a3fa-d44d7ba32b6d)


### Appendix 3: Decoded basic authorization for suspect login
![image](https://github.com/user-attachments/assets/50bcca87-ea44-4550-800b-129304f8164a)


### Appendix 4: HTTP 200 OK response for file transfer
![image](https://github.com/user-attachments/assets/c12f93b9-0f50-4daf-8dea-7cbf31a3bcd5)


### Appendix 5: Detailed TCP Stream analysis of the file request
![image](https://github.com/user-attachments/assets/44ddc848-8155-433e-81e6-f3812696770d)


### Appendix 6 & 7: POST request analysis for potential file uploads
![image](https://github.com/user-attachments/assets/13d0f2a9-e23a-4eda-8b03-ab0b6db3cd8e)
![image](https://github.com/user-attachments/assets/998f880a-0ddb-4109-850b-df81dce0c88b)
![image](https://github.com/user-attachments/assets/81231f88-3a6b-418a-b623-a87d5fe78466)



