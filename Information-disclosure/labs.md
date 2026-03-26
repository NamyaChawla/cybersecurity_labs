**Step 1 — Check robots.txt**
        Visit: target.com/robots.txt
        Note every disallowed path

**Step 2 — View page source**
        View Page Source
        Search for: <!--   password   key   secret   TODO   admin

**Step 3 — Check response headers**
        Open DevTools (F12) → Network tab
        Click any request → Headers
        Look at: Server, X-Powered-By, X-AspNet-Version

**Step 4 — Trigger error messages**
        Enter unexpected input: single quote ', very long strings, special chars
        Look for stack traces or verbose errors

**Step 5 — Check for directory listing**
        Visit common paths: /backup/, /files/, /uploads/, /admin/, /logs/

**Step 6 — Look for backup files**
        Try: config.php.bak, .env, web.config, .git/config

**Step 7 — Use Burp Suite**
        Spider the site, check all responses for hidden info

## Labs
1) Information disclosure in error messages
Open any product page → modify the productId parameter in the URL to something invalid like 'test123' → app throws a verbose error → error reveals the framework name and version number → submit it
2) Information disclosure on debug page
View page source → find a comment containing a link to /cgi-bin/phpinfo.php → open that URL → page shows full PHP debug info → find the SECRET_KEY environment variable → submit it
3) Source code disclosure via backup files
Open /robots.txt → find a disallowed path → navigate to that path → browser downloads the backup file → open it → find the hardcoded database password → submit it
4) Authentication bypass via information disclosure
Try to access /admin → get blocked → send that request to Repeater → change the method to **TRACE** → response reveals a custom header that the server uses for IP checking → add that header with value 127.0.0.1 to a GET /admin request → access granted → delete user carlos  
5) Information disclosure in version control history
Go to /.git in the URL → use wget to download the exposed /.git directory from the server → reconstruct the repo locally → use git log --oneline and git show to inspect commits → find the admin password in a previous commit → login
