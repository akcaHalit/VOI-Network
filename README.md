! Learnt from [Rues](https://github.com/ruesandora).
<h1 align="center">Voi Network</h1>

> All tokens obtained on the testnet are given as a `reward` on the mainnet, so even the test tokens you request are `important`.

> Currently, we are not obtaining many test tokens, block proposals will start from `October 2nd`. You can set up at that time if you wish.

> They stated that the testnet will last a total of `3 months` (`minimum`, subject to change depending on the time) in `3 stages`.

> Rues Community: [Duyuru](https://t.me/RuesAnnouncement) - [Chat](https://t.me/RuesChat) - [Discord](https://discord.gg/t7qVBD6m) 
> [Whitepaper](https://afaf83a4-6c33-4e2a-a40c-9999410c0063.filesusr.com/ugd/7dc173_8e16834f2fbd4866a957d441f392d578.pdf)

<h1 align="center">Harware ve Updates</h1>

```console
# This is sufficient despite the excess in the documentation. !!(I installed Ubuntu 20.04)!!
4 CPU 8 RAM 80 SSD - Ubuntu 22.04
# My recommendation is to install it next to your Ar-io node, that's what I did, I didn't buy a separate server.
# If you don't have Ar-io, you can use another node, no need for a separate server for now.
```

> If you are located roughly in the location I showed in the visual, you can be at the top and receive more rewards;

![image](https://github.com/ruesandora/Voi/assets/101149671/a4acf712-b470-4ce7-bfb2-7bff3d47580e)

```console
# Updates:
sudo apt update && sudo apt-get upgrade -y
sudo systemctl start unattended-upgrades && sudo systemctl enable unattended-upgrades
```

<h1 align="center">Node Setup</h1>

```console
# We're not always installing Cosmos, we're installing Algorand:
sudo apt install -y jq gnupg2 curl software-properties-common
curl -o - https://releases.algorand.com/key.pub | sudo tee /etc/apt/trusted.gpg.d/algorand.asc

# You can press ENTER for its output.
sudo add-apt-repository "deb [arch=amd64] https://releases.algorand.com/deb/ stable main"

# Let's update again and stop the node from starting automatically:
sudo apt update && sudo apt install -y algorand && echo OK
sudo systemctl stop algorand && sudo systemctl disable algorand && echo OK

# Let's do the goal setup
echo -e "\nexport ALGORAND_DATA=/var/lib/algorand/" >> ~/.bashrc && source ~/.bashrc && echo OK
sudo adduser $(whoami) algorand && echo OK

# Configuration process:
sudo algocfg set -p DNSBootstrapID -v "<network>.voi.network" -d /var/lib/algorand/ &&\
sudo algocfg set -p EnableCatchupFromArchiveServers -v true -d /var/lib/algorand/ &&\
sudo chown algorand:algorand /var/lib/algorand/config.json &&\
sudo chmod g+w /var/lib/algorand/config.json &&\
echo OK

# Genesis
sudo curl -s -o /var/lib/algorand/genesis.json https://testnet-api.voi.nodly.io/genesis &&\
sudo chown algorand:algorand /var/lib/algorand/genesis.json &&\
echo OK
```

<h1 align="center">Run the Node</h1>

```console
# Let's configure Algorand as Voi:
sudo cp /lib/systemd/system/algorand.service /etc/systemd/system/voi.service &&\
sudo sed -i 's/Algorand daemon/Voi daemon/g' /etc/systemd/system/voi.service &&\
echo OK

# and let's start the node:
sudo systemctl start voi && sudo systemctl enable voi && echo OK

# let's check the node status:
goal node status

# ==> Genesis ID: voitest-v1
# ==> Genesis hash: IXnoWtviVVJW5LGivNFc0Dq14V3kqaXuK2u5OQrdVZo=
# The output should end like this (hash may vary)

# Let's sync quickly:
goal node catchup $(curl -s https://testnet-api.voi.nodly.io/v2/status|jq -r '.["last-catchpoint"]') &&\
echo OK
# Let's wait a few minutes here

# Let's check the status again but this time we'll see Catchpoint in the logs:
goal node status

# Let's wait for the Sync Time to be reset and Catchpoint to be removed from the logs when we check with this command.
goal node status -w 1000
# When the above conditions are met, press CTRL + C
```

<h1 align="center">Let's Telemetrize to Receive Rewards</h1>

```console
# Edit the part that says darthTest and remove the <> quotation marks
sudo ALGORAND_DATA=/var/lib/algorand diagcfg telemetry name -n <darthTest> - RuesCommunity

sudo ALGORAND_DATA=/var/lib/algorand diagcfg telemetry enable &&\
sudo systemctl restart voi
```

<h1 align="center">Wallet Creation Process</h1>

```console
# Let's create a wallet:
goal wallet new voi
# After setting the password, press Y and store your 24 words.

# Now let's import our wallet into our node:
goal account import
# Enter the password and 24 words, it will give us an Imported address, let's store it, it's our wallet address.

# Now let's enter these codes and it will ask for our Imported address.
# You can copy-paste this code at once
echo -ne "\nEnter your voi address: " && read addr &&\
echo -ne "\nEnter duration in rounds [press ENTER to accept default (2M)]: " && read duration &&\
start=$(goal node status | grep "Last committed block:" | cut -d\  -f4) &&\
duration=${duration:-2000000} &&\
end=$((start + duration)) &&\
dilution=$(echo "sqrt($end - $start)" | bc) &&\
goal account addpartkey -a $addr --roundFirstValid $start --roundLastValid $end --keyDilution $dilution
# After the Imported address, press ENTER to accept the default.
# Wait for the import process to complete and save your Participation ID.

# Let's check our activity, here the output should be !!OFFLINE!!
checkonline() {
  if [ "$addr" == "" ]; then echo -ne "\nEnter your voi address
```

> To continue beyond this stage, get tokens from Discord. `node-runners channel` => `/voi-testnet-faucet`.

> Check [Explorer](https://voi.observer/explorer/home) with your imported address and continue when the token arrives.


<h1 align="center">After Receiving Tokens</h1>

```console
# If you have received your token, let's go online with this command!!
getaddress() {
  if [ "$addr" == "" ]; then echo -ne "\nEnter your voi address: " && read addr; else echo ""; fi
}
getaddress &&\
goal account changeonlinestatus -a $addr -o=1 &&\
sleep 1 &&\
goal account dump -a $addr | jq -r 'if (.onl == 1) then "You are online!" else "You are offline." end'
```

![image](https://github.com/ruesandora/Voi/assets/101149671/6b030e34-9619-4191-a136-6312f94ba7cb)


<h1 align="center">Post-Node Installation Tasks</h1>

> After installing the node,  `1-2 days` later, search for the first 6 digits of your wallet in the `#VoiScout-testnet` channel.

> If you are already `online`, that's correct, but if you also appear here, it means you have executed a proposal. (It's a good thing, it doesn't mean the node isn't working)

> Check yourself [Here](https://cswenor.github.io/voi-proposer-data/health.html)(`Hour`).
