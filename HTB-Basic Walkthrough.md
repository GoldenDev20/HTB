# 🧩 HTB Machine Walkthrough – Starting Template

Use this as a repeatable base process when approaching any Hack The Box machine.

---

## ✅ 1. Connect to the VPN
Make sure you're connected to the HTB network.

```bash
sudo openvpn ~/htb/htb.ovpn
```

Or, if using HTB's own VPN manager:

```bash
htb vpn connect
```

---

## 🗂️ 2. Set Up Workspace

```bash
mkdir -p ~/htb/<machine_name>/scans
cd ~/htb/<machine_name>
```

Organize by machine for clarity.

---

## 🧾 3. Add Entry to /etc/hosts

Open `/etc/hosts`:

```bash
sudo nano /etc/hosts
```

Add the machine:

```plaintext
10.10.10.123    <machine_name>.htb
```

Replace `10.10.10.123` with the actual IP  
Replace `<machine_name>` with the machine's HTB name

---

## 📡 4. Ping Test

```bash
ping -c 3 <machine_name>.htb
```

Confirms basic connectivity.

---

## 🔍 5. Initial Nmap Scan

Basic top ports:

```bash
nmap -T4 -sC -sV -oA scans/initial <machine_name>.htb
```

- `-T4`: faster timing
- `-sC`: default scripts
- `-sV`: version detection
- `-oA`: output in all formats (`.nmap`, `.xml`, `.gnmap`)

Optional full port scan:

```bash
nmap -p- -T4 -oA scans/full <machine_name>.htb
```

---

## 📑 6. Document Observations

Start a notes file:

```bash
nano notes.txt
```

Track:
- IP and hostname
- Open ports/services
- Observed technologies
- Potential attack vectors

---

## 🌐 7. Web Enumeration (If Applicable)

If port 80/443 or others open:

- Access in browser: `http://<machine_name>.htb`
- Use tools:

```bash
whatweb http://<machine_name>.htb
gobuster dir -u http://<machine_name>.htb -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -t 50
```

---

## 📂 8. Continue Enumeration & Exploitation

Now you can:
- Investigate each open service
- Identify possible exploits
- Begin privilege escalation after initial foothold

---

## ✅ Summary Checklist (Condensed)

| Step           | Description             |
|----------------|-------------------------|
| 🛜 VPN          | Connect to HTB VPN      |
| 📁 Workspace    | Create machine folder   |
| 🧾 Hosts        | Add IP + hostname       |
| 📶 Ping         | Test connectivity       |
| 🔍 Nmap         | Initial + full scan     |
| 📓 Notes        | Document findings       |
| 🌐 Web Enum     | If applicable           |
| 🔐 Exploit      | Foothold → PrivEsc      |

---

## 🧠 Future Expansion Ideas

You can later expand this by adding:
- Automated enum scripts (e.g., `AutoRecon`, `LinEnum`)
- Privilege escalation checks
- Post-exploitation checklist
- Cleanup commands
