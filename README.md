# nmap-documentation
NMAP network enumeration

This repo represents a nmap documentation for network enumeration


# Network Enumaration (nmap)

- Audit the security aspects of networks
- Simulate penetration tests
- Check firewall and IDS settings and configurations
- Types of possible connections
- Network mapping
- Response analysis
- Identify open ports
- Vulnerability assessment as well.

## Syntax

`nmap <scan types> <options> <target>`

Example of usage:

nmap -sS localhost

- If our target sends a `SYN-ACK` flagged packet back to us, Nmap detects that the port is `open`.
- If the target responds with an `RST` flagged packet, it is an indicator that the port is `closed`.
- If Nmap does not receive a packet back, it will display it as `filtered`. Depending on the firewall configuration, certain packets may be dropped or ignored by the firewall.

 `-sn` —- Disables port scanning

`-oA tnet` —- Stores the results in all formats starting with the name 'tnet'

`-iL` <file> —- Performs defined scans against targets in provided <file> list

`-PE` —- Performs the ping scan by using 'ICMP Echo requests' against the target

`--packet-trace` —- Shows all packets sent and received

`--reason` —- Displays the reason for specific result

`--disable-arp-ping` --- disable ARP pings

| **State** | **Description** |
| --- | --- |
| `open` | This indicates that the connection to the scanned port has been established. These connections can be **TCP connections**, **UDP datagrams** as well as **SCTP associations**. |
| `closed` | When the port is shown as closed, the TCP protocol indicates that the packet we received back contains an `RST` flag. This scanning method can also be used to determine if our target is alive or not. |
| `filtered` | Nmap cannot correctly identify whether the scanned port is open or closed because either no response is returned from the target for the port or we get an error code from the target. |
| `unfiltered` | This state of a port only occurs during the **TCP-ACK** scan and means that the port is accessible, but it cannot be determined whether it is open or closed. |
| `open/filtered` | If we do not get a response for a specific port, `Nmap` will set it to that state. This indicates that a firewall or packet filter may protect the port. |
| `closed/filtered` | This state only occurs in the **IP ID idle** scans and indicates that it was impossible to determine if the scanned port is closed or filtered by a firewall. |

By default nmap scans the top 1000 ports (-sS/-sT)

`-sT`  —- full TCP scan

`-p 22,25,80,139,445`

`-p 22-445`

`-p-` —- scan all ports

`--top-ports=10`

`-F` —- fast scan (top 100 ports)

`-n` —- disables DNS resolution

`-Pn` —- Disables ICMP Echo request

`-sU` —-  Performs a UDP scan

`-sV` —- Performs a service scan

There are different formats, the output can be saved:

`-oN` —  Normal output `.nmap`

`-oG` —- Greppable output `.gnmap`

`-oX` —- XML output `.xml`

## Style sheets

With the XML output, we can easily create HTML reports that are easy 
to read, even for non-technical people. This is later very useful for 
documentation, as it presents our results in a detailed and clear way.
To convert the stored results from XML format to HTML, we can use the 
tool `xsltproc`.

Command:
`xsltproc target.xml -o target.html`

## **Service Enumeration (Service Version Detection)**

`--stats-every=5s` —- Shows the progress of the scan every 5 seconds

`-v` —- Increases the verbosity of the scan, which displays more detailed information.

## **Nmap Scripting Engine**

Nmap Scripting Engine (`NSE`) is another handy feature of `Nmap`.
 It provides us with the possibility to create scripts in Lua for 
interaction with certain services. There are a total of 14 categories 
into which these scripts can be divided:

| **Category** | **Description** |
| --- | --- |
| `auth` | Determination of authentication credentials. |
| `broadcast` | Scripts, which are used for host discovery by broadcasting and the discovered hosts, can be automatically added to the remaining scans. |
| `brute` | Executes scripts that try to log in to the respective service by brute-forcing with credentials. |
| `default` | Default scripts executed by using the `-sC` option. |
| `discovery` | Evaluation of accessible services. |
| `dos` | These scripts are used to check services for denial of service vulnerabilities and are used less as it harms the services. |
| `exploit` | This category of scripts tries to exploit known vulnerabilities for the scanned port. |
| `external` | Scripts that use external services for further processing. |
| `fuzzer` | This uses scripts to identify vulnerabilities and unexpected packet handling by sending different fields, which can take much time. |
| `intrusive` | Intrusive scripts that could negatively affect the target system. |
| `malware` | Checks if some malware infects the target system. |
| `safe` | Defensive scripts that do not perform intrusive and destructive access. |
| `version` | Extension for service detection. |
| `vuln` | Identification of specific vulnerabilities. |

### **Default Scripts**

`nmap <target> -sC`

### **Specific Scripts Category**

`nmap <target> --script <category>`

### **Defined Scripts**

`nmap <target> --script <script-name>,<script-name>,...`

### **Nmap - Specifying Scripts**

 `nmap 10.129.2.28 -p 25 --script banner,smtp-commands`

`--script banner,smtp-commands` —- Uses specified NSE scripts

`-A` —-  Performs service detection, OS detection, traceroute and uses defaults scripts to scan the target

`--script vuln` —- Uses all related scripts from specified category

# **Performance**

`--initial-rtt-timeout 50ms`  —-  Sets the specified time value as initial RTT timeout

`--max-rtt-timeout 100ms` —-  Sets the specified time value as maximum RTT timeout

`--max-retries 0` —- Sets the number of retries that will be performed during the scan

`--min-rate 300` —-  Sets the minimum number of packets to be sent per second

## Timing

Because such settings cannot always be optimized manually, as in a black-box penetration test, `Nmap` offers six different timing templates (`-T <0-5>`) for us to use. These values (`0-5`)
 determine the aggressiveness of our scans. This can also have negative 
effects if the scan is too aggressive, and security systems may block us
 due to the produced network traffic. The default timing template used 
when we have defined nothing else is the normal (`-T 3`).

- `T 0` / `T paranoid`
- `T 1` / `T sneaky`
- `T 2` / `T polite`
- `T 3` / `T normal`
- `T 4` / `T aggressive`
- `T 5` / `T insane`

## **Decoys**

There are cases in which administrators block specific subnets from 
different regions in principle. This prevents any access to the target 
network. Another example is when IPS should block us. For this reason, 
the Decoy scanning method (`-D`) is the right choice. With 
this method, Nmap generates various random IP addresses inserted into 
the IP header to disguise the origin of the packet sent.
With this method, we can generate random (`RND`) a specific number (for example: `5`) of IP addresses separated by a colon (`:`).
 Our real IP address is then randomly placed between the generated IP 
addresses. In the next example, our real IP address is therefore placed 
in the second position. Another critical point is that the decoys must 
be alive. Otherwise, the service on the target may be unreachable due to
 SYN-flooding security mechanisms.

Example of usage:

`nmap 10.129.2.28 -p 80 -sS -Pn -n --disable-arp-ping --packet-trace -D RND:5`

`-D RND:5` —- Generates five random IP addresses that indicates the source IP the connection comes from

`-O` —- Performs operation system detection scan.

`-S` —- Scans the target by using different source IP address

`-e tun0` —-  Sends all requests through the specified interface

`--source-port 53` —- Performs the scans from specified source port
