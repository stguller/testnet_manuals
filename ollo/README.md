<p style="font-size:14px" align="right">
<a href="https://t.me/kjnotes" target="_blank">Join our telegram <img src="https://user-images.githubusercontent.com/50621007/183283867-56b4d69f-bc6e-4939-b00a-72aa019d1aea.png" width="30"/></a>
<a href="https://discord.gg/JqQNcwff2e" target="_blank">Join our discord <img src="https://user-images.githubusercontent.com/50621007/176236430-53b0f4de-41ff-41f7-92a1-4233890a90c8.png" width="30"/></a>
<a href="https://kjnodes.com/" target="_blank">Visit our website <img src="https://user-images.githubusercontent.com/50621007/168689709-7e537ca6-b6b8-4adc-9bd0-186ea4ea4aed.png" width="30"/></a>
</p>

<p style="font-size:14px" align="right">
<a href="https://hetzner.cloud/?ref=y8pQKS2nNy7i" target="_blank">Deploy your VPS using our referral link to get 20€ bonus <img src="https://user-images.githubusercontent.com/50621007/174612278-11716b2a-d662-487e-8085-3686278dd869.png" width="30"/></a>
</p>
<p style="font-size:14px" align="right">
<a href="https://m.do.co/c/17b61545ca3a" target="_blank">Deploy your VPS using our referral link to get 100$ free bonus for 60 days <img src="https://user-images.githubusercontent.com/50621007/183284313-adf81164-6db4-4284-9ea0-bcb841936350.png" width="30"/></a>
</p>
<p style="font-size:14px" align="right">
<a href="https://www.vultr.com/?ref=7418642" target="_blank">Deploy your VPS using our referral link to get 100$ free bonus <img src="https://user-images.githubusercontent.com/50621007/183284971-86057dc2-2009-4d40-a1d4-f0901637033a.png" width="30"/></a>
</p>

<p align="center">
  <img height="100" height="auto" src="https://user-images.githubusercontent.com/50621007/192699071-461d8ff6-6ddf-4d4f-aba7-d3ddcc4a5563.png">
</p>

# ollo node setup for testnet — ollo-testnet-1

Official documentation:
>- [Validator setup instructions](https://docs.ollo.zone/validators/running_a_node)

Explorer:
>-  https://explorer.kjnodes.com/ollo

## Usefull tools and references
> To set up monitoring for your validator node navigate to [Set up monitoring and alerting for ollo validator](https://github.com/kj89/testnet_manuals/blob/main/ollo/monitoring/README.md)
>
> To migrate your validator to another machine read [Migrate your validator to another machine](https://github.com/kj89/testnet_manuals/blob/main/ollo/migrate_validator.md)

## Hardware Requirements
Like any Cosmos-SDK chain, the hardware requirements are pretty modest.

### Minimum Hardware Requirements
 - 4x CPUs; the faster clock speed the better
 - 8GB RAM
 - 100GB of storage (SSD or NVME)
 - Permanent Internet connection (traffic will be minimal during testnet; 10Mbps will be plenty - for production at least 100Mbps is expected)

### Recommended Hardware Requirements 
 - 8x CPUs; the faster clock speed the better
 - 64GB RAM
 - 1TB of storage (SSD or NVME)
 - Permanent Internet connection (traffic will be minimal during testnet; 10Mbps will be plenty - for production at least 100Mbps is expected)

## Set up your ollo fullnode
### Option 1 (automatic)
You can setup your ollo fullnode in few minutes by using automated script below. It will prompt you to input your validator node name!
```
wget -O ollo.sh https://raw.githubusercontent.com/kj89/testnet_manuals/main/ollo/ollo.sh && chmod +x ollo.sh && ./ollo.sh
```

### Option 2 (manual)
You can follow [manual guide](https://github.com/kj89/testnet_manuals/blob/main/ollo/manual_install.md) if you better prefer setting up node manually

## Post installation

When installation is finished please load variables into system
```
source $HOME/.bash_profile
```

Next you have to make sure your validator is syncing blocks. You can use command below to check synchronization status
```
ollod status 2>&1 | jq .SyncInfo
```

### (OPTIONAL) State Sync
You can state sync your node in minutes by running commands below
```
N/A
```

### Create wallet
To create new wallet you can use command below. Don’t forget to save the mnemonic
```
ollod keys add $WALLET
```

(OPTIONAL) To recover your wallet using seed phrase
```
ollod keys add $WALLET --recover
```

To get current list of wallets
```
ollod keys list
```

### Save wallet info
Add wallet and valoper address into variables 
```
OLLO_WALLET_ADDRESS=$(ollod keys show $WALLET -a)
OLLO_VALOPER_ADDRESS=$(ollod keys show $WALLET --bech val -a)
echo 'export OLLO_WALLET_ADDRESS='${OLLO_WALLET_ADDRESS} >> $HOME/.bash_profile
echo 'export OLLO_VALOPER_ADDRESS='${OLLO_VALOPER_ADDRESS} >> $HOME/.bash_profile
source $HOME/.bash_profile
```

### Fund your wallet
In order to create validator first you need to fund your wallet with testnet tokens. \
You can request testnet tokens from the OLLO testnet faucet in the [OLLO Discord](https://discord.gg/My4BEPcAgP)
- Get verified
- Get `Testnet Explorers role` in `#roles` channel
- Move to `#testnet-faucet` and request tokens
```
!request YOUR_WALLET_ADDRESS
```

### Create validator
Before creating validator please make sure that you have at least 1 strd (1 strd is equal to 1000000 utollo) and your node is synchronized

To check your wallet balance:
```
ollod query bank balances $OLLO_WALLET_ADDRESS
```
> If your wallet does not show any balance than probably your node is still syncing. Please wait until it finish to synchronize and then continue 

To create your validator run command below
```
ollod tx staking create-validator \
  --amount 2000000utollo \
  --from $WALLET \
  --commission-max-change-rate "0.01" \
  --commission-max-rate "0.2" \
  --commission-rate "0.07" \
  --min-self-delegation "1" \
  --pubkey  $(ollod tendermint show-validator) \
  --moniker $NODENAME \
  --chain-id $OLLO_CHAIN_ID
```

## Security
To protect you keys please make sure you follow basic security rules

### Set up ssh keys for authentication
Good tutorial on how to set up ssh keys for authentication to your server can be found [here](https://www.digitalocean.com/community/tutorials/how-to-set-up-ssh-keys-on-ubuntu-20-04)

### Basic Firewall security
Start by checking the status of ufw.
```
sudo ufw status
```

Sets the default to allow outgoing connections, deny all incoming except ssh and 26656. Limit SSH login attempts
```
sudo ufw default allow outgoing
sudo ufw default deny incoming
sudo ufw allow ssh/tcp
sudo ufw limit ssh/tcp
sudo ufw allow ${OLLO_PORT}656,${OLLO_PORT}660/tcp
sudo ufw enable
```

## Monitoring
To monitor and get alerted about your validator health status you can use my guide on [Set up monitoring and alerting for ollo validator](https://github.com/kj89/testnet_manuals/blob/main/ollo/monitoring/README.md)

## Calculate synchronization time
This script will help you to estimate how much time it will take to fully synchronize your node\
It measures average blocks per minute that are being synchronized for period of 5 minutes and then gives you results
```
wget -O synctime.py https://raw.githubusercontent.com/kj89/testnet_manuals/main/ollo/tools/synctime.py && python3 ./synctime.py
```

### Check your validator key
```
[[ $(ollod q staking validator $OLLO_VALOPER_ADDRESS -oj | jq -r .consensus_pubkey.key) = $(ollod status | jq -r .ValidatorInfo.PubKey.value) ]] && echo -e "\n\e[1m\e[32mTrue\e[0m\n" || echo -e "\n\e[1m\e[31mFalse\e[0m\n"
```

### Get list of validators
```
ollod q staking validators -oj --limit=3000 | jq '.validators[] | select(.status=="BOND_STATUS_BONDED")' | jq -r '(.tokens|tonumber/pow(10; 6)|floor|tostring) + " \t " + .description.moniker' | sort -gr | nl
```

## Get currently connected peer list with ids
```
curl -sS http://localhost:${OLLO_PORT}657/net_info | jq -r '.result.peers[] | "\(.node_info.id)@\(.remote_ip):\(.node_info.listen_addr)"' | awk -F ':' '{print $1":"$(NF)}'
```

## Usefull commands
### Service management
Check logs
```
journalctl -fu ollod -o cat
```

Start service
```
sudo systemctl start ollod
```

Stop service
```
sudo systemctl stop ollod
```

Restart service
```
sudo systemctl restart ollod
```

### Node info
Synchronization info
```
ollod status 2>&1 | jq .SyncInfo
```

Validator info
```
ollod status 2>&1 | jq .ValidatorInfo
```

Node info
```
ollod status 2>&1 | jq .NodeInfo
```

Show node id
```
ollod tendermint show-node-id
```

### Wallet operations
List of wallets
```
ollod keys list
```

Recover wallet
```
ollod keys add $WALLET --recover
```

Delete wallet
```
ollod keys delete $WALLET
```

Get wallet balance
```
ollod query bank balances $OLLO_WALLET_ADDRESS
```

Transfer funds
```
ollod tx bank send $OLLO_WALLET_ADDRESS <TO_OLLO_WALLET_ADDRESS> 10000000utollo
```

### Voting
```
ollod tx gov vote 1 yes --from $WALLET --chain-id=$OLLO_CHAIN_ID
```

### Staking, Delegation and Rewards
Delegate stake
```
ollod tx staking delegate $OLLO_VALOPER_ADDRESS 10000000utollo --from=$WALLET --chain-id=$OLLO_CHAIN_ID --gas=auto
```

Redelegate stake from validator to another validator
```
ollod tx staking redelegate <srcValidatorAddress> <destValidatorAddress> 10000000utollo --from=$WALLET --chain-id=$OLLO_CHAIN_ID --gas=auto
```

Withdraw all rewards
```
ollod tx distribution withdraw-all-rewards --from=$WALLET --chain-id=$OLLO_CHAIN_ID --gas=auto
```

Withdraw rewards with commision
```
ollod tx distribution withdraw-rewards $OLLO_VALOPER_ADDRESS --from=$WALLET --commission --chain-id=$OLLO_CHAIN_ID
```

### Validator management
Edit validator
```
ollod tx staking edit-validator \
  --moniker=$NODENAME \
  --identity=<your_keybase_id> \
  --website="<your_website>" \
  --details="<your_validator_description>" \
  --chain-id=$OLLO_CHAIN_ID \
  --from=$WALLET
```

Unjail validator
```
ollod tx slashing unjail \
  --broadcast-mode=block \
  --from=$WALLET \
  --chain-id=$OLLO_CHAIN_ID \
  --gas=auto
```

### Delete node
This commands will completely remove node from server. Use at your own risk!
```
sudo systemctl stop ollod
sudo systemctl disable ollod
sudo rm /etc/systemd/system/ollo* -rf
sudo rm $(which ollod) -rf
sudo rm $HOME/.ollo* -rf
sudo rm $HOME/ollo -rf
sed -i '/OLLO_/d' ~/.bash_profile
```
