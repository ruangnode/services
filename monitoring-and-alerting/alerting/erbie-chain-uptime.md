# Erbie Chain Uptime

```sh
#!/bin/bash

# Environment Variables
HOST="https://monitor.ruangnode.com/api/push/xxxxxx"
TOKEN="your_token"
STATUS_UP="up"
STATUS_DOWN="down"
MSG_UP="Network peer count is greater than 0"
MSG_DOWN="Network peer count is 0"

# Function to URL encode
URL() {
    URL_ENCODED=$(echo "${MSG}" | sed -e 's/%/%25/g' -e 's/ /%20/g' -e 's/!/%21/g' -e 's/"/%22/g' -e 's/#/%23/g' -e 's/\$/%24/g' -e 's/\&/%26/g' -e 's/'\''/%27/g' -e 's/(/%28/g' -e 's/)/%29/g' -e 's/\*/%2a/g' -e 's/+/%2b/g' -e 's/,/%2c/g' -e 's/-/%2d/g' -e 's/\./%2e/g' -e 's/\//%2f/g' -e 's/:/%3a/g' -e 's/;/%3b/g' -e 's//%3e/g' -e 's/?/%3f/g' -e 's/@/%40/g' -e 's/\[/%5b/g' -e 's/\\/%5c/g' -e 's/\]/%5d/g' -e 's/\^/%5e/g' -e 's/_/%5f/g' -e 's/`/%60/g' -e 's/{/%7b/g' -e 's/|/%7c/g' -e 's/}/%7d/g' -e 's/~/%7e/g')
}

# Function to check network peer count and determine status
check_network_status() {
    RESPONSE=$(curl -s -X POST -H 'Content-Type:application/json' --data '{"jsonrpc":"2.0","method":"net_peerCount","id":1}' http://127.0.0.1:8555)

    if [[ "${RESPONSE}" == *"result\":\"0x10"* ]]; then
        STATUS="${STATUS_UP}"
        MSG="${MSG_UP}"
    elif [[ "${RESPONSE}" == "16#"* ]]; then
        STATUS="${STATUS_DOWN}"
        MSG="${MSG_DOWN}"
    else
        echo "Unexpected response: ${RESPONSE}"
        exit 1
    fi
}

# Main script
check_network_status
URL

curl -s "${HOST}?status=${STATUS}&msg=${URL_ENCODED}&ping="
```
