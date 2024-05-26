# eth.eigenlayer.delegation\_manager\_withdrawal\_migrated

Decoded table for [WithdrawalQueued](https://github.com/Layr-Labs/eigenlayer-contracts/blob/mainnet/src/contracts/core/DelegationManager.sol#L741) events from the [DelegationManager](https://etherscan.io/address/0x39053d51b77dc0d36036fc1fcc8cb819df8ef37a) contract on Ethereum mainnet.

Emitted when a withdrawal is queued.

| Column Name         | Data Type         | Description                                                   |
| ------------------- | ----------------- | ------------------------------------------------------------- |
| `depositor`         | CHARACTER VARYING | The identifier of the depositor.                              |
| `nonce`             | DECIMAL           | The nonce value for the transaction.                          |
| `nonce_hex`         | CHARACTER VARYING | The nonce value in hexadecimal format.                        |
| `start_block`       | BIGINT            | The starting block number for the transaction.                |
| `shares_hex`        | ARRAY             | The shares involved in the transaction in hexadecimal format. |
| `withdrawer`        | CHARACTER VARYING | The identifier of the withdrawer.                             |
| `delegated_address` | CHARACTER VARYING | The address to which the withdrawal is delegated.             |
| `withdrawal_root`   | CHARACTER VARYING | The root hash of the withdrawal operation.                    |
| `transaction_hash`  | CHARACTER VARYING | The hash of the transaction.                                  |
| `log_index`         | BIGINT            | The index of the log within the block.                        |
| `block_timestamp`   | BIGINT            | The timestamp of the block.                                   |
| `block_number`      | BIGINT            | The number of the block.                                      |
| `block_hash`        | CHARACTER VARYING | The hash of the block.                                        |
