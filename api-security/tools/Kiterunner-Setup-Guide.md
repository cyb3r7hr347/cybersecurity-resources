# Kiterunner Setup and Basic Usage Guide

This guide will walk you through downloading, installing, and setting up Kiterunner on your Kali Linux environment. Kiterunner is a powerful tool for API enumeration, helping to discover hidden API endpoints quickly and efficiently.

## Installation Steps

1. **Download the Latest Release**

   Head over to the [Kiterunner Releases page](https://github.com/assetnote/kiterunner/releases) on GitHub and download the latest release file that matches your system's architecture.

2. **Create a Directory and Extract the Files**

   Once downloaded, create a directory for Kiterunner, move the downloaded file into it, and extract it as follows:

   ```bash
   ┌──(kali㉿kali)-[~]
   └─$ mkdir kiterunner
   ┌──(kali㉿kali)-[~]
   └─$ mv ~/Downloads/kiterunner_<version>_linux_amd64.tar.gz kiterunner
   ┌──(kali㉿kali)-[~]
   └─$ cd kiterunner                                                            
   ┌──(kali㉿kali)-[~/kiterunner]
   └─$ tar -xf kiterunner_<version>_linux_amd64.tar.gz
   ```

3. **(Optional) Move to /opt for Centralized Tool Storage**

   To keep your tools organized, you can move the Kiterunner executable to `/opt`, a common directory for storing security tools on Linux:

   ```bash
   ┌──(kali㉿kali)-[~/kiterunner]
   └─$ sudo mv kr /opt/kr  
   ```

4. **Create a Symlink for Easier Access**

   Create a symbolic link to run `kr` from anywhere on your terminal without specifying the full path:

   ```bash
   ┌──(kali㉿kali)-[~/kiterunner]
   └─$ sudo ln -s /opt/kr /usr/local/bin/kr    
   ```

## Basic Commands and Usage

1. **List Available Wordlists**

   Check available wordlists included with Kiterunner:

   ```bash
   ┌──(kali㉿kali)-[~/kiterunner]
   └─$ kr wordlist list
   ```

2. **Basic API Enumeration Scan**

   To run an API enumeration scan against a target host, use the following syntax. Replace `<host>` with your target host and select a wordlist (e.g., `apiroutes-240528`):

   ```bash
   ┌──(kali㉿kali)-[~]
   └─$ kr scan <host> -A apiroutes-240528
   ```

This setup ensures that Kiterunner is organized and readily accessible, making API reconnaissance more efficient. Happy hacking!