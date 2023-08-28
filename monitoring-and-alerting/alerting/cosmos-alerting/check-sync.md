# Check Sync

```sh
source $(pwd)/band-env


#### KUMA CONFIG
TOKEN="xxxxxx"  //please input yout token kuma-uptime
PERCETAGE_BLOCK_MISSED=10

####### LOGIC
# (tendermint_consensus_height{group="gravity-node"} - 1) - tendermint_consensus_latest_block_height{group="gravity-node"}


CHECK_JQ=`command -v jq`
if [ $? -eq 1 ]
then
   echo "Install bc | apt install jq or yum install jq |"
   exit
fi


CHECK_BLOCK_LAST_TENDERMINT=`curl -s ${VALIDATOR_RPC}/status | jq -r .result.sync_info.latest_block_height`
CHECK_BLOCK_CONSENSUS_ROOMIT=`curl -s ${VALIDATOR_RPC}/consensus_params | jq -r .result.block_height`
RESULT=`echo "(${CHECK_BLOCK_CONSENSUS_ROOMIT} - 1) - ${CHECK_BLOCK_LAST_TENDERMINT} " |bc`

function URL(){
   URL=$(echo "${MESSAGE}" | sed -e 's/%/%25/g' -e 's/ /%20/g' -e 's/!/%21/g' -e 's/"/%22/g' -e 's/#/%23/g' -e 's/\$/%24/g' -e 's/\&/%26/g' -e 's/'\''/%27/g' -e 's/(/%28/g' -e 's/)/%29/g' -e 's/\*/%2a/g' -e 's/+/%2b/g' -e 's/,/%2c/g' -e 's/-/%2d/g' -e 's/\./%2e/g' -e 's/\//%2f/g' -e 's/:/%3a/g' -e 's/;/%3b/g' -e 's//%3e/g' -e 's/?/%3f/g' -e 's/@/%40/g' -e 's/\[/%5b/g' -e 's/\\/%5c/g' -e 's/\]/%5d/g' -e 's/\^/%5e/g' -e 's/_/%5f/g' -e 's/`/%60/g' -e 's/{/%7b/g' -e 's/|/%7c/g' -e 's/}/%7d/g' -e 's/~/%7e/g')
}

CHECK_VALIDATION=`curl -s ${VALIDATOR_RPC}/status | jq -r ".result|.sync_info.catching_up"`

if [ ${RESULT} -le ${PERCETAGE_BLOCK_MISSED} ] && [ ${CHECK_VALIDATION} == "false" ] 2> /dev/null
then
   MESSAGE="Block Synced"
   URL;
   curl -s "${HOST}/api/push/${TOKEN}?status=up&msg=${URL}&ping="
else
   MESSAGE="Missed"
   URL;
   curl -s "${HOST}/api/push/${TOKEN}?status=down&msg=${URL}&ping="
fi
```
