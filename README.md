# Nmap Scan Command Cheatsheet

This README provides a quick reference to common Nmap (Network Mapper) scan commands. Nmap is a powerful open-source tool for network exploration and security auditing.

## Table of Contents

* [Basic Scans](#basic-scans)
* [Host Discovery (Ping Scans)](#host-discovery-ping-scans)
* [Port Scanning Techniques](#port-scanning-techniques)
* [Port Specification](#port-specification)
* [Service and Version Detection](#service-and-version-detection)
* [Operating System (OS) Detection](#operating-system-os-detection)
* [Nmap Scripting Engine (NSE)](#nmap-scripting-engine-nse)
* [Timing and Performance](#timing-and-performance)
* [Firewall/IDS Evasion and Spoofing](#firewallids-evasion-and-spoofing)
* [Output Formats](#output-formats)
* [Miscellaneous](#miscellaneous)

---

## Basic Scans

These are some of the most fundamental and frequently used scan commands.

* **Scan a single target:**
    ```bash
    nmap <target_ip_or_hostname>
    ```
    *Example:* `nmap 192.168.1.1` or `nmap scanme.nmap.org`

* **Scan multiple targets:**
    ```bash
    nmap <target1> <target2> ...
    ```
    *Example:* `nmap 192.168.1.1 192.168.1.2`

* **Scan a range of IPs:**
    ```bash
    nmap <ip_range>
    ```
    *Example:* `nmap 192.168.1.1-100` (scans IPs from 192.168.1.1 to 192.168.1.100)

* **Scan a subnet:**
    ```bash
    nmap <network/cidr>
    ```
    *Example:* `nmap 192.168.1.0/24`

* **Scan targets from a file:**
    ```bash
    nmap -iL <input_filename>
    ```
    *Example:* `nmap -iL targets.txt`

* **Scan random hosts:**
    ```bash
    nmap -iR <number_of_hosts>
    ```
    *Example:* `nmap -iR 10` (scans 10 random hosts on the internet)

* **Exclude hosts from a scan:**
    ```bash
    nmap <targets> --exclude <host1,host2,...>
    ```
    *Example:* `nmap 192.168.1.0/24 --exclude 192.168.1.5`

* **Exclude hosts listed in a file:**
    ```bash
    nmap <targets> --excludefile <exclude_filename>
    ```
    *Example:* `nmap 192.168.1.0/24 --excludefile excluded_hosts.txt`

---

## Host Discovery (Ping Scans)

Used to identify live hosts on a network without port scanning them.

* **Ping scan only (disable port scan):**
    ```bash
    nmap -sn <targets>
    ```
    *This was formerly `nmap -sP`.*

* **No ping (assume all hosts are online - useful if ICMP is blocked):**
    ```bash
    nmap -Pn <targets>
    ```

* **TCP SYN Ping:**
    ```bash
    nmap -PS<portlist> <targets>
    ```
    *Example:* `nmap -PS80,443 192.168.1.0/24` (sends SYN packets to ports 80 and 443)

* **TCP ACK Ping:**
    ```bash
    nmap -PA<portlist> <targets>
    ```
    *Example:* `nmap -PA22 192.168.1.1`

* **UDP Ping:**
    ```bash
    nmap -PU<portlist> <targets>
    ```
    *Example:* `nmap -PU53 192.168.1.1-50`

* **SCTP INIT Ping:**
    ```bash
    nmap -PY<portlist> <targets>
    ```

* **ICMP Echo Ping:**
    ```bash
    nmap -PE <targets>
    ```

* **ICMP Timestamp Ping:**
    ```bash
    nmap -PP <targets>
    ```

* **ICMP Address Mask Ping:**
    ```bash
    nmap -PM <targets>
    ```

* **IP Protocol Ping:**
    ```bash
    nmap -PO<protocol_list> <targets>
    ```
    *Example:* `nmap -PO1,6,17 192.168.1.1` (probes for IP protocols 1 (ICMP), 6 (TCP), 17 (UDP))

* **ARP Ping (for local networks):**
    ```bash
    nmap -PR <targets>
    ```
    *Example:* `nmap -PR 192.168.1.0/24` (faster discovery on LAN)

* **Traceroute (identify hops to target):**
    ```bash
    nmap --traceroute <targets>
    ```

* **Force Reverse DNS resolution:**
    ```bash
    nmap -R <targets>
    ```

* **Never do DNS resolution:**
    ```bash
    nmap -n <targets>
    ```

* **Use system DNS resolver:**
    ```bash
    nmap --system-dns <targets>
    ```

---

## Port Scanning Techniques

Different methods to determine which ports are open, closed, or filtered.

* **TCP SYN Scan (default for privileged users, stealthy):**
    ```bash
    nmap -sS <targets>
    ```

* **TCP Connect Scan (default for unprivileged users, more likely to be logged):**
    ```bash
    nmap -sT <targets>
    ```

* **UDP Scan (slower, can be unreliable):**
    ```bash
    nmap -sU <targets>
    ```

* **SCTP INIT Scan:**
    ```bash
    nmap -sY <targets>
    ```

* **TCP NULL Scan (exploits a loophole in TCP RFC):**
    ```bash
    nmap -sN <targets>
    ```

* **TCP FIN Scan (stealthy, may bypass some firewalls):**
    ```bash
    nmap -sF <targets>
    ```

* **TCP Xmas Scan (sets FIN, PSH, URG flags):**
    ```bash
    nmap -sX <targets>
    ```

* **TCP ACK Scan (to map out firewall rulesets, determines if ports are filtered/unfiltered):**
    ```bash
    nmap -sA <targets>
    ```

* **TCP Window Scan (similar to ACK scan, can sometimes differentiate open/closed ports on some systems):**
    ```bash
    nmap -sW <targets>
    ```

* **TCP Maimon Scan (FIN/ACK probe):**
    ```bash
    nmap -sM <targets>
    ```

* **Custom TCP Scan Flags:**
    ```bash
    nmap --scanflags <flags> <targets>
    ```
    *Example:* `nmap --scanflags URGACKPSHRSTSYNFIN <target>`

* **IP Protocol Scan (determines which IP protocols are supported by target):**
    ```bash
    nmap -sO <targets>
    ```

* **FTP Bounce Scan (obsolete, rarely works):**
    ```bash
    nmap -b <ftp_relay_host> <targets>
    ```

---

## Port Specification

How to define which ports Nmap should scan.

* **Scan specific ports:**
    ```bash
    nmap -p <port_list> <targets>
    ```
    *Example:* `nmap -p 22,80,443 192.168.1.1`

* **Scan a range of ports:**
    ```bash
    nmap -p <start_port-end_port> <targets>
    ```
    *Example:* `nmap -p 1-1000 192.168.1.1`

* **Scan all 65535 ports:**
    ```bash
    nmap -p- <targets>
    ```
    *Equivalent to `nmap -p 1-65535 <targets>`*

* **Scan specific ports by protocol:**
    ```bash
    nmap -p T:<tcp_ports>,U:<udp_ports> <targets>
    ```
    *Example:* `nmap -p T:22,80,U:53,161 192.168.1.1`

* **Fast scan (scan fewer ports than the default scan - top 100):**
    ```bash
    nmap -F <targets>
    ```

* **Scan top N ports:**
    ```bash
    nmap --top-ports <number> <targets>
    ```
    *Example:* `nmap --top-ports 10 192.168.1.1`

* **Scan ports by ratio (ports more common than the given ratio):**
    ```bash
    nmap --port-ratio <ratio_between_0_and_1> <targets>
    ```
    *Example:* `nmap --port-ratio 0.1 192.168.1.1`

---

## Service and Version Detection

Identify the services running on open ports and their versions.

* **Enable service and version detection:**
    ```bash
    nmap -sV <targets>
    ```

* **Set version detection intensity (0-9, default 7):**
    ```bash
    nmap -sV --version-intensity <level> <targets>
    ```
    *Higher numbers increase accuracy but take longer.*
    *Example:* `nmap -sV --version-intensity 9 <target>`

* **Light version detection (faster, less accurate):**
    ```bash
    nmap -sV --version-light <targets>
    ```
    *(Intensity 2)*

* **Aggressive version detection (slower, more comprehensive):**
    ```bash
    nmap -sV --version-all <targets>
    ```
    *(Intensity 9)*

* **RPC scan (for NFS/NIS/RPC services):**
    ```bash
    nmap -sR <targets>
    ```
    *Typically used with `-sV`.*

---

## Operating System (OS) Detection

Attempt to identify the operating system of the target. Requires at least one open and one closed TCP port.

* **Enable OS detection:**
    ```bash
    nmap -O <targets>
    ```

* **Aggressive OS detection (more likely to get a match):**
    ```bash
    nmap -O --osscan-guess <targets>
    ```
    *Also `--fuzzy`*

* **Limit OS detection to promising targets:**
    ```bash
    nmap -O --osscan-limit <targets>
    ```

* **Set max OS detection retries:**
    ```bash
    nmap -O --max-os-tries <number> <targets>
    ```

---

## Nmap Scripting Engine (NSE)

Leverage Nmap's powerful scripting engine to automate a wide variety of networking tasks.

* **Run default safe scripts:**
    ```bash
    nmap -sC <targets>
    ```
    *Equivalent to `nmap --script default <targets>`*

* **Run a specific script:**
    ```bash
    nmap --script <script_name> <targets>
    ```
    *Example:* `nmap --script http-title 192.168.1.1`

* **Run scripts from a category:**
    ```bash
    nmap --script <category> <targets>
    ```
    *Categories: `auth`, `broadcast`, `brute`, `default`, `discovery`, `dos`, `exploit`, `external`, `fuzzer`, `intrusive`, `malware`, `safe`, `version`, `vuln`.*
    *Example:* `nmap --script vuln 192.168.1.0/24`

* **Run multiple scripts or categories:**
    ```bash
    nmap --script "<script1>,<script2>,<category1>" <targets>
    ```
    *Example:* `nmap --script "http-enum,smb-os-discovery" 192.168.1.1`

* **Run all scripts (can be very intrusive and slow):**
    ```bash
    nmap --script all <targets>
    ```

* **Get help for a script:**
    ```bash
    nmap --script-help <script_name_or_category>
    ```

* **Pass arguments to scripts:**
    ```bash
    nmap --script <script_name> --script-args <arg1=val1,arg2=val2,...> <targets>
    ```
    *Example:* `nmap --script http-brute --script-args userdb=users.txt,passdb=pass.txt <target>`

* **Update script database:**
    ```bash
    nmap --script-updatedb
    ```

---

## Timing and Performance

Control the speed and aggressiveness of the scan.

* **Timing templates (higher is faster/more aggressive):**
    * `-T0`: Paranoid (very slow, for IDS evasion)
    * `-T1`: Sneaky (slow, for IDS evasion)
    * `-T2`: Polite (slows down to consume less bandwidth/resources)
    * `-T3`: Normal (default)
    * `-T4`: Aggressive (assumes fast, reliable network)
    * `-T5`: Insane (very aggressive, may sacrifice accuracy)
    ```bash
    nmap -T<0-5> <targets>
    ```
    *Example:* `nmap -T4 192.168.1.1`

* **Set minimum host group size:**
    ```bash
    nmap --min-hostgroup <size> <targets>
    ```

* **Set maximum host group size:**
    ```bash
    nmap --max-hostgroup <size> <targets>
    ```

* **Set minimum parallelism:**
    ```bash
    nmap --min-parallelism <number> <targets>
    ```

* **Set maximum parallelism:**
    ```bash
    nmap --max-parallelism <number> <targets>
    ```

* **Set minimum RTT timeout:**
    ```bash
    nmap --min-rtt-timeout <time> <targets>
    ```
    *Example:* `nmap --min-rtt-timeout 50ms <target>`

* **Set maximum RTT timeout:**
    ```bash
    nmap --max-rtt-timeout <time> <targets>
    ```
    *Example:* `nmap --max-rtt-timeout 1000ms <target>`

* **Set initial RTT timeout:**
    ```bash
    nmap --initial-rtt-timeout <time> <targets>
    ```

* **Set maximum retries:**
    ```bash
    nmap --max-retries <number> <targets>
    ```

* **Set host timeout:**
    ```bash
    nmap --host-timeout <time> <targets>
    ```
    *Example:* `nmap --host-timeout 30m <target>` (give up on host after 30 minutes)

* **Set scan delay (wait between probes):**
    ```bash
    nmap --scan-delay <time> <targets>
    ```
    *Example:* `nmap --scan-delay 1s <target>`

* **Set maximum scan delay:**
    ```bash
    nmap --max-scan-delay <time> <targets>
    ```

* **Set minimum packet rate:**
    ```bash
    nmap --min-rate <number> <targets>
    ```

* **Set maximum packet rate:**
    ```bash
    nmap --max-rate <number> <targets>
    ```

---

## Firewall/IDS Evasion and Spoofing

Techniques to bypass security measures. Use responsibly and ethically.

* **Fragment packets:**
    ```bash
    nmap -f <targets>
    ```
    *(MTU of 8 bytes)*

* **Specify a custom MTU (must be multiple of 8):**
    ```bash
    nmap --mtu <value> <targets>
    ```
    *Example:* `nmap --mtu 16 <target>`

* **Use decoys to mask your IP:**
    ```bash
    nmap -D <decoy1,decoy2,ME,...> <targets>
    ```
    *`ME` indicates your real IP's position. Use `RND` for random decoys.*
    *Example:* `nmap -D RND:10,192.168.1.101,ME 192.168.1.1`

* **Spoof source IP address (requires root/admin, often doesn't work across internet):**
    ```bash
    nmap -S <spoofed_ip> <targets>
    ```

* **Spoof source port:**
    ```bash
    nmap --source-port <port_number> <targets>
    ```
    *Also `-g <port_number>`*

* **Send packets with a specific interface:**
    ```bash
    nmap -e <interface> <targets>
    ```
    *Example:* `nmap -e eth0 192.168.1.1`

* **Append custom data to sent packets:**
    ```bash
    nmap --data <hex_string> <targets>
    ```
    *Example:* `nmap --data 001122aabbcc <target>`

* **Append custom string to sent packets:**
    ```bash
    nmap --data-string "<string>" <targets>
    ```
    *Example:* `nmap --data-string "Hello" <target>`

* **Append random data to sent packets:**
    ```bash
    nmap --data-length <number> <targets>
    ```
    *Sends packets with specified length of random data.*

* **Set IP Time To Live (TTL):**
    ```bash
    nmap --ttl <value> <targets>
    ```

* **Randomize target scan order:**
    ```bash
    nmap --randomize-hosts <targets>
    ```

* **Spoof MAC address:**
    ```bash
    nmap --spoof-mac <MAC_address|0|vendor_name> <targets>
    ```
    *Example:* `nmap --spoof-mac 00:01:02:03:04:05 <target>` or `nmap --spoof-mac Dell <target>` or `nmap --spoof-mac 0 <target>` (random MAC)

* **Send bad checksums (can be useful to elicit responses from some firewalls):**
    ```bash
    nmap --badsum <targets>
    ```

---

## Output Formats

Save scan results in various formats.

* **Normal output (to console and file):**
    ```bash
    nmap -oN <filename.nmap> <targets>
    ```

* **XML output:**
    ```bash
    nmap -oX <filename.xml> <targets>
    ```

* **Script Kiddie output (l33t speak):**
    ```bash
    nmap -oS <filename.skriptkiddie> <targets>
    ```

* **Grepable output (easy to parse with tools like grep, awk, cut):**
    ```bash
    nmap -oG <filename.gnmap> <targets>
    ```

* **Output to all major formats:**
    ```bash
    nmap -oA <basename> <targets>
    ```
    *Creates `<basename>.nmap`, `<basename>.xml`, and `<basename>.gnmap`.*

* **Append to output files (rather than overwrite):**
    Use `--append-output` with any output option.
    *Example:* `nmap -oN existing_scan.nmap --append-output <new_targets>`

* **Increase verbosity level (-v or -vv):**
    ```bash
    nmap -v <targets>
    nmap -vv <targets>
    ```

* **Increase debugging level (-d or -dd, up to -d9):**
    ```bash
    nmap -d <targets>
    ```

* **Reason for port state and other info:**
    ```bash
    nmap --reason <targets>
    ```

* **Only show open (or possibly open) ports:**
    ```bash
    nmap --open <targets>
    ```

* **Print host interface and route list:**
    ```bash
    nmap --iflist
    ```

* **Resume an aborted scan:**
    ```bash
    nmap --resume <logfilename>
    ```
    *(Works with normal (`.nmap`) or grepable (`.gnmap`) output files.)*

---

## Miscellaneous

Other useful options.

* **Enable IPv6 scanning:**
    ```bash
    nmap -6 <targets>
    ```
    *Target must be specified using IPv6 address or hostname resolving to IPv6.*

* **Aggressive scan options (enables OS detection, version detection, script scanning, and traceroute):**
    ```bash
    nmap -A <targets>
    ```

* **Display Nmap version:**
    ```bash
    nmap -V
    ```
    *Or `nmap --version`*

* **Nmap help (shows common options):**
    ```bash
    nmap -h
    ```
    *Or `nmap --help`*

---

**Disclaimer:** Use Nmap responsibly and ethically. Always ensure you have explicit permission before scanning any network or system that you do not own. Unauthorized scanning can be illegal.
