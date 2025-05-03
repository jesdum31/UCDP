# All code for logging user commands with a service

## The service file:
```
[Unit]
Description=Logs user commands
After=network.target

[Service]
Type=simple
ExecStart=/bin/bash /usr/local/bin/commandlogger.sh
Restart=always
User=root

[Install]
WantedBy=multi-user.target
```

## The commandlogger script:
```
#!/bin/bash

LOGFILE="/var/log/user_commands.log"

if [ ! -f "$LOGFILE"]; then
    sudo touch "$LOGFILE"
    sudo chmod 666 "$LOGFILE"
fi

log_command() {
  local CMD=$(history 1 | sed 's/^[ ]*[0-9]*[ ]*//')
  local USER=$(whoami)
  local DATETIME$(date "+%Y-%m-%d %H:%M:%S")

  if [ -n "$CMD" ]; then
    echo "$DATETIME | $USER | $CMD" >> "$LOGFILE"
  fi
}

PROMPT_COMMAND="log_command"
export PROMPT_COMMAND

while true; do
  sleep 1
  log_command
done
```
