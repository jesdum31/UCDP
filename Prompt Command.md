# All code for logging user commands with prompt command

## The PROMPT_COMMAND line:
`export PROMPT_COMMAND='last_command=$(fc -ln -1);/usr/local/bin/commandlogger.sh $last_command`

## The commandlogger script:
```
#!/bin/bash

LOGFILE="/var/log/user_commands.log"

if [ ! -f "$LOGFILE"]; then
    sudo touch "$LOGFILE"
    sudo chmod 666 "$LOGFILE"
fi

USER=$(whoami)
DATE=$(date --iso-8601=seconds)
HOST=$(hostname)
CMD="$1"

CMD=$(echo "$CMD" | sed 's/^[[:space:]]*//;s/[[:space:]]*$//')

if [ -n "$CMD" ]; then
  echo "$DATE $USER $HOST \"$CMD\"" >> $LOGFILE
  exho "" >> $LOGFILE
fi
```
