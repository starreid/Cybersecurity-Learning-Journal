# 1 **Introduction**

This report documents the initial phase of the project to create a secure and controlled environment for testing a WordPress site. In this first stage, the focus was on the preparation of the base infrastructure: installation of the specialized operating system, configuration of security tools and creation of the structure that will serve as support for future tests.

The  project is part of a multidisciplinary team context, where developers use Local WP for development and I, as security manager, prepare the validation and test environment in Kali Linux.

At this stage, no practical tests have yet been carried out on the WordPress site, since it is in the initial phase of development by the team. 

# 2 **Objective of Phase 1**

The specific objectives of this first phase were:

- Prepare Kali  Linux as a security workstation
- Install and configure the essential tools for WordPress security analysis
- Create the directory structure and base scripts for future automation
- Familiarization with the tools and validation of their operation
- Document the entire process for reference Future of the team

# 3 **Phase 1 Schedule**


| Data         | Atividade                            | Estado    |
| ------------ | ------------------------------------ | --------- |
| [01/06/26]   | Kali Linux installation and update   | Completed |
| [01/06/26]   | Firewall configuration (iptables)    | Completed |
| [02/06/26]   | WP Site Installation for staging     | Completed |
| [03/06/26]   | WPScan installation                  | Completed |
| [04/06/26]   | Nikto installation                   | Completed |
| [04/06/26]   | PHP_CodeSniffer installation         | Completed |
| [05/06/26]   | ClamAV installation                  | Completed |
| [05/06/26]   | Creation of automation scripts       | Completed |
| [05/06/26]   | Tool operation tests                 | Completed |
| Next stage | Security tests to the WordPress site | Pending |

#  4.1 Preparation Kali Linux

The first step was to ensure that the system Base operation was properly update and secure. Kali Linux was chosen for being the reference distribution for security testing, native  including hundreds of specialized tools.

![Kali Version](kali_system.png)

![[disk_kali.png|454]]


# 4.2 Firewall Configuration

To ensure the isolation of the test environment, a local firewall was configured using iptables. This firewall allows only essential traffic and records unauthorized access attempts.

```bash

sudo tee /bin/firewall-staging.sh << 'EOF'
```

![Firewall_config](firewall_config.png)


```bash
sudo chmod +x /bin/firewall-staging.sh
```

```bash
sudo bash /bin/firewall-staging.sh
```

```bash
sudo iptables -L -n -v
```

![Firewall Config](firewall_done.png)


# 4.3 **Git Repository Configuration (GitHub)**


To manage the WordPress site code and facilitate team collaboration, GitHub was chosen as the central repository. GitHub enables version control of the theme, custom plugins, and site configurations, making it easier for developers to collaborate and integrate with security tools.

The repository will serve as the single source of truth for the code, which will later be analyzed and tested in the secure staging environment on Kali Linux

Currently, a staging WordPress site is already installed and running locally on Kali Linux (using a standard LAMP stack). The GitHub repository is prepared to receive the custom theme and plugins from the developers. As soon as the first commit is made, the code will be cloned into the staging site’s `wp-content` directory and the security analysis will begin.

# 4.4 **Installing WPScan**

WPScan is the industry-standard tool for identifying vulnerabilities in WordPress sites. Although it comes pre-installed on Kali Linux, it was updated to the latest version and configured with the API key to access the vulnerability database.

![WPSCAN](wpscan.png)


```bash
$ wpscan --help | grep -E "enumerate|password|stealthy
```
![WPSCAN|697](wps_tools.png)


# 4.5 Installing Nikto

Nikto is a web server scanner that identifies insecure configurations, dangerous files, and known vulnerabilities. It complements WPScan by analyzing the web server layer.

![](update_and_installation_nikto.png)

```bash
nikto -Help | head -30
```

![Nikto Setting](nikto_func.png)

# 4.6 Installing PHP_CodeSniffer

PHP_CodeSniffer (PHPCS) is an essential tool for static analysis of PHP code. It will be used to verify if the code developed by programmers follows WordPress security standards.

```bash

git clone https://github.com/squizlabs/PHP_CodeSniffer.git ~/php-codesniffer

sudo ln -s ~/php-codesniffer/bin/phpcs /usr/local/bin/phpcs
sudo ln -s ~/php-codesniffer/bin/phpcbf /usr/local/bin/phpcbf

chmod +x ~/php-codesniffer/bin/phpcs
chmod +x ~/php-codesniffer/bin/phpcbf

phpcs --version
```

![PHPCS](finish_phpcs.png)

# 4.7  Installing ClamAV


ClamAV is an open-source antivirus engine used to detect malicious files, malware, and suspicious code inside the WordPress installation (uploads, plugins, themes).

**Installation and update of virus definitions:**

```bash
sudo apt install clamav clamav-daemon -y
sudo systemctl stop clamav-freshclam
sudo freshclam
sudo systemctl start clamav-freshclam
sudo systemctl enable clamav-freshclam
clamscan --version
```

![scan](clamscan.png)


![Verify](verificate_clamscan.png)

ClamAV is now ready to scan the WordPress staging directory as soon as the code is available.

---
# 4.8 Directory Structure and Base Scripts


To keep the project organized, a dedicated directory structure was created along with base scripts that will automate the security checks once the site is live.

**Directory tree:**

```bash
mkdir -p ~/staging-seguranca/{scripts,logs,backups,relatorios,ferramentas}
tree ~/staging-seguranca/
```

**Tool verification script:**

```bash
nano ~/staging-seguranca/scripts/check-tools.sh
```

```bash
#!/bin/bash
echo "--- TOOL CHECK ---"
for tool in wpscan nikto phpcs clamscan nmap; do
    if command -v "$tool" &>/dev/null; then
        echo "1 $tool"
    else
        echo "0 $tool missing"
    fi
done

echo "WordPress staging site:"
[ -d "$HOME/Local Sites/staging-wordpress" ] && echo "Found" || echo "Not Found"

echo "Firewall:"
sudo iptables -L -n | grep -q DROP && echo "Active" || echo "Not Active"

```

![Script](status.png)

This script confirms that the entire environment is ready for the next phase.


---

# 5  Results of Phase 1


The first phase of the project has been successfully completed. The secure test environment on Kali Linux is fully operational and waiting for the first version of the custom WordPress code.

The following table summarises the current state:

| Component               | State          | Observations                         |
|------------------------|----------------|--------------------------------------|
| Kali Linux             | ✅ Ready       | Updated, firewall active             |
| WordPress staging site | ✅ Installed   | Empty, awaiting developer code       |
| GitHub repository      | ✅ Configured  | Linked to staging, currently empty   |
| WPScan                 | ✅ Ready       | API key configured, DB updated       |
| Nikto                  | ✅ Ready       | DB updated                           |
| PHP_CodeSniffer        | ✅ Ready       | WordPress Security standard added    |
| ClamAV                 | ✅ Ready       | Virus definitions up to date         |
| Automation scripts     | ✅ Created     | check-tools.sh and others            |
| Security tests         | ⏳ Pending     | To be executed when code arrives     |

All tools have been individually tested and are functioning correctly. The environment now awaits the first commit from the developers to begin practical security analysis.

# 6 Difficulties Encountered

During this phase, a few challenges were faced and resolved:

1. **Local WP / WordPress on Kali** – The Local WP tool is not officially supported on Kali, so manual installation of additional libraries (`libgtk-3-0`, `libnss3-tools`) was necessary. Alternatively, a standard LAMP stack can be used without issues.

2. **Firewall rules** – Initially, the firewall blocked loopback traffic, preventing local services from working. This was fixed by adding the rule `iptables -A INPUT -i lo -j ACCEPT`.

3. **PHP_CodeSniffer missing extensions** – PHPCS required the `xmlwriter` and `SimpleXML` extensions. Installing `php-xml php-tokenizer php-simplexml` resolved the problem.

4. **ClamAV freshclam failure** – The first virus database update failed due to insufficient memory. Closing other applications during the update solved it.