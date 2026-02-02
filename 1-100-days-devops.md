This document summarizes our deep dive into Linux system administration, security hardening, and geopolitical analysis. Use this for your study notes and exam preparation.

---

## 1. User & Access Management

Proper user creation is the first line of defense in a professional datacenter like Stratos.

* **Creating Expiring Users:**
* **Command:** `sudo useradd -e 2027-01-28 jim`
* **Purpose:** Automatically disables accounts after a project ends to prevent "ghost" accounts from being hacked.


* **The "Nologin" User (`ravi`):**
* **Command:** `sudo useradd -s /sbin/nologin ravi`
* * **Command:** `sudo cat /etc/passwd`

* **Concept:** Used for **Service Accounts**. The system can run apps as this user, but no human can log in to a shell.
* **Benefit:** If the app is compromised, the hacker is trapped in a "shell-less" environment with limited permissions (**Least Privilege**).



---

## 2. Server Hardening (SSH Security)

Securing the entry point to your App Servers.

* **Task:** Disable direct Root login.
* **File:** `/etc/ssh/sshd_config`
* **Change:** Set `PermitRootLogin no`.
* **Reason:** Forces admins to log in as a regular user first. This creates an **audit trail** (you know exactly *who* logged in before they became root) and stops automated "root" password-guessing attacks.
* **Action:** Always run `sudo systemctl restart sshd` after changes.

---

## 3. Permissions & The "777" Trap

Managing how scripts like `/tmp/xfusioncorp.sh` are handled.

* **Recommended (755):** `rwxr-xr-x`
* **Owner:** Read, Write, Execute.
* **Everyone else:** Read and Execute only.
* **Why:** Ensures the script works for everyone but cannot be maliciously edited or deleted by non-admins.


* **Dangerous (777):** `rwxrwxrwx`
* **The Risk:** Anyone can modify the script. A hacker could add a "backdoor" to a 777 script; when an admin runs it, the hacker gets admin control. **Never use 777 for scripts.**



---

## 4. Automation for "Nologin" Users

How a user who can't log in (like `ravi`) actually gets work done:

1. **Systemd Services:** The server starts the app as `ravi` automatically on boot.
2. **Cron Jobs:** Scheduled tasks run by the system as `ravi`.
* *Edit command:* `sudo crontab -u ravi -e`


3. **Impersonation:** An admin runs a command on behalf of the user:
* *Command:* `sudo -u ravi ./script.sh`
