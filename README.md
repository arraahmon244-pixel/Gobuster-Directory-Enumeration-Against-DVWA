# Gobuster Directory Enumeration Lab

A hands-on web reconnaissance lab using **Gobuster** against **Damn Vulnerable Web Application (DVWA)** in a controlled Kali Linux virtual machine.

The goal of this lab was to understand how Gobuster can be used to discover directories, files, and other web resources that may not be directly visible through normal browsing.

---

## Disclaimer

This project was performed in a controlled local laboratory using DVWA, an intentionally vulnerable web application designed for security training.

All enumeration was performed against my own local environment. This project is intended for educational and authorized security testing only.

---

## Table of Contents

- [Overview](#overview)
- [Objectives](#objectives)
- [Lab Environment](#lab-environment)
- [Target Application](#target-application)
- [Preparing the Environment](#preparing-the-environment)
- [Installing Gobuster](#installing-gobuster)
- [Gobuster Directory Mode](#gobuster-directory-mode)
- [Basic Directory Enumeration](#basic-directory-enumeration)
- [Expanding Output to Full URLs](#expanding-output-to-full-urls)
- [Enumerating File Extensions](#enumerating-file-extensions)
- [Appending a Trailing Slash](#appending-a-trailing-slash)
- [Controlling Concurrency](#controlling-concurrency)
- [Setting a Custom Timeout](#setting-a-custom-timeout)
- [Saving Results to a File](#saving-results-to-a-file)
- [Running Gobuster Silently](#running-gobuster-silently)
- [Suppressing Status Information](#suppressing-status-information)
- [Whitelisting Status Codes](#whitelisting-status-codes)
- [Blacklisting Status Codes](#blacklisting-status-codes)
- [Hiding Response Length](#hiding-response-length)
- [Passing Cookies](#passing-cookies)
- [Setting a Custom User-Agent](#setting-a-custom-user-agent)
- [Using a Random User-Agent](#using-a-random-user-agent)
- [Using a Custom HTTP Method](#using-a-custom-http-method)
- [Disabling URL Canonicalization](#disabling-url-canonicalization)
- [Key Findings](#key-findings)
- [Challenges Encountered](#challenges-encountered)
- [Lessons Learned](#lessons-learned)
- [Conclusion](#conclusion)
- [References](#references)
- [Author](#author)

---

# Overview

Web directory enumeration is an important part of the reconnaissance phase of a penetration test.

A web application may expose only a login page or a small number of links to normal users, while additional directories, files, documentation, configuration resources, and development artifacts may still exist on the server.

In this lab, I used **Gobuster** to explore the structure of DVWA and tested a range of options available in Gobuster's directory enumeration mode.

The exercise was not just about running one command. I wanted to understand how changing different Gobuster options affects the way results are discovered, displayed, filtered, and saved.

---

# Objectives

The main objectives of this lab were to:

- Understand Gobuster directory enumeration.
- Configure and access DVWA locally.
- Learn how Gobuster's directory mode works.
- Use the `common.txt` wordlist for enumeration.
- Discover directories and files on the target.
- Experiment with different Gobuster options.
- Understand HTTP status codes returned during enumeration.
- Customize scan speed and timeout settings.
- Test cookies and User-Agent values.
- Filter unwanted results.
- Save enumeration results for documentation.
- Build practical reconnaissance experience in a controlled environment.

---

# Lab Environment

| Component | Details |
|---|---|
| Operating System | Kali Linux |
| Virtualization | VMware |
| Web Server | Apache2 |
| Database | MariaDB |
| Target | DVWA |
| Enumeration Tool | Gobuster v3.8.2 |
| Wordlist | `/usr/share/wordlists/dirb/common.txt` |
| Target URL | `http://127.0.0.1/DVWA` |

---

# Target Application

## Damn Vulnerable Web Application (DVWA)

DVWA is an intentionally vulnerable web application designed for practicing web application security testing in a legal and controlled environment.

I deployed DVWA locally inside my Kali Linux virtual machine and used it as the target for the Gobuster exercises.

The final working target was:

```text
http://127.0.0.1/DVWA
```

### DVWA Target

![DVWA Target Application](screenshots/01-dvwa-target-application.png)

---

# Preparing the Environment

Before starting the enumeration, I configured the required web application environment.

The main components were:

- Apache2
- MariaDB
- PHP
- DVWA

There were several configuration issues during setup, including Apache and MariaDB startup problems and a DVWA database authentication error.

After troubleshooting the services and configuring the DVWA database credentials, the application became accessible through the browser.

---

# Installing Gobuster

Gobuster was available in the Kali Linux environment.

I verified the installation and version before beginning the enumeration exercises.

```bash
gobuster version
```

The lab used:

```text
Gobuster v3.8.2
```

### Gobuster Installation

![Gobuster Installed](screenshots/02-gobuster-installed-kali.png)

---

# Gobuster Directory Mode

Gobuster supports several enumeration modes. For this lab, I focused on **directory enumeration**.

The help menu can be viewed with:

```bash
gobuster dir -h
```

Directory mode is designed to enumerate directories and files using a wordlist.

Some of the options available include:

- Target URL
- Wordlist
- Cookies
- HTTP method
- User-Agent
- Random User-Agent
- Redirect handling
- Timeout
- Threads
- Status-code filtering
- Output options
- File extensions

### Directory Mode Help

![Gobuster Directory Mode](screenshots/03-directory-mode.png)

---

# Basic Directory Enumeration

My first attempt used:

```bash
gobuster dir -u http://192.168.1.12 -w common.txt
```

This failed because `common.txt` was not located in the current directory.

The error was:

```text
wordlist file "common.txt" does not exist
```

This was a useful reminder that Gobuster needs the correct path to the wordlist.

I then used the actual DIRB wordlist installed on Kali:

```bash
gobuster dir \
-u http://127.0.0.1/DVWA \
-w /usr/share/wordlists/dirb/common.txt
```

The scan completed successfully.

### Basic Directory Scan

![Basic Directory Scan](screenshots/04-basic-directory-scan.png)

The scan returned several interesting resources, including:

```text
.git/HEAD
config/
database/
docs/
external/
robots.txt
tests/
php.ini
phpinfo.php
```

The results demonstrate why directory enumeration is useful during reconnaissance.

---

# Expanding Output to Full URLs

Gobuster can display complete URLs instead of only the discovered path.

I tested the expanded output option using:

```bash
gobuster dir \
-u http://127.0.0.1/DVWA \
-w /usr/share/wordlists/dirb/common.txt \
-e
```

This makes the results easier to copy into notes and reports.

### Full URL Output

![Expand Output to Full URLs](screenshots/05-expand-output-full-urls.png)

---

# Enumerating File Extensions

Directories are not the only resources worth discovering.

Web applications commonly contain files such as:

- `.php`
- `.html`
- `.txt`
- `.bak`
- `.conf`

Gobuster can search for specific extensions using the `-x` option.

Example:

```bash
gobuster dir \
-u http://127.0.0.1/DVWA \
-w /usr/share/wordlists/dirb/common.txt \
-x php,txt,html
```

This allows the enumeration process to search for files with the selected extensions.

### File Extension Enumeration

![Enumerate File Extensions](screenshots/06-enumerate-file-extension.png)

---

# Appending a Trailing Slash

The `-f` option can be used to append a trailing slash to requests.

Example:

```bash
gobuster dir \
-u http://127.0.0.1/DVWA \
-w /usr/share/wordlists/dirb/common.txt \
-f
```

This can be useful when testing directory-style resources.

### Trailing Slash

![Append a Trailing Slash](screenshots/07-append-a-trail-slash.png)

---

# Controlling Concurrency

Gobuster uses multiple threads to perform requests.

The number of threads can be adjusted with `-t`.

Example:

```bash
gobuster dir \
-u http://127.0.0.1/DVWA \
-w /usr/share/wordlists/dirb/common.txt \
-t 50
```

Increasing the number of threads can make enumeration faster, but it also increases the number of requests being sent at the same time.

For this reason, thread count should be chosen according to the environment being tested.

### Concurrency

![Control Concurrency](screenshots/08-control-concurrency.png)

---

# Setting a Custom Timeout

Gobuster has a default HTTP timeout, but it can be changed when necessary.

The timeout option can be used as follows:

```bash
gobuster dir \
-u http://127.0.0.1/DVWA \
-w /usr/share/wordlists/dirb/common.txt \
--timeout 20s
```

A custom timeout can be useful when a target responds slowly or when network conditions are less reliable.

### Custom Timeout

![Set a Custom Timeout](screenshots/09-set-a-custom-timeout.png)

---

# Saving Results to a File

Reconnaissance results should be documented rather than lost in the terminal.

Gobuster provides an output option that allows results to be saved to a file.

Example:

```bash
gobuster dir \
-u http://127.0.0.1/DVWA \
-w /usr/share/wordlists/dirb/common.txt \
-o gobuster-results.txt
```

This is especially useful when the results need to be reviewed later or included in a penetration-testing report.

### Saved Output

![Save Output to a File](screenshots/10-save-output-to-a-file.png)

---

# Running Gobuster Silently

Gobuster also provides options for reducing the amount of information displayed in the terminal.

A quieter output can make long enumeration scans easier to review.

Example:

```bash
gobuster dir \
-u http://127.0.0.1/DVWA \
-w /usr/share/wordlists/dirb/common.txt \
-q
```

### Quiet Mode

![Run Silently](screenshots/11-run-silently.png)

---

# Suppressing Status Information

HTTP status codes are useful during enumeration, but sometimes the tester may want to reduce the amount of status information displayed.

I tested Gobuster's status-output controls as part of the lab.

The purpose is to make the terminal output easier to focus on when performing larger scans.

### Status Output

![Suppress Status Code](screenshots/12-suppress-status-code.png)

---

# Whitelisting Status Codes

HTTP status codes provide important information about how the web server responds to different requests.

Gobuster can be configured to focus on specific status codes.

For example:

```bash
gobuster dir \
-u http://127.0.0.1/DVWA \
-w /usr/share/wordlists/dirb/common.txt \
-s 200,301,302
```

This allows the enumeration results to focus on responses that are more interesting for the current assessment.

### Whitelisted Status Codes

![Whitelist Specific Status Codes](screenshots/13-whitelist-specific-status-code.png)

---

# Blacklisting Status Codes

The opposite approach is to exclude specific status codes.

For example:

```bash
gobuster dir \
-u http://127.0.0.1/DVWA \
-w /usr/share/wordlists/dirb/common.txt \
-b 404,500
```

In this lab, I used status-code blacklisting to remove unwanted responses from the results.

### Blacklisted Status Codes

![Blacklist Status Codes](screenshots/14-blacklist-status-code.png)

---

# Hiding Response Length

Gobuster can also work with response lengths when determining which results are useful.

Response length can sometimes help identify repeated or default responses that would otherwise create unnecessary noise during enumeration.

### Response Length Filtering

![Hide Response Length](screenshots/15-hide-the-response-length.png)

---

# Passing Cookies

Some web applications expose additional resources only after authentication.

Gobuster supports passing cookies with requests using the `-c` option.

Example:

```bash
gobuster dir \
-u http://127.0.0.1/DVWA \
-w /usr/share/wordlists/dirb/common.txt \
-c "PHPSESSID=example"
```

This allows enumeration to be performed using the session context represented by the cookie.

### Cookies

![Pass Cookies](screenshots/16-pass-cookies.png)

> The cookie value shown above is only an example. In an actual assessment, the appropriate authorized session cookie would be supplied.

---

# Setting a Custom User-Agent

Web servers can identify clients through the HTTP User-Agent header.

Gobuster allows a custom User-Agent to be specified using `-a`.

Example:

```bash
gobuster dir \
-u http://127.0.0.1/DVWA \
-w /usr/share/wordlists/dirb/common.txt \
-a "Mozilla/5.0"
```

This is useful when testing how an application responds to different client identifiers.

### Custom User-Agent

![Set Custom User-Agent](screenshots/17-set-custom-user-agent.png)

---

# Using a Random User-Agent

Gobuster can also generate a random User-Agent for requests.

Example:

```bash
gobuster dir \
-u http://127.0.0.1/DVWA \
-w /usr/share/wordlists/dirb/common.txt \
--random-agent
```

This changes the User-Agent between requests and can be useful when testing how filtering mechanisms respond to different client identifiers.

### Random User-Agent

![Random User-Agent](screenshots/18-random-agent.png)

---

# Using a Custom HTTP Method

Gobuster normally uses the HTTP `GET` method for directory enumeration.

The HTTP method can be changed using `-m`.

Example:

```bash
gobuster dir \
-u http://127.0.0.1/DVWA \
-w /usr/share/wordlists/dirb/common.txt \
-m GET
```

Other HTTP methods can be tested where the application and assessment requirements justify doing so.

### Custom HTTP Method

![Custom HTTP Method](screenshots/19-setup-a-custom-http-method.png)

---

# Disabling URL Canonicalization

Gobuster normally performs URL handling and canonicalization before making requests.

The `--nc` option disables URL canonicalization.

Example:

```bash
gobuster dir \
-u http://127.0.0.1/DVWA \
-w /usr/share/wordlists/dirb/common.txt \
--nc
```

This can be useful when testing applications where different URL representations may produce different responses.

### URL Canonicalization

![Disable URL Canonicalization](screenshots/20-disable-url-canonicalization.png)

---

# Key Findings

The directory enumeration identified several interesting resources within DVWA.

Some of the notable results included:

| Resource | HTTP Status | Observation |
|---|---:|---|
| `.git/HEAD` | 200 | Git metadata was accessible |
| `.htaccess` | 403 | Resource exists but access was denied |
| `.htpasswd` | 403 | Resource exists but access was denied |
| `config/` | 301 | Configuration directory discovered |
| `database/` | 301 | Database-related directory discovered |
| `docs/` | 301 | Documentation directory discovered |
| `external/` | 301 | External resources directory discovered |
| `tests/` | 301 | Testing resources discovered |
| `robots.txt` | 200 | Robots file accessible |
| `php.ini` | 200 | PHP configuration file accessible |
| `index.php` | 302 | Redirected to `login.php` |

The most notable result was:

```text
.git/HEAD
```

Returning HTTP `200` indicates that Git metadata was accessible from the web application.

In a real production environment, exposed version-control metadata could potentially reveal useful information about an application and should therefore be investigated and secured.

---

# Understanding the HTTP Status Codes

The scan produced several different HTTP response codes.

### HTTP 200

The requested resource was successfully returned.

Example:

```text
.git/HEAD (Status: 200)
```

### HTTP 301

The resource redirected to another URL.

Example:

```text
config (Status: 301)
```

### HTTP 302

The resource temporarily redirected somewhere else.

Example:

```text
index.php (Status: 302) --> login.php
```

### HTTP 403

The resource exists, but access is forbidden.

Example:

```text
.htaccess (Status: 403)
.htpasswd (Status: 403)
```

### HTTP 404

The requested resource was not found.

Gobuster normally treats 404 as a negative response during directory enumeration.

---

# Challenges Encountered

This lab was not completely straightforward. I encountered several issues while setting up the environment and running the enumeration.

## 1. Incorrect Target Address

My initial documentation referenced:

```text
192.168.1.12
```

However, the DVWA instance I eventually configured was running locally inside the Kali VM.

The working target became:

```text
http://127.0.0.1/DVWA
```

This was an important networking lesson because the target address depends on where the vulnerable application is actually running.

---

## 2. Wordlist Path Error

My first Gobuster command used:

```bash
-w common.txt
```

Gobuster returned:

```text
wordlist file "common.txt" does not exist
```

I corrected the command by using the full path:

```bash
-w /usr/share/wordlists/dirb/common.txt
```

---

## 3. Apache Service Problems

During the DVWA setup, Apache initially failed to start.

I checked the service using:

```bash
sudo systemctl status apache2
```

and tested the Apache configuration with:

```bash
sudo apache2ctl -t
```

Eventually Apache started successfully and began listening on port 80.

---

## 4. MariaDB Problems

MariaDB also initially failed to start during the setup.

After troubleshooting, the service was successfully started:

```bash
sudo systemctl start mariadb
```

I then verified that MariaDB was accepting connections.

---

## 5. DVWA Database Authentication

DVWA initially returned an HTTP 500 error because the database credentials in the configuration did not match the MariaDB user credentials.

The Apache error log showed:

```text
Access denied for user 'dvwa'@'localhost'
```

I checked the DVWA configuration and the MariaDB users, corrected the database configuration, and eventually reached the DVWA setup page successfully.

---

# Lessons Learned

This lab taught me that reconnaissance is not simply about knowing the syntax of a tool.

A successful enumeration depends on understanding the entire environment.

Some of my main takeaways were:

### 1. Verify the target before scanning

Before running Gobuster, I needed to make sure I was targeting the correct system and service.

### 2. Wordlist paths matter

A valid wordlist is essential. Using only the filename does not work if the file is not in the current directory.

### 3. HTTP status codes matter

A `200`, `301`, `302`, and `403` response can each tell a different story about the target.

### 4. Enumeration creates noise

Large scans can produce a lot of output. Filtering, status-code selection, response-length handling, and quiet output can make the results easier to analyze.

### 5. Reconnaissance should be documented

Saving results to a file is useful because terminal output can easily be lost.

### 6. Tool knowledge is more than memorizing commands

Understanding why a flag is being used is more valuable than simply memorizing its syntax.

---

# What I Would Investigate Next

The enumeration results provide several areas that could be investigated further in a controlled lab environment.

For example:

- Investigate the exposed `.git/HEAD` resource.
- Review discovered directories.
- Examine the application's robots.txt.
- Investigate configuration-related files.
- Enumerate additional file extensions.
- Perform authenticated enumeration where appropriate.
- Compare responses using different HTTP methods.
- Analyze unusual HTTP status codes.
- Continue with vulnerability-specific testing inside DVWA.

These steps would move the exercise from basic reconnaissance toward deeper web application security testing.

---

# Conclusion

This lab gave me practical experience using Gobuster for web directory enumeration against a deliberately vulnerable application.

Rather than only running the default command, I tested different options for:

- Directory enumeration
- Full URL output
- File extensions
- Trailing slashes
- Thread control
- Timeouts
- Output files
- Quiet output
- Status-code filtering
- Response-length filtering
- Cookies
- User-Agent customization
- Random User-Agents
- HTTP methods
- URL canonicalization

The most valuable part of the exercise was troubleshooting the environment along the way. Setting up DVWA required working with Apache, MariaDB, PHP, database authentication, and Linux services before I could even begin the enumeration.

Overall, the lab improved my understanding of web reconnaissance and showed me how a simple directory-enumeration tool can provide useful information about an application's attack surface.

---

# References

- Gobuster — directory and file enumeration tool
- Damn Vulnerable Web Application (DVWA)
- DIRB common wordlist included in Kali Linux
- Gobuster documentation and command-line help

---

# Author

**Abdurrahmon Idris**

Cybersecurity Student | Security Enthusiast

This repository documents my hands-on cybersecurity learning and laboratory exercises.

---
