## 1. Unix User Management: The `ravi` Case Study

**The Goal:** Create a user named `ravi` with a **non-interactive shell** on a server.

### The Core Command

```bash
sudo useradd -s /sbin/nologin ravi

```

* **`-s /sbin/nologin`**: This prevents a human from logging into a command prompt as `ravi`.

### Why create a user who can't login?

1. **Security (Service Accounts):** Apps run as `ravi`. If the app is hacked, the attacker has no shell access to run more commands.
2. **Least Privilege:** `ravi` only has permission to touch files he owns. He can't break the rest of the system.
3. **Isolation:** Separates different services (e.g., Logistics vs. Healthcare) so they can't interfere with each other.

---

## 3. Managing Files for "Nologin" Users

Since `ravi` can't log in, **you** (the admin) manage things for him using `sudo`.

| Task | Command Example | Explanation |
| --- | --- | --- |
| **Give ownership** | `sudo chown ravi:ravi data.csv` | Makes `ravi` the owner so his apps can read it. |
| **Run as ravi** | `sudo -u ravi touch test.log` | Creates a file *as if* you were ravi. |
| **Edit file** | `sudo nano /home/ravi/config.txt` | Admins can edit any user's files directly. |

---

## 4. How "Nologin" Users Run Scripts

If `ravi` can't type a command, the system must trigger it.

* **Systemd Services:** For apps that run 24/7 (like a web server).
* *Example:* Defining `User=ravi` in a `.service` file.


* **Cron Jobs:** For scheduled tasks (like a daily 2 AM cleanup).
* *Example:* `sudo crontab -u ravi -e`


* **Manual Background:**
* *Example:* `sudo -u ravi ./script.sh &` (The `&` puts it in the background).
