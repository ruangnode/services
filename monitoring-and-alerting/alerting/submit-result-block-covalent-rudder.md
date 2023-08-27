# Submit Result Block Covalent Rudder

```python
import requests
from requests.auth import HTTPBasicAuth
import json
import time

API_KEY = 'COVALENT_API_KEY' //change with api_key covalent 
ADDRESS = 'WALLET_ADDRESS' //change with your wallet address
KUMA_UPTIME_WEBHOOK = 'https://monitor.ruangnode.com/api/push/TOKEN' //Please change TOKEN with your personal token

last_total_count = None
unchanged_count_duration = 0

def get_total_count():
    url = f"https://api.covalenthq.com/v1/moonbeam-mainnet/address/{ADDRESS}/transactions_summary/"
    headers = {"accept": "application/json"}
    auth = HTTPBasicAuth(API_KEY, '')
    response = requests.get(url, headers=headers, auth=auth)
    data = response.json()
    if "data" in data and "items" in data["data"]:
        total_count = data["data"]["items"][0]["total_count"]
        return total_count
    return None

def send_to_kuma_uptime(status):
    payload = {
        "status": status,
        "msg": f"Total Count: {get_total_count()}",
        "ping": ""
    }
    response = requests.get(KUMA_UPTIME_WEBHOOK, params=payload)
    return response

def check_status():
    global last_total_count
    global unchanged_count_duration

    total_count = get_total_count()

    if total_count is not None:
        if last_total_count is None:
            last_total_count = total_count
            unchanged_count_duration = 0
            status = "Up"  # Initial status
        elif total_count == last_total_count:
            unchanged_count_duration += 1
            if unchanged_count_duration >= 10:
                status = "Down"
            else:
                status = "Up"
        else:
            last_total_count = total_count
            unchanged_count_duration = 0
            status = "Up"

        print(f"Status: {status}")
        response = send_to_kuma_uptime(status)
        print("Response from Kuma Uptime:", response.text)
    else:
        last_total_count = None
        unchanged_count_duration = 0
        status = "Down"
        print(f"Status: {status}")
        response = send_to_kuma_uptime(status)
        print("Response from Kuma Uptime:", response.text)

if __name__ == "__main__":
    while True:
        check_status()
        time.sleep(60)  # Wait for 1 minute before checking again
```
