# Uptime Kuma

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

{% hint style="info" %}
Reference [https://github.com/louislam/uptime-kuma](https://github.com/louislam/uptime-kuma)
{% endhint %}
