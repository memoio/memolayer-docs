# Running a Node

Instructions on how to set up your own full node for read-only operations. Two clients are required to run a full node:

- op-geth
- op-node

## op-geth

Use the `op-geth` implementation found within the op-geth [repository](https://github.com/ethereum-optimism/op-geth) from optimism.

Check [here](https://docs.optimism.io/builders/chain-operators/tutorials/create-l2-rollup#initialize-op-geth) to start your `op-geth`. For more configuration options, check out [this page](https://docs.optimism.io/builders/node-operators/configuration/execution-config) to configurate it correctly.

For example: (remember to generate a `jwt` at the outset)

```bash
#!/bin/bash
NETWORK_ID=<your-network-id>
./geth \
  --datadir ./datadir \
  --http \
  --http.corsdomain="*" \
  --http.vhosts="*" \
  --http.addr=0.0.0.0 \
  --http.api=web3,debug,eth,txpool,net,engine \
  --ws \
  --ws.addr=0.0.0.0 \
  --ws.port=8546 \
  --ws.origins="*" \
  --ws.api=debug,eth,txpool,net,engine \
  --syncmode=full \
  --gcmode=archive \
  --nodiscover \
  --maxpeers=0 \
  --networkid=$NETWORK_ID \
  --authrpc.vhosts="*" \
  --authrpc.addr=0.0.0.0 \
  --authrpc.port=8551 \
  --authrpc.jwtsecret=./jwt.txt \
  --rollup.disabletxpoolgossip=true
  > op-geth.log 2>&1 &
```

Each `chainID` is listed below:

| Network Name | chainID |
| --- | --- |
| mainnet | 9859 |
| testnet | 11155985 |
| devnet | 985985 |

For genesis files used on each chain, check [here](https://github.com/memoio/memolayer-config) to download specific genesis file.

## op-node

Use the `op-node` implementation found within the optimism [repository](https://github.com/memoio/optimism) updated by Memolabs. Choose a release branch and compile the `op-node`.

Use the command below to start up your `op-node`: (same `jwt` as generated before)

```bash
#!/bin/bash
L1_RPC_URL=<your-L1-RPC-URL>
L1_RPC_KIND=standard
DA_RPC=<your-DA-RPC>
STATIC_NODE=<your-STATIC-NODE>

./op-node \
  --l2=http://localhost:8551 \
  --l2.jwt-secret=./jwt.txt \
  --sequencer.l1-confs=5 \
  --sequencer.enabled \
  --sequencer.stopped \
  --verifier.l1-confs=4 \
  --rollup.config=./rollup.json \
  --rpc.addr=0.0.0.0 \
  --rpc.port=8547 \
  --p2p.no-discovery \
  --p2p.static=$STATIC_NODE \
  --rpc.enable-admin \
  --l1=$L1_RPC_URL \
  --l1.rpckind=$L1_RPC_KIND \
  --l1.trustrpc \
  --da.rpc=$DA_RPC \
  > op-node.log 2>&1 &
```

For more configuration options, check out [this page](https://docs.optimism.io/builders/node-operators/configuration/consensus-config) to configurate it correctly.

For rollup files used on each chain, check [here](https://github.com/memoio/memolayer-config) to download specific rollup file.

Available `da.rpc` and `p2p.static` are listed below:

| parameter | value |
| --- | --- |
| da.rpc | http://183.240.197.189:11180 |
| p2p.static (mainnet) | /ip4/3.16.113.242/tcp/9222/p2p/16Uiu2HAmNM1HyJLZ7APxzba1zRwWCPi7iSnq7PLy7dQcpxLjLLqu |
| p2p.static (testnet) | /ip4/52.14.111.12/tcp/9222/p2p/16Uiu2HAmE4W3r7eADMgBxCwHLxyt3rfUnpMeUpFBEeBNHAgTvbSb |
| p2p.static (devnet) | /ip4/13.58.245.154/tcp/9222/p2p/16Uiu2HAmSckdyi4jtSkhAbRQRUoEhbJyhRMdYqtCxMxSCNdwrBzr |
| L1_RPC_URL (ethereum) | https://rpc-testnet.memolayer.org |
| L1_RPC_URL (sepolia) | https://rpc-sepolia.memolayer.org |
| L1_RPC_URL (megrez) | http://3.134.99.244:8102 |

You need to connect `op-node` to a L1 node in order to trace L1 blocks. Infura or other similar services are recommended, or run a self-operated full node.

## Synchronization

After these two clients are run correctly, `op-node` will start synchronizing, and new blocks will be introduced to `op-geth`. You can check the status by the logs and `geth.ipc`.
