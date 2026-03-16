## What is information Disclosure?
Accidently revealing sensitive information

## Why does it matter?
It is commonly the first step in a large chain attack

## What kind of information is leaked?
- Software version numbers | Look up known CVEs for that version
- Username/email | Credential stuffing, phishing
- Internal IP address | Map the internal network
- API keys/tokens | Directly access backend files
- Source Code | Find hardcoded passwords, logic flaws 
- Database table/ column names | Craft targeted SQL injection payloads

## Common sources:
**files for web crawlers**
- /robots.txt
- /sitemap.xml
**Dictionary listings**
If dictionary does not have a default index page/file , and dictionary listing is enabled, the dictionary automatically lists the directory contents.
This reveals folder structure and maybe sometimes sensitive files like backup, logs, temp files, etc.
**Developer comments**
Usually removed before deployment, if missed, they may leak sesitive information
**Error message**
input expectations, file paths, backend logic, database/server type and version
**Debugging data**
Debug logs used during developmenr can leak session variable values, backend hostnames, credentials, file paths, encryption keys, etc
**User account pages**
Access control vulnerability
**Source code disclosure**
- exposed via backend files
- client-side JS(API keys, end points)
- comment out code
**Insecure configuration**
Developers forget to disable debugging option while using 3rd party technologies
**Version control history**
/.git, /.sun, ./hg stores history, which may backfire incase developers leave sensitive information and then remove it in next commit

##Prevention
- Developers need to be aware of what information is sensitive
- Using generic error messages
- Dissable debuggibg features in production
- Strip developer comments
- Being aware of 3rd part technologies and their permissions
