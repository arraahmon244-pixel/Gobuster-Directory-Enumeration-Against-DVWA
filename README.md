# Gobuster Directory Enumeration Lab

> A hands-on web reconnaissance lab demonstrating directory enumeration using Gobuster against the Damn Vulnerable Web Application (DVWA) in a controlled Kali Linux environment.

![Platform](https://img.shields.io/badge/Platform-Kali%20Linux-blue)
![Tool](https://img.shields.io/badge/Tool-Gobuster-green)
![Target](https://img.shields.io/badge/Target-DVWA-orange)
![Status](https://img.shields.io/badge/Lab-Completed-success)

---

## Disclaimer

This project was conducted in a **local virtual laboratory** using DVWA (Damn Vulnerable Web Application). It is intended strictly for learning and authorized security testing. No public systems or third-party networks were scanned.

# Project Overview

Reconnaissance is the foundation of every penetration test. Before attempting to identify or exploit vulnerabilities, it is essential to understand the structure of the target application.

Many web applications expose only a handful of pages to users while hiding numerous directories, configuration files, backups, documentation, and administrative resources. Directory enumeration helps uncover these hidden assets and provides a clearer picture of the application's attack surface.

In this lab, I configured DVWA on Kali Linux and used **Gobuster** to perform directory enumeration using several commonly used options. Along the way, I also resolved issues involving Apache, MariaDB, and DVWA configuration before successfully completing the lab.

---
# Objectives

- Install and configure DVWA on Kali Linux.
- Configure Apache and MariaDB.
- Perform directory enumeration using Gobuster.
- Explore commonly used Gobuster options.
- Interpret scan results.
- Document the reconnaissance process.
  
  ---
# Lab Environment

| Component | Details |
|------------|----------|
| Operating System | Kali Linux |
| Web Server | Apache2 |
| Database | MariaDB |
| Target Application | DVWA |
| Enumeration Tool | Gobuster v3.8.2 |
| Wordlist | `/usr/share/wordlists/dirb/common.txt` |

---

# Installing Gobuster

Gobuster comes pre-installed on many Kali Linux systems. If it is unavailable, install it using:

```bash
sudo apt install gobuster
```

---

# Setting Up DVWA

Before running Gobuster, DVWA was deployed locally on Apache and connected to a MariaDB database.

The setup involved:
- Installing Apache2
- Installing MariaDB
- Creating the DVWA database
- Configuring `config.inc.php`
- Starting both services
- Initializing the database through `setup.php`

After successful configuration, the application became accessible locally.

**Target URL**

```text
http://127.0.0.1/DVWA
```

  
