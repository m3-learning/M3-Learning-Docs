# Guide to Setup IPMI Credentials

## Overview

This guide will walk you through the process of setting up IPMI credentials for your server. IPMI (Intelligent Platform Management Interface) is a protocol that allows you to manage your server remotely.

## Prerequisites

- A server with IPMI support
- A way to access the server's IPMI interface (e.g. a console cable, SSH, or a web browser)
- IPMI Tool (e.g. ipmitool, IPMI CLI, or a web-based IPMI tool)

## Installing IPMI Tool

```bash
sudo apt-get install ipmitool
```

## Steps

1. SSH into the server
2. Run the following command to set the IPMI credentials

```bash
ipmitool user disable 4
ipmitool user set name 4 m3learning
ipmitool user set password 4 'anQc7Kxrj8rQf8DJ'
ipmitool user enable 4
ipmitool channel setaccess 1 4 link=on ipmi=on callin=on privilege=4
```

## Verify the IPMI Credentials

```bash
ipmitool user list
```

