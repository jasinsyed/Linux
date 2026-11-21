# 🐧 Linux Networking & Package Management Commands

> **Linux & DevOps Learning Journey — Networking and Package Management**

Networking and package management are two essential skills for Linux system administration and DevOps engineering.

Linux networking commands help us inspect network interfaces, troubleshoot connectivity, investigate open ports, resolve DNS records, download resources, and manage firewall rules.

Package management commands help us install, update, upgrade, search, and remove software packages depending on the Linux distribution.

This topic covers commonly used networking and package management commands across Debian/Ubuntu, Red Hat-based distributions, and Arch Linux.

---

## 📚 Table of Contents

1. 🌐 [Linux Network Interfaces](#1--linux-network-interfaces)
2. 🔌 [Network Interface Details](#2--network-interface-details)
3. 📡 [Network Connections and Ports](#3--network-connections-and-ports)
4. 🛰️ [Network Connectivity Testing](#4--network-connectivity-testing)
5. 🌍 [DNS and Domain Information](#5--dns-and-domain-information)
6. 📥 [Downloading and Accessing Web Resources](#6--downloading-and-accessing-web-resources)
7. 🔥 [Firewall Management](#7--firewall-management)
8. ⚙️ [NetworkManager CLI](#8--networkmanager-cli)
9. 📦 [Linux Package Management](#9--linux-package-management)
10. 🚀 [Real-World Production Scenario](#10--real-world-production-scenario)
11. 📌 [Quick Command Reference](#11--quick-command-reference)
12. 🎯 [Interview Questions](#12--interview-questions)
13. 🧪 [Hands-On Practice](#13--hands-on-practice)

---

## 🎯 Introduction

A Linux server communicates with other systems through network interfaces and network protocols.

For example, a production application server may need to:

- Communicate with a database server.
- Receive HTTP and HTTPS traffic.
- Resolve domain names using DNS.
- Download application dependencies.
- Expose specific ports through a firewall.
- Install and update software packages.

As a DevOps engineer, you must understand how to inspect and troubleshoot these components.

Package management is equally important because servers require software such as Docker, Nginx, Git, Python, and monitoring agents.

---

# 🌐 Part 1: Linux Networking Commands

## 1. 🌐 Linux Network Interfaces

A network interface is a connection point through which a Linux system communicates with a network.

Examples include:

- `eth0` — Common traditional name for an Ethernet interface.
- `ens33` — Common predictable network interface name.
- `wlan0` — Common wireless interface name.
- `lo` — Loopback interface used for local communication.

### 1.1 `ifconfig` — Display Network Interfaces

**Syntax:**

```bash
ifconfig
```

Displays available active network interfaces along with their IP addresses and network configuration.

**Example:**

```bash
ifconfig
```

**Sample output:**

```text
eth0: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>
      inet 192.168.1.10
      netmask 255.255.255.0
      broadcast 192.168.1.255

lo:   flags=73<UP,LOOPBACK,RUNNING>
      inet 127.0.0.1
      netmask 255.0.0.0
```

**Understanding the output:**

| Field | Meaning |
|---|---|
| eth0 | Network interface name |
| inet | IPv4 address |
| netmask | Network subnet mask |
| broadcast | Broadcast address |
| UP | Interface is enabled |
| RUNNING | Interface is operational |

**Important:** `ifconfig` is a legacy command and may not be installed by default on modern Linux distributions. It is provided by the `net-tools` package.

### 1.2 `ifconfig -a` — Display All Network Interfaces

**Syntax:**

```bash
ifconfig -a
```

Displays all network interfaces, including interfaces that are currently down.

**Example:**

```bash
ifconfig -a
```

**Difference:**

| Command | Description |
|---|---|
| `ifconfig` | Displays active interfaces |
| `ifconfig -a` | Displays all interfaces, including inactive ones |

**DevOps Insight:**

Suppose a server is unable to communicate with another machine. Running `ifconfig -a` can help you identify whether an expected network interface exists but is currently down.

### 1.3 `ip a` — Display Network Interfaces and IP Addresses

**Syntax:**

```bash
ip a
```

Displays network interfaces and their assigned IP addresses.

**Example:**

```bash
ip a
```

**Sample output:**

```text
1: lo: <LOOPBACK,UP,LOWER_UP>
    inet 127.0.0.1/8

2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP>
    inet 192.168.1.10/24
```

**Understanding the output:**

- `lo` represents the loopback interface.
- `eth0` represents an Ethernet interface.
- `inet` represents an IPv4 address.
- `inet6` represents an IPv6 address.
- `/24` represents the network prefix length.
- `UP` indicates that the interface is administratively enabled.

**Why use `ip a`?**

The `ip` command is part of the modern Linux networking utilities (`iproute2`) and is preferred over the older `ifconfig` command.

**Useful variations:**

```bash
ip -br a
```

Displays interfaces and addresses in a compact format.

```bash
ip link show
```

Displays network interface link information.

**DevOps Insight:**

When deploying an application on a cloud VM, use `ip a` to identify its network interfaces and private IP addresses.

---

## 2. 🔌 Network Interface Details

### 2.1 `ifconfig eth0` — Inspect a Specific Interface

**Syntax:**

```bash
ifconfig eth0
```

Displays IP addresses and configuration details of the specific interface named `eth0`.

**Example:**

```bash
ifconfig eth0
```

**Sample output:**

```text
eth0: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>
      inet 192.168.1.10
      netmask 255.255.255.0
      ether 00:1a:2b:3c:4d:5e
```

**Understanding the output:**

| Field | Meaning |
|---|---|
| eth0 | Interface name |
| inet | IPv4 address |
| netmask | Subnet mask |
| ether | MAC address |

**Modern alternative:**

```bash
ip addr show eth0
```

### 2.2 `ethtool eth0` — Inspect Network Hardware and Driver Settings

**Syntax:**

```bash
ethtool eth0
```

Queries network driver and hardware settings for the specified Ethernet interface.

**Example:**

```bash
sudo ethtool eth0
```

**Sample output:**

```text
Settings for eth0:
    Speed: 1000Mb/s
    Duplex: Full
    Auto-negotiation: on
    Link detected: yes
```

**Understanding the output:**

| Field | Meaning |
|---|---|
| Speed | Network link speed |
| Duplex | Full or half duplex communication |
| Auto-negotiation | Whether link settings are negotiated automatically |
| Link detected | Whether a physical or virtual link is detected |

**DevOps Insight:**

If a physical Linux server experiences unexpected network performance, `ethtool` can help investigate link speed, duplex settings, and driver-related issues.

---

## 3. 📡 Network Connections and Ports

### 3.1 `netstat` — Display Network Statistics and Connections

**Syntax:**

```bash
netstat
```

Displays network information such as open sockets, network connections, routing tables, interface statistics, and multicast memberships.

To make large outputs easier to read, combine it with `less`.

```bash
netstat -a | less
```

**Why use `less`?**

`less` allows you to scroll through long command output without displaying everything at once.

Useful navigation:

| Key | Action |
|---|---|
| `Space` | Move forward one page |
| `b` | Move backward one page |
| `/keyword` | Search for a keyword |
| `q` | Quit |

**Important:** `netstat` is a legacy utility and may require the `net-tools` package.

### 3.2 `netstat -a` — Display All Sockets

**Syntax:**

```bash
netstat -a
```

Displays both listening and non-listening sockets.

**Example:**

```bash
netstat -a
```

This helps administrators inspect network connections and services.

### 3.3 `netstat -l` — Display Listening Sockets

**Syntax:**

```bash
netstat -l
```

Displays only listening sockets.

**Example:**

```bash
netstat -l
```

A listening socket represents a service waiting for incoming network connections.

For example:

- SSH may listen on port `22`.
- HTTP may listen on port `80`.
- HTTPS may listen on port `443`.

### 3.4 `netstat -nutlp` — Display Listening TCP and UDP Ports

**Syntax:**

```bash
sudo netstat -nutlp
```

Displays listening TCP and UDP ports along with their associated process information, where available.

**Understanding the options:**

| Option | Meaning |
|---|---|
| `-n` | Display numerical addresses and port numbers |
| `-u` | Display UDP sockets |
| `-t` | Display TCP sockets |
| `-l` | Display listening sockets |
| `-p` | Display associated process information |

**Sample output:**

```text
Proto  Local Address       State    PID/Program name
tcp    0.0.0.0:22          LISTEN   850/sshd
tcp    0.0.0.0:80          LISTEN   1200/nginx
tcp    0.0.0.0:443         LISTEN   1200/nginx
udp    127.0.0.1:53                 900/dnsmasq
```

**Understanding the output:**

| Port | Service |
|---|---|
| 22 | SSH |
| 80 | HTTP |
| 443 | HTTPS |
| 53 | DNS |

**Modern alternative:**

```bash
sudo ss -nutlp
```

The `ss` command is preferred on modern Linux systems for inspecting sockets.

**DevOps Insight:**

Imagine an application is not accessible through a browser. You can check whether the application is actually listening on its expected port.

```bash
sudo ss -nutlp
```

If the application should listen on port `8080`, check specifically:

```bash
sudo ss -lntp | grep ':8080'
```

If no matching listener appears, investigate whether the application started successfully and whether it is configured to bind to that port.

---

## 4. 🛰️ Network Connectivity Testing

### 4.1 `ping` — Test Network Connectivity

**Syntax:**

```bash
ping host
```

Sends ICMP Echo Request packets to another host and waits for Echo Replies.

The host can be:

- A hostname.
- A domain name.
- An IP address.

**Example using a domain:**

```bash
ping google.com
```

**Example using an IP address:**

```bash
ping 8.8.8.8
```

**Sample output:**

```text
PING google.com (142.250.183.14): 56 data bytes

64 bytes from 142.250.183.14:
icmp_seq=1 ttl=117 time=12.5 ms

64 bytes from 142.250.183.14:
icmp_seq=2 ttl=117 time=11.8 ms
```

**Understanding the output:**

| Field | Meaning |
|---|---|
| icmp_seq | ICMP packet sequence number |
| ttl | Time To Live |
| time | Round-trip time |

**Useful variation:**

```bash
ping -c 4 google.com
```

Sends four packets and stops.

**DevOps Insight:**

Suppose a Linux application server cannot communicate with a database server.

You can test basic connectivity:

```bash
ping 192.168.1.20
```

However, a failed ping does not always mean the server is unreachable. Firewalls may block ICMP traffic even when the application port is accessible.

---

## 5. 🌍 DNS and Domain Information

DNS (Domain Name System) translates domain names into IP addresses.

For example:

```text
google.com → IP address
```

DNS troubleshooting is important when applications cannot connect to external services using domain names.

### 5.1 `whois` — Display Domain Registration Information

**Syntax:**

```bash
whois domain
```

Displays available WHOIS registration information for a domain.

**Example:**

```bash
whois google.com
```

Depending on the domain registry, the output may include:

- Registrar information.
- Domain creation date.
- Domain expiration date.
- Name servers.
- Registration status.

**DevOps Insight:**

WHOIS can help investigate domain ownership and registration details during infrastructure or domain troubleshooting.

### 5.2 `dig` — Display DNS Information

**Syntax:**

```bash
dig domain
```

Queries DNS servers and displays DNS information for a domain.

**Example:**

```bash
dig google.com
```

**Sample output:**

```text
; <<>> DiG <<>> google.com

;; QUESTION SECTION:
;google.com.        IN    A

;; ANSWER SECTION:
google.com.    300    IN    A    142.250.183.14

;; Query time: 15 msec
;; SERVER: 192.168.1.1#53
```

**Understanding the output:**

| Field | Meaning |
|---|---|
| A | IPv4 address record |
| AAAA | IPv6 address record |
| MX | Mail exchange record |
| NS | Name server record |
| TTL | Time To Live |
| SERVER | DNS server that answered the query |

**Useful variations:**

```bash
dig google.com A
```

Queries IPv4 address records.

```bash
dig google.com MX
```

Queries mail exchange records.

```bash
dig google.com NS
```

Queries name server records.

### 5.3 `dig -x` — Reverse DNS Lookup

**Syntax:**

```bash
dig -x address
```

Performs a reverse DNS lookup for an IPv4 or IPv6 address.

**Example:**

```bash
dig -x 8.8.8.8
```

This queries the reverse DNS record associated with the IP address.

A reverse lookup commonly uses a PTR record.

**DevOps Insight:**

If you know an IP address but need to identify its associated hostname, a reverse DNS lookup can help.

### 5.4 `host` — Display DNS IP Address

**Syntax:**

```bash
host domain
```

Displays DNS information for a domain, commonly including its resolved IP addresses.

**Example:**

```bash
host google.com
```

**Sample output:**

```text
google.com has address 142.250.183.14
google.com has IPv6 address 2404:6800:4007:81a::200e
```

**Difference between `dig` and `host`:**

| Command | Purpose |
|---|---|
| `dig` | Provides detailed DNS query information |
| `host` | Provides a simpler DNS lookup result |

**DevOps Insight:**

If a production application cannot connect to an external API, use `dig` or `host` to determine whether the domain resolves correctly.

---

## 6. 📥 Downloading and Accessing Web Resources

### 6.1 `wget` — Download Files from the Internet

**Syntax:**

```bash
wget LINK
```

Downloads a resource from the specified URL.

**Example:**

```bash
wget https://example.com/file.zip
```

Downloads `file.zip` into the current working directory.

**Useful variations:**

```bash
wget -O application.zip https://example.com/file.zip
```

Downloads the file using a custom filename.

```bash
wget -c https://example.com/file.zip
```

Attempts to continue a partially downloaded file.

**DevOps Insight:**

During deployment, a Linux server may need to download an application package or installation file from a trusted artifact repository.

### 6.2 `curl` — Access Web Resources

**Syntax:**

```bash
curl LINK
```

Retrieves content from the specified URL and displays the response body in the terminal.

For a webpage, this commonly displays its HTML source.

**Example:**

```bash
curl https://example.com
```

**Sample output:**

```html
<!doctype html>
<html>
<head>
    <title>Example Domain</title>
</head>
<body>
    <h1>Example Domain</h1>
</body>
</html>
```

**Useful variations:**

```bash
curl -I https://example.com
```

Displays response headers.

```bash
curl -L https://example.com
```

Follows HTTP redirects.

```bash
curl -o output.html https://example.com
```

Saves the response body to a file.

```bash
curl -O https://example.com/file.zip
```

Saves the file using its remote filename.

**DevOps Insight:**

`curl` is widely used in CI/CD pipelines and application troubleshooting.

For example, you can test whether a deployed application responds to HTTP requests:

```bash
curl -I http://localhost:8080
```

You can also test an API endpoint:

```bash
curl https://api.example.com/health
```

A successful HTTP response helps confirm that the endpoint is responding, although application health may require additional checks.

---

## 7. 🔥 Firewall Management

A firewall controls incoming and outgoing network traffic according to configured rules.

For example, a production server may allow SSH access while permitting HTTP and HTTPS traffic to reach a web application.

### 7.1 `ufw allow port` — Allow Traffic Through the Firewall

**Syntax:**

```bash
sudo ufw allow port
```

Allows traffic through a specified port using the Uncomplicated Firewall (UFW).

**Example:**

```bash
sudo ufw allow 80
```

Allows incoming traffic on port `80` using the default protocol behavior.

To allow HTTPS:

```bash
sudo ufw allow 443
```

To allow SSH:

```bash
sudo ufw allow 22
```

**Specify a protocol:**

```bash
sudo ufw allow 8080/tcp
```

Allows TCP traffic on port `8080`.

**DevOps Insight:**

If an application listens on port `8080` but external clients cannot access it, check the firewall rules as part of troubleshooting.

Do not expose unnecessary ports to the public internet.

### 7.2 `ufw status` — Check Firewall Status

**Syntax:**

```bash
sudo ufw status
```

Displays whether UFW is active and lists configured firewall rules.

**Example:**

```bash
sudo ufw status
```

**Sample output:**

```text
Status: active

To        Action    From
--        ------    ----
22/tcp    ALLOW     Anywhere
80/tcp    ALLOW     Anywhere
443/tcp   ALLOW     Anywhere
```

**Useful variations:**

```bash
sudo ufw status verbose
```

Displays more detailed firewall information.

```bash
sudo ufw status numbered
```

Displays firewall rules with numbers.

**Important:** UFW may not be installed or enabled on every Linux distribution. Other systems commonly use tools such as `firewalld` or nftables.

<blockquote>
**⚠️ Production Warning**

Before enabling or changing firewall rules on a remote server, ensure your SSH access is allowed. Incorrect firewall configuration can lock you out of the server.
</blockquote>

---

## 8. ⚙️ NetworkManager CLI

### 8.1 `nmcli` — Manage Network Connections

**Syntax:**

```bash
nmcli
```

`nmcli` is a command-line tool used to manage network connections through NetworkManager.

It can display network status, inspect connections, connect to networks, and modify network settings.

**Example:**

```bash
nmcli
```

Displays an overview of network connectivity and device information.

**Useful variations:**

```bash
nmcli device status
```

Displays the status of network devices.

```bash
nmcli connection show
```

Displays configured network connections.

```bash
nmcli device show
```

Displays detailed network device information.

**DevOps Insight:**

On Linux systems using NetworkManager, `nmcli` helps administrators inspect and manage network configurations without a graphical interface.

---

# 📦 Part 2: Linux Package Management Commands

## 9. 📦 Linux Package Management

Package management is the process of installing, updating, upgrading, and removing software on a Linux system.

Different Linux distributions use different package managers.

For this topic, we cover three major package management families:

| Linux Distribution | Package Manager |
|---|---|
| Debian / Ubuntu | APT and DPKG |
| Rocky Linux / Fedora / RHEL | DNF |
| Arch Linux / Manjaro / Garuda / Chakra | Pacman |

**Important distinction:**

- A package manager handles software installation and dependency management.
- A repository is a source from which package managers retrieve software.
- A package is a collection of files and metadata required to install software.

### 9.1 Updating the Package Repository Information

Before installing or upgrading packages, Linux systems often need to refresh their package metadata.

**Debian / Ubuntu:**

```bash
sudo apt update
```

Refreshes the local package index from configured repositories.

**Rocky Linux / Fedora / RHEL:**

```bash
sudo dnf check-update
```

Checks for available package updates.

A nonzero exit status can be returned when updates are available; this does not necessarily indicate a failure.

**Arch Linux / Manjaro:**

```bash
sudo pacman -Syu
```

Synchronizes package databases and upgrades installed packages.

**Important Arch Linux note:**

Avoid using `pacman -Sy` by itself for routine updates. Refreshing package databases without upgrading installed packages can create a partial-upgrade situation.

**Comparison:**

| Distribution | Command |
|---|---|
| Debian / Ubuntu | `sudo apt update` |
| Rocky / Fedora / RHEL | `sudo dnf check-update` |
| Arch Linux | `sudo pacman -Syu` |

### 9.2 Upgrading Installed Packages

Upgrading updates installed software to newer versions available through configured repositories.

**Debian / Ubuntu:**

```bash
sudo apt upgrade
```

Upgrades installed packages without normally removing packages or installing new dependencies that require removal.

**Rocky Linux / Fedora / RHEL:**

```bash
sudo dnf upgrade
```

Upgrades installed packages to available newer versions.

**Arch Linux:**

```bash
sudo pacman -Syu
```

Synchronizes package databases and performs a full system upgrade.

**Comparison:**

| Distribution | Command |
|---|---|
| Debian / Ubuntu | `sudo apt upgrade` |
| Rocky / Fedora / RHEL | `sudo dnf upgrade` |
| Arch Linux | `sudo pacman -Syu` |

**DevOps Insight:**

Regular package updates are important for security patches, bug fixes, and software stability. In production, updates should be tested and scheduled rather than applied without considering application compatibility.

### 9.3 Searching for Packages

Package managers allow you to search for software using keywords.

**Debian / Ubuntu:**

```bash
apt search keyword
```

Example:

```bash
apt search nginx
```

Searches available package metadata for packages matching `nginx`.

**Rocky Linux / Fedora / RHEL:**

```bash
dnf search keyword
```

Example:

```bash
dnf search nginx
```

**Arch Linux:**

```bash
pacman -Ss keyword
```

Example:

```bash
pacman -Ss nginx
```

**Comparison:**

| Distribution | Command |
|---|---|
| Debian / Ubuntu | `apt search nginx` |
| Rocky / Fedora / RHEL | `dnf search nginx` |
| Arch Linux | `pacman -Ss nginx` |

### 9.4 Viewing Package Information

Before installing software, you may want to inspect its description, version, and other metadata.

**Debian / Ubuntu:**

```bash
apt show package
```

Example:

```bash
apt show nginx
```

**Rocky Linux / Fedora / RHEL:**

```bash
dnf info package
```

Example:

```bash
dnf info nginx
```

**Arch Linux:**

```bash
pacman -Si package
```

Example:

```bash
pacman -Si nginx
```

**Comparison:**

| Distribution | Command |
|---|---|
| Debian / Ubuntu | `apt show nginx` |
| Rocky / Fedora / RHEL | `dnf info nginx` |
| Arch Linux | `pacman -Si nginx` |

### 9.5 Installing Packages

Package managers install software and resolve required dependencies.

**Debian / Ubuntu:**

```bash
sudo apt install nginx
```

Installs Nginx from configured repositories.

**Rocky Linux / Fedora / RHEL:**

```bash
sudo dnf install nginx
```

Installs Nginx from configured repositories.

**Arch Linux:**

```bash
sudo pacman -S nginx
```

Installs Nginx from configured repositories.

**Comparison:**

| Distribution | Command |
|---|---|
| Debian / Ubuntu | `sudo apt install nginx` |
| Rocky / Fedora / RHEL | `sudo dnf install nginx` |
| Arch Linux | `sudo pacman -S nginx` |

**Installing a local package file:**

Sometimes you download a package file directly instead of installing from a repository.

**Debian / Ubuntu:**

```bash
sudo dpkg -i package.deb
```

Installs a local Debian package file.

If dependencies are missing, you can often resolve them using:

```bash
sudo apt --fix-broken install
```

**Rocky Linux / Fedora / RHEL:**

```bash
sudo dnf install ./package.rpm
```

Installs a local RPM file and allows DNF to resolve dependencies.

**Important correction:** `pacman -S package` installs a package from configured repositories. It is not the command for installing an arbitrary local package file. For a local Arch package archive, use:

```bash
sudo pacman -U package.pkg.tar.zst
```

### 9.6 Removing or Uninstalling Packages

Package managers also provide commands to remove software.

**Debian / Ubuntu:**

```bash
sudo apt remove nginx
```

Removes the package but generally leaves its system configuration files.

To remove the package and its configuration files:

```bash
sudo apt purge nginx
```

**Rocky Linux / Fedora / RHEL:**

```bash
sudo dnf erase nginx
```

Removes the specified package.

**Arch Linux:**

```bash
sudo pacman -R nginx
```

Removes the specified package.

**Comparison:**

| Distribution | Command |
|---|---|
| Debian / Ubuntu | `sudo apt remove nginx` |
| Rocky Linux / Fedora / RHEL | `sudo dnf erase nginx` |
| Arch Linux | `sudo pacman -R nginx` |

**Useful Arch Linux variation:**

```bash
sudo pacman -Rns nginx
```

Removes the package along with unused dependencies and configuration files managed by Pacman.

<blockquote>
**⚠️ Production Warning**

Before removing packages from a production server, verify whether other applications or services depend on them. Removing a package may interrupt running workloads.
</blockquote>

---

## 10. 🚀 Real-World Production Scenario

### Scenario: Troubleshooting and Preparing a Linux Web Server

Imagine you are a DevOps engineer responsible for deploying an Nginx web server on a Linux machine.

The application is not accessible from the browser.

You need to inspect the network, verify connectivity, configure firewall access, and install the required software.

### Step 1: Identify the server's IP address

```bash
ip a
```

Check the network interface and identify its assigned IP address.

### Step 2: Inspect network interfaces

```bash
ifconfig -a
```

Check whether the expected network interface exists and whether it is active.

### Step 3: Check network connectivity

```bash
ping -c 4 8.8.8.8
```

Test basic IP connectivity.

### Step 4: Check DNS resolution

```bash
dig google.com
```

Verify whether DNS queries are resolving correctly.

### Step 5: Check existing listening ports

```bash
sudo ss -nutlp
```

Identify services listening on network ports.

### Step 6: Update package information

For Ubuntu:

```bash
sudo apt update
```

### Step 7: Install Nginx

```bash
sudo apt install nginx
```

### Step 8: Allow HTTP and HTTPS traffic

```bash
sudo ufw allow 80/tcp
sudo ufw allow 443/tcp
```

### Step 9: Verify firewall configuration

```bash
sudo ufw status
```

### Step 10: Test the web server locally

```bash
curl -I http://localhost
```

### Step 11: Verify the listening port

```bash
sudo ss -lntp | grep ':80'
```

**Expected result:**

The Nginx service should respond locally, and the server should have a listener on port `80`.

External accessibility also depends on cloud security groups, network ACLs, routing, and any additional firewall layers.

**DevOps Lesson:**

Troubleshooting networking requires checking multiple layers:

1. Network interface.
2. IP configuration.
3. Connectivity.
4. DNS resolution.
5. Application listening port.
6. Operating system firewall.
7. Cloud network security rules.

---

## 11. 📌 Quick Command Reference

### Networking Commands

| Command | Description |
|---|---|
| `ifconfig` | Display active network interfaces |
| `ifconfig -a` | Display all network interfaces |
| `ifconfig eth0` | Display details of `eth0` |
| `ip a` | Display interfaces and IP addresses |
| `ethtool eth0` | Inspect network driver and hardware settings |
| `netstat` | Display network statistics and connections |
| `netstat -a` | Display listening and non-listening sockets |
| `netstat -l` | Display listening sockets |
| `netstat -nutlp` | Display listening TCP/UDP ports and processes |
| `ping host` | Test network connectivity |
| `whois domain` | Display domain registration information |
| `dig domain` | Display DNS information |
| `dig -x address` | Perform reverse DNS lookup |
| `host domain` | Display DNS information |
| `wget LINK` | Download a resource |
| `curl LINK` | Retrieve content from a URL |
| `ufw allow port` | Allow traffic through UFW |
| `ufw status` | Display UFW status and rules |
| `nmcli` | Manage NetworkManager connections |

### Package Management Commands

| Operation | Debian / Ubuntu | Rocky / Fedora / RHEL | Arch Linux |
|---|---|---|---|
| Refresh package information | `sudo apt update` | `sudo dnf check-update` | `sudo pacman -Syu` |
| Upgrade packages | `sudo apt upgrade` | `sudo dnf upgrade` | `sudo pacman -Syu` |
| Search packages | `apt search nginx` | `dnf search nginx` | `pacman -Ss nginx` |
| View package information | `apt show nginx` | `dnf info nginx` | `pacman -Si nginx` |
| Install package | `sudo apt install nginx` | `sudo dnf install nginx` | `sudo pacman -S nginx` |
| Install local package | `sudo dpkg -i package.deb` | `sudo dnf install ./package.rpm` | `sudo pacman -U package.pkg.tar.zst` |
| Remove package | `sudo apt remove nginx` | `sudo dnf erase nginx` | `sudo pacman -R nginx` |

---

## 12. 🎯 Interview Questions

### Basic Level

**Q1. What is the difference between `ifconfig` and `ip a`?**

- `ifconfig` is a legacy networking utility.
- `ip a` is part of the modern `iproute2` networking utility suite.
- `ip a` is preferred on modern Linux systems.

**Q2. What is the purpose of the `ping` command?**

It sends ICMP Echo Requests to test basic network connectivity and measure response time.

**Q3. What is DNS?**

DNS translates human-readable domain names into IP addresses and provides other domain-related records.

**Q4. What is the purpose of `curl`?**

It transfers data to or from URLs and is commonly used to test HTTP endpoints and APIs.

**Q5. What is the difference between `wget` and `curl`?**

- `wget` is commonly used for downloading files.
- `curl` is commonly used for transferring data and testing APIs or HTTP endpoints.

### Intermediate Level

**Q6. What is the difference between `netstat -a` and `netstat -l`?**

- `netstat -a` displays listening and non-listening sockets.
- `netstat -l` displays only listening sockets.

**Q7. What does `netstat -nutlp` do?**

It displays listening TCP and UDP sockets using numerical addresses and port numbers, along with associated processes when permissions and platform support allow it.

**Q8. What is the difference between `dig` and `dig -x`?
