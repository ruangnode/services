# Covalent Rudder: Submit Result Block with LOG

```sh
#!/bin/bash

# Environment Variables
HOST="https://monitor.ruangnode.com/api/push/xxxxxxx" //please change with you URL API Kuma
CONTAINER_NAME="rudder"
LOG_KEYWORD="upload_success:"
STATUS_UP="up"
STATUS_DOWN="pending"
MSG="upload success"
TIMEOUT_THRESHOLD=600  # 10 minutes in seconds
LOGS_TO_CHECK=1000  # Number of recent log lines to check
LOG_NOT_FOUND_TIMEOUT=3600  # 60 minutes in seconds

# Function to URL encode
URL() {
    URL_ENCODED=$(echo "${MSG}" | sed -e 's/%/%25/g' -e 's/ /%20/g' -e 's/!/%21/g' -e 's/"/%22/g' -e 's/#/%23/g' -e 's/\$/%24/g' -e 's/\&/%26/g' -e 's/'\''/%27/g' -e 's/(/%28/g' -e 's/)/%29/g' -e 's/\*/%2a/g' -e 's/+/%2b/g' -e 's/,/%2c/g' -e 's/-/%2d/g' -e 's/\./%2e/g' -e 's/\//%2f/g' -e 's/:/%3a/g' -e 's/;/%3b/g' -e 's//%3e/g' -e 's/?/%3f/g' -e 's/@/%40/g' -e 's/\[/%5b/g' -e 's/\\/%5c/g' -e 's/\]/%5d/g' -e 's/\^/%5e/g' -e 's/_/%5f/g' -e 's/`/%60/g' -e 's/{/%7b/g' -e 's/|/%7c/g' -e 's/}/%7d/g' -e 's/~/%7e/g')
}

# Function to check log and count occurrences
check_log() {
    LOG_COUNT=$(docker logs ${CONTAINER_NAME} --tail=${LOGS_TO_CHECK} 2>&1 | grep -c "${LOG_KEYWORD}")
}

# Main script
check_log
URL

if [ "${LOG_COUNT}" -gt 0 ]; then
    # If LOG_COUNT > 0, consider it as 1 and push once
    URL
    curl -s "${HOST}?status=${STATUS_UP}&msg=${URL_ENCODED}&ping="
else
    CURRENT_TIME=$(date +%s)
    LAST_LOG_TIME=$(docker logs ${CONTAINER_NAME} --tail=${LOGS_TO_CHECK} 2>&1 | grep "${LOG_KEYWORD}" | tail -n 1 | awk '{print $1 " " $2}')

    if [ -n "${LAST_LOG_TIME}" ]; then
        LAST_LOG_TIMESTAMP=$(date -d "${LAST_LOG_TIME}" +%s)
        TIME_DIFF=$((CURRENT_TIME - LAST_LOG_TIMESTAMP))

        if [ ${TIME_DIFF} -ge ${LOG_NOT_FOUND_TIMEOUT} ]; then
            curl -s "${HOST}?status=${STATUS_DOWN}&msg=Log%20not%20found&ping="
        fi
    fi
fi
```
