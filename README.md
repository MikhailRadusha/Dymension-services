# Dymension-services
[StingRay validator](https://www.mintscan.io/dymension/validators/dymvaloper1vgptdq22lpjcdeh9a5fp29uzf3xuw93kl2pa3c)
=

 <h1 align="center"> mainnet public endpoints</h1>

API:          https://api-dymension.stingray.plus  \
RPC:          https://rpc-dymension.stingray.plus                  `Snapshot-interval = 1000` \
GRPC:         httpc://grpc-dymension.stingray.plus:443 


# StateSync Dymension Mainnet
```python
systemctl stop dymd
SNAP_RPC=https://rpc-sei.stingray.plus
peers="bae333d3ccd605909d00d7d8956db41d3683b329@65.109.88.96:21657"
sed -i.bak -e "s/^persistent_peers *=.*/persistent_peers = \"$peers\"/" $HOME/.dymension/config/config.toml

LATEST_HEIGHT=$(curl -s $SNAP_RPC/block | jq -r .result.block.header.height); \
BLOCK_HEIGHT=$((LATEST_HEIGHT - 1000)); \
TRUST_HASH=$(curl -s "$SNAP_RPC/block?height=$BLOCK_HEIGHT" | jq -r .result.block_id.hash); \
echo $LATEST_HEIGHT $BLOCK_HEIGHT $TRUST_HASH

sed -i.bak -E "s|^(enable[[:space:]]+=[[:space:]]+).*$|\1true| ; \
s|^(rpc_servers[[:space:]]+=[[:space:]]+).*$|\1\"$SNAP_RPC,$SNAP_RPC\"| ; \
s|^(trust_height[[:space:]]+=[[:space:]]+).*$|\1$BLOCK_HEIGHT| ; \
s|^(trust_hash[[:space:]]+=[[:space:]]+).*$|\1\"$TRUST_HASH\"| ; \
s|^(seeds[[:space:]]+=[[:space:]]+).*$|\1\"\"|" $HOME/.dymension/config/config.toml

dymd tendermint unsafe-reset-all --home /root/.dymension --keep-addr-book

systemctl restart dymd && journalctl -u dymd -f -o cat

```
