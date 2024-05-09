# MemoLayer Mainnet

**MeeDA Supported Optimism Superchain**

MemoLayer demonstrates that MeeDA can be integrated into Memo Layer 2 (L2) on the [Optimism](https://optimism.io/) Superchain, launched as a pure Ethereum Virtual Machine (EVM) fork. This L2 chain is designed to serve as a low-cost EVM platform, allowing for active engagement, TVL growth, and project launches.

> What's MeeDA?
> MeeDA is a data availability solution for Ethereum Layer 2. It can be integrated into most OP L2 chains without compromising the security. It enables much cheaper price to store the transaction batches compared to sending them on-chain. Check out for more information in its [repository](https://github.com/memoio/meeda-docs).

Currently, MemoLayer employs a centralized sequencer for transaction processing (like any other OP L2s), with plans to adopt a decentralized model. This transition towards a shared sequencer across the Optimism chains is a crucial step towards achieving greater security and transparency within the ecosystem.

Importantly, MemoLayer has been deployed on the Ethereum mainnet, operating with real assets, which sets it apart from traditional testnets. Users and developers engaging with MemoLayer should be aware of its real-stakes environment.

## Use the MemoLayer

### Metamask

[Download & Install Metamask](https://metamask.io/)

Visit the [Memo](https://memolabs.org/) website and simply click the "Connect to Mainnet" button, or proceed with the following instructions for alternative methods.

To add MemoLayer as a custom network to MetaMask:

1. Open the MetaMask browser extension.
2. Click the network selection dropdown button at the top of the extension.
3. Select **Add network**.
4. Choose **Add a network manually**.
5. In the **Add a network manually** dialog, enter the following information:
    1. **Network Name**: MemoLayer
    2. **RPC Endpoint**:
    3. **Chain ID**: 9859
    4. **Currency Symbol**: ETH
    5. **Block Explorer**:

### Other Wallet

You can do the same with other EVM based wallets.

Additionally, support for the Pontem wallet is on the horizon, further enhancing accessibility and user experience right from the start.

## Bridge

The bridging process can take anywhere from 1 minute to 30 minutes. Please plan accordingly.

MemoLayer enables users to deposit ETH and other tokens, such as USDC, USDT, and more, from the Ethereum L1 mainnet. This can be accomplished through the native L1 to L2 bridge, which is accessible via the following links, including those from our partners:

[Memo Bridge]() - This bridge is maintained by Memo. It's important to note that while the bridge supports deposits of ETH and other tokens from the Ethereum L1 mainnet, withdrawals are not yet available through the user interface. We are diligently working on implementing this feature and expect it to be available shortly. 

## Network Information

<table>
  <tr>
    <td>Network Name</td>
    <td>MemoLayer</td>
  </tr>
  <tr>
    <td>RPC Endpoint</td>
    <td></td>
  </tr>
  <tr>
    <td>Chain ID</td>
    <td>9859</td>
  </tr>
  <tr>
    <td>Currency Symbol</td>
    <td>ETH</td>
  </tr>
  <tr>
    <td>Block Explorer</td>
    <td></td>
  </tr>
  <tr>
    <td>Bridge</td>
    <td></td>
  </tr>
  <tr>
    <td>Block Time</td>
    <td>2 secs</td>
  </tr>
  <tr>
    <td>L1</td>
    <td>Ethereum Mainnet</td>
  </tr>
</table>

## Contracts

**L1**

The contracts deployed on the Ethereum Mainnet are:

| Contract Name | Deployment Addresses |
| :--- | :--- |
| SystemConfigProxy | 0xc6d310539CDEc791a10Fdce9f08774B70cF7B07D |
| L2OutputOracleProxy | 0x1F011aF2ef1D8446938f4EF07fA262d33578e781 |
| OptimismPortalProxy | 0xf0caDf6E9A8C1Ff66b156e33A2CC65d061E7E2C8 |
| OptimismMintableERC20FactoryProxy | 0x5f29dD23eE51B0666A52588E54C0B895202334aD |
| L1StandardBridgeProxy | 0x7898059dCDf0d2CEA662aEa8914cb14921A57061 |
| L1CrossDomainMessengerProxy | 0xCA1d36F843eb189e25d4F2A6cc2B15D1456f956f |
| L1ERC721BridgeProxy | 0x2d3bFd7d61A5CE45e088331440C2545DDDd64907 |

**L2**

The deployment addresses for L2 contracts are consistent across the entire Optimism stack, which means they are pre-deployed. You can find these addresses as constants in the Optimism [repository](https://github.com/ethereum-optimism/optimism/blob/c87a469d7d679e8a4efbace56c3646b925bcc009/packages/core-utils/src/optimism/constants.ts#L11).

## Fees

The fee model is akin to Optimism's, based on the Data Availability (DA) costs of transactions and execution expenses. For more details on L2 fees, please refer to further [documentation](https://docs.optimism.io/stack/transactions/fees). 

With our implementation of MeeDA integrating into Optimism, fees have become relatively more affordable. Instead of uploading a whole bunch of transactions onto the Mainnet (via `calldata` or `blob` in the batch transaction), only a short-bytes commitment is attached to the `calldata`. This dramatically reduces the gasfee in L1 Mainnet.
