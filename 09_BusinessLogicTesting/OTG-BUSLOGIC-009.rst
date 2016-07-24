============================================================================================
OTG-BUSLOGIC-009 (악성 파일 형식 업로드 테스트)
============================================================================================

|

개요
============================================================================================

수많은 어플리케이션 비즈니스 프로세스는 데이터 및 정보 업로드를 허가합니다.

We regularly check the validity and security of text but accepting files can introduce even more risk. To reduce the risk we may only accept certain file extensions, but attackers are able to encapsulate malicious code into inert file types. Testing for malicious files verifies that the application/system is able to correctly protect against attackers uploading malicious files. 

Vulnerabilities related to the uploading of malicious files is unique in that these "malicious" files can easily be rejected through including business logic that will scan files during the upload process and reject those perceived as malicious. Additionally, this is different from uploading unexpected files in that while the file type may be accepted the file may still be malicious to the system. 

Finally, "malicious" means different things to different systems, for example Malicious files that may exploit SQL server vulnerabilities may not be considered a "malicious" to a main frame flat file environment. 

The application may allow the upload of malicious files that include exploits or shellcode without submitting them to malicious file scanning. Malicious files could be detected and stopped at various points of the application architecture such as: IPS/IDS, application server anti-virus software or anti-virus scanning by application as files are uploaded (perhaps offloading the scanning using SCAP). 

|

예제
============================================================================================

|

Suppose a picture sharing application allows users to upload their .gif or .jpg graphic files to the web site. What if an attacker is able to upload a PHP shell, or exe file, or virus? The attacker may then upload the file that may be saved on the system and the virus may spread itself or through remote processes exes or shell code can be executed. 

|

테스트 방법
============================================================================================

일반 테스트 방법
-----------------------------------------------------------------------------------------

- Review the project documentation and use exploratory testing looking at the application/system to identify what constitutes and "malicious" file in your environment. 
- Develop or acquire a known "malicious" file. 
- Try to upload the malicious file to the application/system and verify that it is correctly rejected. 
- If multiple files can be uploaded at once, there must be tests in place to verify that each file is properly evaluated. 

구체적인 테스트 방법 1 
-----------------------------------------------------------------------------------------

- Using the Metasploit payload generation functionality generates a shellcode as a Windows executable using the Metasploit "msfpayload" command. 
- Submit the executable via the application's upload functionality and see if it is accepted or properly rejected. 

구체적인 테스트 방법 2
-----------------------------------------------------------------------------------------

- Develop or create a file that should fail the application malware detection process. There are many available on the Internet such as ducklin.htm or ducklin-html.htm. 
- Submit the executable via the application's upload functionality and see if it is accepted or properly rejected. 

구체적인 테스트 방법 3
-----------------------------------------------------------------------------------------

- Set up the intercepting proxy to capture the "valid" request for an accepted file. 
- Send an "invalid" request through with a valid/acceptable file extension and see if the request is accepted or properly rejected. 

|

Related Test Cases 
============================================================================================

- 민감한 정보를 확인하기 위해 파일 확장자 처리 테스트 (OTG-CONFIG-003) 
- 예기치 않은 파일 형식 업로드 테스트 (OTG-BUSLOGIC-008) 

|

Tools 
============================================================================================
 
- Metasploit의 payload 생성 기능
- Intercepting proxy 

|

References 
============================================================================================

- OWASP - Unrestricted File Upload: https://www.owasp.org index.php/Unrestricted_File_Upload 
- Why File Upload Forms are a Major Security Threat: http:/ www.acunetix.com/websitesecurity/upload-forms-threat/ 
- File upload security best practices: Block a malicious file upload: http://www.computerweekly.com/answer/File-uploadsecurity-best-practices-Block-a-malicious-file-upload 
- Overview of Malicious File Upload Attacks: http:/securitymecca.com/article/overview-of-malicious-fileupload-attacks/ 
- Stop people uploading malicious PHP files via forms - http://stackoverflow.com/questions/602539/stop-peopleuploading-malicious-php-files-via-forms 
- How to Tell if a File is Malicious - http://www.techsupportalert.com/content/how-tell-if-filemalicious.htm 
- CWE-434: Unrestricted Upload of File with Dangerous Type - http://cwe.mitre.org/data/definitions/434.html 
- Implementing Secure File Upload - http://infosecauditor.wordpress.com/tag/malicious-fileupload/ 
- Watchful File Upload - http://palizine.plynt.com/issues/2011Apr/file-upload/ 
- Matasploit Generating Payloads - http://www.offensive-security.com/metasploit-unleashed/ Generating_Payloads 
- Project Shellcode . Shellcode Tutorial 9: Generating - Shellcode Using Metasploit http://www.projectshellcode. com/?q=node/29 
- Anti-Malware Test file - http://www.eicar.org/86-0-Intended use.html 

|

Remediation 
============================================================================================

While safeguards such as black or white listing of file extensions, using "Content-Type" from the header, or using a file type recognizer may not always be protections against this type of vulnerability. Every application that accepts files from users must have a mechanism to verify that the uploaded file does not contain malicious code. Uploaded files should never be stored where the users or attackers can directly access them. 

|