
# The Ultiamte HTB Cheat Sheet

This is the Ultimate Cheat Sheet for Hack the Box machines as well as some pentesting stuff aswell 
this guide will be formated for the most part in markdown format, the information wont be organised in any particular way 

To identify a new topic look for the large heading, each main heading my include smaller headings underneath in a way to highlight certain information 


# Ping

There are some common parameters that can be used in conjunction with ping to return certian results and do diffrent things they are as follows
(Usage varies slightly between Linux/macOS and Windows)

    Linux/macOS:
    - `-c <count>`  
    → Number of ping requests to send (e.g., `-c 4`)

    - `-i <interval>`  
    → Time between pings in seconds (default: 1 second)

    - `-s <size>`  
    → Size of the packet payload in bytes

    - `-t <ttl>`  
    → Set Time To Live (packet hop limit)

    Windows:
    - `-n <count>`  
    → Number of echo requests to send

    - `-l <size>`  
    → Send buffer size (packet size)

    - `-t`  
    → Ping the target continuously until stopped

    Example Usage:
    --------------
    - ping -c 4 10.10.10.10 # Linux/macOS – Send 4 pings
    - ping -n 4 10.10.10.10 # Windows – Send 4 pings

    Understanding Output:
    ---------------------
    - **Reply from <ip>**: Target is online
    - **Request timed out**: No response (host down, ICMP blocked, or filtered)
    - **Packet loss**: Indicates network issues
    - **Time=<ms>**: Round-trip latency

    Limitations:
    ------------
    - Many servers block or filter ICMP (ping) requests for security reasons.
    - A host not responding to ping doesn't always mean it's offline.


# NMAP 

    What is Nmap?
    -------------
    - **Nmap (Network Mapper)** is a powerful open-source tool used for **network discovery**, **port scanning**, **service enumeration**, and **vulnerability detection**.
    - Sends various types of requests to a target's ports to determine which are open, what services are running, and sometimes their versions and configurations.

    Common Use Cases:
    -----------------
    - Identify open ports and services
    - Detect operating systems and software versions
    - Find misconfigurations or vulnerable services
    - Perform stealth or aggressive scans during penetration tests

    Basic Syntax:
    -------------
    nmap [options] <target>

    Examples:
    ---------
    - nmap 10.10.10.10 → Basic scan
    - nmap -sV 10.10.10.10 → Scan with service/version detection
    - nmap -sC -sV -oN scan.txt 10.10.10.10 → Default scripts, version detection, output to file

    Commonly Used Nmap Flags:
    -------------------------
    - `-p <ports>`  
    → Scan specific ports (e.g., `-p 22,80,443`, or `-p-` for all 65535)

    - `-sS`  
    → SYN scan (stealth scan – fast and common)

    - `-sT`  
    → TCP connect scan (used when SYN scan isn't permitted)

    - `-sV`  
    → Service and version detection (identifies running services on open ports)

    - `-sC`  
    → Run default scripts (equivalent to `--script=default`)

    - `-O`  
    → OS detection

    - `-A`  
    → Aggressive scan (includes `-sC`, `-sV`, `-O`, and traceroute)

    - `-Pn`  
    → Skip host discovery (treat all hosts as online)

    - `-T<0–5>`  
    → Timing template (0 = slowest, 5 = fastest; use `-T4` for fast scans)

    - `--script=<name>`  
    → Use a specific NSE script (e.g., `--script=smb-enum-shares`)

    - `-oN`, `-oX`, `-oG`  
    → Output formats: Normal, XML, Grepable

    - `-v` or `-vv`  
    → Increase verbosity for more scan detail
    
    - '--min-rate=1000'
    - this is used to specify the minimum number of packets that Nmap should send per second; it speeds up the scan the higher the number goes 





    Important Nmap Scripts (HTB Context):
    -------------------------------------
    Used with `--script=<name>` or `-sC` (for default set):
    - `smb-enum-shares`       → Enumerate SMB shares
    - `ftp-anon`              → Check for anonymous FTP access
    - `http-title`            → Get webpage title (quick web recon)
    - `ssh-hostkey`           → Show SSH host keys
    - `vuln`                  → Run vulnerability detection scripts

    Example: Full Port Scan with Service Detection
    ----------------------------------------------
    - nmap -p- -sC -sV -T4 -oN full_scan.txt 10.10.10.10

    Security Notes:
    ---------------
    - May trigger firewalls or intrusion detection systems (especially with `-A` or `-T5`)
    - Always get permission before scanning networks not under your control


# FTP

What is FTP?
    ------------
    - **FTP (File Transfer Protocol)** is a **standard network protocol** used to transfer files between a client and server over a TCP/IP-based network.
    - FTP operates on a **client-server model**, where the client initiates the connection to download or upload files.

    Where FTP is Seen:
    ------------------
    - Default FTP port: **21 (TCP)**
    - During scanning (e.g., with `nmap`), open port 21 indicates an FTP service is available.

    Anonymous FTP Access:
    ---------------------
    - Some FTP servers allow **anonymous login** for public access.
    Common credentials:
    - **Username**: `anonymous`
    - **Password**: `anno123` (or any email address)
    - If anonymous login is allowed, users may be able to:
    - Browse directories
    - Download files
    - Sometimes upload files (less common)

    FTP Encryption Options:
    -----------------------
    - By default, FTP transmits **usernames, passwords, and files in plaintext**, making it insecure.
    - Two common methods to secure FTP traffic:
    1. **FTPS** – FTP over SSL/TLS (explicit or implicit encryption)
    2. **SFTP** – FTP-like access over SSH (not actually FTP protocol)

    FTP CLI Usage (Linux):
    ----------------------
    To connect:
        ftp <target-ip>

    Helpful FTP Commands:
    ---------------------
    - `ftp -?`  
    → Shows a list of available commands

    Once connected:
    ---------------
    - `ls` or `dir`      → List directory contents
    - `cd <directory>`   → Change directory
    - `get <filename>`   → Download a file
    - `put <filename>`   → Upload a file
    - `mget *`           → Download multiple files
    - `bye` or `exit`    → Close the session

    Example Session:
    ----------------
    - ftp <target-ip>
    - Name: anonymous
    - Password: anno123
    - ftp> ls
    - ftp> get secret.txt
    - ftp> bye

    Security Notes (HTB Context):
    -----------------------------
    - Plain FTP is often misconfigured and may expose sensitive files.
    - If anonymous login is enabled, it may allow attackers to:
    - Download public or sensitive data
    - Upload malicious files (e.g., web shells in web directories)
    - Passive and active FTP modes affect firewall behavior (can be relevant in restricted environments)

# SMB

 What is SMB?
    ------------
    - **SMB (Server Message Block)** is a **network communication protocol** that provides shared access to:
    - Files
    - Printers
    - Serial ports
    - Networked devices

    - SMB is most commonly found on **Windows-based systems**.

    Where SMB is Seen:
    ------------------
    - Commonly observed on **TCP port 445**, reserved for SMB over TCP.
    - May also use **port 139** when running over NetBIOS (SMB over NetBIOS/NetBT).
    - During scans (e.g., with `nmap`), open ports 445 and/or 139 usually indicate SMB services.

    OSI Layer Info:
    ---------------
    - SMB operates at the **Application and Presentation layers** of the OSI model.
    - Relies on lower-layer protocols (such as TCP/IP) for transport.
    - Microsoft SMB often uses **NetBIOS over TCP/IP (NBT)** for communication on older systems.

    Protocols Used:
    ---------------
    - **Port 445 (TCP)** → Direct SMB over TCP (modern usage)
    - **Port 139 (TCP)** → SMB over NetBIOS (legacy support)
    - Associated protocols:
    - **NetBIOS Name Service (NBNS)** → Port 137
    - **NetBIOS Datagram Service** → Port 138

    Important SMB Versions:
    -----------------------
    - **SMBv1**: Legacy, insecure, vulnerable to exploits like **EternalBlue** (used in WannaCry, etc.)
    - **SMBv2/v3**: Improved performance and security; supported on modern Windows systems.

    Common Tools for SMB Enumeration:
    ---------------------------------
    - `smbclient` → Command-line SMB access tool (like FTP)
    - `smbmap` → Lists shares and permissions
    - `enum4linux` → Full enumeration suite (users, shares, OS info)
    - `crackmapexec` → Automates credential checks, enumeration, and attacks
    - `nmap` → With scripts like `smb-enum-shares`, `smb-os-discovery`, `smb-vuln*`

    Example Nmap Scan:
    ------------------
    - nmap -p 445,139 --script smb-enum-shares,smb-enum-users <target-ip>

    Security Notes (HTB Context):
    -----------------------------
    - SMBv1 is frequently left enabled on misconfigured or legacy systems → major attack surface.
    - Anonymous access may allow:
    - Listing of shared folders
    - Reading files
    - Dumping usernames
    - Can be used to relay or capture NTLM hashes (e.g., via Responder or Inveigh).

# Basic Networking

    What is Networking?
    -------------------
    - **Networking** refers to the practice of connecting computers, devices, and services to exchange data.
    - Networks can be local (**LAN**) or wide (**WAN**, like the internet), and consist of hardware (e.g., routers, switches) and protocols that govern communication.

    Key Concept: SMB Shares
    ------------------------
    - In a networked environment, **SMB (Server Message Block)** is commonly used for **file and resource sharing** between devices.
    - A **SMB-enabled storage location** on a network is called a **share**.

    How SMB Sharing Works:
    ----------------------
    - Any client on the network that knows the **IP address or hostname** of the SMB server and has **valid credentials** can attempt to connect to the share.
    - Shares can include:
    - Files and folders
    - Printers
    - Networked services

    Authentication:
    ---------------
    - SMB requires authentication in the form of a **username and password** to access private or protected shares.
    - Authentication is handled at the **application layer** of the OSI model.

    Security Considerations:
    ------------------------
    - Properly configured SMB shares should:
    - Require strong credentials
    - Limit access to specific users or groups
    - Log access attempts for auditing

    Misconfigurations:
    ------------------
    - Network administrators may accidentally:
    - Enable **guest** or **anonymous access**
    - Leave shares **unprotected**
    - Grant **read/write permissions** to everyone

    These misconfigurations may allow:
    - Unauthorized users to browse directories
    - Downloading of sensitive files
    - Uploading or replacing of files (in writable shares)

    Example in CTF/HTB Context:
    ---------------------------
    - Port **445/tcp** is typically associated with SMB.
    - A common attack flow includes:
    1. Discover the open SMB port with `nmap`
    2. Enumerate shares using tools like `smbclient` or `enum4linux`
    3. Attempt access using:
        - Guest or anonymous login
        - Known or brute-forced credentials

    Example Tools:
    --------------
    - `smbclient //10.10.10.10/share -U guest`  
    → Access a share using guest credentials

    - `smbmap -H 10.10.10.10`  
    → Quickly check available shares and their permissions

    - `enum4linux -a 10.10.10.10`  
    → Enumerate usernames, shares, and policies


# Redis

 What is Redis?
    --------------
    - Redis stands for **REmote DIctionary Server**.
    - It is an open-source, in-memory, advanced **NoSQL key-value data store**.
    - Frequently used for **caching**, **session management**, **real-time analytics**, and **message brokering**.
    - Redis stores data as **key-value pairs** in memory, allowing extremely fast read/write access.

    How Redis is Used:
    ------------------
    - Often used by web applications to cache frequently requested data.
    Example: A website may cache prices on its homepage in Redis.
    - On request, it checks Redis first → If key is not found → falls back to main database → then stores the result in Redis for future use.

    Key Characteristics:
    --------------------
    - In-memory: Data is stored in RAM, not on disk (can persist optionally).
    - Non-relational: Doesn't use tables, rows, or SQL.
    - Data types supported: strings, hashes, lists, sets, sorted sets, bitmaps, streams, and more.

    Redis CLI Usage:
    ----------------
    Use `redis-cli` to interact with a Redis server from the terminal.

    Run `redis-cli --help` to see all command-line options.

    Basic Redis CLI Commands:
    -------------------------
    - `info`  
    → Displays server info and stats. Look under the **Keyspace** section for active databases and key counts.

    - `select <db>`  
    → Switch between databases. Default is `select 0` (DB 0). Redis supports DB 0 to DB 15 by default.

    - `keys *`  
    → Lists all keys in the current database. (Use with caution — slow on large datasets)

    - `get <key>`  
    → Retrieves the value of a key if it is a string.

    - `set <key> <value>`  
    → Sets a key to hold the specified value.

    - `del <key>`  
    → Deletes the key and its value.

    - `flushdb`  
    → Deletes all keys in the current database.

    - `flushall`  
    → Deletes all keys in all databases.

    Security Notes (HTB Context):
    -----------------------------
    - Redis by default does **not use authentication**, making it exploitable if exposed on the network.
    - If accessible unauthenticated, attackers can:
    - Read/write arbitrary keys.
    - Set up persistence to write a malicious file to disk (e.g., SSH key to authorized_keys).
    - Often used in initial foothold scenarios during CTFs and HTB challenges.

    Redis Auth:
    -----------
    - `AUTH <password>`  
    → Used to authenticate when the Redis server is protected.

    Example Session:
    ----------------
    - redis-cli
    - info
    - select 0
    - keys *
    - get <key>

    Use Cases on Hack The Box:
    ---------------------------
    - Exploiting unauthenticated Redis access.
    - Enumerating and dumping keys.
    - Injecting payloads via stored keys.
    - Abusing Redis persistence to gain shell access (e.g., writing to `.ssh/authorized_keys`).


# Gobuster

What is Gobuster?
    -----------------
    Gobuster is a command-line brute-force tool used to discover hidden files/directories, DNS subdomains, and virtual hosts on web servers. It uses a wordlist to send HTTP requests and identifies valid paths based on HTTP responses.

    How Gobuster Works:
    -------------------
    - Takes a wordlist of names (directories, files, subdomains, etc.)
    - Appends each to the base URL or domain
    - Sends requests and checks for valid HTTP responses (200, 301, 403, etc.)
    - Outputs found directories, files, or hosts

    Common Modes:
    -------------
    - dir    → Brute-force directories/files (most used in HTB)
    - dns    → Brute-force subdomains
    - vhost  → Virtual host discovery
    - s3     → Amazon S3 bucket enumeration
    - fuzz   → General fuzzing (advanced use)

    Basic Syntax:
    -------------
    gobuster <mode> [options]

    Example – Directory Bruteforce:
    -------------------------------
    gobuster dir -u http://10.10.10.10 -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt

    Flags:
    ------
    - dir          : Directory brute-force mode
    - -u <URL>     : Target URL (e.g., http://IP)
    - -w <wordlist>: Path to wordlist
    - -x <ext>     : Try file extensions (comma-separated: php,html,txt)
    - -t <threads> : Number of concurrent threads (default: 10)
    - -o <file>    : Output results to a file
    - -s <codes>   : Status codes to include (default: 200,204,301,302,307,401,403)
    - --wildcard   : Ignore wildcard responses

    Example – With File Extensions:
    -------------------------------
    gobuster dir -u http://10.10.10.10 -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -x php,html,txt

    Example – Subdomain Bruteforce:
    -------------------------------
    gobuster dns -d target.com -w /usr/share/wordlists/seclists/Discovery/DNS/subdomains-top1million-5000.txt

    Flags for DNS mode:
    --------------------
    - dns          : Subdomain brute-force mode
    - -d <domain>  : Target domain
    - -w <wordlist>: Wordlist of subdomains

    Use Cases on Hack The Box:
    ---------------------------
    - Finding hidden directories or admin panels
    - Discovering developer/test folders
    - Identifying configuration or backup files
    - Subdomain enumeration for lateral movement



