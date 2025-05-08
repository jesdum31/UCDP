# Using auditd to Track File Access and Modifications
> The auditd (Linux Audit Daemon) system allows you to track file access, modifications, and other system events. It’s useful for security monitoring, compliance, and debugging unauthorized file changes.

## 1. Install and Enable auditd
- Most Linux distributions come with auditd pre-installed, but you can install it manually if needed:
    > sudo apt update 
    
    > sudo apt install auditd audispd-plugins -y
- Enable and start the audit daemon:
    > sudo systemctl enable auditd
    > sudo systemctl start auditd
## 2. Set up file monitoring rules
- Example: monitor read, write and attribute changes in /etc/passwd
    >sudo auditctl -w /etc/passwd -p rw -k passwd_watch
    >- -w: watch certain file or directory
    >- -p rw: monitor read and write changes
    >- -k passwd_watch: add custom key for easy filtering
- Example 2: Monitor all file changes in /var/www
    > sudo auditctl -w /var/www/ -p rw -k www_changes
    >- -w: watch certain file or directory
    >- -p rw: monitor read and write changes (a for attributes if you want)
    >- -k www_changes: add custom key for easy filtering

## 3. Make Rules Persistent
By default, rules set with auditctl are lost after a reboot. To make them permanent:
- sudo nano /etc/audit/rules.d/audit.rules
    - -w /etc/passwd -p rw -k passwd_watch
    - -w /var/www/ -p rw -k www_changes
- sudo systemctl restart auditd

## 4. View Audit Logs
- See recorded file access and modifications:
    > sudo ausearch -k passwd_watch
- Filter for certain user:
    > sudo ausearch -ua $(id -u REPLACE_WITH_USERNAME)
- Check all audit logs:
    > sudo aureport -f
- If you want to monitor in real-time:
    > sudo tail -f /var/log/audit/audit.log

## 5. Remove rules
- Suppose you have added a rule called passwd_watch:
    > sudo auditctl -d -w /etc/passwd -p rwa -k passwd_watch
