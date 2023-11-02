# Upgrade

```
systemctl stop namada.service
curl -sL https://github.com/anoma/namada/releases/download/v0.23.2/namada-v0.23.2-Linux-x86_64.tar.gz | tar -C $HOME/.local/bin -xzf- --strip-components=1 --exclude='LICENSE*'
systemctl start namada.service
```
