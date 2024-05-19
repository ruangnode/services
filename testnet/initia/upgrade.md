# Upgrade

### Download and build upgrade binaries <a href="#download-and-build-upgrade-binaries" id="download-and-build-upgrade-binaries"></a>

```
# Clone project repository
cd $HOME
rm -rf initia
git clone https://github.com/initia-labs/initia
cd initia
git checkout v0.2.15
export GOPATH="${HOME}/lib"
make install

mv ${HOME}/lib/bin/* ~/go/bin/

sudo systemctl restart initiad && sudo journalctl -u initiad -f
```
