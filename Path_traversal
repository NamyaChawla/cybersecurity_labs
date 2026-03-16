## What is Directory/Path traversal?
Manipulating file path inputs to access files/directories outside the intended folder on the server

## Why does it matter?
Allows an attacker to read sensitive files without authentication
- files like credentials, config files, source code, etc

## How does it work?
Application sometimes load files based on user input, like: https://xyz.com/loadImage?filename=abc.jpg
Here, the server reads: /var/www/images/abc..jpg
**an attacker can traverse the file using ../**
somwthing like https:/xyz.com/loadImage?filename=../../../etc/passwd
now, server will read /etc/passwd which contains usernames

**Some developers block **../**
##Common bypass
- **if they strip ../**: use ....//
- **url decodes input**: %2e%2e%2f
- **double url encoding**: %252e%252e%252f
- **absolute path**: /etc/passwd
- **file extension**: ../../../etc/passwd%00.jpg

## What can be accessed?
- **/etc/passwd**: usernames
- **/etc/shadow**: hashed passwords
- **web.config**: app configuration, credentials
- **config.php**: database credentials
- **id_rsa**: Private SSH key
- **/proc/self/environ**: environment variables

## Prevention
- Never pass user input directly to filesystem APIs
- Validate input against a whitelist of allowed filenames
- After resolvinga path, ensure that it starts with the expected base directory
- Reject input containing **../** or any path seperator
