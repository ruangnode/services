# Upgrade

## Manual upgrade

```bash
cd $HOME
wget https://github.com/crossfichain/crossfi-node/releases/download/v0.3.0-prebuild9/crossfi-node_0.3.0-prebuild9_linux_amd64.tar.gz
tar -xvf crossfi-node_0.3.0-prebuild9_linux_amd64.tar.gz
chmod +x $HOME/bin/crossfid
rm crossfi-node_0.3.0-prebuild9_linux_amd64.tar.gz
sudo mv $HOME/bin/crossfid $(which crossfid)
sudo systemctl restart crossfid && sudo journalctl -u crossfid -f
```

