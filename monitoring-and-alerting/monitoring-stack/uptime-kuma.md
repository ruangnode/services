# Uptime Kuma

<figure><img src="../../.gitbook/assets/icon.svg" alt="" width="160"><figcaption></figcaption></figure>

Please create file docker-compose.yml

```
nano docker-compoe.yml
```

```docker
version: '3.3'

services:
  uptime-kuma:
    image: louislam/uptime-kuma:1
    container_name: uptime-kuma
    volumes:
      - ./uptime-kuma-data:/app/data
    ports:
      - 3001:3001  # <Host Port>:<Container Port>
    restart: always
```
