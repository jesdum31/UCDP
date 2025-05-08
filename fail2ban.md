# 1. Installation of fail2ban

> sudo apt update
> sudo apt install fail2ban

# 2. Configuration of fail2ban

> sudo nano /etc/fail2ban/jail.local (create the file if it doesn’t exist yet)
![fail2ban jail.local file](./images/fail2ban%20jail.local.png)

# 3. Jail.local file glossary
- bantime: amount of seconds an IP is banned from the system
- <span style="color:red">findtime</span>: the amount of seconds in which a user should have <span style="color:orange">maxentry</span> tries before being banned
- action: defines how an IP is blocked and whether additional actions are taken, such as sending an email notification
    - Actions are defined here: /etc/fail2ban/action.d/
    - It is also possible to define custom actions
![fail2ban actions](./images/fail2ban%20actions.png)
- banaction: defines how an IP address is banned when a rule is triggered
    ![fail2ban banactions](./images/fail2ban%20banactions.png)
- <span style="color:orange">maxentry</span>: the amount of times a user can enter the wrong credentials in the given <span style="color:red">findtime</span>.
- ignoreip: (a list of) IP’s to ignore

