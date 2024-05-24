# eth.eigenlayer.delegation\_manager\_operator\_metadata\_uri\_updated

Decoded table for [OperatorMetadataUriUpdated](https://github.com/Layr-Labs/eigenlayer-contracts/blob/mainnet/src/contracts/core/DelegationManager.sol#L132) events (or from [`registerAsOperator` function](https://github.com/Layr-Labs/eigenlayer-contracts/blob/mainnet/src/contracts/core/DelegationManager.sol#L111)) from the [DelegationManager](https://etherscan.io/address/0x39053d51b77dc0d36036fc1fcc8cb819df8ef37a) contract on Ethereum mainnet.

Emitted when the operator metadata URI is updated.

| Column Name         | Data Type         | Description                                       |
| ------------------- | ----------------- | ------------------------------------------------- |
| operator            | CHARACTER VARYING | The identifier of the operator.                   |
| metadata_uri        | CHARACTER VARYING | The URI of the metadata.                          |
| transaction_hash    | CHARACTER VARYING | The hash of the transaction.                      |
| log_index           | BIGINT            | The index of the log within the block.            |
| block_timestamp     | BIGINT            | The timestamp of the block.                       |
| block_number        | BIGINT            | The number of the block.                          |
| block_hash          | CHARACTER VARYING | The hash of the block.                            |
