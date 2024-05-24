# eth.eigenlayer.delegation\_manager\_staker\_force\_undelegated

Decoded table for [StakerForceUndelegated](https://github.com/Layr-Labs/eigenlayer-contracts/blob/mainnet/src/contracts/core/DelegationManager.sol#L229) events from the [DelegationManager](https://etherscan.io/address/0x39053d51b77dc0d36036fc1fcc8cb819df8ef37a) contract on Ethereum mainnet.

Emitted when a staker is force undelegated.

| Column Name         | Data Type         | Description                                                   |
| ------------------- | ----------------- | ------------------------------------------------------------- |
| staker              | CHARACTER VARYING | The identifier of the staker.                                 |
| operator            | CHARACTER VARYING | The identifier of the operator.                               |
| transaction_hash    | CHARACTER VARYING | The hash of the transaction.                                  |
| log_index           | BIGINT            | The index of the log within the block.                        |
| block_timestamp     | BIGINT            | The timestamp of the block.                                   |
| block_number        | BIGINT            | The number of the block.                                      |
| block_hash          | CHARACTER VARYING | The hash of the block.                                        |
