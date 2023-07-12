# Run with Docker Compose

#### Install Docker <a href="#start-services" id="start-services"></a>

Follow the [docker install instructions](https://docs.docker.com/engine/install) for your platform/architecture.

Onboarding instructions will be [based on Ubuntu 22.04 LTS x86\_64 / amd64.](https://docs.docker.com/engine/install/ubuntu/)

#### Install & updates <a href="#start-services" id="start-services"></a>

```
sudo apt update
sudo apt install direnv

# bash users - add the following line to your ~/.bashrc
eval "$(direnv hook bash)"
source ~/.bashrc

# zsh users - add the following line to your ~/.zshrc
eval "$(direnv hook zsh)"
source ~/.zshrc
```

#### Clone and Set Env Var <a href="#clone-and-set-env-vars" id="clone-and-set-env-vars"></a>

1. Clone the [covalenthq/rudder](https://github.com/covalenthq/rudder) repo.

```
git clone https://github.com/covalenthq/rudder
cd rudder
cat docker-compose-mbase.yml
```

2. Check global environment variables

```
$ cat .envrc

export IPFS_PINNER_URL="http://127.0.0.1:3001"
export EVM_SERVER_URL="http://127.0.0.1:3002"

[[ -f .envrc.local ]] && source_env .envrc.local
```

**Note**: `.envrc.local` overrides any env vars set with `.envrc` on calling `direnv allow .`

*   Set local environment variables

    ```
    $ touch .envrc.local
    $ nano .envrc.local

    //add the command in .envrc.local

    export BLOCK_RESULT_OPERATOR_PRIVATE_KEY="BRP-OPERATOR-PK-WITHOUT-0x-PREFIX"
    export NODE_ETHEREUM_MAINNET="<<ASK-ON-DISCORD>>"
    export IPFS_PINNER_URL="http://ipfs-pinner:3001"
    export EVM_SERVER_URL="http://evm-server:3002"
    export WEB3_JWT="<<WEB3.STORAGE-API-TOKEN>>"
    ```



> change the NODE\_ETHEREUM\_MAINNET\
> `NODE_ETHEREUM_MAINNET="https://rpc.api.moonbase.moonbeam.network"`

#### Start Services <a href="#start-services" id="start-services"></a>

1. Load env vars into the shell.

```
$ direnv allow .
```

```
# make sure you see output these being loaded
direnv: loading ~/rudder/.envrc
direnv: loading ~/rudder/.envrc.local
direnv: export +BLOCK_RESULT_OPERATOR_PRIVATE_KEY +ERIGON_NODE +EVM_SERVER_URL +IPFS_PINNER_URL +NODE_ETHEREUM_MAINNET +WEB3_JWT
```

2. Start all 3 services in the background for Moonbase alpha

```
$ docker compose -f "docker-compose-mbase.yml" up -d --remove-orphans


[+] Running 3/3
 ⠿ Container rudder       Started                            3.2s
 ⠿ Container ipfs-pinner  Started                            1.9s
 ⠿ Container evm-server   Started                            1.8s
```

**NOTE**: On a system where an `ipfs-pinner` instance is already running, check the instruction in the [Appendix](https://www.covalenthq.com/docs/cqt-network/operator-onboarding-refiner/#appendix) to run `rudder` docker alongside.

3. Monitor the logs for Block Result submissions.

```
$ docker compose -f "docker-compose-mbase.yml" logs -f

rudder       | [info] curr_block: 4591264 and latest_block_num:4591263
ipfs-pinner  | 2023/06/22 13:45:49 Received /health request: source= 127.0.0.1:54420 status= OK
rudder       | [info] curr_block: 4591264 and latest_block_num:4591263
ipfs-pinner  | 2023/06/22 13:46:00 Received /health request: source= 127.0.0.1:54430 status= OK
rudder       | [info] curr_block: 4591264 and latest_block_num:4591264
rudder       | [info] listening for events at 4591264
rudder       | [info] found 0 bsps to process
```

#### Deployment As Service Unit <a href="#deployment-as-service-unit" id="deployment-as-service-unit"></a>

{% hint style="info" %}
Optional&#x20;
{% endhint %}

Here you can find an example of a `systemd` service unit file that can be used to auto-start/restart of the docker-compose service for Refiner.

```
[Unit]
Description=Refiner docker compose
PartOf=docker.service
After=docker.service

[Service]
User=blockchain
Group=blockchain
Environment=HOME=/home/blockchain/tmp
Environment="BLOCK_RESULT_OPERATOR_PRIVATE_KEY=BRP-OPERATOR-PK-WITHOUT-0x-PREFIX"
Environment="NODE_ETHEREUM_MAINNET=<<ASK-ON-DISCORD>>"
Environment="IPFS_PINNER_URL=http://ipfs-pinner:3001" #service in docker
Environment="EVM_SERVER_URL=http://evm-server:3002" #service in docker
Environment="WEB3_JWT=<<WEB3.STORAGE-API-TOKEN>>"
Type=simple
ExecStart=docker compose -f "/home/blockchain/tmp/docker-compose-mbase.yml" up --remove-orphans
Restart=always
TimeoutStopSec=infinity

[Install]
WantedBy=multi-user.target
```

After adding the env vars in their respective fields in the service unit file, enable the service and start it.

```
sudo systemctl enable rudder-compose.service
sudo systemctl start rudder-compose.service
```
