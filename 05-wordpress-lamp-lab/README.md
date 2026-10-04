# Lab 05: WordPress on a LAMP Stack

**Lab:** Server and database administration
**Status:** In progress (installation and first database practice completed)
**Stack:** Ubuntu Server 26.04.1 LTS, Apache 2.4.66, MariaDB 11.8.6, PHP 8.5.4, WordPress 7.1.2

Passwords in this document are replaced with placeholders such as `<your-ssh-password>`. Use your own values.

---

## 1. Objective

Build a WordPress website from scratch on a Linux server, the way a company's internal or public website is run, then use its database to practice SQL, backups and restores.

LAMP stands for the four layers of the setup:

| Layer | Software | Role |
|---|---|---|
| **L**inux | Ubuntu Server | The operating system |
| **A**pache | Apache HTTP Server | Receives requests from browsers and returns web pages |
| **M**ariaDB | MariaDB (MySQL-compatible) | Stores all posts, pages, users and settings |
| **P**HP | PHP | Builds each page by reading from the database |

When someone opens the site, Apache receives the request, PHP runs WordPress's code, PHP queries MariaDB for the content, and the finished HTML page is sent back to the browser.

---

## 2. Environment

| Item | Value |
|---|---|
| Host | Windows 11 laptop |
| Hypervisor | Oracle VirtualBox |
| VM name | Ubuntu-Server |
| Server hostname | `wp-server` |
| ISO | `ubuntu-26.04.1-live-server-amd64.iso` |
| RAM / CPUs / Disk | 4096 MB / 2 / 25 GB |
| Adapter 1 | Host-only (`enp0s3`): lets the laptop reach the server, address `192.168.56.103` |
| Adapter 2 | NAT (`enp0s8`): gives the server internet access, address `10.0.3.15` |

```
Windows laptop (browser + PowerShell)
        |
   Host-only network 192.168.56.0/24
        |
 wp-server 192.168.56.103
   Apache -> PHP -> MariaDB
        |
   NAT (internet for updates and downloads)
```

---

## 3. Installation

### 3.1 Ubuntu Server

Installer choices:

| Screen | Choice |
|---|---|
| Language | English |
| Keyboard | Japanese (to match the JIS keyboard) |
| Installer update | Continue without updating |
| Install type | Ubuntu Server (not minimized) |
| Network | Defaults (DHCP on both adapters) |
| Storage | Use an entire disk, LVM enabled, no encryption |
| Profile | Name `<your-name>`, server `wp-server`, username `<your-username>`, password `<your-ssh-password>` |
| Ubuntu Pro | Skipped |
| SSH | **Install OpenSSH server** ticked |
| Snaps | None |

The guided storage layout gave `/` only 11.5 GB and left 11.5 GB free in the LVM volume group. This is normal for Ubuntu and enough for this lab. The free space can be added later with `lvextend` and `resize2fs` without reinstalling.

### 3.2 Remote access with SSH

All further work was done from Windows PowerShell instead of the VM window, so commands can be copied and pasted.

```powershell
ssh <your-username>@192.168.56.103
```

On the first connection, SSH shows the server's key fingerprint and asks to confirm it. After answering `yes`, the key is saved in Windows' `known_hosts` file, and future connections are checked against it.

### 3.3 System update

```bash
sudo apt update && sudo apt upgrade -y
```

`apt update` refreshes the list of available packages; `apt upgrade` installs the newer versions. 21 packages were upgraded. This is always the first step on a new server, because security fixes arrive through updates.

### 3.4 LAMP stack

```bash
sudo apt install -y apache2 mariadb-server php libapache2-mod-php php-mysql php-curl php-gd php-mbstring php-xml php-zip php-intl
```

| Package | Purpose |
|---|---|
| `apache2` | Web server |
| `mariadb-server` | Database server |
| `php`, `libapache2-mod-php` | PHP, loaded inside Apache |
| `php-mysql` | Lets PHP talk to MariaDB |
| `php-curl` | Lets WordPress contact other servers (updates, plugins) |
| `php-gd` | Image processing (thumbnails) |
| `php-mbstring`, `php-intl` | Multi-byte text and language support, including Japanese |
| `php-xml`, `php-zip` | XML handling and installing plugins/themes from zip files |

```bash
sudo systemctl restart apache2
```

**Test:** opening `http://192.168.56.103` from the laptop showed the **Apache2 Default Page ("It works!")**, confirming the web server and the network path.

### 3.5 Securing MariaDB

```bash
sudo mariadb-secure-installation
```

| Question | Answer | Why |
|---|---|---|
| Current root password | Enter (none) | Root uses unix_socket login on Ubuntu |
| Switch to unix_socket | n | Already protected |
| Change root password | n | Not needed with socket login |
| Remove anonymous users | Y | Anonymous accounts let anyone log in |
| Disallow root login remotely | Y | Root may only connect from the server itself |
| Remove test database | Y | The test database is open to everyone |
| Reload privilege tables | Y | Apply the changes |

### 3.6 Database and user for WordPress

```bash
sudo mariadb -e "CREATE DATABASE wordpress CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci; CREATE USER 'wpuser'@'localhost' IDENTIFIED BY '<your-db-password>'; GRANT ALL PRIVILEGES ON wordpress.* TO 'wpuser'@'localhost'; FLUSH PRIVILEGES;"
```

| Statement | Meaning |
|---|---|
| `CREATE DATABASE wordpress ... utf8mb4` | An empty database using full Unicode (all languages and emoji) |
| `CREATE USER 'wpuser'@'localhost'` | A database account that can only connect from the server itself |
| `GRANT ALL PRIVILEGES ON wordpress.*` | Full rights on the `wordpress` database **only** |
| `FLUSH PRIVILEGES` | Apply the permission changes |

Giving WordPress its own limited user (instead of root) means that if the website were compromised, the attacker could not reach other databases on the server. This is the principle of least privilege.

### 3.7 WordPress files

```bash
cd /tmp && wget https://wordpress.org/latest.tar.gz && tar -xzf latest.tar.gz
sudo rm /var/www/html/index.html
sudo cp -r /tmp/wordpress/* /var/www/html/
sudo chown -R www-data:www-data /var/www/html
```

| Command | Meaning |
|---|---|
| `wget` | Download the latest WordPress (34 MB) |
| `tar -xzf` | Extract the compressed archive |
| `rm .../index.html` | Remove the Apache test page so WordPress loads instead |
| `cp -r` | Copy WordPress into Apache's document root, `/var/www/html` |
| `chown -R www-data:www-data` | Give ownership to `www-data`, the user Apache runs as, so WordPress can write uploads and its config file |

### 3.8 Web installer

Opened `http://192.168.56.103` and completed the setup:

| Field | Value |
|---|---|
| Database name | `wordpress` |
| Username | `wpuser` |
| Password | `<your-db-password>` |
| Database host | `localhost` |
| Table prefix | `wp_` |
| Site title | Factory Lab |
| Admin username | `<your-wp-admin-username>` (not `admin`, which attackers try first) |
| Admin password | `<your-wp-admin-password>` |
| Search engine visibility | Discouraged (lab site) |

Result: WordPress 7.1.2 running the Twenty Twenty-Five theme, admin dashboard at `http://192.168.56.103/wp-admin`.

---

## 4. Database Practice

### 4.1 Logging in as the WordPress user

```bash
mariadb -u wpuser -p
```

```sql
SHOW DATABASES;
```
```
+--------------------+
| Database           |
+--------------------+
| information_schema |
| wordpress          |
+--------------------+
```
`wpuser` only sees its own database. Other databases on the server (such as `mysql`, which holds all accounts) are invisible to it: the limited permissions are working.

```sql
USE wordpress;
SHOW TABLES;
```

| Tables | What they store |
|---|---|
| `wp_posts`, `wp_postmeta` | Posts, pages, and their extra details |
| `wp_comments`, `wp_commentmeta` | Comments |
| `wp_users`, `wp_usermeta` | User accounts, roles and preferences |
| `wp_options` | Site settings: title, URL, theme, active plugins |
| `wp_terms`, `wp_term_taxonomy`, `wp_term_relationships`, `wp_termmeta` | Categories and tags |
| `wp_links` | Legacy feature, usually empty |

### 4.2 Reading data

```sql
SELECT ID, user_login, user_email, user_registered FROM wp_users;
```
```
+----+------------+--------------------+---------------------+
| ID | user_login | user_email         | user_registered     |
+----+------------+--------------------+---------------------+
|  1 | <your-wp-admin-username> | <your-email>       | 2026-10-03 10:32:54 |
+----+------------+--------------------+---------------------+
```

```sql
SELECT user_login, user_pass FROM wp_users;
```
```
| <your-wp-admin-username> | $wp$2y$12$<hash-redacted> |
```
WordPress never stores the real password, only a **hash**. The prefix shows how it was made: `$wp$` is WordPress's marker, `$2y$` means bcrypt, and `$12$` is the cost factor (how much work each guess takes). At login, WordPress hashes the typed password and compares the result.

```sql
SELECT option_name, option_value FROM wp_options
WHERE option_name IN ('blogname', 'blogdescription', 'siteurl', 'home');
```
```
+-----------------+-----------------------+
| option_name     | option_value          |
+-----------------+-----------------------+
| blogdescription |                       |
| blogname        | Factory Lab           |
| home            | http://192.168.56.103 |
| siteurl         | http://192.168.56.103 |
+-----------------+-----------------------+
```
WordPress stores its own address in `home` and `siteurl`. If the server's IP address changes, the site tries to load from the old address and appears broken; the fix is updating these two rows.

### 4.3 Changing the site with SQL

```sql
UPDATE wp_options SET option_value = 'Factory Lab - <your-city>' WHERE option_name = 'blogname';
UPDATE wp_options SET option_value = 'Network and server practice site' WHERE option_name = 'blogdescription';
```

After refreshing the browser, the site title and tagline changed, without using the WordPress admin panel. The database and the website are the same data seen from two sides.

An `UPDATE` without a `WHERE` clause changes every row in the table, which is why the `WHERE` part must always be checked before running it.

### 4.4 Backup

```bash
mariadb-dump -u wpuser -p wordpress > ~/wordpress-backup.sql
ls -lh ~/wordpress-backup.sql
```
```
-rw-rw-r-- 1 <your-username> <your-username> 119K Oct  4 17:34 /home/<your-username>/wordpress-backup.sql
```

The whole site's database fits in a 119 KB text file. Its content is plain SQL:

- `DROP TABLE IF EXISTS`: delete the table if it exists
- `CREATE TABLE`: recreate it with the same structure
- `INSERT INTO`: put every row back

Restoring it rebuilds the database exactly as it was at the moment of the backup.

### 4.5 Break and restore (next step)

```bash
# Simulate damage: delete the first post and change the title
mariadb -u wpuser -p wordpress -e "DELETE FROM wp_posts WHERE ID = 1; UPDATE wp_options SET option_value = 'HACKED' WHERE option_name = 'blogname';"

# Restore from the backup
mariadb -u wpuser -p wordpress < ~/wordpress-backup.sql
```

In the backup, `>` sends the database out into a file; in the restore, `<` feeds the file back into the database.

---

## 5. Problems Encountered

**Installer stuck at "Loading essential drivers".** The Ubuntu installer sits on this screen for several minutes in VirtualBox. Fixes if it doesn't continue: raise video memory to 128 MB (Settings > Display), or boot with the `nomodeset` kernel option (press `e` on the boot menu and add it before `---`). It eventually continued.

**Pasted command split in two.** The long `apt install` line broke across two lines when pasted, so the second half ran on its own and returned `php-gd: command not found`. Running the full command again on one line installed the missing packages; already-installed ones were skipped.

**"Error establishing a database connection" in the web installer.** Caused by a typo in the form. Testing the login directly on the server separates a database problem from a form problem:

```bash
mariadb -u wpuser -p<your-db-password> wordpress -e "SELECT 'login works';"
```

**"Access denied for user 'wpuser'".** The SSH password was used instead of the database password. The Linux user and the database user are separate accounts with separate passwords.

---

## 6. Apache vs. Nginx

Checking the response headers of a real company website showed `Server: nginx`, while this lab uses Apache. WordPress, PHP and MariaDB work the same with either; only the web server layer changes.

| | Apache (LAMP) | Nginx (LEMP) |
|---|---|---|
| Runs PHP | Inside Apache (`mod_php`) | In a separate service, `php-fpm` |
| Strength | Simple, widely documented, `.htaccess` per-folder settings | Fast and efficient under heavy traffic |
| Common setup | Standalone | Standalone, or as a reverse proxy in front of Apache |

A header showing `nginx` can also mean Nginx sits in front of another server as a **reverse proxy**. A planned follow-up is adding Nginx in front of this Apache setup.

To check any site's web server:
```bash
curl -I https://example.com
```
and read the `Server:` line.

---

## 7. Next Steps

- Complete the break and restore test
- Automate a nightly backup with `cron`
- WordPress maintenance: core and plugin updates, removing unused plugins, basic hardening
- Grow the root partition into the free LVM space (`lvextend`, `resize2fs`)
- Add Nginx as a reverse proxy in front of Apache
- Optional: redo the setup with Docker (`docker compose` with WordPress and MariaDB containers)

---

## 8. Glossary

| Term | Meaning | In this lab |
|---|---|---|
| LAMP | Linux, Apache, MariaDB/MySQL, PHP: the classic stack for running websites | The whole setup |
| LEMP | The same with Nginx ("engine-x") instead of Apache | The company website's likely setup |
| Ubuntu Server | Ubuntu without a desktop, managed through the command line | `wp-server` |
| LTS | Long-term support: a release with about five years of updates | 26.04 LTS |
| SSH | Encrypted remote login to a server's command line | `ssh <your-username>@192.168.56.103` from PowerShell |
| Host key fingerprint | A server's identity code, checked on every SSH connection | Confirmed with `yes` on first login |
| sudo | Run a command with administrator rights | Installing software, editing system files |
| apt | Ubuntu's package manager: installs, updates and removes software | `apt update`, `apt upgrade`, `apt install` |
| Package | A piece of software prepared for installation by apt | `apache2`, `php-gd` |
| systemctl | Starts, stops, restarts and checks services | `systemctl restart apache2` |
| Service | A program that runs in the background permanently | Apache, MariaDB, SSH |
| LVM | Logical Volume Manager: flexible disk layout that can be resized later | Root volume 11.5 GB, 11.5 GB free |
| Host-only adapter | VirtualBox network shared between the laptop and VMs | `192.168.56.103` |
| NAT adapter | VirtualBox network giving a VM internet through the laptop | `10.0.3.15` |
| DHCP lease | An address assigned for a limited time | The server's address could change after a restart |
| Apache | Open-source web server | Serves the WordPress pages |
| Nginx | Fast web server, often used as a reverse proxy | Planned follow-up |
| Reverse proxy | A server that receives visitors' requests and forwards them to another server behind it | Nginx in front of Apache |
| php-fpm | PHP running as a separate service, used with Nginx | Not used yet |
| mod_php | PHP running inside Apache | `libapache2-mod-php` |
| Document root | The folder a web server serves files from | `/var/www/html` |
| www-data | The Linux user Apache runs as | Owner of the WordPress files |
| chown | Change the owner of files | `chown -R www-data:www-data` |
| wget | Download a file from the command line | Downloading WordPress |
| tar | Packs and unpacks archive files | `tar -xzf latest.tar.gz` |
| MariaDB | Open-source database server, compatible with MySQL | Stores the WordPress site |
| Database | A collection of related tables | `wordpress` |
| Table | Data organised in rows and columns, like a spreadsheet sheet | `wp_users`, `wp_options` |
| Row / column | One record / one field of a table | One user / `user_login` |
| SQL | The language used to read and change database data | All database practice |
| SELECT | Reads data | Reading users and settings |
| WHERE | Filters which rows a statement applies to | `WHERE option_name = 'blogname'` |
| UPDATE | Changes existing data | Changing the site title |
| DELETE | Removes rows | Simulated damage |
| INSERT | Adds rows | Inside the backup file |
| utf8mb4 | Full Unicode character set, covers every language and emoji | Database character set |
| Database user | An account inside the database, separate from Linux users | `wpuser` |
| GRANT | Gives a database user permissions | Rights on `wordpress` only |
| Least privilege | Giving each account only the access it needs | `wpuser` can't see other databases |
| unix_socket authentication | Database login based on the Linux user, without a password | How MariaDB's root logs in on Ubuntu |
| mariadb-secure-installation | Script that removes insecure database defaults | Run once after installing |
| Hash | A one-way fingerprint of data; the original can't be read back | Stored WordPress passwords |
| bcrypt | A deliberately slow password hashing algorithm | `$2y$` in the stored hash |
| Cost factor | How much work bcrypt does per hash; higher is harder to crack | `$12$` |
| Table prefix | Text added to the start of every table name | `wp_` |
| wp_options | WordPress's settings table | Site title, tagline, site URL |
| siteurl / home | WordPress's own address, stored in the database | `http://192.168.56.103` |
| mariadb-dump | Exports a database into a file of SQL statements | `wordpress-backup.sql` |
| Restore | Loading a backup back into the database | `mariadb ... < backup.sql` |
| Redirection `>` / `<` | Send command output into a file / feed a file into a command | Backup / restore |
| cron | Linux's task scheduler | Planned nightly backups |
| curl -I | Fetches only the response headers of a web page | Checking the `Server:` header |
| Docker | Runs applications in lightweight containers | Optional future version of this lab |
