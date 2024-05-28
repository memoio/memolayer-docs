# The Standard Bridge

You can find those operations below:

1. How to deposit L1 native tokens (e.g., ETH) to L2 native tokens?
2. How to withdraw L2 native tokens back to L1 native tokens?
3. How to create a representation on L2 for an L1 ERC20 native token?
4. How to transfer ERC20 native tokens on L1 to representation tokens on L2?
5. How to withdraw ERC20 representation?

Note that the ERC20 token and the representation mentioned above are all in standard form, so they can be bridging through the `StandardBridge` .

More guidance and information can be found on [optimism](https://docs.optimism.io/builders/app-developers/bridging/standard-bridge).

## Bridging Native Token

### L1 to L2

There are two ways to transfer L1 native tokens to L2, let us assume that the native token of L1 is `ETH`.

1. Directly transfer `ETH` to `L1StandardBridgeProxy`. Then the call will fall into the `fallback` function of the contract. Same address on L2 will receive the same amount of `ETH`.
2. Using [SDK](https://docs.optimism.io/builders/app-developers/tutorials/cross-dom-bridge-eth) to call `depositETH` or `depositETHTo`. Method 1 is the same as Way 1, and Method 2 can transfer `ETH` to another account address on L2.

Same amount of `ETH` will be minted on L2 after the transaction is confirmed on L1. This process will take 1-3 minutes to get done. Then you can check your wallet balance on L2.

If you're using SDK to make this transfer, find contract addresses on this [page](../../network/contracts.md) to initialize ([initialization](https://docs.optimism.io/builders/chain-operators/tutorials/sdk) is necessary for SDK). **Note that you should use the `Proxy` address instead of the original contract address when it is available.**

### L2 to L1

Compared to the transfer from L1 to L2, it is a bit more complicated to withdraw tokens on L2 back to L1. It involves several steps interacting with contracts on both chains. Therefore, although it is possible to leverage `L2StandardBridge` and manually interact with some contracts on L1, to avoid misoperation, [SDK](https://docs.optimism.io/builders/app-developers/tutorials/cross-dom-bridge-eth) is recommended in this case. 

Normally, five steps are needed:

1. Send a withdrawal transaction on L2. Similarly, call `bridgeETHTo` with some tokens to withdraw.
2. Wait for the proposal of L2 is proposed to L1 by proposer. (mainnet: 12 hours, testnet: 1 hours, devnet: 5 minutes)
3. Send an L1 transaction to prove the withdrawal.
4. Wait for the fault challenge period. (explained below)
5. Send an L1 transaction to trigger the finalization.

The withdrawal take time to validate and confirm. The expected time elapse depends on the parameter `finalizationPeriodSeconds` which is set on chain. Different network may have different value. Mainnet based on Ethereum mainnet is 604800 seconds (7 days), testnet based on Sepolia is 12 seconds (1 block), devnet based on Megrez is 5 seconds (1 block).

It is suggested to test this process on testnet or devnet.

## Bridging ERC20 Native Token

The process and workflow of bridging ERC20 are detailly illustrated on the [page](https://docs.optimism.io/builders/app-developers/bridging/standard-bridge). Specific operations and steps are concisely described below.

### Create a Representation

Firstly, you need to create a representation of an L1 ERC20 token by deploying a corresponding contract on L2 (you can also refer to [this](https://docs.optimism.io/builders/app-developers/tutorials/standard-bridge-standard-token)):

Let's assume you have released an ERC20 token on L1 like this:

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

import { ERC20 } from "https://github.com/OpenZeppelin/openzeppelin-contracts/blob/ef699fa6a224de863ffe48347a5ab95d3d8ba2ba/contracts/token/ERC20/ERC20.sol";

/// @notice FaucetTestingToken is a simple ERC20 for testing.
contract FaucetTestingToken is ERC20 {
    /// @notice Creates the ERC20 with standard parameters.
    constructor() 
        ERC20("FaucetTestingToken", "FAUCET")
    {}

    /// @notice Mints the sender 1000 tokens.
    function faucet() external {
        _mint(msg.sender, 1000e18);
    }
}
```

It's an ERC20 contract in which you can make `transferFrom`, `approve`. `Faucet` is a custom method to fund some accounts for testing.

After you have called `approve` for `L1StandardBridgeProxy` contract (e.g., give the bridge an allowance of 1000 FAUCET), then you have fulfilled all requirements on L1. It's time to create a representation for this contract on L2.

There are two ways to create a representation:

1. Use `OptimismMintableERC20Factory` contract on L2 to deploy a standard representation contract.
2. Implement `IOptimismMintableERC20` interface in your own custom representation contract on L2.

For Way 1, you just need to call the method `createOptimismMintableERC20` in that pre-deployed contract locating at the address `0x4200000000000000000000000000000000000012`. Set `_remoteToken` as the L1 address of your ERC20 contract, `_name` and `_symbol` as the representation name and symbol of this contract on L2 (can be different from L1).

For Way 2, an applicable bridged representation of a token is an ERC20 token that implements the `IOptimismMintableERC20` interface, which is listed below:

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

import { IERC165 } from "@openzeppelin/contracts/utils/introspection/IERC165.sol";

/**
 * @title IOptimismMintableERC20
 * @notice This interface is available on the OptimismMintableERC20 contract. We declare it as a
 *         separate interface so that it can be used in custom implementations of
 *         OptimismMintableERC20.
 */
interface IOptimismMintableERC20 is IERC165 {
    function remoteToken() external view returns (address);

    function bridge() external returns (address);

    function mint(address _to, uint256 _amount) external;

    function burn(address _from, uint256 _amount) external;
}
```

After implementing these interface, remember to set correct value for `_bridge` and `_remoteToken` in `constructor`. Normally, `_bridge` on L2 is `0x4200000000000000000000000000000000000010` and `_remoteToken` is the address of your L1 ERC20 contract.

### transfer and withdraw

Refer to this [page](https://docs.optimism.io/builders/app-developers/tutorials/cross-dom-bridge-erc20). Use SDK to call specific method. Note that `approve` is necessary ahead of those operations.
