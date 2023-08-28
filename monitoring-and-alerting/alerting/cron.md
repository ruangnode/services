# Cron

```sh
SHELL=/bin/bash
BLOCKCHAIN="" //user execution, if yout run with root please change root
PATH=/usr/sbin:/usr/bin:/sbin:/bin:/home/${BLOCKCHAIN}/bin

*/1 * * * * cd /home/${BLOCKCHAIN}/crontab/kuma && /bin/bash -x tendermint-check-active >>  /home/${BLOCKCHAIN}/crontab/kuma/logs/band-active.log
*/1 * * * * cd /home/${BLOCKCHAIN}/crontab/kuma && /bin/bash -x tendermint-check-sync >>  /home/${BLOCKCHAIN}/crontab/kuma/logs/band-sync.log
*/1 * * * * cd /home/${BLOCKCHAIN}/crontab/kuma && /bin/bash -x tendermint-check-jail >>  /home/${BLOCKCHAIN}/crontab/kuma/logs/band-jail.log
*/1 * * * * cd /home/${BLOCKCHAIN}/crontab/kuma && /bin/bash -x rudder >>  /home/${BLOCKCHAIN}/crontab/kuma/logs/rudder.log
*/1 * * * * cd /home/${BLOCKCHAIN}/crontab/kuma && /bin/bash -x erbie-node-connection >>  /home/${BLOCKCHAIN}/crontab/kuma/logs/erbie.log
```
