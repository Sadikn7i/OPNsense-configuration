# Command Reference

Every command used in the WordPress on LAMP lab: what it means, when you use it, and the example from the lab. Passwords and usernames are replaced with placeholders.

The commands run in three different places:

| Where | Prompt looks like | Used for |
|---|---|---|
| Windows PowerShell (laptop) | `PS C:\Users\...>` | Connecting to the server, checking the network |
| Linux server, through SSH | `<your-username>@wp-server:~$` | Installing and managing software |
| MariaDB console | `MariaDB [wordpress]>` | Reading and changing the database with SQL |

---

## 1. Windows PowerShell (on the laptop)

| Command | What it means | When you use it | Example |
|---|---|---|---|
| `ssh user@address` | Open a secure remote terminal on another machine | Every time you manage a server; you work on it from your own PC | `ssh <your-username>@192.168.56.103` |
| `yes` (at the SSH prompt) | Trust the server's key fingerprint | Only on the very first connection to a server | First SSH login |
| `ipconfig` | Show the laptop's network adapters and IP addresses | Checking your own address, or when a PC has no network | Finding the host-only adapter `192.168.56.1` |
| `ipconfig /release` / `ipconfig /renew` | Give back the DHCP address / ask for a new one | A Windows PC has a wrong or missing address | First fix for "no internet" on a staff PC |
| `ping address` | Test whether a device answers | First test when something isn't reachable | `ping 192.168.56.103` |
| `arp -a` | Show which IPs on the local network were matched to MAC addresses | Checking whether a device is even visible on the network | Troubleshooting in the OPNsense labs |
| `Get-FileHash file -Algorithm SHA256` | Calculate a file's checksum | Verifying a download wasn't corrupted or tampered with | Checking the OPNsense ISO |

---

## 2. Linux Server Commands (over SSH)

### Administration and software

| Command | What it means | When you use it | Example |
|---|---|---|---|
| `sudo command` | Run a command as administrator | Installing software, changing system settings | `sudo apt update` |
| `apt update` | Refresh the list of available packages | Before installing or upgrading anything | `sudo apt update` |
| `apt upgrade -y` | Install newer versions of installed packages; `-y` answers "yes" automatically | First thing on a new server, then regularly for security fixes | `sudo apt update && sudo apt upgrade -y` |
| `apt install -y packages` | Install software | Adding Apache, MariaDB, PHP, or any tool | `sudo apt install -y apache2 mariadb-server php ...` |
| `apt purge package` | Remove software and its settings | Uninstalling something completely | Removing Apache if switching to Nginx |
| `apt autoremove -y` | Remove packages no longer needed | Cleanup after removing or upgrading | `sudo apt autoremove -y` |
| `&&` | Run the next command only if the previous one succeeded | Chaining steps safely on one line | `apt update && apt upgrade -y` |

### Services

| Command | What it means | When you use it | Example |
|---|---|---|---|
| `systemctl restart service` | Stop and start a service | After changing its settings or installing extensions | `sudo systemctl restart apache2` |
| `systemctl status service` | Show whether a service is running, and recent errors | A website or database isn't responding | `sudo systemctl status mariadb` |
| `systemctl stop / start service` | Stop or start a service | Maintenance, or switching web servers | `sudo systemctl stop apache2` |
| `shutdown now` | Shut the server down cleanly | Before closing for the day or changing VM settings | `sudo shutdown now` |

### Network

| Command | What it means | When you use it | Example |
|---|---|---|---|
| `ip a` | Show network interfaces and their IP addresses | Finding the server's address, checking it got one | Found `192.168.56.103` on `enp0s3` |
| `ping -c 3 address` | Send 3 test messages (Linux needs `-c`, otherwise it pings forever) | Checking reachability from the server | `ping -c 3 8.8.8.8` |
| `curl -I url` | Fetch only a website's headers | Checking which web server a site uses (`Server:` line) | `curl -I https://example.com` |
| `nmcli device connect name` | Bring a network card up and request an address (NetworkManager) | A Linux desktop has no address | Used on Mint in the OPNsense labs |

### Files and folders

| Command | What it means | When you use it | Example |
|---|---|---|---|
| `cd folder` | Change directory (move into a folder) | Going to where you want to work | `cd /tmp` |
| `ls -lh file` | List files with size and owner, in readable units | Checking a file exists and how big it is | `ls -lh ~/wordpress-backup.sql` |
| `head -n 40 file` | Show the first 40 lines of a file | Quickly looking inside a file | `head -n 40 ~/wordpress-backup.sql` |
| `wget url` | Download a file from the internet | Getting software that isn't in apt | `wget https://wordpress.org/latest.tar.gz` |
| `tar -xzf file` | Extract a `.tar.gz` archive (x = extract, z = gzip, f = file) | Unpacking downloads | `tar -xzf latest.tar.gz` |
| `rm file` | Delete a file (no recycle bin) | Removing something you no longer need | `sudo rm /var/www/html/index.html` |
| `cp -r source destination` | Copy files; `-r` includes folders | Moving website files into place | `sudo cp -r /tmp/wordpress/* /var/www/html/` |
| `chown -R user:group folder` | Change who owns files; `-R` includes everything inside | Letting the web server read and write the website files | `sudo chown -R www-data:www-data /var/www/html` |
| `~` | Shortcut for your home folder | Saving personal files like backups | `~/wordpress-backup.sql` |
| `*` | Wildcard: "everything" | Copying all files in a folder | `/tmp/wordpress/*` |

---

## 3. MariaDB Commands (run at the Linux prompt)

| Command | What it means | When you use it | Example |
|---|---|---|---|
| `sudo mariadb` | Open the database console as the database administrator (root) | Creating databases and users, admin work | `sudo mariadb` |
| `mariadb -u user -p` | Log into the database as a specific user; `-p` asks for the password | Working as the app's own user, with its limited rights | `mariadb -u wpuser -p` |
| `mariadb -u user -p database -e "SQL"` | Run SQL directly without opening the console | Quick one-off queries or tests | `mariadb -u wpuser -p wordpress -e "SELECT 'login works';"` |
| `mariadb-secure-installation` | Remove insecure defaults (anonymous users, test database, remote root) | Once, right after installing MariaDB | `sudo mariadb-secure-installation` |
| `mariadb-dump -u user -p database > file.sql` | Back up a whole database into a file of SQL statements | Before any risky change, and on a schedule | `mariadb-dump -u wpuser -p wordpress > ~/wordpress-backup.sql` |
| `mariadb -u user -p database < file.sql` | Restore a database from a backup file | After a mistake, damage, or when moving a site | `mariadb -u wpuser -p wordpress < ~/wordpress-backup.sql` |
| `>` / `<` | Send output into a file / feed a file into a command | Backup (`>`) and restore (`<`) | See above |

`mysql` and `mysqldump` are the older names of the same tools, and still appear in many tutorials.

---

## 4. SQL Statements (inside the MariaDB console)

Every SQL statement ends with `;`. Keywords are written in capitals by convention, but SQL accepts lowercase too.

### Looking around

| Statement | What it means | When you use it | Example |
|---|---|---|---|
| `SHOW DATABASES;` | List the databases you're allowed to see | First thing after logging in | Showed only `wordpress` and `information_schema` |
| `USE database;` | Select which database to work in | Before working with its tables | `USE wordpress;` |
| `SHOW TABLES;` | List the tables in the current database | Exploring an unfamiliar database | Showed the 12 `wp_` tables |
| `EXIT;` | Leave the MariaDB console | When you're done | `EXIT;` |

### Reading data

| Statement | What it means | When you use it | Example |
|---|---|---|---|
| `SELECT columns FROM table;` | Read chosen columns from every row | Looking at data | `SELECT ID, user_login, user_email, user_registered FROM wp_users;` |
| `SELECT * FROM table;` | Read all columns | Quick look at a small table | `SELECT * FROM wp_users;` |
| `WHERE condition` | Only the rows that match | Finding specific records | `WHERE option_name = 'blogname'` |
| `WHERE column IN (a, b, c)` | Match any value in a list | Several specific rows at once | `WHERE option_name IN ('blogname', 'blogdescription', 'siteurl', 'home')` |

### Changing data

| Statement | What it means | When you use it | Example |
|---|---|---|---|
| `UPDATE table SET column = value WHERE condition;` | Change existing rows | Fixing a setting, e.g. after a server's IP changes | `UPDATE wp_options SET option_value = 'Factory Lab - <your-city>' WHERE option_name = 'blogname';` |
| `DELETE FROM table WHERE condition;` | Remove rows | Removing unwanted records | `DELETE FROM wp_posts WHERE ID = 1;` |
| `INSERT INTO table ... VALUES ...;` | Add new rows | Adding data; the backup file is full of these | Inside `wordpress-backup.sql` |

Warning: `UPDATE` or `DELETE` without `WHERE` affects **every row** in the table. Always check the `WHERE` part, and take a backup first.

### Creating databases and users

| Statement | What it means | When you use it | Example |
|---|---|---|---|
| `CREATE DATABASE name CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;` | Create an empty database with full Unicode support | Setting up a new application | `CREATE DATABASE wordpress ...;` |
| `CREATE USER 'user'@'localhost' IDENTIFIED BY 'password';` | Create a database account that can only connect from the server itself | Every application gets its own user | `CREATE USER 'wpuser'@'localhost' IDENTIFIED BY '<your-db-password>';` |
| `GRANT ALL PRIVILEGES ON database.* TO 'user'@'localhost';` | Give a user full rights on **one** database only | Right after creating the user | `GRANT ALL PRIVILEGES ON wordpress.* TO 'wpuser'@'localhost';` |
| `FLUSH PRIVILEGES;` | Apply permission changes | After changing users or rights | `FLUSH PRIVILEGES;` |

### Backup file statements (seen inside `wordpress-backup.sql`)

| Statement | What it does during a restore |
|---|---|
| `DROP TABLE IF EXISTS table;` | Deletes the table if it's there |
| `CREATE TABLE table (...);` | Recreates it with the same structure |
| `INSERT INTO table VALUES (...);` | Puts every row of data back |

---

## 5. Quick "When Something Breaks" List

| Problem | First commands to run |
|---|---|
| Website doesn't load | `ping <server>` from Windows, then `sudo systemctl status apache2` on the server |
| "Error establishing a database connection" | `sudo systemctl status mariadb`, then test the login with `mariadb -u wpuser -p wordpress -e "SELECT 1;"` |
| "Access denied" in MariaDB | Check you're using the **database** password, not the Linux one |
| Site looks broken after the server's IP changed | `ip a` to find the new address, then `UPDATE wp_options` on `siteurl` and `home` |
| Made a mistake in the database | `mariadb -u wpuser -p wordpress < ~/wordpress-backup.sql` |
| Command says "command not found" after pasting | The line probably split in two; paste it again on one line |
