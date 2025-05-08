# How to install and configure inotify
- Inotify is a Linux kernel subsystem that allows you to monitor file and directory changes in real-time. It is more lightweight and better for real-time monitoring than auditd.
## 1. Install inotify-tools
- If not already installed, install inotify-tools (Debian-based systems like Ubuntu):
  
    `sudo apt update`

    `sudo apt install inotify-tools -y`

## 2. Monitor File Changes in Real-Time
- Example: Monitor a Specific File (/etc/passwd)

    `inotifywait -m /etc/passwd`
    - -m: monitor contiuously

## 3. Automate File Change Alerts
- You can combine inotifywait with a script to send alerts when a file is modified.

- Example 1: Alert when /etc/passwd is modified (you should have a mail server running to make this example work!)

``` bash
#!/bin/bash
inotifywait -m -e modify /etc/passwd |
while read path action file; do
	echo "Alert: $file was modified at $(date)" | mail -s "File Change Alert" admin@example.com
done
```

- Save this script as monitor.sh, then run it:
  
    `bash monitor.sh`

## 4. Use inotifywatch for Summary Reports
- If you don’t need real-time monitoring but want a summary of file changes, use:
  
    `inotifywatch -t 10 -r /etc/passwd`
  
- This watches /var/www/ for 10 seconds and then reports changes.

