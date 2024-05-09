# Fees

The network fees on MemoLayer are not yet finalized and will be subject to change or configuration of MeeDA after it got stabilized. Currently the fees are aligned with Optimism. That is, the transaction fees consist of two parts -- L2 execution fee and L1 data fee.

Compared to L1 network fees, it is a lot cheaper to send transaction on MemoLayer. The execution fee is extremely low on L2 and the data is compressed before sending to MeeDA, so the data fee is also low.

Futhermore, if we consider the cost of uploading data onto L1 blockchain, it will reduce transaction costs significantly by using MeeDA as DA Layer, since only the 'commitment' of those transaction data are needed to be settled on L1. The integration of MeeDA also allows for a fallback to Ethereum if the MeeDA settlement layer encounters issues.
