# Joomla Scan
A free and open source software to find the components installed in Joomla CMS, built out of the ashes of Joomscan.

# Features
* Scanning the Joomla CMS sites in search of components/extensions (database of more than 600 components);
* Locate the browsable folders of component (Index of ...);
* Locate the components disabled or protected
* Locate each file useful to identify the version of a components (Readme, Manifest, License, Changelog)
* Locate the robots.txt file or error_log file
* Supports HTTP or HTTPS connections
* Connection timeout

# Usage

usage: python joomlascan.py [-h] [-u URL] [-t THREADS] [-v]

optional arguments:
``````
    -h, --help              show this help message and exit
 
    -u URL, --url URL       The Joomla URL/domain to scan.
    -t THREADS, --threads   THREADS
                            The number of threads to use when multi-threading
                            requests (default: 10).
    -v, --version           show program's version number and exit
``````
# Screenshot
![[Pasted image 20220218030339.png]]
# Requirements
* Python
* beautifulsoup4 (To install this library from terminal type: $ sudo easy_install beautifulsoup4 or $ sudo pip install beautifulsoup4)

