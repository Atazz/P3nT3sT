# Footprinting the website 

1. First manually clicking the website through and see what parts are accessible from unauthenticated users and which are for the authenticated users. 
2. Scan website with nmap to see if other http(s) services are running or some backend/API is running.
3. Open Burp and create a new project - Do not start active/passive scans yet. Start clicking around and see which request are sent and what is received. 
4. Check LocalStorage, SessionStorage, Cookies, IndexDB and headers.
5. Start passive scan and look for anything interesting like technologies, website version or other information leaks about the website.